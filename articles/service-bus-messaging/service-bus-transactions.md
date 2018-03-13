---
title: "Vue d’ensemble du traitement des transactions dans Azure Service Bus | Microsoft Docs"
description: "Vue d’ensemble des transactions atomiques Azure Service Bus et de la fonctionnalité de transfert"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: d2e3fc7c59e0b57e77d2239ff73368f96426ef39
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="overview-of-service-bus-transaction-processing"></a>Vue d’ensemble du traitement des transactions Service Bus

Cet article décrit les fonctionnalités de transaction de Microsoft Azure Service Bus. Le propos est principalement illustré avec l’exemple [Atomic Transactions with Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions) (Transactions atomiques avec Service Bus). Cet article se limite à une vue d’ensemble du traitement des transactions et de la fonctionnalité de *transfert* de Service Bus, tandis que l’exemple des transactions atomiques est de portée plus étendue et plus complexe.

## <a name="transactions-in-service-bus"></a>Transactions dans Service Bus

Une [*transaction*](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) regroupe plusieurs opérations dans une *étendue d’exécution*. Par nature, ce type de transaction doit garantir que toutes les opérations appartenant à un groupe d’opérations donné réussissent ou échouent ensemble. À cet égard, les transactions agissent comme une unité, ce qui est souvent désigné par le terme *atomicité*. 

Service Bus est un courtier de messages transactionnel. Il garantit l’intégrité transactionnelle de toutes les opérations internes par rapport à ses banques de messages. Tous les transferts de messages dans Service Bus, notamment le déplacement des messages vers une [file d’attente de lettres mortes](service-bus-dead-letter-queues.md) ou le [transfert automatique](service-bus-auto-forwarding.md) de messages entre des entités, sont transactionnels. Par conséquent, si Service Bus accepte un message, il a déjà été stocké et étiqueté avec un numéro de séquence. Dès lors, tous les transferts de messages dans Service Bus sont des opérations coordonnées entre les entités et n’entraînent jamais de perte (réussite de la source et échec de la cible) ou de duplication (échec de la source et réussite de la cible) du message.

Service Bus prend en charge les opérations de regroupement par rapport à une entité de messagerie unique (file d’attente, rubrique, abonnement) dans l’étendue d’une transaction. Par exemple, vous pouvez envoyer plusieurs messages à une file d’attente à partir d’une étendue de transaction, et les messages seront validés dans le journal de la file d’attente uniquement lorsque la transaction se terminera correctement.

## <a name="operations-within-a-transaction-scope"></a>Opérations au sein d’une étendue de transaction

Les opérations qui peuvent être effectuées dans une étendue de transaction sont les suivantes :

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)** : Send, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)** : Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

Les opérations de réception ne sont pas incluses, car on suppose que l’application acquiert d’abord des messages à l’aide du mode [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) dans une boucle de réception ou avec un rappel [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) avant d’ouvrir une étendue de transaction pour le traitement du message.

Le traitement du message (exécution, abandon, lettre morte, report) se produit dans l’étendue du résultat global de la transaction et selon ce résultat.

## <a name="transfers-and-send-via"></a>Transferts

Pour activer la remise transactionnelle des données à partir d’une file d’attente à un processeur, puis à une autre file d’attente, Service Bus prend en charge les *transferts*. Dans une opération de transfert, un expéditeur envoie d’abord un message à une *file d’attente de transfert*. Celle-ci déplace immédiatement le message vers la file d’attente de destination prévue à l’aide d’une implémentation de transfert particulièrement fiable (celle sur laquelle repose également la fonctionnalité de transfert automatique). Le message n’est jamais validé dans le journal de la file d’attente de transfert de telle façon qu’il devient visible pour les consommateurs de la file d’attente de transfert.

La puissance de cette fonctionnalité transactionnelle devient évidente lorsque la file d’attente de transfert elle-même est la source des messages d’entrée de l’expéditeur. En d’autres termes, Service Bus peut transférer le message vers la file d’attente de destination « via » la file d’attente de transfert, tout en effectuant une opération d’exécution (ou une opération de report ou de lettre morte) sur le message d’entrée, en une opération atomique. 

### <a name="see-it-in-code"></a>Apparence dans le code

Pour configurer ces transferts, vous devez créer un expéditeur de message qui cible la file d’attente de destination via la file d’attente de transfert. Il vous faut également un destinataire qui extrait les messages de cette même file d’attente. Par exemple : 

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Une simple transaction utilise alors ces éléments, comme dans l’exemple suivant :

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les files d’attente de lettres mortes Service Bus, consultez les articles suivants :

* [Utilisation des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Chaînage des entités Service Bus avec transfert automatique](service-bus-auto-forwarding.md)
* Exemple [Auto-forward](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward) (Transfert automatique)
* Exemple [Atomic Transactions with Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions) (Transactions atomiques avec Service Bus)
* [Comparaison des files d’attente Azure et Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


