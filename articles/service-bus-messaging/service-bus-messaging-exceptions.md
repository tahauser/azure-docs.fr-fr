---
title: Exceptions de la messagerie Azure Service Bus | Microsoft Docs
description: "Liste des exceptions de la messagerie Service Bus et les actions suggérées."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: efcfad2834c2d6775c6693f5c705a0531b2650d6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="service-bus-messaging-exceptions"></a>Exceptions de la messagerie Service Bus
Cet article répertorie certaines exceptions générées par les API de la messagerie Microsoft Azure Service Bus. Cette référence est susceptible de changer, donc consultez-la régulièrement.

## <a name="exception-categories"></a>Catégories d'exceptions
Les API de messagerie génèrent des exceptions qui peuvent être classées dans les catégories suivantes, ainsi que l'action associée que vous pouvez mener pour essayer de les résoudre. Notez que la signification et les causes d’une exception peuvent varier en fonction du type d’entité de messagerie :

1. Erreur de codage utilisateur ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Action générale : essayez de corriger le code avant de poursuivre.
2. Erreur d’installation ou de configuration ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Action générale : révisez la configuration et modifiez-la si besoin.
3. Exceptions temporaires ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Action générale : relancez l'opération ou avertissez les utilisateurs.
4. Autres exceptions ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Action générale : propre au type d’exception ; reportez-vous au tableau de la section suivante. 

## <a name="exception-types"></a>Types d'exceptions
Le tableau suivant répertorie les types d'exceptions de la messagerie, leurs causes et les propositions d'actions que vous pouvez effectuer.

