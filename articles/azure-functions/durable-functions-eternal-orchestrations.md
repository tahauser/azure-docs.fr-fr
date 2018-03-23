---
title: Orchestrations externes dans Fonctions durables - Azure
description: Découvrez comment implémenter des orchestrations externes à l’aide de l’extension Fonctions durables pour Azure Functions.
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
ms.openlocfilehash: cb4115b98091f55a0324ea795ffcc83cb29223a4
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orchestrations externes dans Fonctions durables (Azure Functions)

Les *orchestrations externes* sont des fonctions d’orchestrateur qui ne se terminent jamais. Elles sont utiles si vous souhaitez utiliser [Fonctions Durable](durable-functions-overview.md) pour des agrégateurs et tout scénario qui nécessite une boucle infinie.

## <a name="orchestration-history"></a>Historique de l’orchestration

Comme expliqué dans la section [Création de points de contrôle et réexécution](durable-functions-checkpointing-and-replay.md), l’infrastructure des tâches durables effectue le suivi de l’historique de chaque orchestration de fonction. Cet historique augmente sans cesse tant que la fonction d’orchestrateur continue de planifier une nouvelle tâche. Si la fonction d’orchestrateur entre dans une boucle infinie et planifie sans cesse des tâches, cet historique risque de devenir très volumineux et de provoquer d’importants problèmes de performances. Le concept *d’orchestration externe* a été conçu pour limiter ces types de problèmes dans les applications nécessitant des boucles infinies.

## <a name="resetting-and-restarting"></a>Réinitialisation et redémarrage

Au lieu d’utiliser des boucles infinies, les fonctions d’orchestrateur réinitialisent leur état en appelant la méthode [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_). Cette méthode n’utilise qu’un seul paramètre JSON sérialisable, qui devient la nouvelle entrée de la prochaine génération de la fonction d’orchestrateur.

Lorsque la méthode `ContinueAsNew` est appelée, l’instance garde un message dans sa propre file d’attente avant de se fermer. Le message redémarre l’instance avec la nouvelle valeur d’entrée. Le même ID d’instance est conservé, mais l’historique de la fonction d’orchestrateur est tronqué.

> [!NOTE]
> L’infrastructure des tâches durables conserve le même ID d’instance, mais crée en interne un nouvel *ID d’exécution* pour la fonction d’orchestrateur réinitialisée par `ContinueAsNew`. Cet ID d’exécution n’est généralement pas exposé en externe, mais il peut être utile d’en tenir compte lors du débogage de l’exécution d’orchestration.

## <a name="periodic-work-example"></a>Exemple de travail périodique

Voici un cas d’utilisation des orchestrations externes : un code a besoin d’effectuer un travail périodique indéfiniment.

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

La différence entre cet exemple et une fonction déclenchée par un minuteur est que les heures de déclenchement du nettoyage ne sont pas ici basées sur une planification. Par exemple, une planification CRON qui exécute une fonction toutes les heures se produira à 1 h 00, 2 h 00, 3 h 00 etc., et risque d’entraîner des problèmes de chevauchement. Mais dans cet exemple, si le nettoyage prend 30 minutes, il sera alors planifié à 1 h 00, 2 h 30, 4 h 00, etc., et il n’existe aucun risque de chevauchement.

## <a name="counter-example"></a>Exemple de compteur

Voici un exemple simplifié de fonction *compteur* qui écoute en externe des événements d*’incrémentation* et de *décrémentation*.

```csharp
[FunctionName("SimpleCounter")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int counterState = context.GetInput<int>();

    string operation = await context.WaitForExternalEvent<string>("operation");

    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }
    
    context.ContinueAsNew(counterState);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Fermeture d’une orchestration externe

Si une fonction d’orchestrateur doit se terminer, il vous suffit de ne *pas* appeler `ContinueAsNew` et de laisser la fonction se terminer.

Si une fonction d’orchestrateur entre dans une boucle infinie et doit être arrêtée, utilisez dans ce cas la méthode [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_). Pour plus d’informations, consultez [Gestion d’instance](durable-functions-instance-management.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Apprendre à implémenter des orchestrations de singleton](durable-functions-singletons.md)
