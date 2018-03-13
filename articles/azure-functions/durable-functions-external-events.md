---
title: "Gestion des événements externes dans Fonctions durables - Azure"
description: "Découvrez comment gérer des événements externes dans l’extension Fonctions durables pour Azure Functions."
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
ms.openlocfilehash: 1f581be0abaff542285abc0d4c2f4bffe7281d20
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Gestion des événements externes dans Fonctions durables (Azure Functions)

Les fonctions d’orchestrateur ont la capacité d’attendre et d’écouter des événements externes. Cette fonctionnalité de [Fonctions durables](durable-functions-overview.md) est souvent utile pour gérer l’interaction humaine ou d’autres déclencheurs externes.

## <a name="wait-for-events"></a>Attendre des événements

La méthode [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) permet à une fonction d’orchestrateur d’attendre de manière asynchrone et d’écouter un événement externe. La fonction d’orchestrateur qui écoute déclare le *nom* de l’événement et la *forme des données* qu’il s’attend à recevoir.

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

Dans l’exemple précédent, la fonction écoute un événement unique spécifique et le traite après sa réception.

Vous pouvez écouter plusieurs événements simultanément, comme dans l’exemple suivant, où la fonction attend l’une des trois notifications d’événement possibles.

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

Dans l’exemple précédent, la fonction écoute *tous* les événements. Elle peut également attendre *tous* les événements.

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) attend indéfiniment une entrée.  L’application de fonction peut être déchargée en toute sécurité durant l’attente. Si et quand un événement arrive pour cette instance d’orchestration, elle est automatiquement réveillée et traite immédiatement l’événement.

> [!NOTE]
> Si votre application de fonction utilise le plan de consommation, aucun frais de facturation n’est encouru quand une fonction d’orchestrateur attend une tâche de `WaitForExternalEvent`, quelle que soit la durée d’attente.

Si la charge utile de l’événement ne peut pas être convertie au type attendu `T`, une exception est levée.

## <a name="send-events"></a>Envoyer des événements

La méthode [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) de la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) envoie les événements attendus par `WaitForExternalEvent`.  La méthode `RaiseEventAsync` utilise *eventName* et *eventData* comme paramètres. Les données d’événement doivent être sérialisables au format JSON.

Voici un exemple de fonction déclenchée par une file d’attente qui envoie un événement « Approval » à une instance de la fonction d’orchestrateur. L’ID d’instance de l’orchestration provient du corps du message de file d’attente.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

En interne, `RaiseEventAsync` empile un message qui est récupéré par la fonction d’orchestrateur en attente.

> [!WARNING]
> S’il n’existe aucune instance d’orchestration avec l’*ID d’instance* spécifié ou si l’instance n’attend pas le *nom d’événement* spécifié, le message d’événement est ignoré. Pour plus d’informations sur ce comportement, consultez le [problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer des orchestrations externes](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Exécuter un exemple qui attend des événements externes](durable-functions-counter.md)

> [!div class="nextstepaction"]
> [Exécuter un exemple qui attend une interaction humaine](durable-functions-phone-verification.md)

