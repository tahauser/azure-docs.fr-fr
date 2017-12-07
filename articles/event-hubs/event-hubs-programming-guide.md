---
title: Guide de programmation pour Azure Event Hubs | Microsoft Docs
description: "Écrivez du code pour les hubs d’événements Azure à l'aide du kit .NET SDK d'Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/16/2017
ms.author: sethm
ms.openlocfilehash: 7d5f14d5a65253cf0aad1811ace419bf2f39f7db
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="event-hubs-programming-guide"></a>Guide de programmation Event Hubs

Cet article décrit quelques scénarios courants de l’écriture de code à l’aide d’Azure Event Hubs et du kit .NET SDK Azure. Il suppose une connaissance préalable des hubs d’événements. Pour une vue d’ensemble conceptuelle des hubs d’événements, consultez [Vue d'ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Éditeurs d'événements

Vous envoyez des événements vers un hub d’événements soit en utilisant HTTP POST, soit via une connexion AMQP 1.0. Le choix entre les deux méthodes à utiliser et à quel moment dépend du scénario spécifique qui est adressé. Les connexions AMQP 1.0 sont limitées en tant que connexions réparties dans Service Bus et sont plus appropriées dans les scénarios avec des volumes de messages plus importants fréquents et des conditions de latence plus faible, car elles fournissent un canal de messagerie permanent.

Vous pouvez créer et gérer des hubs d’événements à l’aide de la classe [NamespaceManager][]. L’utilisation des API gérées avec .NET, les constructions principales pour publier des données sur les hubs d’événements sont les classes [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) et [EventData][]. [EventHubClient][] fournit le canal de communication AMQP par le biais duquel les événements sont envoyés au hub d’événements. La classe [EventData][] représente un événement et sert à publier des messages sur un hub d’événements. Cette classe inclut le corps, certaines métadonnées et les informations d'en-tête sur l'événement. D’autres propriétés sont ajoutées à l’objet [EventData][] lorsqu’il traverse un hub d’événements.

## <a name="get-started"></a>Prise en main

Les classes .NET qui prennent en charge les hubs d'événements font partie de l'assembly Microsoft.ServiceBus.dll. Le moyen le plus simple de référencer l'API Service Bus et de configurer votre application avec toutes les dépendances Service Bus est de télécharger le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Vous pouvez également utiliser la [Console du gestionnaire de package](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) dans Visual Studio. Pour cela, entrez la commande suivante dans la fenêtre de la [console du gestionnaire du package](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) :

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Création d'un hub d'événements
Vous pouvez utiliser la classe [NamespaceManager][] pour créer des hubs d'événements. Par exemple :

```csharp
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

Dans la plupart des cas, il est recommandé d'utiliser les méthodes [CreateEventHubIfNotExists][] pour éviter de générer des exceptions si le service redémarre. Par exemple :

```csharp
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Toutes les opérations de création de hub d'événements, y compris [CreateEventHubIfNotExists][], nécessitent des autorisations de **gestion** sur l'espace de noms en question. Si vous souhaitez limiter les autorisations de votre éditeur ou de vos applications clientes, vous pouvez éviter ces appels d'opération de création dans le code de production lorsque vous utilisez des informations d'identification avec des autorisations limitées.

La classe [EventHubDescription](/dotnet/api/microsoft.servicebus.messaging.eventhubdescription) contient des détails sur un hub d'événements, notamment les règles d'autorisation, l'intervalle de rétention de message, les ID de partition, l’état et le chemin. Vous pouvez utiliser cette classe pour mettre à jour les métadonnées sur un hub d'événements.

## <a name="create-an-event-hubs-client"></a>Création d’un client Event Hubs
La classe principale d’interaction avec Event Hubs est [Microsoft.ServiceBus.Messaging.EventHubClient][EventHubClient]. Cette classe fournit les fonctionnalités de l'expéditeur et du récepteur. Vous pouvez instancier cette classe à l’aide de la méthode [Create](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.create), comme indiqué dans l’exemple suivant :

```csharp
var client = EventHubClient.Create(description.Path);
```

Cette méthode utilise les informations de connexion de Service Bus dans le fichier App.config, dans la section `appSettings` . Pour obtenir un exemple du XML `appSettings` utilisé pour stocker les informations de connexion Service Bus, consultez la documentation pour la méthode [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) .

Vous pouvez également créer le client à partir d'une chaîne de connexion. Cette option fonctionne bien lorsque vous utilisez des rôles de travail Azure, car vous pouvez stocker la chaîne dans les propriétés de configuration du travail. Par exemple :

```csharp
EventHubClient.CreateFromConnectionString("your_connection_string");
```

La chaîne de connexion sera au même format que celui dans lequel elle apparaît dans le fichier App.config pour les méthodes précédentes :

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[key]
```

Enfin, il est également possible de créer un objet [EventHubClient][] à partir d’une instance [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory), comme illustré dans l’exemple suivant :

```csharp
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Il est important de noter que des objets [EventHubClient][] supplémentaires créés à partir d’une instance de fabrique de messagerie réutilisent la même connexion TCP sous-jacente. Par conséquent, ces objets ont une limite de débit côté client. La méthode [Create](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) réutilise une fabrique de messagerie unique. Si vous avez besoin du débit très élevé d'un expéditeur unique, vous pouvez créer plusieurs fabriques de messages et un objet [EventHubClient][] à partir de chaque fabrique de messagerie.

## <a name="send-events-to-an-event-hub"></a>Envoyer des événements à un hub d’événements
Vous pouvez envoyer des événements à un hub d’événements en créant une instance [EventData][] et en l’envoyant via la méthode [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_). Cette méthode prend un seul paramètre d’instance [EventData][] et l’envoie de façon synchrone à un hub d'événements.

## <a name="event-serialization"></a>Sérialisation d'événement
La classe [EventData][] possède [quatre constructeurs surchargés](/dotnet/api/microsoft.servicebus.messaging.eventdata#constructors_) qui prennent un grand nombre de paramètres, comme un objet et un sérialiseur, un tableau d’octets ou un flux de données. Il est également possible d'instancier la classe [EventData][] et de définir le flux de données du corps par la suite. Lorsque vous utilisez JSON avec [EventData][], vous pouvez utiliser **Encoding.UTF8.GetBytes()** pour récupérer le tableau d'octets d'une chaîne encodée JSON.

## <a name="partition-key"></a>Clé de partition
La classe [EventData][] a une propriété [PartitionKey][] qui permet à l’expéditeur de spécifier une valeur qui est hachée pour produire une affectation de partition. L’utilisation d'une clé de partition garantit que tous les événements avec la même clé sont envoyés à la même partition dans le hub d’événements. Les clés de partition courantes incluent des ID de session utilisateur et des ID d’expéditeur uniques. La propriété [PartitionKey][] est facultative et peut être fournie lorsque vous utilisez les méthodes [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) ou [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendAsync_Microsoft_ServiceBus_Messaging_EventData_). Si vous ne fournissez pas de valeur pour [PartitionKey][], des événements envoyés sont distribués aux partitions à l'aide d'un modèle de tourniquet (round-robin).

### <a name="availability-considerations"></a>Considérations relatives à la disponibilité

L’utilisation d’une clé de partition est facultative, et vous devez réfléchir attentivement au besoin d’en utiliser une ou non. Dans de nombreux cas, l’utilisation d’une clé de partition est un bon choix si l’ordre des événements est important. Lorsque vous utilisez une clé de partition, les partitions nécessitent une disponibilité sur un nœud unique, et des interruptions peuvent se produire dans le temps, par exemple, lors du redémarrage et de la correction des nœuds de calcul. Par conséquent, si vous définissez un ID de partition et que cette partition devient indisponible pour une raison quelconque, une tentative d’accès aux données de la partition échouera. Si la haute disponibilité représente le critère le plus important, ne spécifiez pas de clé de partition ; dans ce cas, les événements sont envoyés aux partitions à l’aide du modèle de tourniquet (round-robin) décrit précédemment. Dans ce scénario, vous effectuez un choix explicite entre la disponibilité (aucun ID de partition) et la cohérence (épinglage d’événements à un ID de partition).

Une autre considération peut être la gestion des retards dans le traitement des événements. Dans certains cas, il est préférable de supprimer les données et d’effectuer une nouvelle tentative plutôt que d’essayer de poursuivre le traitement, ce qui peut entraîner davantage de retards de traitement en aval. Par exemple, avec un téléscripteur pour le marché boursier, il est préférable d’attendre les données à jour complètes, mais dans une conversation en direct ou un scénario VOIP, vous préférerez plutôt obtenir les données rapidement, même incomplètes.

Étant donné ces considérations relatives à la disponibilité, dans ces scénarios vous pouvez choisir l’une des stratégies de gestion des erreurs suivantes :

- Arrêter (arrêter la lecture à partir d’Event Hubs jusqu’à la résolution des problèmes)
- Supprimer (les messages ne sont pas importants, les supprimer)
- Réessayer (effectuer une nouvelle tentative pour les messages selon vos besoins)
- [Lettre morte](../service-bus-messaging/service-bus-dead-letter-queues.md) (utiliser une file d’attente ou un autre hub d’événements pour mettre en file d’attente de lettres mortes uniquement les messages que vous n’avez pas pu traiter)

Pour obtenir plus d’informations et consulter une discussion concernant les compromis entre la disponibilité et la cohérence, consultez [Disponibilité et cohérence dans Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Opérations d'envoi d’événements par lot
L’envoi d’événements par lots permet d’augmenter le débit. La méthode [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) prend un paramètre **IEnumerable** de type [EventData][] et envoie l’ensemble du lot comme une opération atomique au hub d’événements.

```csharp
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Notez qu'un seul lot ne doit pas dépasser la limite de 256 Ko d'un événement. En outre, chaque message du lot utilise la même identité d’éditeur. Il incombe à l'expéditeur de s’assurer que le lot ne dépasse pas la taille d'événement maximale. Le cas échéant, une erreur **Send** cliente est générée. Vous pouvez utiliser la méthode d’assistance [EventHubClient.CreateBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.createbatch) pour vous assurer que le lot ne dépasse pas 256 Ko. Vous obtenez un [EventDataBatch](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch) vide de l’API [CreateBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.createbatch), puis vous utilisez [TryAdd](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch.tryadd#Microsoft_ServiceBus_Messaging_EventDataBatch_TryAdd_Microsoft_ServiceBus_Messaging_EventData_) pour ajouter des événements pour construire le lot. Enfin, utilisez [EventDataBatch.ToEnumerable](/dotnet/api/microsoft.servicebus.messaging.eventdatabatch.toenumerable) pour obtenir les événements sous-jacents à passer à l’API [EventHubClient.Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.send).

## <a name="send-asynchronously-and-send-at-scale"></a>Envoi de manière asynchrone et envoi à l'échelle
Vous pouvez également envoyer de manière asynchrone des événements à un hub d'événements. L’envoi de manière asynchrone peut augmenter la vitesse à laquelle un client peut envoyer des événements. Les deux méthodes [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.send) et [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendbatch) sont disponibles dans les versions asynchrones qui retournent un objet [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Tandis que cette technique peut augmenter le débit, elle peut également entraîner le client à continuer à envoyer des événements même lorsqu’elle est limitée par le service Event Hubs et peut entraîner des échecs du client ou la perte de messages si elle n'est pas implémentée correctement. En outre, vous pouvez utiliser la propriété [RetryPolicy](/dotnet/api/microsoft.servicebus.messaging.cliententity.retrypolicy) sur le client pour les options de nouvelle tentative du client.

## <a name="create-a-partition-sender"></a>Création d'un expéditeur de partition
Même s’il est plus courant d’envoyer des événements à un hub d’événements sans clé de partition, dans certains cas vous pouvez envoyer des événements directement à une partition donnée. Par exemple :

```csharp
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_CreatePartitionedSender_System_String_) retourne un objet [EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) que vous pouvez utiliser pour publier des événements dans une partition de hubs d’événements spécifique.

## <a name="event-consumers"></a>Consommateurs d'événements
Les hubs d’événements ont deux modèles principaux pour la consommation d'événements : des récepteurs directs et des abstractions de niveau supérieur, comme [EventProcessorHost][]par exemple. Les récepteurs directs sont responsables de leur propre coordination de l’accès aux partitions dans un *groupe de consommateurs*. Un groupe de consommateurs est une vue (état, position ou décalage) d’un hub d’événements partitionné.

### <a name="direct-consumer"></a>Consommateur direct
Le moyen le plus direct pour lire à partir d’une partition consiste à utiliser la classe [EventHubReceiver](/dotnet/apie/microsoft.servicebus.messaging.eventhubreceiver). Pour créer une instance de cette classe, vous devez utiliser une instance de la classe [EventHubConsumerGroup](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup) . Dans l’exemple suivant, l’ID de partition doit être spécifié lors de la création du récepteur pour le groupe de consommateurs :

```csharp
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

La méthode [CreateReceiver](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup#methods_summary) a plusieurs surcharges qui facilitent le contrôle sur le lecteur qui est créé. Ces méthodes incluent la spécification d'un décalage en tant que chaîne ou horodatage et la capacité à spécifier s'il faut inclure ce décalage spécifié dans le flux de données retourné, ou à le démarrer après. Une fois le récepteur créé, vous pouvez démarrer la réception d’événements sur l'objet retourné. La méthode [Receive](/dotnet/api/microsoft.servicebus.messaging.eventhubreceiver#methods_summary) a quatre surcharges qui contrôlent les paramètres d'opération de réception, tels que la taille du lot et le temps d'attente. Vous pouvez utiliser les versions asynchrones de ces méthodes pour augmenter le débit d'un consommateur. Par exemple :

```csharp
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

Pour une partition spécifique, les messages sont reçus dans l'ordre dans lequel ils ont été envoyés au hub d'événements. Le décalage est un jeton de chaîne utilisé pour identifier un message dans une partition.

Notez qu’une partition unique ne peut pas avoir plus de 5 lecteurs simultanés connectés à tout moment. Lorsque les lecteurs se connectent ou sont déconnectés, leurs sessions peuvent rester actives pendant plusieurs minutes avant que ce service ne reconnaisse qu'ils sont déconnectés. Pendant ce temps, la reconnexion à une partition peut échouer. Pour obtenir un exemple complet de l’écriture d’un récepteur direct pour Event Hubs, consultez l’exemple [Récepteurs directs Event Hubs](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) .

### <a name="event-processor-host"></a>Hôte du processeur d’événements
La classe [EventProcessorHost][] traite les données à partir des hubs d’événements. Vous devez utiliser cette implémentation lors de la création de lecteurs d'événement sur la plateforme .NET. [EventProcessorHost][] fournit un environnement d'exécution sécurisé, multiprocessus, thread-safe pour des implémentations d’événements qui fournissent également une gestion de contrôle et de location de partition.

Pour utiliser la classe [EventProcessorHost][], vous pouvez implémenter [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor). Cette interface contient trois méthodes :

* [OpenAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_OpenAsync_Microsoft_ServiceBus_Messaging_PartitionContext_)
* [CloseAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_CloseAsync_Microsoft_ServiceBus_Messaging_PartitionContext_Microsoft_ServiceBus_Messaging_CloseReason_)
* [ProcessEventsAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_ProcessEventsAsync_Microsoft_ServiceBus_Messaging_PartitionContext_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)

Pour commencer le traitement des événements, vous devez instancier [EventProcessorHost][]en fournissant les paramètres appropriés pour votre hub d'événements. Appelez ensuite [RegisterEventProcessorAsync](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost#Microsoft_ServiceBus_Messaging_EventProcessorHost_RegisterEventProcessorAsync__1) pour enregistrer votre implémentation [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) avec le runtime. À ce stade, l’hôte tente d’acquérir un bail sur chaque partition dans le hub d’événements à l’aide d’un algorithme « gourmand ». Ces baux sont valables pour une période donnée et doivent ensuite être renouvelés. Étant donné que de nouveaux nœuds (ici, des instances de rôle) sont en ligne, ils placent des réservations de bail et, en même temps, la charge est déplacée entre les nœuds tandis que chaque nœud tente d’acquérir plus de baux.

![Hôte du processeur d’événements](./media/event-hubs-programming-guide/IC759863.png)

Au fil du temps, un équilibre est établi. Cette fonctionnalité dynamique permet d’appliquer aux consommateurs une mise à l'échelle basée sur le processeur pour une augmentation et une diminution d’échelle. Event Hubs n’ayant pas de concept direct du nombre de messages, l’utilisation moyenne du processeur est souvent la meilleure solution pour mesurer la mise à l’échelle du serveur principal ou du consommateur. Si les éditeurs commencent à publier plus d'événements que ce que les consommateurs peuvent traiter, l'augmentation du processeur sur les consommateurs peut être utilisée pour effectuer une mise à l’échelle automatique sur le nombre d’instances de travail.

La classe [EventProcessorHost][] implémente également un mécanisme de point de contrôle basé sur le stockage Azure. Ce mécanisme stocke le décalage sur une base par partition, afin que chaque consommateur puisse déterminer quel était le dernier point de contrôle du consommateur précédent. Étant donné que la transition des partitions entre les nœuds se fait via les baux, il s’agit d’un mécanisme de synchronisation qui facilité le déplacement de la charge.

## <a name="publisher-revocation"></a>Révocation de l’éditeur
Outre les fonctionnalités d’exécution avancées de [EventProcessorHost][], les hubs d’événements permettent la révocation de l’éditeur pour empêcher certains éditeurs d’envoyer des événements à un hub d’événements. Ces fonctionnalités sont utiles si le jeton d’un éditeur a été compromis ou une mise à jour de logiciel les fait se comporter de façon inappropriée. Dans ces situations, l’identité de l'éditeur, qui fait partie de leur jeton SAP, peut être bloquée à partir d'événements de publication.

Pour plus d’informations sur la révocation de l’éditeur et le mode d’envoi vers Event Hubs en tant qu’éditeur, consultez l’exemple [Publication sécurisée à grande échelle des hubs d’événements de Service Bus](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les scénarios Event Hubs, consultez ces liens :

* [Vue d’ensemble de l'API Event Hubs](event-hubs-api-overview.md)
* [Qu’est-ce qu’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Disponibilité et cohérence dans Event Hubs](event-hubs-availability-and-consistency.md)
* [Informations de référence des API hôtes du processeur d’événements](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.servicebus.messaging.eventhubclient
[EventData]: /dotnet/api/microsoft.servicebus.messaging.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
