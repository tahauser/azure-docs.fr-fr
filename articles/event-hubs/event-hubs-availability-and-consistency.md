---
title: "Disponibilité et cohérence dans Azure Event Hubs | Microsoft Docs"
description: "Découvrez comment obtenir la quantité maximale de disponibilité et de cohérence avec Azure Event Hubs à l’aide de partitions."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: be1398e9b0a10efcd694e46d6322d5d7b9e7a843
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilité et cohérence dans Event Hubs

## <a name="overview"></a>Vue d'ensemble
Azure Event Hubs utilise un [modèle de partitionnement](event-hubs-features.md#partitions) pour améliorer la disponibilité et la parallélisation dans un hub d’événements unique. Par exemple, si un hub d’événements a quatre partitions et que l’une de ces partitions est déplacée d’un serveur à l’autre dans une opération d’équilibrage de charge, vous pouvez quand même envoyer et recevoir à partir des trois autres partitions. En outre, avoir davantage de partitions vous permet d’avoir plus lecteurs pour traiter vos données simultanément, ce qui améliore le débit global. Comprendre les implications en matière de partitionnement et de classement dans un système distribué est un aspect essentiel de la conception de la solution.

Pour expliquer le compromis entre classement et disponibilité, reportez-vous au [théorème CAP](https://en.wikipedia.org/wiki/CAP_theorem), également connu sous le nom de théorème de Brewer. Ce théorème discute le choix entre la cohérence, la disponibilité et la tolérance de la partition.

Le théorème de Brewer définit la cohérence et la disponibilité de la façon suivante :
* Tolérance de la partition : la capacité d’un système de traitement de données à continuer le traitement des données même en cas de défaillance d’une partition.
* Disponibilité : un nœud sans échec renvoie une réponse raisonnable dans un délai raisonnable (sans erreurs ou expirations de délai).
* Cohérence : une lecture renvoie toujours la dernière écriture pour un client donné.

## <a name="partition-tolerance"></a>Tolérance de la partition
Event Hubs repose sur un modèle de données partitionné. Vous pouvez configurer le nombre de partitions dans votre hub d’événements pendant l’installation, mais vous ne pouvez pas modifier cette valeur par la suite. Étant donné que vous devez utiliser des partitions avec Event Hubs, vous devez prendre une décision concernant la disponibilité et la cohérence de votre application.

## <a name="availability"></a>Availability
La méthode la plus simple pour se familiariser avec Event Hubs est d’utiliser le comportement par défaut. Si vous créez un objet **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** et que vous utilisez la méthode **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)**, les événements sont automatiquement distribués entre les partitions de votre Event Hub. Ce comportement offre le meilleur temps d’activité.

Pour les cas d’utilisation qui exigent un temps d’activité maximum, ce modèle est conseillé.

## <a name="consistency"></a>Cohérence
Dans certains scénarios, l’ordre des événements peut être important. Par exemple, vous souhaiterez peut-être que votre système principal traite une commande de mise à jour avant une commande de suppression. Dans ce cas, vous pouvez définir la clé de partition sur un événement, ou utiliser un objet `PartitionSender` pour envoyer uniquement les événements à une certaine partition. Cela garantit que ces événements sont lus dans l’ordre lorsqu’ils sont lus à partir de la partition.

Avec cette configuration, gardez à l’esprit que si la partition particulière à laquelle vous envoyez les événements n’est pas disponible, vous recevrez une réponse d’erreur. À titre de comparaison, si vous n’avez pas d’affinité avec une partition unique, le service Event Hubs envoie votre événement à la prochaine partition disponible.

Pour garantir le classement tout en optimisant aussi le temps d’activité, vous pouvez regrouper les événements dans le cadre de votre application de traitement des événements. Pour cela, le moyen le plus simple consiste à marquer votre événement avec une propriété de numéro de séquence personnalisée. Le code suivant montre un exemple :

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

L’exemple envoie votre événement à l’une des partitions disponibles dans votre hub d’événements et définit le numéro de séquence correspondant à partir de votre application. Cette solution nécessite que l’état soit conservé par votre application de traitement, mais offre à vos expéditeurs un point de terminaison plus susceptible d’être disponible.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Présentation du service Event Hubs](event-hubs-what-is-event-hubs.md)
* [Créer un hub d’événements](event-hubs-create.md)
