---
title: "Conseils de gestion des erreurs d’Azure Functions | Microsoft Docs"
description: "Fournit des conseils généraux pour la gestion des erreurs qui se produisent dans lors de l’exécution de vos fonctions, ainsi que des liens vers des rubriques d’erreurs spécifiques d’une liaison."
services: functions
cloud: 
documentationcenter: 
author: ggailey777
manager: cfowler
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 82cdc62b3070811186583fdf1ce5e6ce421ebc34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-functions-error-handling"></a>Gestion d’erreurs d’Azure Functions

Cette rubrique fournit des conseils généraux pour la gestion des erreurs qui se produisent dans lors de l’exécution de vos fonctions. Elle fournit également des liens vers des rubriques décrivant des erreurs spécifiques d’une liaison qui peuvent se produire. 

## <a name="handing-errors-in-functions"></a>Gestion des erreurs dans Azure Functions
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Codes d’erreur de liaison

Lors de l’intégration avec les services Azure, vous pouvez rencontrer des erreurs provenant des API des services sous-jacents. Vous pouvez trouver des liens vers de la documentation relative aux codes d’erreur pour ces services dans la section **Exceptions et codes de retour** du déclencheur et des rubriques de référence de liaison suivants :

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Stockage Blob](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Concentrateurs d'événements](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Stockage de files d’attente](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Stockage Table](functions-bindings-storage-table.md#exceptions-and-return-codes)
