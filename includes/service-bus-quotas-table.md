---
title: Fichier Include
description: Fichier Include
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/12/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 74732008b336dc1b95ec96e8550d218105973ca4
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
Le tableau suivant répertorie les informations de quota propres à la messagerie Service Bus. Pour plus d’informations sur la tarification et d’autres quotas pour Service Bus, voir la présentation [Tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) .

| Nom du quota | Étendue | Notes | Valeur |
| --- | --- | --- | --- | --- |
| Nombre maximal d’espaces de noms de base/standard par abonnement Azure |Espace de noms |Les demandes suivantes d’espaces de noms de base/standard supplémentaires sont rejetées par le portail. |100|
| Nombre maximal d’espaces de noms premium par abonnement Azure |Espace de noms |Les demandes suivantes d’espaces de noms premium supplémentaires sont rejetées par le portail. |10 |
| Taille de la file d’attente/rubrique |Entité |Définie lors de la création de la file d’attente/rubrique. <br/><br/> Les messages entrants suivants sont rejetés et le code appelant reçoit une exception. |1, 2, 3, 4 ou 5 GB.<br /><br />Si le [partitionnement](../articles/service-bus-messaging/service-bus-partitioning.md) est activé, la taille maximale de la file d’attente/rubrique est de 80 Go. |
| Nombre de connexions simultanées sur un espace de noms |Espace de noms |Les demandes suivantes de connexions supplémentaires sont rejetées et le code appelant reçoit une exception. Les opérations REST ne sont pas comptées parmi les connexions TCP simultanées. |NetMessaging : 1 000<br /><br />AMQP : 5 000 |
| Nombre de demandes de réception simultanées sur une entité de file d’attente/rubrique/abonnement |Entité |Les demandes de réception suivantes sont rejetées et le code appelant reçoit une exception. Ce quota s’applique au nombre combiné d’opérations de réception simultanées sur tous les abonnements à une rubrique. |5 000 |
| Nombre de rubriques/files d’attente par espace de noms de service |Espace de noms |Les requêtes suivantes de création de rubrique ou de file d’attente dans l’espace de noms de service sont rejetées. Par conséquent, en cas de configuration via le [portail Azure][Azure portal], un message d’erreur est généré. Si elle est appelée à partir de l’API de gestion, une exception est reçue par le code appelant. |10 000<br /><br />Le nombre total de rubriques et de files d’attente dans un espace de noms de service doit être inférieur ou égal à 10 000.<br/>Cela ne s’applique pas à Premium, car toutes les entités sont partitionnées. |
| Nombre de rubriques/files d’attente partitionnées par espace de noms de service |Espace de noms |Les requêtes suivantes de création de rubrique ou de file d’attente partitionnée dans l’espace de noms de service sont rejetées. Par conséquent, en cas de configuration via le [portail Azure][Azure portal], un message d’erreur est généré. Si elle est appelée à partir de l’API de gestion, une exception **QuotaExceededException** est reçue par le code appelant. |Niveaux de base et Standard : 100<br />[Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) : 1 000 (par unité de messagerie)<br/><br />Chaque file d’attente ou rubrique partitionnée est comptabilisée dans le quota de 10 000 entités par espace de noms. |
| Taille maximale de tout chemin d’entité de messagerie : file d’attente ou rubrique |Entité |- |260 caractères |
| Taille maximale de tout nom d’entité de messagerie : espace de noms, abonnement ou règle d’abonnement |Entité |- |50 caractères |
| Taille de message d’une entité de file d’attente/rubrique/abonnement |Entité |Les messages entrants dont la taille dépasse ces quotas sont rejetés et le code appelant reçoit une exception. |Taille maximale du message : 256 Ko ([niveau Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md))/1 Mo ([niveau Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />**Note** : en raison d’une surcharge du système, cette limite est généralement légèrement inférieure.<br /><br />Taille d’en-tête maximale : 64 Ko<br /><br />Nombre maximal de propriétés d’en-tête dans le conteneur de propriétés : **byte/int.MaxValue**<br /><br />Taille maximale d’une propriété dans le conteneur de propriétés : pas de limite explicite. Cette valeur est limitée par la taille d’en-tête maximale. |
| Taille de propriété de message d’une entité de file d’attente/rubrique/abonnement |Entité |Une exception **SerializationException** est générée. |La taille de propriété de message maximale est de 32 Ko. La taille cumulée de toutes les propriétés ne peut pas dépasser 64 Ko. Cela s’applique à l’intégralité de l’en-tête du paramètre [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), qui inclut les propriétés de l’utilisateur ainsi que les propriétés système (telles que [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid), etc.). |
| Nombre d’abonnements par rubrique |Entité |Les requêtes suivantes de création d’abonnements supplémentaires à la rubrique sont rejetées. Par conséquent, en cas de configuration via le portail, un message d’erreur est affiché. Si elle est appelée à partir de l’API de gestion, une exception est reçue par le code appelant. |2 000 |
| Nombre de filtres SQL par rubrique |Entité |Les requêtes suivantes de création de filtres supplémentaires sur la rubrique sont rejetées et le code appelant reçoit une exception. |2 000 |
| Nombre de filtres de corrélation par rubrique |Entité |Les requêtes suivantes de création de filtres supplémentaires sur la rubrique sont rejetées et le code appelant reçoit une exception. |100 000 |
| Taille d’actions/filtres SQL |Espace de noms |Les requêtes suivantes de création de filtres supplémentaires sont rejetées et le code appelant reçoit une exception. |Longueur maximale de la chaîne de condition de filtre : 1 024 (1 Ko).<br /><br />Longueur maximale de la chaîne d’action de règle : 1 024 (1 Ko).<br /><br />Nombre maximal d’expressions par action de règle : 32. |
| Nombre de règles [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) par espace de noms, file d’attente ou rubrique |Entité, espace de noms |Les requêtes suivantes de création de règles supplémentaires sont rejetées et le code appelant reçoit une exception. |Nombre maximal de règles : 12. <br /><br /> Les règles qui sont configurées sur un espace de noms Service Bus s’appliquent à toutes les files d’attente ou rubriques appartenant à cet espace de noms. |
| Nombre de messages par transaction | Transaction | Les messages entrants supplémentaires sont rejetés et une exception indiquant « Impossible d’envoyer plus de 100 messages dans une transaction unique » est reçue par le code appelant. | 100 <br /><br /> Pour les deux operations **Send()** et **SendAsync()**. |

[Azure portal]: https://portal.azure.com