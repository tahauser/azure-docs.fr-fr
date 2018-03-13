---
title: "Vue d’ensemble des API Azure Event Hubs .NET Standard | Microsoft Docs"
description: "Vue d’ensemble de l’API .NET Standard"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a173f8e4-556c-42b8-b856-838189f7e636
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 855f6e7f401621d7f923d68215ca880c05d38629
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="event-hubs-net-standard-api-overview"></a>Vue d’ensemble de l’API .NET Standard des hubs d’événements

Cet article passe en revue certaines des principales API clientes .NET Standard des hubs d’événements. Il existe actuellement deux bibliothèques clientes .NET Standard :

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs) : fournit toutes les opérations runtime de base.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor) : ajoute des fonctionnalités supplémentaires pour suivre les événements traités et offre le moyen le plus simple pour lire à partir d’un concentrateur d’événements.

## <a name="event-hubs-client"></a>Client de concentrateur d’événements

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) est l’objet principal utilisé pour envoyer des événements, créer des destinataires et obtenir des informations de runtime. Ce client est lié à un hub d’événements particulier et crée une connexion au point de terminaison des concentrateurs d’événements.

### <a name="create-an-event-hubs-client"></a>Création d’un client de concentrateurs d’événements

Un objet [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) est créé à partir d’une chaîne de connexion. L’exemple suivant présente la méthode la plus simple pour instancier un nouveau client :

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("{Event Hubs connection string}");
```

Pour modifier la chaîne de connexion par programmation, vous pouvez utiliser la classe [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) et transmettre la chaîne de connexion en tant que paramètre à [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("{Event Hubs connection string}")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Envoyer des événements

Pour envoyer des événements vers un concentrateur d’événements, utilisez la classe [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata). Le corps doit être un tableau `byte` ou un segment de tableau `byte`.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Recevoir des événements

Pour recevoir des événements des Event Hubs, nous vous recommandons d’utiliser [l’hôte du processeur d’événements](#event-processor-host-apis), qui fournit une fonctionnalité de suivi automatique du décalage du concentrateur d’événements et des informations de partition. Toutefois, il peut arriver que vous souhaitiez vous reposer sur la flexibilité de la bibliothèque principale des hubs d’événements pour recevoir des événements.

#### <a name="create-a-receiver"></a>Créer un destinataire

Les destinataires sont liés à des partitions spécifiques. Par conséquent, pour recevoir tous les événements dans un concentrateur d’événements, vous devez créer plusieurs instances. Il est recommandé d’obtenir les informations de partition par programmation, au lieu de coder en dur les ID de partition. Pour ce faire, vous pouvez utiliser la méthode [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync).

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Dans la mesure où les événements ne sont jamais supprimés d’un concentrateur d’événements (ils arrivent seulement à expiration), vous devez spécifier le point de départ approprié. L’exemple suivant montre les combinaisons possibles :

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Consommer un événement

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>API de l’hôte du processeur d’événements

Ces API offrent une résilience aux processus de travail qui peuvent devenir indisponibles, en distribuant les partitions sur les workers disponibles.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Voici un exemple d’implémentation de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les scénarios des concentrateurs d’événements, consultez ces liens :

* [Nouveautés des concentrateurs d'événements Azure ?](event-hubs-what-is-event-hubs.md)
* [API des hubs d’événements disponibles](event-hubs-api-overview.md)

Les informations de référence de l'API .NET se trouvent ici :

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)