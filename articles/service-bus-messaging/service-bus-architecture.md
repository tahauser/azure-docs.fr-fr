---
title: "Présentation de l’architecture de traitement des messages d’Azure Service Bus | Microsoft Docs"
description: "Décrit l’architecture de traitement de message d’Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: c3bf541c14e6d869f77ca7d7a6e520bd3489fcad
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-architecture"></a>Architecture de Service Bus

Cet article décrit l’architecture de traitement de message d’[Azure Service Bus](https://azure.microsoft.com/services/service-bus/).

## <a name="service-bus-scale-units"></a>Unités d'échelle de Service Bus

Service Bus est organisé par *unités d'échelle*. Une unité d'échelle est une unité de déploiement et contient tous les composants requis pour exécuter le service. Chaque région déploie une ou plusieurs unités d'échelle Service Bus.

Un espace de noms Service Bus est mappé à une unité d'échelle. L’unité d’échelle gère tous les types d’entités Service Bus : files d’attente, rubriques, abonnements. Une unité d'échelle Service Bus est constituée des éléments suivants :

* **Un ensemble de nœuds de passerelle.** Les nœuds de passerelle authentifient les requêtes entrantes. Chaque nœud de passerelle a une adresse IP publique.
* **Un ensemble de nœuds de broker de messagerie.** Les nœuds de broker de messagerie traitent les requêtes concernant les entités de messagerie.
* **Une banque de passerelle.** La banque de passerelle conserve les données pour chaque entité définie dans cette unité d'échelle. La banque de passerelle est implémentée sur une instance SQL Database.
* **Plusieurs banques de messagerie.** Les banques de messagerie conservent les messages de l’ensemble des files d’attente, rubriques et abonnements qui sont définis dans cette unité d’échelle. Elles contiennent également toutes les données d’abonnement. Une file d’attente ou une rubrique est mappée à une banque de messagerie, sauf si l’option [Partitionnement des entités de messagerie](service-bus-partitioning.md) est activée. Les abonnements sont stockés dans la même banque de messagerie que leur rubrique parent. Sauf pour Service Bus [Premium Messaging](service-bus-premium-messaging.md), les banques de messagerie sont implémentées sur des instances [SQL Database](https://azure.microsoft.com/services/sql-database/).

## <a name="containers"></a>Conteneurs

Un conteneur spécifique est assigné à chaque entité de messagerie. Un conteneur est une construction logique qui utilise un magasin de messagerie pour stocker toutes les données pertinentes pour ce conteneur. Chaque conteneur est affecté à un nœud de broker de messagerie. En règle générale, il existe plus de conteneurs que de nœuds de broker de messagerie. Par conséquent, chaque nœud de broker de messagerie charge plusieurs conteneurs. La distribution de conteneurs sur un nœud de broker de messagerie est organisée de façon à ce que tous les nœuds de broker de messagerie soient chargés de façon égale. Si le modèle de charge change (par exemple, un des conteneurs devient très occupé) ou si un nœud de broker de messagerie devient temporairement indisponible, les conteneurs sont redistribués entre les nœuds de broker de messagerie.

## <a name="processing-of-incoming-messaging-requests"></a>Traitement des requêtes de messagerie entrantes

Lorsqu'un client envoie une requête à Service Bus, l'équilibrage de charge Azure la transmet à l'un des nœuds de passerelle. Le nœud de passerelle autorise la requête. Si la requête concerne une entité de messagerie (file d’attente, rubrique, abonnement), le nœud de passerelle recherche l’entité dans le magasin de passerelle et détermine dans quelle banque de messagerie se trouve l’entité. Il recherche ensuite quel nœud de broker de messagerie traite actuellement ce conteneur et envoie la requête à ce nœud de broker de messagerie. Le nœud de broker de messagerie traite la requête et met à jour l'état de l'entité dans le magasin de conteneur. Le nœud de broker de messagerie envoie ensuite la réponse au nœud de passerelle, qui envoie une réponse appropriée au client qui a émis la requête d'origine.

![Traitement des requêtes de messagerie entrantes](./media/service-bus-architecture/ic690644.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d'ensemble de l’architecture Service Bus, consultez les liens suivants pour plus d'informations :

* [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)