| **Type d'exception** | **Description/Cause/Exemples** | **Action suggérée** | **Remarques sur la nouvelle tentative automatique/immédiate** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Le serveur n'a pas répondu à l'opération demandée dans le délai spécifié qui est contrôlé par le paramètre [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Le serveur peut avoir terminé l'opération demandée. Cela peut se produire en raison de délais sur le réseau ou autre infrastructure. |Vérifiez la cohérence de l'état du système et réessayez si nécessaire. Consultez [Exceptions au délai d’expiration](#timeoutexception). |Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L'opération utilisateur demandée n'est pas autorisée sur le serveur ou le service. Consultez le message de l'exception pour obtenir plus d'informations. Par exemple, le paramètre [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) génère cette exception si le message a été reçu en mode [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode). |Vérifiez le code et consultez la documentation. Assurez-vous que l'opération demandée est valide. |Une nouvelle tentative ne sera pas bénéfique. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Une tentative est effectuée pour appeler une opération sur un objet qui a déjà été fermé, abandonné ou supprimé. Dans de rares cas, la transaction ambiante est déjà supprimée. |Vérifiez le code et assurez-vous qu'il n'appelle pas d'opérations sur un objet supprimé. |Une nouvelle tentative ne sera pas bénéfique. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |L'objet [TokenProvider](/dotnet/api/microsoft.azure.servicebus.tokenprovider) n'a pas pu obtenir de jeton, le jeton n'est pas valide ou le jeton ne contient pas les revendications requises pour effectuer l'opération. |Assurez-vous que le fournisseur de jetons est créé avec les valeurs correctes. Vérifiez la configuration du service de contrôle d'accès (ACS). |Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Un ou plusieurs des arguments fournis à la méthode ne sont pas valides.<br /> L’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) contient un ou plusieurs segments de chemin d’accès.<br /> Le schéma d’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) n’est pas valide. <br />La valeur de la propriété est supérieure à 32 ko. |Vérifiez le code appelant et assurez-vous que les arguments sont corrects. |Une nouvelle tentative ne sera pas bénéfique. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |L'entité associée à l'opération n'existe pas ou a été supprimée. |Assurez-vous que l'entité existe. |Une nouvelle tentative ne sera pas bénéfique. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentative de réception d'un message avec un numéro de séquence spécifique. Ce message est introuvable. |Assurez-vous que le message n'a pas déjà été reçu. Vérifiez la file d'attente de lettres mortes pour voir si le message a été désactivé. |Une nouvelle tentative ne sera pas bénéfique. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Le client ne peut pas établir de connexion à Service Bus. |Assurez-vous que le nom d'hôte fourni est correct et que l'hôte est accessible. |Une nouvelle tentative peut aider en cas de problèmes de connectivité intermittents. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Le service n'est pas en mesure de traiter la demande pour l'instant. |Le client peut attendre pendant une période de temps, puis recommencer l'opération. |Le client peut réessayer après un certain temps. Si une nouvelle tentative provoque une exception différente, vérifiez le comportement de nouvelle tentative de cette exception. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Le jeton de verrouillage associé au message a expiré ou le jeton de verrouillage est introuvable. |Supprimez le message. |Une nouvelle tentative ne sera pas bénéfique. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Le verrou associé à cette session est perdu. |Abandonnez l’objet [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Une nouvelle tentative ne sera pas bénéfique. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Exception de messagerie générique qui peut être levée dans les cas suivants :<br /> une tentative est effectuée pour créer un [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) à l’aide d’un nom ou d’un chemin d’accès qui appartient à un autre type d’entité (par exemple, une rubrique).<br />  Une tentative est effectuée pour envoyer un message de taille supérieure à 256 Ko. Le serveur ou le service a rencontré une erreur lors du traitement de la demande. Consultez le message de l'exception pour obtenir plus d'informations. Il s'agit généralement d'une exception temporaire. |Vérifiez le code et assurez-vous que seuls les objets sérialisables sont utilisés dans le corps du message (ou utilisez un sérialiseur personnalisé). Consultez la documentation pour connaître les types de valeurs des propriétés pris en charge et utilisez uniquement les types pris en charge. Vérifiez la propriété [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) . Si sa valeur est **true**, vous pouvez réessayer d’effectuer l’opération. |Le comportement de la nouvelle tentative n'est pas défini et peut ne pas être utile. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentative de création d'une entité dont le nom est déjà utilisé par une autre entité de l'espace de noms de ce service. |Supprimez l'entité existante ou choisissez un autre nom pour l'entité à créer. |Une nouvelle tentative ne sera pas bénéfique. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |L’entité de messagerie a atteint sa taille maximale autorisée, ou le nombre maximal de connexions à un espace de noms a été dépassé. |Créez de l'espace dans l'entité en recevant des messages à partir de l'entité ou de ses files d'attente secondaires. Consultez [QuotaExceededException](#quotaexceededexception). |Une nouvelle tentative peut aider si des messages ont été supprimés entre-temps. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus renvoie cette exception si vous essayez de créer une action de règle non valide. Service Bus associe cette exception à un message désactivé si une erreur se produit lors du traitement de l'action de règle pour ce message. |Vérifiez que l'action de règle est correcte. |Une nouvelle tentative ne sera pas bénéfique. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus renvoie cette exception si vous essayez de créer un filtre non valide. Service Bus associe cette exception à un message désactivé si une erreur se produit lors du traitement du filtre pour ce message. |Vérifiez l'exactitude du filtre. |Une nouvelle tentative ne sera pas bénéfique. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tentative d'acceptation d'une session avec un ID de session spécifique, mais la session est actuellement verrouillée par un autre client. |Assurez-vous que la session est déverrouillée par les autres clients. |Une nouvelle tentative peut aider si la session a été libérée entre-temps. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Trop d'opérations font partie de la transaction. |Réduisez le nombre d'opérations qui font partie de cette transaction. |Une nouvelle tentative ne sera pas bénéfique. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Demande d'une opération d'exécution sur une entité désactivée. |Activez l'entité. |Une nouvelle tentative peut aider si l'entité a été activée entre-temps. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus renvoie cette exception si vous envoyez un message à une rubrique avec le filtre préalable activé et si aucun des filtres ne correspond. |Assurez-vous qu'au moins un filtre correspond. |Une nouvelle tentative ne sera pas bénéfique. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Une charge utile de message dépasse la limite de 256 Ko. Notez que la limite de 256 Ko correspond à la taille totale du message, qui peut inclure des propriétés système et toute surcharge .NET. |Réduisez la taille de la charge utile de message, puis recommencez l'opération. |Une nouvelle tentative ne sera pas bénéfique. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |La transaction ambiante (*Transaction.Current*) n'est pas valide. Elle a peut-être été terminée ou annulée. Une exception en interne peut fournir des informations supplémentaires. | |Une nouvelle tentative ne sera pas bénéfique. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Une opération est tentée sur une transaction incertaine, ou une tentative est faite pour valider la transaction et la transaction devient incertaine. |Votre application doit gérer cette exception (comme un cas spécial), car la transaction a peut-être déjà été validée. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indique que le quota d’une entité spécifique a été dépassé.

### <a name="queues-and-topics"></a>Files d’attente et rubriques
Pour les files d’attente et les rubriques, il s’agit souvent de la taille de la file d’attente. La propriété du message d’erreur contient davantage d’informations, comme dans l’exemple suivant :

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Ce message indique que la rubrique a dépassé sa limite de taille, dans ce cas 1 Go (limite de taille par défaut). 

### <a name="namespaces"></a>Espaces de noms

En ce qui concerne les espaces de noms, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) peut indiquer qu’une application a dépassé le nombre maximal de connexions à un espace de noms. Par exemple : 

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Causes courantes
Il existe deux causes courantes pour cette erreur : la file d'attente de lettres mortes et des récepteurs de messages non fonctionnels.

