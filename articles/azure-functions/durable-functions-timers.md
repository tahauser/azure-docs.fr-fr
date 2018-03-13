---
title: Minuteurs dans Fonctions durables - Azure
description: "Découvrez comment implémenter des minuteurs durables dans l’extension Fonctions durables pour Azure Functions."
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
ms.openlocfilehash: e29e472860890e3f44af79c42c31ff524acb9276
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="timers-in-durable-functions-azure-functions"></a>Minuteurs dans Fonctions durables (Azure Functions)

[Fonctions durables](durable-functions-overview.md) fournit *des minuteurs durables* à utiliser dans les fonctions de l’orchestrateur pour implémenter des retards ou configurer des délais d’expiration sur des actions asynchrones. Les minuteurs durables doivent être utilisés dans les fonctions de l’orchestrateur à la place de `Thread.Sleep` ou `Task.Delay`.

Vous pouvez créer un minuteur durable en appelant la méthode [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) dans [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). La méthode retourne une tâche qui reprend à une date et une heure spécifiées.

## <a name="timer-limitations"></a>Limitations des minuteurs

Lorsque vous créez un minuteur qui expire à 16:30, l’infrastructure des tâches durables sous-jacent empile un message qui est uniquement visible à 16:30. Durant l’exécution dans le plan de consommation d’Azure Functions, le nouveau message visible du minuteur garantit que l’application de fonction est activée sur une machine virtuelle appropriée.

> [!WARNING]
> * En raison des limitations du stockage Azure, les minuteurs durables ne peuvent pas dépasser 7 jours. Nous travaillons actuellement sur une [demande de fonctionnalité pour étendre les minuteurs à plus de 7 jours](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Utilisez toujours [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) au lieu de `DateTime.UtcNow` comme indiqué dans les exemples ci-dessous pour le calcul d’une échéance relative d’un minuteur durable.

## <a name="usage-for-delay"></a>Utilisation des retards

L’exemple suivant montre comment utiliser des minuteurs durables pour retarder une exécution. Dans cet exemple, une notification de facturation est émise tous les jours pendant dix jours.

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallFunctionAsync("SendBillingEvent");
    }
}
```

> [!WARNING]
> Evitez les boucles infinies dans les fonctions de l’orchestrateur. Pour plus d’informations sur la façon d’implémenter efficacement et en toute sécurité des scénarios de boucles infinies, consultez [Orchestrations externes](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Utilisation des délais d’expiration

Cet exemple montre comment utiliser des minuteurs durables pour implémenter des délais d’expiration.

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallFunctionAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!WARNING]
> Utilisez `CancellationTokenSource` pour annuler un minuteur durable si votre code n’attendra pas qu’il se termine. Dans l’infrastructure des tâches durables, une orchestration ne passe à l’état « terminé » que quand toutes les tâches en attente sont terminées ou annulées.

Ce mécanisme ne termine pas réellement l’exécution des fonctions d’activité en cours. Il permet simplement à la fonction de l’orchestrateur d’ignorer le résultat et de continuer. Si votre application de fonction utilise le plan de consommation, vous serez toujours facturé pour le temps consacré et la mémoire consommée par la fonction d’activité abandonnée. Par défaut, les fonctions exécutées dans le plan de consommation ont un délai d’expiration de cinq minutes. Si cette limite est dépassée, l’hôte d’Azure Functions est recyclé pour arrêter toute exécution en cours et éviter toute facturation supplémentaire. Le [délai d’expiration des fonctions est configurable](functions-host-json.md#functiontimeout).

Pour obtenir un exemple plus détaillé d’implémentation de délais d’expiration dans les fonctions de l’orchestrateur, consultez la procédure pas à pas [Interaction humaine et délais d’expiration - Vérification par téléphone](durable-functions-phone-verification.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déclenchement et gestion d’événements externes](durable-functions-external-events.md)

