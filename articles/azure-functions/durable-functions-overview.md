---
title: "Vue d’ensemble de Fonctions durables - Azure (préversion)"
description: "Introduction à l’extension Fonctions durables pour Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: f1def2a43edee58bc8b5a33880e206130a1b4687
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="durable-functions-overview-preview"></a>Vue d’ensemble de Fonctions durables (préversion)

*Fonctions durables* est une extension d[’Azure Functions](functions-overview.md) et d[’Azure WebJobs](../app-service/web-sites-create-web-jobs.md) qui vous permet d’écrire des fonctions avec état dans un environnement sans serveur. L’extension gère l’état, les points de contrôle et les redémarrages à votre place.

L’extension vous permet de définir des flux de travail avec état dans un nouveau type de fonction appelé une *fonction d’orchestrateur*. Voici quelques-uns des avantages des fonctions d’orchestrateur :

* Elles définissent des flux de travail dans le code. Elles ne nécessitent aucun schéma JSON ou concepteur.
* Elles peuvent appeler d’autres fonctions de façon synchrone et asynchrone. La sortie des fonctions appelées peut être enregistrée dans des variables locales.
* Elles créent automatiquement des points de contrôle de leur progression chaque fois que la fonction attend. L’état local n’est jamais perdu si le processus est recyclé ou si la machine virtuelle redémarre.

> [!NOTE]
> Fonctions durables (en préversion) est une extension avancée pour Azure Functions qui ne convient pas à toutes les applications. Le reste de cet article suppose que vous maîtrisez parfaitement les concepts [Azure Functions](functions-overview.md) et les défis qu’impose le développement d’applications sans serveur.

Le principal cas d’usage principal pour Fonctions durables est la simplification de problèmes complexes de coordination avec état dans des applications sans serveur. Les sections suivantes décrivent certains modèles d’application standard qui peuvent tirer parti de Fonctions durables.

## <a name="pattern-1-function-chaining"></a>Modèle 1 : chaînage de fonctions

*Chaînage de fonctions* fait référence au modèle d’exécution d’une séquence de fonctions dans un ordre particulier. La sortie d’une fonction doit souvent être appliquée à l’entrée d’une autre fonction.

![Diagramme de chaînage de fonctions](media/durable-functions-overview/function-chaining.png)

Fonctions durables vous permet d’implémenter ce modèle de manière concise dans le code.

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

Les valeurs « F1 », « F2 », « F3 » et « F4 » représentent les noms des autres fonctions dans l’application de la fonction. Le flux de contrôle est implémenté à l’aide de constructions de code impératives normales. Autrement dit, le code s’exécute de haut en bas et peut impliquer une sémantique de flux contrôle de langage existante, notamment des instructions conditionnelles et des boucles.  Une logique de gestion des erreurs peut être incluse dans les blocs try/catch/finally.

Le paramètre `ctx` ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) fournit des méthodes permettant d’appeler d’autres fonctions par nom, de transmettre des paramètres et de retourner la sortie d’une fonction. Chaque fois que le code appelle `await`, l’infrastructure Fonctions durables *crée des points de contrôle* de la progression de l’instance de la fonction actuelle. En cas de recyclage du processus ou de la machine virtuelle au milieu de l’exécution, l’instance de la fonction reprend à partir de l’appel `await` précédent. De plus amples informations sur ce comportement de redémarrage seront présentées ultérieurement.

## <a name="pattern-2-fan-outfan-in"></a>Modèle 2 : fan-out/fan-in

*Fan-out/fan-in* fait référence à un modèle qui exécute plusieurs fonctions en parallèle puis attend que toutes ces fonctions se terminent.  Un travail d’agrégation est souvent effectué sur les résultats retournés par les fonctions.

![Diagramme Fan-out/fan-in](media/durable-functions-overview/fan-out-fan-in.png)

Avec des fonctions normales, le processus fan-out peut être effectué en configuration la fonction afin qu’elle envoie plusieurs messages vers une file d’attente. Mais le processus fan-in est beaucoup plus difficile. Vous devez écrire du code pour effectuer le suivi lorsque les fonctions déclenchées en file d’attente se terminent puis stocker les sorties des fonctions. L’extension Fonctions durables gère ce modèle avec un code relativement simple.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

Le processus fan-out est réparti sur plusieurs instances de la fonction `F2`, puis suivi à l’aide d’une liste dynamique de tâches. L’API .NET `Task.WhenAll` est appelée pour attendre la fin de toutes les fonctions appelées. Les sorties de la fonction `F2` sont ensuite agrégées à partir de la liste dynamique de tâches puis transmises à la fonction `F3`.

La création automatique de points de contrôle qui se produit lors de l’appel `await` sur `Task.WhenAll` garantit qu’un incident ou qu’un redémarrage survenu au milieu du processus ne nécessite aucun redémarrage d’une quelconque tâche déjà complétée.

