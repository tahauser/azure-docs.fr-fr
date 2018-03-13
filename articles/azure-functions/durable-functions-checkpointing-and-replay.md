---
title: "Points de contrôle et réexécution dans Fonctions durables - Azure"
description: "Découvrez comment fonctionnent la création de points de contrôle et la réexécution dans l’extension Fonctions durables pour Azure Functions."
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
ms.openlocfilehash: b1bca62e256c1ede5df6888dd7c47ce2aa816bb9
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Points de contrôle et réexécution dans Fonctions durables (Azure Functions)

L’un des attributs clés de Fonctions durables est une **exécution fiable**. Les fonctions d’orchestrateur et les fonctions d’activité peuvent être exécutées sur différentes machines virtuelles d’un centre de données, et ces machines virtuelles ou l’infrastructure réseau sous-jacente ne sont pas fiables à 100 %.

Malgré cela, Fonctions durables garantit une exécution fiable des orchestrations. Pour ce faire, cette fonctionnalité utilise des files d’attente de stockage pour l’appel de fonction et crée régulièrement des points de contrôle de l’historique d’exécution dans des tables de stockage (à l’aide d’un modèle de conception cloud appelé [approvisionnement en événements](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Cet historique peut ensuite être réexécuté pour régénérer automatiquement l’état en mémoire d’une fonction d’orchestrateur.

## <a name="orchestration-history"></a>Historique d’orchestration

Supposons que vous avez la fonction d’orchestrateur suivante.

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

À chaque instruction `await`, l’infrastructure Durable Task Framework crée un point de contrôle de l’état d’exécution de la fonction dans le stockage de tables. Cet état est appelé *historique d’orchestration*.

## <a name="history-table"></a>Table d’historique

En règle générale, Durable Task Framework réalise les tâches suivantes à chaque point de contrôle :

1. Il enregistre l’historique d’exécution dans des tables de stockage Azure.
2. Il empile les messages pour les fonctions que l’orchestrateur souhaite appeler.
3. Il empile les messages pour l’orchestrateur lui-même &mdash; par exemple, les messages d’un minuteur durable.

Une fois le point de contrôle terminé, la fonction d’orchestrateur peut être supprimée de la mémoire jusqu'à ce qu’elle ait de nouvelles tâches à réaliser.

> [!NOTE]
> Le stockage Azure ne fournit aucune garantie transactionnelle entre l’enregistrement des données dans le stockage de tables et les files d’attente. Pour gérer les erreurs, le fournisseur de stockage Fonctions durables utilise les modèles de *cohérence éventuelle*. Ces modèles évitent la perte de données en cas d’incident ou de perte de connectivité au cours d’un point de contrôle.

À la fin, l’historique de la fonction mentionnée précédemment ressemble à ce qui suit dans Stockage Table Azure (présentation abrégée à des fins d’illustration) :

| PartitionKey (InstanceId)                     | Type d’événement             | Timestamp               | Entrée | NOM             | Résultat                                                    | Statut | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Completed           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Quelques remarques sur les valeurs de colonne :
* **PartitionKey** : contient l’ID d’instance de l’orchestration.
* **EventType** : représente le type de l’événement, qui peut être l’un des suivants :
    * **OrchestrationStarted** : la fonction d’orchestrateur a repris après une expression await ou s’exécute pour la première fois. La colonne `Timestamp` sert à indiquer la valeur déterministe pour l’API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime).
    * **ExecutionStarted** : la fonction d’orchestrateur a commencé à s’exécuter pour la première fois. Cet événement contient également l’entrée de la fonction dans la colonne `Input`.
    * **TaskScheduled** : une fonction d’activité a été planifiée. Le nom de la fonction d’activité est indiqué dans la colonne `Name`.
    * **TaskCompleted** : une fonction d’activité s’est terminée. Le résultat de la fonction se trouve dans la colonne `Result`.
    * **TimerCreated** : un minuteur durable a été créé. La colonne `FireAt` contient l’heure UTC planifiée à laquelle le minuteur expire.
    * **TimerFired** : un minuteur durable s’est déclenché.
    * **EventRaised** : un événement externe a été envoyé à l’instance d’orchestration. La colonne `Name` indique le nom de l’événement et la colonne `Input` indique la charge utile de l’événement.
    * **OrchestratorCompleted** : la fonction d’orchestrateur a attendu.
    * **ContinueAsNew** : la fonction d’orchestrateur s’est terminée et a redémarré avec un nouvel état. La colonne `Result` contient la valeur, qui est utilisée comme entrée dans l’instance redémarrée.
    * **ExecutionCompleted** : la fonction d’orchestrateur s’est exécutée entièrement (ou a échoué). Les sorties de la fonction ou les détails de l’erreur sont stockés dans la colonne `Result`.
* **Timestamp** : horodatage UTC de l’événement d’historique.
* **Name** : nom de la fonction qui a été appelée.
* **Input** : entrée au format JSON de la fonction.
* **Result** : sortie de la fonction ; autrement dit, sa valeur renvoyée.

> [!WARNING]
> Même si cette table est utile en tant qu’outil de débogage, vous ne devez pas en dépendre. Elle peut changer à mesure que l’extension Fonctions durables évolue.

Chaque fois que la fonction reprend après une expression `await`, Durable Task Framework réexécute la fonction d’orchestrateur depuis le début. À chaque réexécution, il consulte l’historique d’exécution pour déterminer si l’opération asynchrone en cours a eu lieu.  Si l’opération a eu lieu, l’infrastructure réexécute immédiatement la sortie de cette opération et passe à l’expression `await` suivante. Ce processus se poursuit jusqu'à ce que tout l’historique ait été réexécuté, après quoi toutes les valeurs précédentes des variables locales dans la fonction d’orchestrateur sont restaurées.

## <a name="orchestrator-code-constraints"></a>Contraintes du code d’orchestrateur

Le comportement de réexécution crée des contraintes concernant le type de code qui peut être écrit dans une fonction d’orchestrateur :

* Le code d’orchestrateur doit être **déterministe**. Il sera réexécuté plusieurs fois et doit générer le même résultat à chaque fois. Par exemple, pas d’appels directs pour obtenir la date/heure actuelle, pour obtenir des nombres aléatoires, pour générer des GUID aléatoires ou pour appeler des points de terminaison distants.

  Si le code d’orchestrateur doit obtenir la date/heure actuelle, il doit utiliser l’API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime), qui est sûre pour la réexécution.

  Les opérations non déterministes doivent être effectuées dans les fonctions d’activité. Cela inclut toutes les interactions avec d’autres liaisons d’entrée ou de sortie. Cela permet de s’assurer que les valeurs non déterministes ne seront générées qu’une seule fois durant la première exécution, puis enregistrées dans l’historique d’exécution. Les exécutions suivantes utiliseront automatiquement la valeur enregistrée.

