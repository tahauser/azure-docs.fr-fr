---
title: Exceptions de la messagerie Azure Event Hubs | Microsoft Docs
description: "Liste des exceptions de la messagerie Azure Event Hubs et les actions suggérées."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 964475ba8b42ac41707fa78468bfe551677c595f
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="event-hubs-messaging-exceptions"></a>Exceptions de la messagerie Event Hubs

Cet article répertorie certaines exceptions générées par la bibliothèque d’API de la messagerie Azure Service Bus, notamment les API Event Hubs. Cette référence est susceptible de changer, donc consultez-la régulièrement.

## <a name="exception-categories"></a>Catégories d'exceptions

Les API Event Hubs génèrent des exceptions qui peuvent être classées dans les catégories suivantes, accompagnées de l’action associée que vous pouvez mettre en place pour les résoudre.

1. Erreur de codage utilisateur : [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Action générale : essayez de corriger le code avant de poursuivre.
2. Erreur d’installation ou de configuration : [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Action générale : révisez la configuration et modifiez-la si besoin.
3. Exceptions temporaires : [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Action générale : relancez l'opération ou avertissez les utilisateurs.
4. Autres exceptions : [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Action générale : propre au type d’exception ; reportez-vous au tableau de la section suivante. 

## <a name="exception-types"></a>Types d'exceptions
Le tableau suivant répertorie les types d'exceptions de la messagerie, leurs causes et les propositions d'actions que vous pouvez effectuer.

| **Type d'exception** | **Description/Cause/Exemples** | **Action suggérée** | **Remarques sur la nouvelle tentative automatique/immédiate** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Le serveur n'a pas répondu à l'opération demandée dans le délai spécifié qui est contrôlé par le paramètre [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Le serveur peut avoir terminé l'opération demandée. Cela peut se produire en raison de délais sur le réseau ou autre infrastructure. |Vérifiez la cohérence de l'état du système et réessayez si nécessaire.<br /> Consultez [TimeoutException](#timeoutexception). |Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L'opération utilisateur demandée n'est pas autorisée sur le serveur ou le service. Consultez le message de l'exception pour obtenir plus d'informations. Par exemple, le paramètre [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) génère cette exception si le message a été reçu en mode [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . |Vérifiez le code et consultez la documentation. Assurez-vous que l'opération demandée est valide. |Une nouvelle tentative ne sera pas bénéfique. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Une tentative est effectuée pour appeler une opération sur un objet qui a déjà été fermé, abandonné ou supprimé. Dans de rares cas, la transaction ambiante est déjà supprimée. |Vérifiez le code et assurez-vous qu'il n'appelle pas d'opérations sur un objet supprimé. |Une nouvelle tentative ne sera pas bénéfique. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |L'objet [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) n'a pas pu obtenir de jeton, le jeton n'est pas valide ou le jeton ne contient pas les revendications requises pour effectuer l'opération. |Assurez-vous que le fournisseur de jetons est créé avec les valeurs correctes. Vérifiez la configuration du service de contrôle d'accès (ACS). |Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Un ou plusieurs des arguments fournis à la méthode ne sont pas valides. L’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) contient un ou plusieurs segments de chemin d’accès. Le schéma d’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) n’est pas valide. La valeur de la propriété est supérieure à 32 ko. |Vérifiez le code appelant et assurez-vous que les arguments sont corrects. |Une nouvelle tentative ne sera pas bénéfique. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |L'entité associée à l'opération n'existe pas ou a été supprimée. |Assurez-vous que l'entité existe. |Une nouvelle tentative ne sera pas bénéfique. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Le client ne peut pas établir de connexion à Event Hub. |Assurez-vous que le nom d'hôte fourni est correct et que l'hôte est accessible. |Une nouvelle tentative peut aider en cas de problèmes de connectivité intermittents. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |Le service n'est pas en mesure de traiter la demande pour l'instant. |Le client peut attendre pendant une période de temps, puis recommencer l'opération. <br /> Consultez [ServerBusyException](#serverbusyexception). |Le client peut réessayer après un certain temps. Si une nouvelle tentative provoque une exception différente, vérifiez le comportement de nouvelle tentative de cette exception. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Exception de messagerie générique qui peut être levée dans les cas suivants : une tentative est effectuée pour créer un [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) à l’aide d’un nom ou d’un chemin d’accès qui appartient à un autre type d’entité (par exemple, une rubrique). Une tentative est effectuée pour envoyer un message de taille supérieure à 256 Ko. Le serveur ou le service a rencontré une erreur lors du traitement de la demande. Consultez le message de l'exception pour obtenir plus d'informations. Il s'agit généralement d'une exception temporaire. |Vérifiez le code et assurez-vous que seuls les objets sérialisables sont utilisés dans le corps du message (ou utilisez un sérialiseur personnalisé). Consultez la documentation pour connaître les types de valeurs des propriétés pris en charge et utilisez uniquement les types pris en charge. Vérifiez la propriété [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) . Si sa valeur est **true**, vous pouvez réessayer d’effectuer l’opération. |Le comportement de la nouvelle tentative n'est pas défini et peut ne pas être utile. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentative de création d'une entité dont le nom est déjà utilisé par une autre entité de l'espace de noms de ce service. |Supprimez l'entité existante ou choisissez un autre nom pour l'entité à créer. |Une nouvelle tentative ne sera pas bénéfique. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |L'entité de messagerie a atteint sa taille maximale autorisée. Cela peut se produire si le nombre maximal de destinataires (c’est-à-dire 5) a déjà été ouvert sur un niveau de regroupement par consommateur. |Créez de l'espace dans l'entité en recevant des messages à partir de l'entité ou de ses files d'attente secondaires. <br /> Consultez [QuotaExceededException](#quotaexceededexception). |Une nouvelle tentative peut aider si des messages ont été supprimés entre-temps. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Demande d'une opération d'exécution sur une entité désactivée. |Activez l'entité. |Une nouvelle tentative peut aider si l'entité a été activée entre-temps. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Une charge utile de message dépasse la limite de 256 Ko. Notez que la limite de 256 Ko correspond à la taille totale du message, qui peut inclure des propriétés système et toute surcharge .NET. |Réduisez la taille de la charge utile de message, puis recommencez l'opération. |Une nouvelle tentative ne sera pas bénéfique. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indique que le quota d’une entité spécifique a été dépassé.

Cela peut se produire si le nombre maximal de destinataires (5) a déjà été ouvert sur un niveau de regroupement par consommateur.

### <a name="event-hubs"></a>Event Hubs
Event Hubs a une limite de 20 groupes de consommateurs par Event Hub. Lorsque vous essayez d’en créer plus, vous recevez une [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
Une [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indique qu’une opération lancée par l’utilisateur dépasse le délai d’expiration de l’opération. 

Pour Event Hubs, le délai d'attente est spécifié au sein de la chaîne de connexion ou par [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Le message d'erreur peut varier, mais il contient toujours la valeur du délai d'attente spécifiée pour l'opération en cours. 

### <a name="common-causes"></a>Causes courantes
Il existe deux causes communes pour cette erreur : une configuration incorrecte ou une erreur de service temporaire.

1. **Configuration incorrecte** Le délai d’expiration de l’opération est peut-être trop court pour un bon fonctionnement. La valeur par défaut du délai d'expiration de l'opération dans le Kit de développement logiciel (SDK) client est de 60 secondes. Vérifiez si votre code définit une valeur trop petite. Notez que la condition du réseau et l'utilisation du processeur peuvent affecter le temps nécessaire à la réalisation d’une opération particulière. Par conséquent, le délai d'expiration de l'opération ne doit pas être défini sur une valeur très faible.
2. **Erreur de service temporaire** Parfois, le service Event Hubs peut rencontrer des retards de traitement des requêtes, par exemple pendant les périodes de trafic élevé. Dans ce cas, vous pouvez réessayer l'opération après un certain délai, jusqu'à ce que l'opération réussisse. Si la même opération échoue encore après plusieurs tentatives, consultez le [Site d'état des services Azure](https://azure.microsoft.com/status/) pour voir s'il existe des interruptions de service connues.

## <a name="serverbusyexception"></a>ServerBusyException

Une exception [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indique qu’un serveur est surchargé. Il existe deux codes d’erreur pertinents pour cette exception.

### <a name="error-code-50002"></a>Code d’erreur 50002

Cette erreur peut se produire pour deux raisons :

1. La charge n’est pas répartie de manière égale entre toutes les partitions de l’Event Hub et une partition atteint la limite d’unité de débit locale.
    
    Résolution : revoir la stratégie de distribution de partition ou essayer [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_).

2. L’espace de noms Event Hubs n’a pas suffisamment d’unités de débit (vous pouvez consulter l’écran **Mesures** dans la fenêtre de l’espace de noms Event Hubs du [portail Azure](https://portal.azure.com) pour vous en assurer). Notez que le portail affiche des informations agrégées (1 minute), mais que nous mesurons le débit en temps réel ; cette valeur n’est donc qu’une estimation.

    Résolution : augmenter les unités de débit sur l’espace de noms. Vous pouvez réaliser cette opération sur le portail, dans la fenêtre **Mise à l’échelle** de l’écran de l’espace de noms Event Hubs.

### <a name="error-code-50001"></a>Code d’erreur 50001

Cette erreur survient rarement. Elle se présente lorsque le conteneur exécutant le code pour votre espace de noms n’a pas suffisamment de ressources d’UC ; pas plus de quelques secondes avant que l’équilibrage de charge des Event Hubs commence.


## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d'ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Créer un Event Hub)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)
