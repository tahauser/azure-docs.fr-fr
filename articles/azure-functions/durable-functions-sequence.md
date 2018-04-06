---
title: Chaînage de fonctions dans Fonctions durables - Azure
description: Découvrez comment exécuter un exemple de fonctions durables qui exécute une séquence de fonctions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 0020f19e00f3365c4a0d80ebb67aeeedd7fe76df
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Chaînage de fonctions dans Fonctions durables - Exemple de séquence Hello

Un chaînage de fonctions fait référence au modèle d’exécution d’une séquence de fonctions dans un ordre particulier. La sortie d’une fonction doit souvent être appliquée à l’entrée d’une autre fonction. Cet article décrit un exemple utilisant des [Fonctions durables](durable-functions-overview.md) pour implémenter un chaînage de fonctions.

## <a name="prerequisites"></a>Prérequis


* [Installer Fonctions durables](durable-functions-install.md).

## <a name="the-functions"></a>Les fonctions

Cet article explique les fonctions suivantes dans l’exemple d’application :

* `E1_HelloSequence` : Une fonction d’orchestrateur qui appelle `E1_SayHello` plusieurs fois dans une séquence. Il stocke les sorties à partir des appels de `E1_SayHello` et enregistre les résultats.
* `E1_SayHello` : Une fonction d’activité qui ajoute une chaîne avec « Hello ».

Les sections suivantes décrivent la configuration et le code utilisés pour les scripts C#. Le code de développement de Visual Studio est affiché à la fin de l’article.
 
## <a name="functionjson-file"></a>Fichier function.json

Si vous utilisez Visual Studio Code ou le portail Azure pour le développement, voici le contenu du fichier *function.json* pour la fonction d’orchestrateur. La plupart des fichiers *function.json* d’orchestrateur ressemblent presque exactement à cela.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Le point essentiel est le type de liaison `orchestrationTrigger`. Toutes les fonctions d’orchestrateur doivent utiliser ce type de déclencheur.

> [!WARNING]
> Pour respecter la règle « Aucune E/S » des fonctions d’orchestrateur, n’utilisez aucune liaison d’entrée ou de sortie lors de l’utilisation de la liaison de déclenchement `orchestrationTrigger`.  Si d’autres liaisons d’entrée ou de sortie sont nécessaires, elles doivent plutôt être utilisées dans le contexte des fonctions `activityTrigger`, qui sont appelées par l’orchestrateur.

## <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Script C# (exemple de code Visual Studio Code et portail Azure) 

Voici le code source :

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Toutes les fonctions d’orchestration C# doivent avoir un paramètre de type `DurableOrchestrationContext`, qui existe dans l’assembly `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Si vous utilisez un script C#, l’assembly peut être référencée à l’aide de la notation `#r`. Cet objet de contexte vous permet d’appeler d’autres fonctions *d’activité* et de passer les paramètres d’entrée à l’aide de sa méthode [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

Le code appelle trois fois `E1_SayHello` en séquence avec des valeurs de paramètre différentes. La valeur renvoyée de chaque appel est ajoutée à la liste `outputs`, qui est retournée à la fin de la fonction.

Le fichier *function.json* pour la fonction de l’activité `E1_SayHello` est similaire à celle de `E1_HelloSequence` sauf qu’elle utilise un type de liaison `activityTrigger` à la place d’un type de liaison `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> N’importe quelle fonction appelée par une fonction d’orchestration doit utiliser la liaison `activityTrigger`.

L’implémentation de `E1_SayHello` est une opération de mise en forme de chaîne relativement simple.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Cette fonction a un paramètre de type [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), qu’elle utilise pour obtenir l’entrée qui lui a été passée par l’appel de la fonction d’orchestrateur à [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

## <a name="run-the-sample"></a>Exécution de l'exemple

Pour exécuter l’orchestration `E1_HelloSequence`, envoyez la requête HTTP POST suivante.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

Par exemple, si vous exécutez l’exemple dans une application de fonction nommée « myfunctionapp », remplacez « {hôte} » par « myfunctionapp.azurewebsites.net ».

Le résultat est une réponse HTTP 202, comme celle-ci (ajustée par souci de concision) :

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

À ce stade, l’orchestration est mise en file d’attente et commence à s’exécuter immédiatement. L’URL dans l’en-tête `Location` peut être utilisée pour vérifier l’état de l’exécution.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Le résultat est l'état de l'orchestration. Elle s’exécute et se termine rapidement, vous voyez donc le résultat dans l’état *Terminé* avec une réponse qui ressemble à ceci (ajustée par souci de concision) :

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Comme vous pouvez le voir, le `runtimeStatus` de l’instance est *Terminé* et le `output` contient le résultat sérialisé en JSON de l’exécution de la fonction d’orchestration.

> [!NOTE]
> Le point de terminaison HTTP POST qui a démarré la fonction d’orchestrateur est implémenté dans l’application exemple en tant que fonction de déclenchement HTTP nommée « HttpStart ». Vous pouvez implémenter une logique de démarrage similaire pour d’autres types de déclenchement, comme `queueTrigger`, `eventHubTrigger`, ou `timerTrigger`.

Examinez les journaux d’exécution de fonction. La fonction `E1_HelloSequence` démarrée et terminée plusieurs fois en raison du comportement de relecture décrit dans le [Vue d’ensemble](durable-functions-overview.md). En revanche, il n’y a eu que trois exécutions de `E1_SayHello` étant donné que les exécutions de la fonction ne sont pas relues.

## <a name="visual-studio-sample-code"></a>Exemple de code Visual Studio

Voici l’orchestration, présentée sous la forme d’un seul fichier C# dans un projet Visual Studio :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Étapes suivantes

Cet exemple a illustré une orchestration simple de chaînage de fonction. L’exemple suivant montre comment implémenter le modèle fan-out/fan-in. 

> [!div class="nextstepaction"]
> [Exécuter l’exemple Fan-out/fan-in](durable-functions-cloud-backup.md)
