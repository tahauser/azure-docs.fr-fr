---
title: "Vue d’ensemble d’AMQP 1.0 dans Azure Service Bus | Microsoft Docs"
description: "Découvrez comment utiliser le protocole Advanced Message Queuing Protocol (AMQP) 1.0 dans Azure."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 6d2dffd22ecfc0aaf6e338567d5cf107a2c07383
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="amqp-10-support-in-service-bus"></a>Prise en charge d’AMQP 1.0 dans Service Bus
Le service cloud Azure Service Bus et le [Service Bus pour Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) local prennent en charge le protocole AMQP (Advanced Message Queueing Protocol) 1.0. Le protocole AMQP vous permet de développer des applications hybrides interplateforme à l’aide d’un protocole open standard. Vous pouvez générer des applications à l’aide de composants créés avec plusieurs langages et infrastructures, exécutées sur différents systèmes d’exploitation. Tous ces composants peuvent se connecter à Service Bus et échanger efficacement et sans difficulté des messages professionnels d’une fidélité optimale.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduction : qu’est-ce qu’AMQP 1.0 ? En quoi ce protocole est-il important ?
Traditionnellement, les produits intergiciels (middleware) utilisent des protocoles propriétaires pour la communication entre les applications clientes et les services Broker. Cela signifie qu’une fois que vous avez sélectionné un service Broker de messagerie d’un fournisseur particulier, vous devez utiliser les bibliothèques de ce fournisseur pour connecter vos applications clientes à ce service Broker. Cela aboutit à un niveau de dépendance vis-à-vis de ce fournisseur, puisque le portage d’une application vers un produit différent nécessite la modification du code de toutes les applications connectées. 

De plus, il est compliqué de connecter les services Broker de messagerie de différents fournisseurs. Cela requiert en général le pontage au niveau de l’application pour le déplacement des messages d’un système à l’autre et pour leur conversion entre les différents formats de messages propriétaires. Cela est une exigence courante, par exemple, lors de la fourniture d’une nouvelle interface unifiée à plusieurs anciens systèmes ou de l’intégration de systèmes informatiques suite à une fusion.

Le secteur du logiciel évolue rapidement ; les nouveaux langages de programmation et les nouvelles infrastructures d’application sont parfois inventés à vitesse grand V. De même, les besoins des systèmes informatiques évoluent au fil du temps et les développeurs souhaitent profiter des fonctionnalités de plateforme les plus récentes. Cependant, le fournisseur de messagerie sélectionné ne prend parfois pas en charge ces plateformes. Ces protocoles de messagerie étant propriétaires, il n’est pas possible pour les autres de fournir des bibliothèques pour ces nouvelles plateformes. Par conséquent, vous devez utiliser des approches comme la création de ponts ou de passerelles pour continuer à utiliser le produit de messagerie.

Ce sont ces problèmes qui ont motivé le développement d’AMQP (Advanced Message Queuing Protocol) 1.0. Il a vu le jour chez JP Morgan Chase, qui, comme la plupart des entreprises proposant des services financiers, est un grand consommateur d’intergiciels orientés messagerie. L’objectif était simple : créer un protocole de messagerie open standard permettant le développement d’applications basées sur des messages à l’aide de composants créés avec plusieurs langages, infrastructures et systèmes d’exploitation, utilisant tous les meilleurs composants disponibles chez divers fournisseurs.

## <a name="amqp-10-technical-features"></a>Fonctionnalités techniques d’AMQP 1.0
AMQP 1.0 est un protocole de messagerie « wire-level » efficace et fiable qui peut être utilisé pour créer des applications de messagerie interplateforme robustes. Il a un objectif simple : définir les mécanismes du transfert sécurisé, fiable et efficace des messages entre deux tiers. Les messages sont eux-mêmes codés à l’aide d’une représentation portable de données qui permet l’échange haute-fidélité de messages professionnels structurés entre des expéditeurs et des destinataires hétérogènes. La liste suivante décrit les principales caractéristiques :

