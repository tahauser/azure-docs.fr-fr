---
title: "Gérer des instances dans Fonctions durables (Azure)"
description: "Découvrez comment gérer des instances dans l’extension Fonctions durables pour Azure Functions."
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
ms.openlocfilehash: a938e5949896ad3bfa91903106d56ccdf827c725
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Gérer des instances dans Fonctions durables (Azure Functions)

Les instances d’orchestration de [Fonctions durables](durable-functions-overview.md) peuvent être des événements de notification démarrés, arrêtés, interrogés et envoyés. La gestion de toutes les instances est effectuée à l’aide de la [liaison du client d’orchestration](durable-functions-bindings.md). Cet article explique en détail chaque opération de gestion d’instance.

## <a name="starting-instances"></a>Démarrage des instances

La méthode [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) du [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) démarre la nouvelle instance d’une fonction d’orchestrateur. Les instances de cette classe peuvent être obtenues à l’aide de la liaison `orchestrationClient`. En interne, cette méthode empile un message dans la file d’attente de contrôle, qui déclenche ensuite une fonction avec le nom spécifié utilisant la liaison de déclenchement `orchestrationTrigger`.

Les paramètres de [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) sont les suivants :

* **Nom** : nom de la fonction de l’orchestrateur à planifier.
* **Entrée** : toutes les données JSON sérialisables devant être transmises comme entrée à la fonction de l’orchestrateur.
* **InstanceId** : (facultatif) ID unique de l’instance. S’il n’est pas spécifié, un ID d’instance aléatoire est généré.

Voici un exemple simple C# :

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Pour les langages autres que .NET, la liaison de sortie de la fonction peut être utilisée pour démarrer également de nouvelles instances. Dans ce cas, tout objet sérialisable JSON ayant les trois paramètres ci-dessus comme champs peut être utilisé. Considérez par exemple la fonction Node.js suivante :

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Nous vous recommandons d’utiliser un identificateur aléatoire pour l’ID d’instance. Cela permet de garantir une distribution égale de la charge lors de la mise à l’échelle des fonctions de l’orchestrateur sur plusieurs machines virtuelles. Le moment qui convient pour l’utilisation des ID d’instance non aléatoires correspond au moment où l’ID provient d’une source externe ou lors de l’implémentation du modèle [orchestrateur singleton](durable-functions-singletons.md).

## <a name="querying-instances"></a>Interrogation des instances

La méthode [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) interroge l’état d’une instance d’orchestration. Il prend `instanceId` comme paramètre et renvoie un objet avec les propriétés suivantes :

* **Nom** : nom de la fonction de l’orchestrateur.
* **InstanceId** : ID d’instance de l’orchestration (doit être identique à l’entrée `instanceId`).
* **CreatedTime** : Heure à laquelle la fonction de l’orchestrateur a commencé à s’exécuter.
* **LastUpdatedTime** : Heure du dernier point de contrôle d’orchestration.
* **Input** : entrée de la fonction sous forme de valeur JSON.
* **Output** : sortie de la fonction sous forme de valeur JSON (si cette fonction est terminée). En cas d’échec de la fonction, cette propriété inclut les détails de l’échec. En cas d’interruption de la fonction de l’orchestrateur, cette propriété indique pour quel motif (le cas échéant).
* **RuntimeStatus** : une des valeurs suivantes :
    * **Running** : l’instance a commencé à s’exécuter.
    * **Completed** : l’instance s’est terminée normalement.
    * **ContinuedAsNew** : l’instance a redémarré automatiquement d’elle-même avec un nouvel historique. Il s’agit d’un état temporaire.
    * **Failed** : échec de l’instance avec une erreur.
    * **Terminated** : l’instance a été brusquement interrompue.
    
Cette méthode renvoie `null` si l’instance n’existe pas ou n’est pas encore exécutée.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Actuellement, les requêtes d’instances ne sont prises en charge que pour les fonctions d’orchestrateur C#.

## <a name="terminating-instances"></a>Arrêt des instances

Une instance en cours d’exécution peut être interrompue à l’aide de la méthode [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Les deux paramètres sont une chaîne `instanceId` et `reason`, qui est écrite dans les journaux et dans l’état de l’instance. Une instance interrompue cesse de s’exécuter dès qu’elle atteint le point `await` suivant, ou s’arrête immédiatement si elle se trouve déjà sur un `await`.

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Actuellement, l’arrêt d’instance n’est pris en charge que pour les fonctions d’orchestrateur C#.

## <a name="sending-events-to-instances"></a>Envoi d’événements à des instances

Des notifications d’événements peuvent être envoyées aux instances en cours d’exécution à l’aide de la méthode [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Les instances pouvant gérer ces événements sont celles en attente d’un appel à [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Les paramètres de [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) sont les suivants :

* **InstanceId** : ID unique de l’instance.
* **EventName** : nom de l’événement à envoyer.
* **EventData** : charge utile JSON sérialisable à envoyer à l’instance.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Actuellement, le déclenchement d’événements n’est pris en charge que pour les fonctions d’orchestrateur C#.

> [!WARNING]
> S’il n’existe aucune instance d’orchestration avec l’*ID d’instance* spécifié ou si l’instance n’attend pas le *nom d’événement* spécifié, le message d’événement est ignoré. Pour plus d’informations sur ce comportement, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment utiliser les API HTTP pour la gestion des instances](durable-functions-http-api.md)
