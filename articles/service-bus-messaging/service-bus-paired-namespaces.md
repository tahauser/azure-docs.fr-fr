---
title: "Espaces de noms associés Azure Service Bus | Microsoft Docs"
description: "Détails sur l'implémentation de l'espace de noms associé et coût"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: f16c65286b0aa079889c9d53e98bf54e3d57c95f
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Détails sur l'implémentation de l'espace de noms associé et implications en termes de coût

La méthode [PairNamespaceAsync][PairNamespaceAsync], à l’aide d’une instance [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], effectue des tâches visibles à votre place. Étant donné qu'il existe des considérations relatives aux coûts lors de l'utilisation de la fonctionnalité, il est utile de comprendre ces tâches afin que vous vous attendiez au comportement lorsque celui-ci se produit. L'API engage le comportement automatique suivant à votre place :

* Création de files d'attente de travaux en souffrance.
* Création d’un objet [MessageSender][MessageSender] qui communique avec les files d’attente ou les rubriques.
* Lorsqu'une entité de messagerie devient indisponible, envoie des messages ping à l'entité dans le but de détecter lorsque cette entité est à nouveau disponible.
* Ou, crée un ensemble de « pompes de messages » qui déplacent les messages depuis les files d'attente de travaux en souffrance vers les files d'attente principales.
* Coordonne la clôture/défaillance des instances [MessagingFactory][MessagingFactory] principale et secondaire.

À un niveau élevé, la fonctionnalité fonctionne comme suit : lorsque l'entité principale est intègre, aucun changement de comportement ne se produit. Lorsque la durée [FailoverInterval][FailoverInterval] est écoulée et que l’entité principale ne constate aucun envoi réussi après une valeur [MessagingException][MessagingException] ou [TimeoutException][TimeoutException] non temporaire, le comportement suivant se produit :

1. Les opérations d'envoi à l'entité principale sont désactivées et le système interroge l'entité principale jusqu'à ce que les tests ping puissent être remis avec succès.
2. Une file d'attente de travaux en souffrance aléatoire est sélectionnée.
3. Les objets [BrokeredMessage][BrokeredMessage] sont acheminés vers la file d’attente de travaux en souffrance choisie.
4. En cas d'échec d'une opération d'envoi vers la file d'attente de travaux en souffrance choisie, cette file d'attente est extraite de la rotation et une nouvelle file d'attente est sélectionnée. Tous les expéditeurs de l’instance [MessagingFactory][MessagingFactory] sont mis au courant de l’échec.

Les figures suivantes décrivent la séquence. Tout d'abord, l'expéditeur envoie des messages.

![Espaces de noms associés][0]

En cas d'échec de l'envoi à la file d'attente principale, l'expéditeur commence à envoyer des messages à une file d'attente de travaux en souffrance choisie de façon aléatoire. Simultanément, il démarre une tâche ping.

![Espaces de noms associés][1]

À ce stade, les messages sont toujours dans la file d'attente secondaire et n'ont pas été remis à la file d'attente principale. Une fois que la file d'attente principale redevient intègre, au moins un processus doit exécuter le siphon. Le siphon remet les messages de toutes les files d’attente de travaux en souffrance aux entités de destination appropriées (files d’attente et rubriques).

![Espaces de noms associés][2]

Le reste de cette rubrique décrit les détails spécifiques du fonctionnement de ces éléments.

## <a name="creation-of-backlog-queues"></a>Création de files d'attente de travaux en souffrance
L’objet [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] transmis à la méthode [PairNamespaceAsync][PairNamespaceAsync] indique le nombre de files d’attente de travaux en souffrance que vous voulez utiliser. Chaque file d’attente de travaux en souffrance est créée avec les propriétés suivantes explicitement définies (toutes les autres valeurs sont définies sur la valeur [QueueDescription][QueueDescription] par défaut) :