1. **[File d’attente de lettres mortes](service-bus-dead-letter-queues.md)** Un lecteur ne parvient pas à terminer les messages et ceux-ci sont renvoyés à la file d’attente/rubrique après expiration du verrouillage. Cela peut se produire si le lecteur rencontre une exception qui l’empêche d’appeler [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Une fois un message lu 10 fois, il passe à la file d'attente de lettres mortes par défaut. Ce comportement est contrôlé par la propriété [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) et a une valeur par défaut de 10. Quand les messages s'accumulent dans la file d'attente de lettres mortes, ils prennent de la place.
   
    Pour résoudre ce problème, lisez et terminez les messages de la file d'attente de lettres mortes, comme vous le feriez pour une autre file d'attente. Vous pouvez utiliser la méthode [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) pour aider à mettre en forme le chemin d’accès de la file d’attente de lettres mortes.
2. **Récepteur arrêté** Un récepteur a arrêté de recevoir des messages d’une file d’attente ou d’un abonnement. Pour l’identifier, examinez la propriété [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , qui affiche la répartition complète des messages. Si la propriété [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) est élevée ou croissante, les messages ne sont pas lus aussi rapidement qu’ils sont écrits.

## <a name="timeoutexception"></a>TimeoutException
Une [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indique qu’une opération lancée par l’utilisateur dépasse le délai d’expiration de l’opération. 

Vous devez vérifier la valeur de la propriété [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) car cette limite, si elle est atteinte, peut également entraîner une exception [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Files d’attente et rubriques
Pour les files d’attente et les rubriques, le délai d’attente est spécifié dans la propriété [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout), au sein de la chaîne de connexion ou par le biais de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Le message d'erreur peut varier, mais il contient toujours la valeur du délai d'attente spécifiée pour l'opération en cours. 



## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations complètes sur l’API .NET Service Bus, consultez les [informations de référence sur l’API .NET Azure](/dotnet/api/overview/azure/service-bus).

Pour en savoir plus sur [Service Bus](https://azure.microsoft.com/services/service-bus/), lisez les articles suivants :

* [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Architecture de Service Bus](service-bus-architecture.md)

