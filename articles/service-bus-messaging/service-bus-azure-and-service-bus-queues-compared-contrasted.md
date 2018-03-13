---
title: "Comparaison des files d’attente Azure et des files d’attente Service Bus | Microsoft Docs"
description: "Analyse les différences et les similitudes entre les deux types de files d'attente proposés par Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/08/2017
ms.author: sethm
ms.openlocfilehash: d564f3974b2bc6355bb5dc5320a5193fe3c196af
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Files d’attente Azure et files d’attente Service Bus : comparaison et différences
Cet article analyse les différences et les ressemblances entre les deux types de file d’attente proposés aujourd’hui par Microsoft Azure : les files d’attente Azure et les files d’attente Service Bus. À l'aide de ces informations, vous pouvez comparer les technologies respectives et être en mesure de prendre une décision éclairée concernant la solution adaptée à vos besoins.

## <a name="introduction"></a>Introduction
Azure prend en charge deux types de mécanismes de file d’attente : les **files d’attente de stockage** et les **files d’attente Service Bus**.

Les **files d’attente de stockage**, qui font partie de l’infrastructure de [stockage Azure](https://azure.microsoft.com/services/storage/), incluent une simple interface GET/PUT/PEEK basée sur REST, qui fournit une messagerie fiable et persistante au sein des services et entre ces derniers.

Les **files d’attente Service Bus** font partie d’une infrastructure de [messagerie Azure](https://azure.microsoft.com/services/service-bus/) plus large qui prend en charge la mise en file d’attente, ainsi que la publication/l’abonnement, l’accès distant au service Web et les modèles d’intégration. Pour plus d’informations sur les files d’attente, les rubriques et les abonnements Service Bus, consultez [Vue d’ensemble de Service Bus](service-bus-messaging-overview.md).

Bien que les deux technologies de file d’attente coexistent, les files d’attente Azure sont apparues en premier, en tant que mécanisme de stockage en file d’attente dédié, basé sur les services de stockage Azure. Les files d’attente Service Bus font partie d’une infrastructure de messagerie plus large conçue pour intégrer des applications ou des composants d’applications qui peuvent s’étendre sur plusieurs protocoles de communication, contrats de données, domaines de confiance et/ou environnements réseau.

## <a name="technology-selection-considerations"></a>Considérations relatives à la sélection de la technologie
Les files d’attente Azure et Service Bus sont des implémentations du service de mise en file d’attente des messages, actuellement proposé par Microsoft Azure. Chaque technologie possède un ensemble de fonctionnalités légèrement différent, ce qui signifie que vous pouvez choisir l'une ou l'autre, ou utiliser les deux, selon les besoins de votre solution spécifique ou du problème d'entreprise/technique à résoudre.

Pour déterminer quelle technologie de file d’attente est adaptée à une solution donnée, les développeurs et les architectes de solutions doivent prendre en compte ces recommandations. Pour plus d'informations, consultez la section suivante.

En tant que développeur/architecte de solutions, **vous devez envisager d’utiliser les files d’attente Azure** dans les cas de figure suivants :

* Votre application doit stocker plus de 80 Go de messages dans une file d'attente, où les messages ont une durée de vie inférieure à 7 jours.
* Votre application veut suivre la progression du traitement d'un message dans la file d'attente. Cela est utile si le processus traitant un message se bloque. Un processus suivant peut alors utiliser ces informations pour continuer là où le processus précédent s'était arrêté.
* Vous avez besoin de journaux côté serveur de toutes les transactions exécutées sur les files d'attente.

En tant que développeur/architecte de solutions, **vous devez envisager d’utiliser les files d’attente Service Bus** lorsque :

* Votre solution doit être en mesure de recevoir des messages sans devoir interroger la file d'attente. Avec Service Bus, ceci peut être réalisé avec l'utilisation de l'opération de réception à interrogation longue à l'aide des protocoles TCP pris en charge par Service Bus.
* Votre solution nécessite la file d'attente pour fournir une livraison organisée selon la méthode Premier entré, premier sortie.
* Vous souhaitez créer une expérience symétrique dans Azure et Windows Server (cloud privé). Pour plus d’informations, consultez [Service Bus pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).
* Votre solution doit pouvoir prendre en charge la détection automatique des doublons.
* Vous voulez que votre application traite les messages sous forme de flux de longue durée parallèles (les messages sont associés à un flux à l’aide de la propriété [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) du message). Dans ce modèle, chaque nœud de l'application consommatrice entre en concurrence pour les flux, contrairement aux messages. Lorsqu'un flux est donné à un nœud consommateur, le nœud peut examiner l'état du flux de l'application à l'aide de transactions.
* Votre solution nécessite un comportement transactionnel et l'atomicité lors de l'envoi ou de la réception de plusieurs messages à partir d'une file d'attente.
* La caractéristique de durée de vie (TTL) de la charge de travail spécifique à l'application peut dépasser la période de 7 jours.
* Votre application gère des messages qui peuvent dépasser 64 Ko, mais qui n'atteindront sans doute pas la limite de 256 Ko.
* Vous êtes confronté à l'exigence de fournir un modèle d'accès basé sur les rôles aux files d'attente et des droits/autorisations différents pour les expéditeurs et les destinataires.
* La taille de la file d'attente ne sera pas supérieure à 80 Go.
* Vous souhaitez utiliser le protocole de messagerie basé sur les normes AMQP 1.0. Pour plus d’informations sur AMQP, consultez [Présentation d’AMQP Service Bus](service-bus-amqp-overview.md).
* Vous pouvez prévoir une migration éventuelle de la communication point à point basée sur la file d'attente vers un modèle d'échange de messages qui permet une intégration transparente de récepteurs (abonnés) supplémentaires, chacun d'entre eux recevant des copies indépendantes de certains ou de tous les messages envoyés à la file d'attente. Ce dernier point fait référence à la fonctionnalité publication/abonnement en mode natif fournie par Service Bus.
* Votre solution de messagerie doit être en mesure de prendre en charge la garantie de remise « Une fois au maximum » sans que vous ayez besoin de créer des composants d’infrastructure supplémentaires.
* Vous souhaitez être en mesure de publier et d'utiliser des lots de messages.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Comparaison des files d’attente Azure et des files d’attente Service Bus
Dans les sections suivantes, les tableaux regroupent logiquement les fonctionnalités de file d’attente et vous permettent de comparer, en un clin d’œil, les fonctionnalités disponibles dans les files d’attente du stockage Azure et les files d’attente Service Bus.

## <a name="foundational-capabilities"></a>Fonctions de base
Cette section compare certaines des fonctionnalités de base fournies par les files d’attente Azure et les files d’attente Service Bus.

| Critères de comparaison | Files d’attente de stockage | Files d'attente Service Bus |
| --- | --- | --- |
| Garantie de classement |**Non** <br/><br>Pour plus d’informations, consultez la première remarque dans la section « Informations supplémentaires ».</br> |**Oui - Premier entré premier sorti (PEPS)**<br/><br>(par le biais de l’utilisation de sessions de messagerie) |
| Garantie de livraison |**Au moins une fois** |**Au moins une fois**<br/><br/>**Une fois au maximum** |
| Prise en charge des opérations atomiques |**Non** |**Oui**<br/><br/> |
| Comportement de réception |**Non bloquant**<br/><br/>(se termine immédiatement si aucun nouveau message n’est trouvé) |**Blocage avec ou sans délai d’expiration**<br/><br/>(offre une interrogation longue, dite [« technique Comet »](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Non bloquant**<br/><br/>(via l’utilisation d’une API gérée sur .NET uniquement) |
| API style Push |**Non** |**Oui**<br/><br/>API .NET [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) et sessions **OnMessage**. |
| Mode de réception |**Aperçu et attribution** |**Aperçu et verrouillage**<br/><br/>**Réception et suppression** |
| Mode d'accès exclusif |**Basé sur attribution** |**Basé sur verrouillage** |
| Durée attribution/verrouillage |**30 secondes (par défaut)**<br/><br/>**7 jours (maximum)** (Vous pouvez renouveler ou libérer l’attribution d’un message à l’aide de l’API [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage).) |**60 secondes (par défaut)**<br/><br/>Vous pouvez renouveler le verrouillage d’un message à l’aide de l’API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock). |
| Précision attribution/verrouillage |**Au niveau du message**<br/><br/>(chaque message peut avoir une valeur de délai d’attente différente, que vous pouvez ensuite mettre à jour en fonction des besoins lors du traitement du message, à l’aide de l’API [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage)) |**Au niveau de la file d’attente**<br/><br/>(pour chaque file d’attente, une précision de verrouillage est appliquée à tous les messages, mais vous pouvez renouveler le verrouillage à l’aide de l’API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock).) |
| Réception par lots |**Oui**<br/><br/>(en spécifiant explicitement le nombre de messages lors de la récupération des messages, avec un maximum de 32 messages) |**Oui**<br/><br/>(en activant implicitement une propriété de pré-récupération ou explicitement par le biais de l’utilisation de transactions) |
| Envoi par lots |**Non** |**Oui**<br/><br/>(par le biais de l’utilisation de transactions ou du traitement par lots côté client) |

### <a name="additional-information"></a>Informations supplémentaires
* Les messages dans les files d’attente de stockage se voient en général appliquer la méthode Premier entré, premier sorti. Mais ils peuvent parfois être dans le désordre. C’est le cas lorsque le délai de visibilité d’un message expire (par exemple, à cause du blocage d’une application cliente pendant le traitement). Lorsque le délai de visibilité expire, le message est de nouveau visible dans la file d'attente et un autre processus peut le retirer de la file d'attente. À ce stade, le message nouvellement visible peut être placé dans la file d'attente (pour en être de nouveau retiré) après un message qui se trouvait à l'origine dans la file d'attente après lui.
* Le modèle Premier entré, premier sorti garanti dans les files d'attente Service Bus requiert l'utilisation de sessions de messagerie. Dans le cas où l’application se bloque lors du traitement d’un message reçu en mode **Aperçu et verrouillage**, la prochaine fois qu’un destinataire de file d’attente acceptera une session de messagerie, celle-ci démarrera avec le message ayant échoué après que sa durée de vie ait expiré.
* Les files d’attente de stockage sont conçues pour prendre en charge des scénarios de mise en file d’attente standard, tels que le découplage de composants d’application pour augmenter l’évolutivité et la tolérance aux pannes, le nivellement de charge et la création des workflows de processus.
* Les files d’attente Service Bus prennent en charge la garantie de livraison *Au moins une fois*. En outre, la sémantique *Une fois au maximum* peut être prise en charge à l’aide de l’état de session pour stocker l’état de l’application et à l’aide de transactions pour recevoir atomiquement des messages et mettre à jour l’état de la session.
* Les files d’attente de stockage fournissent un modèle de programmation uniforme et cohérent entre les files d’attente, les tables et les objets blob, pour les développeurs et les équipes d’exploitation.
* Les files d'attente Service Bus prennent en charge les transactions locales dans le contexte d'une file d'attente unique.
* Le mode **Réception et suppression** pris en charge par Service Bus offre la possibilité de réduire le nombre d’opérations de messagerie (et le coût associé) en échange d’une garantie de livraison réduite.
* Les files d’attente de stockage fournissent des attributions avec la possibilité de les étendre aux messages. Ainsi, les processus peuvent maintenir des attributions de courte durée pour les messages. Par conséquent, si un processus se bloque, le message peut être rapidement traité à nouveau par un autre processus. En outre, un processus peut étendre l'attribution d'un message s'il doit le traiter pendant une période plus longue que la durée de l'attribution.
* Les files d’attente de stockage offrent un délai de visibilité que vous pouvez définir au moment de la mise en file d’attente ou du retrait de la file d’attente d’un message. En outre, vous pouvez mettre à jour un message avec des valeurs d’attribution différentes lors de l’exécution et mettre à jour des valeurs différentes entre des messages dans la même file d’attente. Les délais de verrouillage Service Bus sont définis dans les métadonnées de la file d’attente. Toutefois, vous pouvez renouveler le verrouillage en appelant la méthode [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock).
* Le délai d'attente maximal pour une opération de réception de blocage dans les files d'attente Service Bus est de 24 jours. Toutefois, les délais d'attente basés sur REST ont une valeur maximale de 55 secondes.
* Le traitement par lots côté client fourni par Service Bus permet à un client de file d'attente de traiter par lots plusieurs messages dans une seule opération d'envoi. Le traitement par lots n'est disponible que pour les opérations d'envoi asynchrones.
* Certaines fonctionnalités telles que la limite de 200 To pour les files d’attente de stockage (davantage lorsque vous virtualisez des comptes) et un nombre illimité de files d’attente en font une plateforme idéale pour les fournisseurs SaaS.
* Les files d’attente de stockage fournissent un mécanisme performant et flexible de contrôle d’accès délégué.

## <a name="advanced-capabilities"></a>Fonctionnalités avancées
Cette section compare les fonctionnalités avancées des files d’attente Azure et des files d’attente Service Bus.

| Critères de comparaison | Files d’attente de stockage | Files d'attente Service Bus |
| --- | --- | --- |
| Remise planifiée |**Oui** |**Oui** |
| Lettre morte automatique |**Non** |**Oui** |
| Augmenter la valeur de durée de vie de la file d'attente |**Oui**<br/><br/>(par le biais de la mise à jour sur place du délai de visibilité) |**Oui**<br/><br/>(par le biais d’une fonction API dédiée) |
| Prise en charge des messages incohérents |**Oui** |**Oui** |
| Mise à jour sur place |**Oui** |**Oui** |
| Journal des transactions côté serveur |**Oui** |**Non** |
| Métriques de stockage |**Oui**<br/><br/>**Métriques par minute** : fournit des métriques en temps réel pour la disponibilité, le TPS, le nombre d’appels API, le nombre d’erreurs, etc., le tout en temps réel (métriques agrégées par minute et consignées en l’espace de quelques minutes à partir de ce qui vient de se passer en production). Pour plus d’informations, voir la page [À propos des mesures Storage Analytics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Oui**<br/><br/>(requêtes en bloc en appelant [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Gestion de l'état |**Non** |**Oui**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Transfert automatique des messages |**Non** |**Oui** |
| Fonction de purge de la file d'attente |**Oui** |**Non** |
| Groupes de messages |**Non** |**Oui**<br/><br/>(par le biais de l’utilisation de sessions de messagerie) |
| État de l'application par groupe de messages |**Non** |**Oui** |
| Détection des doublons |**Non** |**Oui**<br/><br/>(configurable du côté expéditeur) |
| Consultation des groupes de messages |**Non** |**Oui** |
| Récupération des sessions de messagerie par ID |**Non** |**Oui** |

### <a name="additional-information"></a>Informations supplémentaires
* Les deux technologies de mise en file d'attente permettent de planifier la remise d'un message à une date ultérieure.
* Le transfert automatique de file d'attente permet à des milliers de files d'attente de transférer automatiquement leurs messages à une seule file d'attente, à partir de laquelle l'application réceptrice consomme le message. Vous pouvez utiliser ce mécanisme pour garantir la sécurité, contrôler le flux et isoler le stockage entre chaque serveur de publication de message.
* Les files d’attente de stockage prennent en charge la mise à jour du contenu des messages. Vous pouvez utiliser cette fonctionnalité pour les informations d'état persistantes et les mises à jour incrémentielles de progression dans le message, afin que celui-ci puisse être traité à partir du dernier point de contrôle connu, au lieu de recommencer depuis le début. Avec les files d'attente Service Bus, vous pouvez activer le même scénario à l'aide des sessions de messagerie. Les sessions vous permettent d’enregistrer et de récupérer l’état de traitement de l’application (à l’aide de [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) et [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* Le concept de [Lettre morte](service-bus-dead-letter-queues.md), qui est uniquement pris en charge par les files d’attente Service Bus, peut être utile pour isoler les messages qui ne peuvent pas être traités correctement par l’application réceptrice ou lorsque les messages n’atteignent pas leur destination en raison d’une propriété de durée de vie (TTL) expirée. La valeur de durée de vie spécifie la durée pendant laquelle un message reste dans la file d'attente. Avec Service Bus, le message est déplacé vers une file d'attente spéciale appelée $DeadLetterQueue lorsque la durée de vie expire.
* Pour trouver les messages « incohérents » dans les files d’attente Azure, lors du retrait d’un message de la file d’attente, l’application examine la propriété [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) du message. Si la propriété **DequeueCount** dépasse un seuil donné, l’application déplace le message vers une file d’attente de type « lettre morte » définie par l’application.
* Les files d’attente de stockage vous permettent d’obtenir un journal détaillé de toutes les transactions exécutées sur la file d’attente, ainsi que des mesures agrégées. Ces deux options sont utiles pour déboguer et comprendre comment votre application utilise les files d’attente de stockage. Elles sont également utiles pour régler les performances de votre application et réduire les coûts d'utilisation des files d'attente.
* Le concept de « sessions de messagerie » pris en charge par Service Bus permet aux messages appartenant à un certain groupe logique d’être associés à un destinataire spécifique, ce qui ensuite crée une affinité de type session entre les messages et leurs récepteurs respectifs. Vous pouvez activer cette fonctionnalité avancée dans Service Bus en définissant la propriété [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) d’un message. Les récepteurs peuvent ensuite écouter par le biais d'un ID de session spécifique et recevoir les messages qui partagent l'identificateur de session spécifié.
* La fonctionnalité de détection des doublons prise en charge par les files d’attente Service Bus supprime automatiquement les messages en double envoyés à une file d’attente ou une rubrique, selon la valeur de la propriété [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId).

## <a name="capacity-and-quotas"></a>Capacité et quotas
Cette section compare les files d’attente de stockage et les files d’attente Service Bus du point de vue [des capacités et des quotas](service-bus-quotas.md) applicables.

| Critères de comparaison | Files d’attente de stockage | Files d'attente Service Bus |
| --- | --- | --- |
| Taille de file d'attente maximale |**500 To**<br/><br/>(limitée à une [capacité de compte de stockage unique](../storage/common/storage-introduction.md#queue-storage)) |**1 Go à 80 Go**<br/><br/>(définie lors de la création d’une file d’attente et d’une [activation du partitionnement](service-bus-partitioning.md) – consultez la section « Informations supplémentaires ») |
| Taille de message maximale |**64 Ko**<br/><br/>(48 Ko avec un codage en **Base64**)<br/><br/>Azure prend en charge les messages volumineux en combinant des files d’attente et des objets blob. Dans ce cas, vous pouvez placer jusqu’à 200 Go en file d’attente pour un seul élément. |**256 Ko** ou **1 Mo**<br/><br/>(y compris l’en-tête et le corps, taille maximale d’en-tête : 64 Ko).<br/><br/>Dépend du [niveau de service](service-bus-premium-messaging.md). |
| Durée de vie maximale des messages |**7 jours** |**TimeSpan.Max** |
| Nombre maximal de files d'attente |**Illimité** |**10,000**<br/><br/>(par espace de noms de service) |
| Nombre maximal de clients simultanés |**Illimité** |**Illimité**<br/><br/>(la limite de 100 connexions simultanées s’applique uniquement à la communication basée sur le protocole TCP) |

### <a name="additional-information"></a>Informations supplémentaires
* Service Bus applique les limites en termes de taille de file d'attente. La taille de file d'attente maximale est spécifiée lors de la création de la file d'attente et peut avoir une valeur comprise entre 1 Go et 80 Go. Si la valeur de taille de la file d'attente définie lors de la création de celle-ci est atteinte, les messages entrants supplémentaires seront rejetés et une exception sera reçue par le code appelant. Pour plus d’informations sur les quotas dans Service Bus, consultez [Quotas Service Bus](service-bus-quotas.md).
* Dans le [niveau Standard](service-bus-premium-messaging.md), vous pouvez créer des files d'attente Service Bus avec des tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est 1 Go). Dans le niveau Premium, vous pouvez créer des files d’attente pouvant atteindre 80 Go. Dans le niveau Standard, avec le partitionnement activé (qui est la valeur par défaut), Service Bus crée 16 partitions pour chaque Go que vous spécifiez. Par conséquent, si vous créez une file d’attente de 5 Go, avec 16 partitions la taille maximale de la file d’attente est (5 * 16) = 80 Go. Vous pouvez voir la taille maximale de votre file d’attente ou rubrique partitionnée en examinant son entrée sur le [portail Azure][Azure portal]. Dans le niveau Premium, seules 2 partitions sont créées par file d’attente.
* Avec les files d’attente de stockage, si le contenu du message n’est pas sécurisé pour XML, il doit être encodé au format **Base64**. Si vous encodez le message au format **Base64**, la charge utilisateur peut atteindre 48 Ko, au lieu de 64 Ko.
* Avec les files d’attente Service Bus, chaque message stocké dans une file d’attente est composé de deux parties : un en-tête et un corps. La taille totale du message ne peut pas dépasser la taille de message maximale prise en charge par le niveau de service.
* Lorsque des clients communiquent avec des files d'attente Service Bus au moyen du protocole TCP, le nombre maximal de connexions simultanées à une file d'attente Service Bus unique est limité à 100. Ce nombre est partagé entre les expéditeurs et les destinataires. Si ce quota est atteint, les requêtes suivantes pour des connexions supplémentaires sont rejetées et une exception sera reçue par le code appelant. Cette limite n'est pas appliquée aux clients qui se connectent aux files d'attente à l'aide d'une API REST.
* Si vous avez besoin de plus de 10 000 files d'attente dans un seul espace de noms Service Bus, vous pouvez contacter l'équipe de support Azure et demander l'augmentation du nombre de files d'attente. Pour aller au-delà de 10 000 files d’attente avec Service Bus, vous pouvez également créer des espaces de noms supplémentaires à l’aide du [portail Azure][Azure portal].

## <a name="management-and-operations"></a>Gestion et opérations
Cette section compare les fonctionnalités de gestion fournies par les files d’attente de stockage et les files d’attente Service Bus.

| Critères de comparaison | Files d’attente de stockage | Files d’attente Service Bus |
| --- | --- | --- |
| Protocole de gestion |**REST sur HTTP/HTTPS** |**REST sur HTTPS** |
| Protocole d'exécution |**REST sur HTTP/HTTPS** |**REST sur HTTPS**<br/><br/>**Norme AMQP 1.0 (TCP avec TLS)** |
| API .NET |**Oui**<br/><br/>(API de client de stockage .NET) |**Oui**<br/><br/>(API Service Bus .NET) |
| C++ natif |**Oui** |**Oui** |
| API Java |**Oui** |**Oui** |
| API PHP |**Oui** |**Oui** |
| API Node.js |**Oui** |**Oui** |
| Prise en charge des métadonnées arbitraires |**Oui** |**Non** |
| Règles d'affectation des noms aux files d'attente |**Jusqu’à 63 caractères**<br/><br/>(les lettres dans un nom de file d’attente doivent être en minuscules) |**Jusqu’à 260 caractères**<br/><br/>(les chemins d’accès et noms des files d’attente ne sont pas sensibles à la casse) |
| Fonction d'obtention de la longueur de la file d'attente |**Oui**<br/><br/>(valeur approximative si les messages expirent au-delà de la durée de vie sans être supprimés) |**Oui**<br/><br/>(valeur exacte, à un moment donné) |
| Fonction Peek (aperçu) |**Oui** |**Oui** |

### <a name="additional-information"></a>Informations supplémentaires
* Les files d’attente de stockage prennent en charge les attributs arbitraires qui peuvent s’appliquer à la description de la file d’attente, sous la forme de paires nom/valeur.
* Les deux technologies de file d'attente offrent la possibilité d'obtenir un aperçu d'un message sans avoir à le verrouiller, ce qui peut être utile lors de la mise en œuvre d'un outil de type explorateur/navigateur de file d'attente.
* Les API de messagerie répartie .NET de Service Bus exploitent les connexions TCP en duplex intégral pour améliorer les performances par rapport au protocole REST sur HTTP, et elles prennent en charge le protocole standard AMQP 1.0.
* Les noms des files d’attente de stockage peuvent compter entre 3 et 63 caractères, et contenir des lettres minuscules, des nombres et des traits d’union. Pour plus d’informations, consultez [Affectation de noms pour les files d’attente et les métadonnées](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Les noms des files d'attente Service Bus peuvent compter jusqu'à 260 caractères. Les règles d'affectation de noms sont moins restrictives. Les noms des files d’attente Service Bus peuvent contenir des lettres, des nombres, des points (.), des traits d’union (-) et des traits de soulignement (_).

## <a name="authentication-and-authorization"></a>Authentification et autorisation
Cette section décrit les fonctionnalités d’authentification et d’autorisation prises en charge par les files d’attente Service Bus et les files d’attente de stockage.

| Critères de comparaison | Files d’attente de stockage | Files d'attente Service Bus |
| --- | --- | --- |
| Authentification |**Clé symétrique** |**Clé symétrique** |
| Modèle de sécurité |Accès délégué via des jetons SAS. |SAS |
| Fédération de fournisseur d’identité |**Non** |**Oui** |

### <a name="additional-information"></a>Informations supplémentaires
* Chaque requête à l'une des technologies de file d'attente doit être authentifiée. Les files d'attente publiques avec accès anonyme ne sont pas prises en charge. À l’aide de la [SAP](service-bus-sas.md), vous pouvez résoudre ce scénario en publiant une SAP en écriture seule, une SAP en lecture seule ou une SAP à accès total.
* Le schéma d’authentification fourni par les files d’attente de stockage implique l’utilisation d’une clé symétrique, qui est un code d’authentification de message basé sur le hachage (HMAC), calculée avec l’algorithme SHA-256 et encodée comme une chaîne **Base64**. Pour plus d’informations sur le protocole respectif, consultez [Authentification pour les services Azure Storage](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Les files d'attente Service Bus prennent en charge un modèle similaire utilisant des clés symétriques. Pour plus d’informations, consultez [Authentification par signature d’accès partagé avec Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Conclusion
En ayant une meilleure compréhension des deux technologies, vous serez en mesure de prendre une décision plus informée sur les technologies de file d'attente à utiliser et quand. La décision d’utiliser des files d’attente de stockage ou des files d’attente Service Bus dépend de plusieurs facteurs. Ces facteurs peuvent reposer largement sur les besoins spécifiques de votre application et de son architecture. Si votre application utilise déjà les principales fonctionnalités de Microsoft Azure, il peut être intéressant de choisir les files d’attente de stockage, surtout si vous avez besoin d’une communication et d’une messagerie de base entre les services ou si vous avez besoin de files d’attente dont la taille peut être supérieure à 80 Go.

Étant donné que les files d'attente Service Bus fournissent plusieurs fonctionnalités avancées, comme les sessions, les transactions, la détection de doublons, la lettre morte automatique et des fonctions de publication/d'abonnement durables, elles peuvent constituer un meilleur choix si vous créez une application hybride ou si votre application nécessite ces fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes
Les articles suivants fournissent des conseils et des informations sur l’utilisation des files d’attente de stockage ou Service Bus.

* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation du service de stockage de files d’attente](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Meilleures pratiques relatives aux améliorations de performances à l’aide de la messagerie répartie Service Bus](service-bus-performance-improvements.md)
* [Présentation des files d’attente et des rubriques dans Azure Service Bus (blog)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Guide du développeur pour Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Utilisation du service de mise en file d’attente dans Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