* Le code d’orchestrateur doit être **non bloquant**. Cela signifie, par exemple, aucune e/s et aucun appel à `Thread.Sleep` ou à une API équivalente.

  Si un orchestrateur doit être différé, il peut utiliser l’API [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_).

* Le code d’orchestrateur doit **uniquement lancer une opération asynchrone** à l’aide de l’API [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). Par exemple, pas de `Task.Run`, `Task.Delay` ni `HttpClient.SendAsync`. Durable Task Framework exécute le code d’orchestrateur sur un thread unique et ne peut pas interagir avec d’autres threads planifiés par d’autres API asynchrones.

* **Évitez les boucles infinies** dans le code d’orchestrateur. Étant donné que Durable Task Framework enregistre l’historique d’exécution à mesure que la fonction d’orchestration s’exécute, une boucle infinie risquerait de laisser une instance d’orchestrateur sans mémoire suffisante. Pour les scénarios de boucle infinie, utilisez des API comme [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) pour redémarrer l’exécution de la fonction et ignorer l’historique d’exécution précédent.

Bien que ces contraintes puissent sembler décourageantes au départ, dans la pratique, elles ne sont pas difficiles à suivre. Durable Task Framework tente de détecter des violations des règles mentionnées ci-dessus et lève une expression `NonDeterministicOrchestrationException`. Notez toutefois que même si ce comportement de détection est utile, vous ne devez pas en dépendre.

> [!NOTE]
> Toutes ces règles s’appliquent uniquement aux fonctions déclenchées par la liaison `orchestrationTrigger`. Les fonctions d’activité déclenchées par la liaison `activityTrigger` et les fonctions qui utilisent la liaison `orchestrationClient` n’ont pas ces limitations.

## <a name="durable-tasks"></a>Tâches durables

> [!NOTE]
> Cette section décrit en détail l’implémentation interne de Durable Task Framework. Vous pouvez utiliser Fonctions durables sans disposer de ces informations. Elles sont uniquement destinées à vous aider à mieux comprendre le comportement de réexécution.

Les tâches qui peuvent être attendues en toute sécurité dans les fonctions d’orchestrateur sont parfois appelées des *tâches durables*. Ces tâches sont créées et gérées par Durable Task Framework. C’est le cas, par exemple, des tâches retournées par `CallActivityAsync`, `WaitForExternalEvent` et `CreateTimer`.

Ces *tâches durables* sont gérées en interne à l’aide d’une liste d’objets `TaskCompletionSource`. Durant la réexécution, ces tâches sont créées dans le cadre de l’exécution du code d’orchestrateur et exécutées à mesure que le répartiteur énumère les événements d’historique correspondants. L’ensemble est effectué de manière synchrone à l’aide un seul thread jusqu'à ce que tout l’historique ait été réexécuté. Toutes les tâches durables non terminées à la fin de la réexécution de l’historique feront l’objet d’actions appropriées. Par exemple, un message peut être empilé pour appeler une fonction d’activité.

Le comportement d’exécution décrit ici devrait vous aider à mieux comprendre pourquoi le code de fonction d’orchestrateur ne doit jamais attendre (`await`) une tâche non durable : le thread du répartiteur ne peut pas attendre qu’elle se termine et tout rappel par cette tâche pourrait corrompre l’état de suivi de la fonction d’orchestrateur. Certaines vérifications de l’exécution sont configurées pour tenter d’éviter ce problème.

Pour plus d’informations sur la façon dont Durable Task Framework exécute les fonctions d’orchestrateur, consultez le [code source des tâches durables sur GitHub](https://github.com/Azure/durabletask). Consultez en particulier [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) et [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la gestion des instances](durable-functions-instance-management.md)