## <a name="pattern-3-async-http-apis"></a>Modèle 3 : API HTTP Async

Le troisième modèle concerne le problème de coordination de l’état des opérations de longue durée avec des clients externes. Une méthode courante pour implémenter ce modèle consiste à déclencher l’action de longue durée par un appel HTTP, puis à rediriger le client vers un point de terminaison d’état interrogeable pour savoir quand l’opération se termine.

![Diagramme de l’API HTTP](media/durable-functions-overview/async-http-api.png)

Fonctions durables fournit des API intégrées qui simplifient le code que vous écrivez pour interagir avec les exécutions de fonctions de longue durée. Les [exemples](durable-functions-install.md) montrent une commande REST simple qui peut être utilisée pour démarrer de nouvelles instances de fonction d’orchestrateur. Lorsqu’une instance est démarrée, l’extension expose des API HTTP webhook qui interrogent l’état de la fonction d’orchestrateur. L’exemple suivant montre les commandes REST permettant de démarrer un orchestrateur et d’interroger son état. Pour plus de clarté, certains détails ont été retirés de l’exemple.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Étant donné que l’état est géré par le runtime de Fonctions durables, vous n’avez pas à implémenter votre propre mécanisme de suivi de l’état.

Même si l’extension Fonctions durables intègre des webhooks pour la gestion des orchestrations de longue durée, vous pouvez implémenter ce modèle vous-même à l’aide de vos propres déclencheurs de fonction (par exemple HTTP, une file d’attente ou Event Hub) et la liaison `orchestrationClient`. Ainsi, vous pouvez utiliser un message de file d’attente pour déclencher l’arrêt.  Sinon, servez-vous d’un déclencheur HTTP protégé par une stratégie d’authentification Azure Active Directory à la place des webhooks intégrés qui utilisent une clé générée pour l’authentification. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

