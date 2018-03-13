---
title: Expiration des messages Azure Service Bus | Microsoft Docs
description: "Expiration et durée de vie des messages Azure Service Bus"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 6e1f6177ccacf24955763982189bcdb1ef69c788
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="message-expiration-time-to-live"></a>Expiration des messages (durée de vie)

La charge utile dans un message, ou une commande ou demande transmise par un message à un destinataire, est presque toujours soumise à une forme de délai d’expiration au niveau de l’application. Passé ce délai, le contenu n’est plus remis, ou l’opération demandée n’est plus exécutée.

Dans les environnements de développement et de test où les files d’attente et les rubriques sont souvent utilisées dans le contexte d’exécutions partielles d’applications ou de parties d’application, il est également préférable que les messages de test abandonnés soient automatiquement nettoyés de la mémoire pour permettre à la prochaine série de tests de démarrer le nettoyage.

Le délai d’expiration de chaque message peut être contrôlé à l’aide de la propriété système [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive), qui définit une durée relative. Ce délai devient un instant absolu quand le message est mis en file d’attente dans l’entité. La propriété [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) prend alors la valeur [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Quand l’instant **ExpiresAtUtc** est passé, les messages ne sont plus éligibles pour la récupération. Le délai d’expiration ne s’applique pas aux messages qui sont actuellement verrouillés pour la remise, lesquels continuent d’être traités normalement. Si le verrou expire ou si le message est abandonné, le délai d’expiration prend effet immédiatement.

Lorsque le message est verrouillé, l’application peut être en possession d’un message qui a expiré. L’application peut alors poursuivre le traitement ou choisir d’abandonner le message. C’est au responsable de l’implémentation d’en décider.

## <a name="entity-level-expiration"></a>Expiration au niveau de l’entité

Tous les messages envoyés dans une file d’attente ou une rubrique sont soumis à un délai d’expiration par défaut qui est défini au niveau de l’entité avec la propriété [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues). Ce délai peut également être défini dans le portail au moment de la création et modifié plus tard. Le délai d’expiration par défaut est utilisé pour tous les messages envoyés à l’entité où la propriété [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) n’est pas définie explicitement. Le délai d’expiration par défaut sert également de plafond pour la valeur **TimeToLive**. Les messages qui ont un délai d’expiration **TimeToLive** plus long que le délai par défaut sont ajustés en mode silencieux à la valeur **defaultMessageTimeToLive** avant d’être mis en file attente.

Vous pouvez éventuellement choisir de déplacer les messages expirés vers une [file d’attente de lettres mortes](service-bus-dead-letter-queues.md) en définissant la propriété [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) ou en cochant la case correspondante dans le portail. Si l’option reste désélectionnée, les messages expirés sont supprimés. La distinction entre les messages expirés déplacés vers la file d’attente de lettres mortes et les autres messages de lettres mortes est possible grâce à la valeur de la propriété [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) que le répartiteur stocke dans la section des propriétés utilisateur (sa valeur est [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) dans le cas présent).

Dans le cas cité plus haut où le message n’est pas soumis au délai d’expiration du fait qu’il est verrouillé, et si l’indicateur est défini sur l’entité, le message est déplacé dans la file d’attente de lettres mortes aussitôt après l’abandon ou l’expiration du verrou. Toutefois, le message n’est pas déplacé s’il s’agit d’un message réglé, ce qui suppose que l’application a pu le traiter, en dépit du délai d’expiration nominal.

L’utilisation combinée de la propriété [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) et du déplacement automatique (et transactionnel) vers la file d’attente de lettres mortes à l’expiration est une solution efficace pour garantir qu’un travail donné à un gestionnaire ou un groupe de gestionnaires avec un délai d’expiration sera bien récupéré pour être traité avant la fin du délai d’expiration.

Prenons l’exemple d’un site web qui doit exécuter des travaux de manière fiable sur un serveur principal avec des contraintes de mise à l’échelle, et qui connaît parfois des pics de trafic ou doit être isolé pendant les périodes de disponibilité de ce serveur principal. En situation normale, le gestionnaire côté serveur envoie (push) les données transmises par l’utilisateur dans une file d’attente et reçoit par la suite une réponse qui confirme le traitement réussi de la transaction dans une file d’attente de réponse. En présence d’un pic de trafic, si le gestionnaire du serveur principal ne peut pas traiter ses éléments de backlog dans le temps imparti, les travaux expirés sont retournés dans la file d’attente de lettres mortes. L’utilisateur interactif peut être averti que l’opération demandée va nécessiter un peu plus de temps que normalement, et la demande peut ensuite être placée dans une autre file d’attente pour un chemin d’accès de traitement où le résultat du traitement final est envoyé à l’utilisateur par e-mail. 

## <a name="temporary-entities"></a>Entités temporaires

Les files d’attente, rubriques et abonnements Service Bus peuvent être créés en tant qu’entités temporaires, qui sont automatiquement supprimées quand elles n’ont pas été utilisées pendant une période spécifiée.
 
Le nettoyage automatique est utile dans les scénarios de développement et de test où les entités sont créées de façon dynamique et ne sont pas nettoyées après leur utilisation, en raison d’une interruption de la série de tests ou d’un débogage. Il est également utile quand une application crée des entités dynamiques, telles qu’une file d’attente de réponse, pour recevoir des réponses dans un processus de serveur web ou dans un autre objet relativement éphémère. En effet, dans ce cas, il est difficile de nettoyer correctement ces entités quand l’instance de l’objet disparaît.

Vous activez la fonctionnalité à l’aide de la propriété [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues), qui est définie à la durée pendant laquelle une entité peut rester inactive (non utilisée) avant d’être automatiquement supprimée. La durée minimale est de 5 minutes.
 
La propriété **autoDeleteOnIdle** doit être définie par le biais d’une opération Azure Resource Manager ou des API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) du client .NET Framework. Elle ne peut pas être définie dans le portail.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)