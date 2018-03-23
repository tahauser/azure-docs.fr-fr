---
title: Singletons pour l’extension Fonctions durables - Azure
description: Explique comment utiliser des singletons dans l’extension Fonctions durables pour Azure Functions.
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
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ea8b5db946d6b35ea4583d9170ec36e5f95e16cd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orchestrateurs de singleton dans l’extension Fonctions durables (Azure Functions)

Pour les orchestrations de style acteur ou les travaux en arrière-plan, vous avez souvent besoin de vous assurer qu’une seule instance d’un orchestrateur spécifique soit exécutée. Vous pouvez le vérifier grâce à l’extension [Fonctions durables](durable-functions-overview.md), en affectant un ID d’instance spécifique à un orchestrateur lors de sa création.

## <a name="singleton-example"></a>Exemple de singleton

L’exemple C# suivant illustre une fonction de déclencheur HTTP, qui crée une orchestration singleton de travail en arrière-plan. Le code garantit que seule une instance existe pour un ID d’instance spécifié.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

Par défaut, les ID d’instance sont des identificateurs globaux uniques générés de manière aléatoire. Dans ce cas, cependant, l’ID d’instance est passé dans les données d’itinéraire à partir de l’URL. Le code appelle [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) pour vérifier si une instance ayant l’ID spécifié est déjà en cours d’exécution. Si ce n’est pas le cas, une instance est créée avec cet ID.

Les détails liés à l’implémentation de la fonction d’orchestrateur ne sont pas importants. Il peut s’agir d’une fonction d’orchestrateur classique, qui démarre et se termine, ou d’une fonction qui s’exécute sans s’arrêter (une [orchestration externe](durable-functions-eternal-orchestrations.md)). Ce qui importe, c’est qu’une seule instance s’exécute à la fois, systématiquement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’appel d’orchestrations secondaires](durable-functions-sub-orchestrations.md)