Le paramètre [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` est une valeur provenant de la liaison de sortie `orchestrationClient`, qui fait partie de l’extension Fonctions durables. Il fournit des méthodes pour démarrer, envoyer des événements, terminer, et rechercher les instances de fonctions d’orchestrateur nouvelles ou existantes. Dans l’exemple ci-dessus, une fonction déclenchée par HTTP utilise une valeur `functionName` provenant de l’URL entrante et transmet cette valeur à [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Cette API de liaison renvoie ensuite une réponse qui contient un en-tête `Location` et des informations supplémentaires sur l’instance, qui peuvent être utilisées ultérieurement pour rechercher l’état de l’instance démarrée ou y mettre fin.

## <a name="pattern-4-stateful-singletons"></a>Modèle 4 : singletons avec état

La plupart des fonctions ont un début et une fin explicites, et n’interagissent pas directement avec les sources d’événements externes. Cependant, les orchestrations prennent en charge un modèle [singleton avec état](durable-functions-singletons.md) qui leur permet de se comporter comme des [acteurs](https://en.wikipedia.org/wiki/Actor_model) fiables dans une informatique distribuée.

Le diagramme suivant illustre une fonction qui s’exécute dans une boucle infinie tout en traitant les événements reçus à partir de sources externes.

![Diagramme Singleton avec état](media/durable-functions-overview/stateful-singleton.png)

Même si Fonctions durables n’est pas une implémentation du modèle acteur, les fonctions d’orchestrateur partagent plusieurs des mêmes caractéristiques d’exécution. Par exemple, elles sont de longue durée (voire infinies), avec état, fiables, monothread, transparentes au niveau de leur emplacement et globalement adressables. Ces fonctions d’orchestrateur peuvent donc être utiles dans des scénarios de type « acteur ».

Les fonctions ordinaires sont sans état et par conséquent pas adaptées à l’implémentation d’un modèle de singleton avec état. Mais avec l’extension Fonctions durables, le modèle de singleton avec état est relativement simple à implémenter. Le code suivant est une fonction d’orchestrateur simple qui implémente un compteur.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int counterState = ctx.GetInput<int>();

    string operation = await ctx.WaitForExternalEvent<string>("operation");
    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }

    ctx.ContinueAsNew(counterState);
}
```

Ce code peut être décrit comme une « orchestration externe » &mdash; , autrement dit, une orchestration qui commence mais ne se termine jamais. Il exécute les étapes suivantes :

* Commence par une valeur d’entrée dans `counterState`.
* Attend indéfiniment un message appelé `operation`.
* Applique une certaine logique pour mettre à jour son état local.
* « Redémarre » en appelant `ctx.ContinueAsNew`.
* Attend de nouveau indéfiniment l’opération suivante.

## <a name="pattern-5-human-interaction"></a>Modèle 5 : interaction humaine

De nombreux processus impliquent un certain type d’interaction humaine. La difficulté d’impliquer des personnes dans un processus automatisé réside dans le fait que ces personnes ne sont pas toujours aussi disponibles et réactives que les services cloud. Des processus automatisés doivent être mis en place, qui utilisent souvent des délais d’expiration et une logique de compensation.

Un processus d’approbation est un exemple de processus d’entreprise impliquant une interaction humaine. Par exemple, l’approbation d’un manager peut être requise si une note de frais dépasse un certain montant. Si le manager n’approuve pas cette note de frais sous 72 heures (par exemple, s’il est en vacances), un processus d’escalade est déclenché pour obtenir l’approbation d’une autre personne (par exemple, le supérieur hiérarchique de ce manager).

![Diagramme Interaction humaine](media/durable-functions-overview/approval.png)

Ce modèle peut être implémenté à l’aide d’une fonction d’orchestrateur. L’orchestrateur utilise un [minuteur durable](durable-functions-timers.md) pour demander l’approbation et la faire remonter en cas de délai d’expiration. Il attend un [événement externe](durable-functions-external-events.md), soit la notification générée par une intervention humaine.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

Le minuteur durable est créé en appelant `ctx.CreateTimer`. La notification est reçue par `ctx.WaitForExternalEvent`. Puis `Task.WhenAny` est appelée pour déterminer s’il faut faire remonter (le délai d’expiration survient en premier) ou traiter l’approbation (approbation reçue avant le délai d’expiration).

## <a name="the-technology"></a>La technologie

En arrière-plan, l’extension Fonctions durables est construite par-dessus l[’infrastructure des tâches durables](https://github.com/Azure/durabletask), une bibliothèque open source sur GitHub pour la création d’orchestrations de tâches durables. Tout comme Azure Functions est l’évolution sans serveur d’Azure Webjobs, Fonctions durables est l’évolution sans serveur de l’infrastructure des tâches durables. L’infrastructure des tâches durables est très utilisée au sein de Microsoft et à l’extérieur pour automatiser des processus critiques. Il convient parfaitement à l’environnement Azure Functions sans serveur.

### <a name="event-sourcing-checkpointing-and-replay"></a>Approvisionnement d’événements, création de points de contrôle et réexécution

Les fonctions d’orchestrateur conservent de façon fiable leur état d’exécution à l’aide d’un modèle de conception cloud appelé [approvisionnement d’événements](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Au lieu de stocker directement l’état *actuel* d’une orchestration, l’extension durable utilise un magasin d’ajout uniquement pour enregistrer *toute la série d’actions* exécutées par l’orchestration de la fonction. Cette méthode offre de nombreux avantages, notamment une amélioration des performances, de l’évolutivité et de la réactivité par rapport au « vidage » de l’état d’exécution complet. Autres avantages : elle garantit la cohérence des données transactionnelles et fournit des pistes d’audit complètes ainsi qu’un historique. Les pistes d’audit permettent des actions de compensation fiables.

L’utilisation de l’approvisionnement d’événements par cette extension est transparente. En coulisse, l’opérateur `await` d’une fonction d’orchestrateur cède le contrôle du thread orchestrateur au répartiteur de l’infrastructure des tâches durables. Le répartiteur valide ensuite dans le stockage toutes les actions que la fonction d’orchestrateur a planifiées (par exemple, l’appel d’une ou plusieurs fonctions enfant ou la planification d’un minuteur durable). Cette action de validation transparente s’ajoute à *l’historique d’exécution* de l’instance d’orchestration. L’historique est stocké dans une table de stockage. L’action de validation ajoute ensuite des messages à une file d’attente pour planifier le travail réel. À ce stade, la fonction d’orchestrateur peut être déchargée de la mémoire. Sa facturation s’arrête si vous utilisez le plan de consommation Azure Functions.  Si d’autres tâches doivent être effectuées, la fonction redémarre et son état est reconstruit.

Lorsqu’une fonction d’orchestration reçoit plus de tâches à effectuer (par exemple, un message de réponse est reçu ou un minuteur durable expire), l’orchestrateur sort à nouveau de veille et réexécute toute la fonction depuis le début afin de reconstruire l’état local. Si au cours de la réexécution, le code tente d’appeler une fonction (ou toute autre tâche asynchrone), l’infrastructure des tâches durables consulte *l’historique d’exécution* de l’orchestration en cours. Si elle constate que la fonction de l’activité a déjà été exécutée et a produit un résultat, elle réexécute les résultats de cette fonction, et le code d’orchestrateur continue de s’exécuter. Ce processus se poursuit jusqu'à ce que le code de la fonction atteint un point où il se termine ou s’il a planifié une nouvelle tâche asynchrone.

### <a name="orchestrator-code-constraints"></a>Contraintes du code d’orchestrateur

Le comportement de réexécution crée des contraintes concernant le type de code qui peut être écrit dans une fonction d’orchestrateur. Par exemple, le code d’orchestrateur doit être déterministe car il sera réexécuté à plusieurs reprises et doit générer le même résultat à chaque fois. Vous trouverez la liste complète des contraintes dans la section, [Contraintes du code d’orchestrateur](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) de l’article **Création de points de contrôle et redémarrage**.

## <a name="language-support"></a>Support multilingue

Le langage C# est actuellement le seul langage pris en charge par Fonctions durables. Cela inclut les fonctions d’orchestrateur et les fonctions d’activité. À l’avenir, nous inclurons tous les langages pris en charge par Azure Functions. Consultez la liste des [problèmes de référentiel GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) Azure Functions pour afficher les dernières informations concernant la prise en charge de langages supplémentaires.

## <a name="monitoring-and-diagnostics"></a>Surveillance et diagnostics

L’extension Fonctions durables transmet automatiquement des données de suivi structurées à [Application Insights](functions-monitoring.md) quand l’application de fonction est configurée avec une clé d’instrumentation Application Insights. Ces données de suivi peuvent être utilisées pour surveiller le comportement et la progression de vos orchestrations.

Voici un exemple montrant à quoi ressemblent les événements de suivi Fonctions durables dans le portail Application Insights en utilisant [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics) :

![Résultats d’une requête App Insights](media/durable-functions-overview/app-insights-1.png)

Le champ `customDimensions` de chaque entrée de journal contient de nombreuses données structurées. Voici un exemple d’une telle entrée, entièrement développée.

![Champ customDimensions dans la requête App Insights](media/durable-functions-overview/app-insights-2.png)

En raison du comportement de réexécution du répartiteur de l’infrastructure des tâches durables, attendez-vous à voir des entrées de journal redondantes pour les actions réexécutées. Il peut être utile de comprendre le comportement de réexécution du moteur central. L’article [Diagnostics](durable-functions-diagnostics.md) montre des exemples de requêtes qui filtrent les journaux de réexécution pour n’afficher que les journaux « en temps réel ».

## <a name="storage-and-scalability"></a>Stockage et évolutivité

L’extension Fonctions durables utilise des files d’attente Stockage Azure, des tables et des objets blob pour conserver l’état de l’historique d’exécution et déclencher l’exécution d’une fonction. Vous pouvez utiliser le compte de stockage par défaut pour l’application de la fonction ou configurer un compte de stockage distinct. Vous pouvez choisir un compte distinct en raison de limites de débit de stockage. Le code d’orchestrateur que vous écrivez ne doit pas interagir avec les entités de ces comptes de stockage. Les entités sont gérées directement par l’infrastructure des tâches durables en tant que détail d’implémentation.

Les fonctions d’orchestrateur planifient les fonctions d’activité et reçoivent leurs réponses via des messages internes en file d’attente. Lorsque l’application d’une fonction s’exécute dans le plan de consommation Azure Functions, ces files d’attente sont surveillées par le [contrôleur de mise à l’échelle Azure Functions](functions-scale.md#how-the-consumption-plan-works) et de nouvelles instances de calcul sont ajoutées si nécessaire. En cas de montée en charge sur plusieurs machines virtuelles, une fonction d’orchestrateur peut s’exécuter sur une machine virtuelle pendant que les fonctions d’activité qu’elle appelle s’exécutent sur plusieurs machines virtuelles différentes. Vous trouverez plus d’informations sur le comportement de mise à l’échelle de Fonctions durables dans l’article [Performances et mise à l’échelle](durable-functions-perf-and-scale.md).

Un stockage de table est utilisé pour stocker l’historique d’exécution pour les comptes d’orchestrateur. Chaque fois qu’une instance est réalimentée sur une machine virtuelle particulière, elle extrait son historique d’exécution du stockage de table pour reconstruire son état local. Disposer de l’historique dans le stockage de table vous permet de consulter l’historique de vos orchestrations à l’aide d’outils comme [Explorateur Stockage Azure Microsoft](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Capture d’écran Explorateur Stockage Azure](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Même s’il est facile et pratique d’afficher l’historique d’exécution dans le stockage de table, évitez de créer une dépendance sur cette table. Cette situation peut changer à mesure que l’extension Fonctions durables évolue.

## <a name="known-issues-and-faq"></a>Problèmes connus et FAQ

En général, tous les problèmes connus doivent être répertoriés dans la liste [Problèmes GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Si vous rencontrez un problème qui n’apparaît pas dans GitHub, soumettez un nouveau problème en incluant une description détaillée. Même si vous souhaitez simplement poser une question, n’hésitez pas à soumettre un problème GitHub et à le marquer comme une question.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Poursuivre la lecture de la documentation Fonctions durables](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Installer l’extension Fonctions durables et des exemples](durable-functions-install.md)