| path | [primary namespace]/x-servicebus-transfer/[index] où [index] est une valeur dans [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int.MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1 minute |
| EnableDeadLetteringOnMessageExpiration |true |
| EnableBatchedOperations |true |

Par exemple, la première file d’attente de travaux en souffrance créée pour l’espace de noms **contoso** est nommée `contoso/x-servicebus-transfer/0`.

Lorsque vous créez les files d'attente, le code vérifie d'abord si cette file d'attente existe. Dans le cas contraire, la file d'attente est créée. Le code ne nettoie pas les files d'attente de travaux en souffrance « supplémentaires ». Plus précisément, si l’application avec l’espace de noms principal **contoso** demande cinq files d’attente de travaux en souffrance, mais qu’il existe une file d’attente de travaux en souffrance avec le chemin d’accès `contoso/x-servicebus-transfer/7`, cette file d’attente de travaux en souffrance supplémentaire est toujours présente, mais n’est pas utilisée. Le système autorise explicitement l'existence de files d'attente de travaux en souffrance qui ne seraient pas utilisées. En tant que propriétaire de l'espace de noms, vous êtes chargé de nettoyer les files d'attente de travaux en souffrance inutilisées/indésirables. La raison est que Service Bus ne peut pas connaître les objectifs de toutes les files d'attente dans votre espace de noms. En outre, s’il existe une file d’attente du nom donné, mais qu’elle ne se conforme PAS à la valeur [QueueDescription][QueueDescription], alors les raisons de la modification du comportement par défaut vous appartiennent. Aucune garantie n'est donnée pour les modifications apportées aux files d'attente de travaux en souffrance par votre code. Veillez à tester vos modifications de manière approfondie.

## <a name="custom-messagesender"></a>MessageSender personnalisé
Lors de l’envoi, tous les messages passent par un objet [MessageSender][MessageSender] interne qui se comporte normalement lorsque tout fonctionne et redirige vers les files d’attente de travaux en souffrance en cas de dysfonctionnement. Lors de la réception d'une défaillance non temporaire, une minuterie démarre. Après une période [TimeSpan][TimeSpan] comprenant la valeur de la propriété [FailoverInterval][FailoverInterval] au cours de laquelle aucun message n’est envoyé, le basculement est activé. À ce stade, les événements suivants se produisent pour chaque entité :

* Une tâche ping exécute chaque [PingPrimaryInterval][PingPrimaryInterval] pour vérifier si l’entité est disponible. Lorsque cette tâche réussit, tout le code client qui utilise l'entité commence immédiatement à envoyer de nouveaux messages à l'espace de noms principal.
* Les futures requêtes consistant à envoyer à cette même entité à partir de n’importe quel autre expéditeur provoquent la modification du [BrokeredMessage][BrokeredMessage] envoyé, qui doit alors se trouver dans la file d’attente. La modification supprime certaines propriétés de l’objet [BrokeredMessage][BrokeredMessage] pour les stocker ailleurs. Les propriétés suivantes sont effacées et ajoutées sous un nouvel alias, ce qui permet à Service Bus et au Kit de développement logiciel de traiter les messages de manière uniforme :

| Nom de l'ancienne propriété | Nom de la nouvelle propriété |
| --- | --- |
| SessionId |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

Le chemin de destination d'origine est également stocké dans le message en tant que propriété nommée x-ms-path. Cette conception permet à des messages de plusieurs entités de coexister dans une seule file d'attente de travaux en souffrance. Les propriétés sont traduites par le siphon.

L’objet [MessageSender][MessageSender] personnalisé peut rencontrer des problèmes lorsque les messages approchent de la limite de 256 Ko et que le basculement est activé. L’objet [MessageSender][MessageSender] personnalisé stocke les messages pour toutes les files d’attente et rubriques dans les files d’attente de travaux en souffrance. Cet objet combine des messages provenant de plusieurs sources principales avec les files d'attente de travaux en souffrance. Pour gérer l’équilibrage de charge entre plusieurs clients qui ne se connaissent pas mutuellement, le Kit de développement logiciel choisit de façon aléatoire une file d’attente de travaux en souffrance pour chaque [QueueClient][QueueClient] ou [TopicClient][TopicClient] que vous créez dans le code.

## <a name="pings"></a>Tests ping
Un message ping est un [BrokeredMessage][BrokeredMessage] vide avec sa propriété [ContentType][ContentType] définie sur application/vnd.ms-servicebus-ping et une valeur [TimeToLive][TimeToLive] de 1 seconde. Cette commande ping a une caractéristique particulière dans Service Bus : le serveur ne remet jamais une commande ping lorsqu’un appelant demande un [BrokeredMessage][BrokeredMessage]. Par conséquent, vous n'avez pas besoin de savoir comment recevoir et ignorer ces messages. Chaque entité (file d’attente ou rubrique unique) par instance [MessagingFactory][MessagingFactory] par client sera interrogée si elle est considérée comme non disponible. Par défaut, cela se produit une fois par minute. Les messages ping sont considérés comme des messages Service Bus normaux et peuvent entraîner des frais de bande passante et de messagerie. Dès que les clients détectent que le système est disponible, les messages s'arrêtent.

## <a name="the-syphon"></a>Le siphon
Au moins un programme exécutable de l'application doit exécuter activement le siphon. Le siphon effectue une longue interrogation de réception qui dure 15 minutes. Lorsque toutes les entités sont disponibles et que vous avez 10 files d'attente de travaux en souffrance, l'application qui héberge le siphon appelle l'opération de réception 40 fois par heure, 960 fois par jour et 28 800 fois en 30 jours. Lorsque le siphon déplace activement des messages à partir de la file d'attente de travaux en souffrance vers la file d'attente principale, chaque message est soumis aux frais suivants (des frais standard pour la taille des messages et la bande passante s'appliquent à toutes les étapes) :

1. Envoyer à la file d'attente de travaux en souffrance.
2. Recevoir depuis la file d'attente de travaux en souffrance.
3. Envoyer à la file d'attente principale.
4. Recevoir depuis la file d'attente principale.

## <a name="closefault-behavior"></a>Comportement de fermeture/erreur
Dans une application qui héberge le siphon, si l’instance [MessagingFactory][MessagingFactory] principale ou secondaire connaît une défaillance ou est fermée sans que son partenaire connaisse une défaillance ou soit fermé également, et que le siphon détecte cet état, le siphon réagit. Si l’autre instance [MessagingFactory][MessagingFactory] n’est pas fermée dans les 5 secondes, le siphon place l’instance [MessagingFactory][MessagingFactory] toujours ouverte en état de défaillance.

## <a name="next-steps"></a>Étapes suivantes
Consultez [Modèles de messagerie asynchrone et haute disponibilité][Asynchronous messaging patterns and high availability] pour une discussion détaillée sur la messagerie asynchrone de Service Bus. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
