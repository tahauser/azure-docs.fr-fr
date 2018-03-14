---
title: "Files d’attente de lettres mortes Service Bus | Microsoft Docs"
description: "Vue d’ensemble des files d’attente de lettres mortes Azure Service Bus"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: a82d70e7bf776bf470d14e7f061774ccbb136316
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Vue d’ensemble des files d’attente de lettres mortes Service Bus

Les files d’attente Azure Service Bus et les abonnements aux rubriques fournissent une sous-file d’attente secondaire, appelée *file d’attente de lettres mortes*. La file d’attente de lettres mortes n’a pas besoin d’être explicitement créée et ne peut pas être supprimée ou gérée indépendamment de l’entité principale.

Cet article décrit les files d’attente de lettres mortes dans Service Bus. Une grande partie de la discussion est illustrée par l’[exemple de files d’attente de lettres mortes](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) qui se trouve sur GitHub.
 
## <a name="the-dead-letter-queue"></a>La file d’attente de lettres mortes

L’objectif de la file d’attente de lettres mortes est de conserver les messages qui ne peuvent pas être remis aux destinataires ou les messages qui n’ont pas pu être traités. Les messages peuvent alors être supprimés de la file d’attente de lettres mortes et inspectés. Une application peut, à l’aide d’un opérateur, corriger les problèmes et renvoyer le message, consigner le fait qu’une erreur s’est produite et prendre des mesures correctives. 

Du point de vue de l’API et du protocole, la file d’attente de lettres mortes est essentiellement similaire à une autre file d’attente, sauf que les messages peuvent être envoyés uniquement par le biais de l’opération de lettres mortes de l’entité parente. En outre, la durée de vie n’est pas observée, et vous ne pouvez pas supprimer un message d’une file d’attente de lettres mortes. La file d’attente de lettres mortes prend entièrement en charge la remise de verrou d’affichage et les opérations transactionnelles.

Notez qu’il n’y a aucun nettoyage automatique de la file d’attente de lettres mortes. Les messages restent dans la file d’attente de lettres mortes jusqu’à ce que vous les récupériez explicitement et que vous appeliez [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) sur le message de lettres mortes.

## <a name="moving-messages-to-the-dlq"></a>Déplacer des messages vers la file d’attente de lettres mortes

Plusieurs activités dans Service Bus entraînent l’envoi des messages dans la file d’attente de lettres mortes à partir du moteur de messagerie lui-même. Une application peut également explicitement déplacer des messages dans la file d’attente de lettres mortes. 

Comme le message est déplacé par le service broker, deux propriétés sont ajoutées au message quand le service broker appelle sa version interne de la méthode [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) sur le message : `DeadLetterReason` et `DeadLetterErrorDescription`.

Les applications peuvent définir leurs propres codes pour la propriété `DeadLetterReason`, mais le système définit les valeurs suivantes.

| Condition | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Toujours |HeaderSizeExceeded |Le quota de taille pour ce flux a été dépassé. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing et SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Le message a expiré et a été placé dans la file d’attente de lettres mortes. |
| SubscriptionDescription.RequiresSession |L’ID de session a la valeur null. |L’entité activée dans la session n’autorise pas les messages dont l’identificateur de session a la valeur null. |
| !dead letter queue |MaxTransferHopCountExceeded |Null |
| Mise en file d’attente de lettres mortes explicite par l’application |Spécifié par l’application |Spécifié par l’application |

## <a name="exceeding-maxdeliverycount"></a>Dépassement de MaxDeliveryCount

Les files d’attente et les abonnements ont chacun une propriété [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) et [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount), respectivement. La valeur par défaut est 10. Chaque fois qu’un message a été remis sous un verrou ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), mais qu’il a été explicitement abandonné ou que le verrou a expiré, la propriété [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) du message est incrémentée. Quand [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) dépasse [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), le message est déplacé vers la file d’attente de lettres mortes avec le code motif `MaxDeliveryCountExceeded`.

Ce comportement ne peut pas être désactivé, mais vous pouvez définir [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) sur un très grand nombre.

## <a name="exceeding-timetolive"></a>Dépassement de TimeToLive

Quand la propriété [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) ou [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnMessageExpiration) est définie sur **true** (la valeur par défaut est **false**), tous les messages arrivant à expiration sont déplacés vers la file d’attente de lettres mortes avec le code motif `TTLExpiredException`.

Les messages ayant expiré sont uniquement purgés et transférés vers la file d’attente de lettres mortes quand au moins un destinataire actif effectue une collecte à partir de la file d’attente principale ou l’abonnement. Ce comportement est normal.

## <a name="errors-while-processing-subscription-rules"></a>Erreurs pendant le traitement des règles d’abonnement

Quand la propriété [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnFilterEvaluationExceptions) est activée pour un abonnement, les erreurs qui surviennent pendant l’exécution des règles de filtre SQL d’un abonnement sont capturées dans la file d’attente de lettres mortes avec le message incriminé.

## <a name="application-level-dead-lettering"></a>Mise en file d’attente de lettres mortes au niveau de l’application

En plus des fonctionnalités de file d’attente de lettres mortes fournies par le système, les applications peuvent utiliser la file d’attente de lettres mortes pour refuser explicitement les messages inacceptables. Il peut s’agir de messages qui ne peuvent pas être traités correctement en raison d’un problème système, de messages qui contiennent des charges utiles incorrectement formées ou de messages qui n’ont pas pu être authentifiés lors de l’utilisation d’un schéma de sécurité au niveau du message.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Mise en file d’attente de lettres mortes dans des scénarios ForwardTo ou SendVia

Les messages seront envoyés à la file d’attente de lettres mortes de transfert dans les conditions suivantes :

- Un message passe par plus de 3 files d’attente ou rubriques [enchaînées](service-bus-auto-forwarding.md).
- La file d’attente de destination ou la rubrique est désactivée ou supprimée.
- La file d’attente ou la rubrique de destination dépasse la taille d’entité maximale.

Pour récupérer ces messages de lettres mortes, vous pouvez créer un récepteur à l’aide de la méthode utilitaire [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath).

## <a name="example"></a>Exemple

L’extrait de code suivant crée un destinataire de message. Dans la boucle de réception de la file d’attente principale, le code récupère le message avec [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_Receive_System_TimeSpan_), qui demande au service broker de retourner immédiatement les messages disponibles ou de ne retourner aucun résultat. Si le code reçoit un message, il l’abandonne immédiatement, ce qui incrémente le `DeliveryCount`. Une fois que le système déplace le message vers la file d’attente de lettres mortes, la file d’attente principale est vide et la boucle se ferme, car [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_ReceiveAsync_System_TimeSpan_) retourne **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>étapes suivantes

Pour plus d’informations sur les files d’attente de lettres mortes Service Bus, consultez les articles suivants :

* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Comparaison des files d’attente Azure et Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