* **Efficacité** : le protocole orienté connexion AMQP 1.0 utilise un codage binaire pour les instructions de protocole et les messages professionnels transférés par son biais. Il intègre des schémas de contrôle de flux sophistiqués pour optimiser l’utilisation du réseau et des composants connectés. Le protocole a été conçu pour garantir l’équilibre entre efficacité, flexibilité et interopérabilité.
* **Fiabilité** : le protocole AMQP 1.0 permet d’échanger des messages avec diverses garanties de fiabilité (autonomie, fiabilité, remise EOD, etc.).
* **Flexibilité** : AMQP 1.0 est un protocole flexible qui permet de prendre en charge différentes topologies. Vous pouvez utiliser le même protocole pour les communications de client à client, de client à service Broker et de service Broker à service Broker.
* **Indépendance vis-à-vis du répartiteur** : la spécification AMQP 1.0 n’applique aucune condition préalable sur le modèle de messagerie utilisé par un répartiteur. Cela signifie qu’il est possible d’ajouter facilement AMQP 1.0 à des services Broker de messagerie existants.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 est une Norme (avec une majuscule N)
AMQP 1.0 est une norme internationale approuvée par les organismes ISO et IEC sous la dénomination ISO/IEC 19464:2014.

AMQP 1.0 est développé depuis 2008 par un groupe de plus de 20 entreprises, aussi bien fournisseurs de technologie que sociétés utilisatrices. Les dernières ont ainsi pu couvrir leurs besoins professionnels concrets, tandis que les premiers ont fait évoluer le protocole pour les satisfaire. Tout au long du processus, les fournisseurs ont participé à des ateliers pour valider l’interopérabilité entre leurs implémentations.

En octobre 2011, le travail de développement a été confié à un comité technique au sein de l’OASIS (Organization for the Advancement of Structured Information Standards) et la norme OASIS AMQP 1.0 a été publiée en octobre 2012. Les entreprises suivantes ont participé au comité technique dans le cadre du développement de la norme :

* **Fournisseurs de technologie** : Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Sociétés utilisatrices** : Bank of America, Crédit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Les normes ouvertes présentent généralement les avantages suivants :

* Moins de risque de dépendance vis-à-vis d’un fournisseur
* Interopérabilité
* Large disponibilité des bibliothèques et outils
* Protection contre l’obsolescence
* Disponibilité du personnel qualifié
* Risque inférieur et gérable

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 et Service Bus
Grâce à la prise en charge d’AMQP 1.0 dans à Azure Service Bus, vous pouvez désormais tirer parti des fonctionnalités de messagerie répartie de Service Bus de mise en file d’attente et de publication/d’abonnement à partir de diverses plateformes, à l’aide d’un protocole binaire efficace. De plus, vous pouvez générer des applications constituées de composants créés à l'aide de divers langages, structures et systèmes d'exploitation.

La figure ci-dessous montre un exemple de déploiement dans lequel des clients Java exécutés sous Linux, écrits à l’aide de l’API standard JMS (Java Message Service) et des clients .NET exécutés sous Windows, échangent des messages via Service Bus à l’aide d’AMQP 1.0.

![][0]

**Figure 1 : exemple de scénario de déploiement illustrant la messagerie interplateforme avec Service Bus et AMQP 1.0**

Les bibliothèques clientes suivantes fonctionnent actuellement avec Service Bus :

| Langage | Bibliothèque |
| --- | --- |
| Java |Client Apache Qpid Java Message Service (JMS)<br/>Client IIT Software SwiftMQ Java |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .Net Lite |

**Figure 2 : table des bibliothèques clientes d’AMQP 1.0**

## <a name="summary"></a>Résumé
* AMQP 1.0 est un protocole de messagerie efficace et ouvert qui peut être utilisé pour créer des applications hybrides interplateforme. AMQP 1.0 est une norme OASIS.
* La prise en charge d’AMQP 1.0 est désormais disponible dans Azure Service Bus et Service Bus pour Windows Server (Service Bus 1.1). Les tarifs sont les mêmes que pour les protocoles existants.

## <a name="next-steps"></a>Étapes suivantes
Prêt à en savoir plus ? Visitez les liens suivants :

* [Utilisation de Service Bus à partir de .NET avec AMQP]
* [Utilisation de Service Bus à partir de Java avec AMQP]
* [Installation d’Apache Qpid Proton-C sur une machine virtuelle Linux Azure]
* [AMQP dans Service Bus pour Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Utilisation de Service Bus à partir de .NET avec AMQP]: service-bus-amqp-dotnet.md
[Utilisation de Service Bus à partir de Java avec AMQP]: service-bus-amqp-java.md
[Installation d’Apache Qpid Proton-C sur une machine virtuelle Linux Azure]: service-bus-amqp-apache.md
[AMQP dans Service Bus pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
