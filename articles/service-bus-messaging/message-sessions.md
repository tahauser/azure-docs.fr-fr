---
title: Sessions de messagerie Azure Service Bus | Microsoft Docs
description: Traitez les séquences de messages Azure Service Bus à l’aide de sessions.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: sethm
ms.openlocfilehash: 551432cd13c16fdd5423c46ed9c6f740353808f8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Sessions de messagerie : premier entré, premier sorti (FIFO) 

Les sessions Microsoft Azure Service Bus permettent un traitement conjoint et chronologique de séquences illimitées de messages associés. Pour garantir l’application de la méthode FIFO dans Service Bus, utilisez Sessions. Service Bus ne fournit aucun détail concernant la nature de la relation entre les messages, et ne définit aucun modèle spécifique permettant de déterminer le début et la fin d’une séquence de messages.

Lors de l’envoi de messages dans une rubrique ou dans une file d’attente, tout expéditeur peut créer une session en définissant la propriété [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) sur un identificateur défini par l’application unique pour la session. Au niveau du protocole AMQP 1.0, cette valeur est mappée sur la propriété *group-id*.

Dans les files d’attente ou abonnements prenant en charge la session, les sessions entrent en vigueur lorsqu’au moins un message présente la valeur [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) de la session. Une fois qu’une session existe, aucune date ni API ne définissent l’arrivée à expiration ou la disparition de la session. Il est théoriquement possible de recevoir un message pour une session le jour même, puis le message suivant un an après, et si la valeur **SessionId** correspond, Service Bus considère alors qu’il s’agit de la même session.

Toutefois, en général, une application détermine précisément le début et la fin d’un ensemble de messages associés. Service Bus ne définit pas de règles spécifiques.

Un exemple de délimitation d’une séquence dans le cadre du transfert d’un fichier consiste à définir la propriété **Label** du premier message sur **start**, celle des messages intermédiaires sur **content**, et celle du dernier message sur **end**. La position relative des messages de contenu peut être calculée comme étant égale à la différence entre la valeur *SequenceNumber* du message actuel et la valeur *SequenceNumber* du message présentant la valeur **start**.

La fonctionnalité de session dans Service Bus autorise une opération de réception spécifique, sous la forme [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) dans les API C# et Java. Vous activez cette fonctionnalité en définissant la propriété [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) sur la file d’attente ou l’abonnement par le biais d’Azure Resource Manager, ou en définissant l’indicateur dans le portail. Vous devez effectuer cette opération avant de tenter d’utiliser les opérations d’API associées.

Dans le portail, définissez l’indicateur avec la case à cocher suivante :

![][2]

Les API relatives aux sessions existent sur les clients de file d’attente et d’abonnement. Il existe un modèle impératif qui contrôle le moment de la réception des sessions et des messages, ainsi qu’un modèle basé sur le gestionnaire, semblable à *OnMessage*, qui simplifie la gestion de la boucle de réception.

## <a name="session-features"></a>Fonctionnalités de session

Les sessions assurent un démultiplexage simultané de flux de messages entrelacés tout en préservant et en garantissant la remise chronologique des messages.

![][1]

Un destinataire [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) est créé par le client qui accepte une session. Le client appelle [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) en C#. Dans le modèle de rappel réactif, il inscrit un gestionnaire de sessions.

Lorsque l’objet [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) est accepté et détenu par un client, ce dernier maintient un verrouillage exclusif sur tous les messages présentant la valeur [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) de cette session dans la file d’attente ou dans l’abonnement, ainsi que sur tous les messages dotés de cette valeur **SessionId** qui continuent d’arriver pendant toute la durée de la session.

Ce verrouillage est désactivé lorsque la méthode **Close** ou **CloseAsync** est appelée, ou lorsque le verrouillage arrive à expiration dans les cas où l’application ne parvient pas à effectuer l’opération de fermeture. Le verrouillage de session doit être traité de la même façon qu’un verrouillage exclusif sur un fichier, ce qui signifie que l’application doit fermer la session dès qu’elle n’en a plus besoin ou qu’elle n’attend plus aucun message.

Lorsque plusieurs destinataires simultanés extraient des données de la file d’attente, les messages appartenant à une session spécifique sont envoyés au destinataire concerné qui détient actuellement le verrouillage pour cette session. Grâce à cette opération, un flux de messages entrelacé présent dans une file d’attente ou dans un abonnement est correctement démultiplexé vers différents destinataires, qui peuvent également résider sur des machines clientes distinctes, étant donné que la gestion des verrouillages se produit côté service, au sein de Service Bus.

L’illustration précédente montre trois récepteurs de session simultanée. Une Session avec `SessionId` = 4 ne contenant aucun client actif, propriétaire, aucun message n’est remis à partir de cette session. Une session se comporte à maints égards comme une sous-file d’attente.

Le verrouillage de session détenu par le destinataire de session constitue une protection pour les verrouillages de message utilisés par le mode de règlement *peek-lock*. Un destinataire ne peut pas avoir deux messages « en cours » simultanément, mais les messages doivent être traités dans l’ordre. Un nouveau message ne peut être obtenu que lorsque le message précédent a été traité ou placé dans la file d’attente de lettres mortes. L’abandon d’un message entraîne un nouveau traitement de ce message lors de l’opération de réception suivante.

## <a name="message-session-state"></a>État d’une session de messagerie

Lorsque les workflows sont traités dans des systèmes de cloud à grande échelle et à haute disponibilité, le gestionnaire de workflows associé à une session spécifique doit pouvoir redevenir opérationnel en cas de défaillances inattendues et reprendre un travail incomplet sur un autre processus ou une autre machine que ceux sur lesquels le travail avait commencé.

La fonction d’état de session active une annotation définie par l’application d’une session de messagerie dans le répartiteur, de sorte que l’état de traitement enregistré concernant cette session devient immédiatement disponible lorsque la session est acquise par un nouveau processeur.

Du point de vue de Service Bus, l’état d’une session de messagerie est un objet binaire opaque qui peut contenir une taille de données équivalant à un message, autrement dit 256 Ko pour Service Bus Standard et 1 Mo dans le cas de Service Bus Premium. L’état de traitement relatif à une session peut être stocké dans l’état de session, ou l’état de session peut pointer vers un emplacement de stockage ou un enregistrement de base de données contenant cette information.

Les API de gestion de l’état de session, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) et [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), sont disponibles dans l’objet [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) des API C# et Java. Une session pour laquelle aucun état de session n’a été précédemment défini renvoie une référence **null** pour **GetState**. L’annulation de l’état de session précédemment défini s’effectue à l’aide de [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Notez que l’état de session reste tant qu’il n’est pas libéré (retour de la valeur **Null** ), même si tous les messages dans une session donnée sont consommés.

Toutes les sessions existantes dans une file d’attente ou un abonnement peuvent être énumérées à l’aide de la méthode **SessionBrowser** de l’API Java et au moyen de [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) sur [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) et [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) dans le client .NET.

L’état de session stocké dans une file d’attente ou dans un abonnement est pris en compte dans le quota de stockage de cette entité. Lorsque l’application a terminé avec une session, il est donc recommandé de faire en sorte que l’application supprime l’état de session conservé afin d’éviter un coût de gestion externe.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [un exemple complet](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) des procédures d’envoi et de réception de messages basés sur la session à partir de files d’attente Service Bus à l’aide de la bibliothèque .NET Standard.
- Consultez [un exemple](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) qui utilise le client .NET Framework pour traiter les messages prenant en charge la session. 

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png