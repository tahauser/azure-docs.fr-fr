---
title: "Haute disponibilité : service Azure SQL Database | Microsoft Docs"
description: "En savoir plus sur les fonctionnalités de haute disponibilité du service Azure SQL Database"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>Haute disponibilité et Azure SQL Database
Depuis la sortie de l’offre PaaS Azure SQL Database, Microsoft a promis à ses clients d’intégrer au service la haute disponibilité afin qu’ils n’aient plus à intervenir, à ajouter une logique particulière ou à prendre des décisions dans ce domaine. Microsoft offre aux clients un contrat de niveau de service (SLA) et conserve un contrôle total sur la configuration et l’utilisation du système de haute disponibilité. Le SLA relatif à la haute disponibilité s’applique à une base de données SQL dans une région et ne fournit aucune protection en cas de panne généralisée, dès lors que les raisons de cette panne échappent à son contrôle raisonnable (catastrophe naturelle, guerre, actes de terrorisme, émeutes, action des pouvoirs publics, panne d’un réseau ou d’un appareil autre que celle de ses centres de données, notamment sur le site du client ou entre le site du client et son centre de données).

Pour simplifier le problème d’espace lié à la haute disponibilité, Microsoft part des hypothèses suivantes :
1.  Les défaillances matérielles et logicielles sont inévitables.
2.  Le personnel en charge de l’exploitation commet des erreurs qui entraînent des échecs.
3.  Les opérations de maintenance planifiées provoquent des pannes. 

Si de tels événements sont rares, ils sont fréquents à l’échelle du cloud. Leur fréquence est hebdomadaire, parfois même quotidienne. 

## <a name="fault-tolerant-sql-databases"></a>Bases de données SQL tolérantes aux pannes
Les clients s’intéressent davantage à la résilience de leurs propres bases de données qu’à celle du service SQL Database dans son ensemble. La disponibilité d’un service a beau être de 99,99 %, cela est sans importance si « ma base de données» fait partie des 0,01 % de celles qui sont en panne. Chaque base de données doit être tolérante aux pannes et l’atténuation des risques ne doit jamais provoquer la perte d’une transaction validée. 

Pour les données, SQL Database utilise le stockage local (LS) basé sur des disques/disques durs virtuels à connexion directe et le stockage étendu (RS) basé sur des objets blob de pages de stockage Azure Premium. 
- Le stockage local est utilisé dans les bases de données et les pools Premium lesquels sont conçus pour les applications OLTP ayant des exigences élevées en termes d’IOPS. 
- Le stockage étendu est utilisé pour les niveaux de service De base et Standard lesquels sont conçus pour les petites bases de données, ou encore les bases de données passives ou volumineuses qui requièrent une certaine puissance de stockage et de calcul pour être mises à l’échelle de manière indépendante. Nous utilisons un objet blob de page unique pour la base de données et les fichiers journaux, ainsi que des mécanismes intégrés de réplication et de basculement de stockage.

Dans ces deux cas, la réplication, la détection des défaillances et les mécanismes de basculement de SQL Database sont entièrement automatisés et fonctionnent sans intervention humaine. Cette architecture garantit que les données validées ne sont jamais perdues et que la durabilité des données prévaut.

Principaux avantages :
- Les clients profitent pleinement des bases de données répliquées sans avoir à configurer le matériel, les logiciels, le système d’exploitation ou les environnements de virtualisation, à gérer leur complexité ou à en assurer la maintenance.
- Le système assure la gestion de l’ensemble des propriétés ACID des bases de données relationnelles.
- Les basculements sont entièrement automatisés, sans aucune perte de données validées.
- Le routage des connexions vers le réplica principal est géré de manière dynamique par le service, sans aucune logique d’application.
- De plus, ce niveau élevé de redondance automatique est fourni sans frais supplémentaires.

> [!NOTE]
> L’architecture de haute disponibilité décrite est susceptible de changer sans préavis. 

## <a name="data-redundancy"></a>Redondance des données

La solution de haute disponibilité dans SQL Database s’appuie sur la technologie [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) de SQL Server et la rend disponible à la fois dans les bases de données LS et RS, sans grande différence. AlwaysON est utilisé pour la persistance dans la configuration d’un stockage LS et pour la disponibilité (objectif de délai de récupération faible) dans la configuration d’un stockage RS. 

## <a name="local-storage"></a>Stockage local

Dans le cas d’un stockage local, chaque base de données est mise en ligne par le service de gestion (MS) dans l’anneau de contrôle. Un réplica principal et au moins deux réplicas secondaires (quorum) figurent dans une boucle client. Celle-ci s’étend sur trois sous-systèmes physiques indépendants, au sein du même centre de données. Toutes les lectures et écritures sont envoyées par la passerelle vers le réplica principal, et les écritures sont répliquées de manière asynchrone sur les réplicas secondaires. Azure SQL Database utilise un schéma de validation par quorum selon lequel les données sont écrites sur le réplica principal et sur au moins l’un des réplicas secondaires avant que la transaction ne soit validée.

Le système de basculement [Service Fabric](/azure/service-fabric/service-fabric-overview.md) reconstruit automatiquement les réplicas à mesure que des nœuds tombent en panne et actualise l’appartenance au quorum à mesure que des nœuds quittent ou rejoignent le système. La maintenance planifiée est soigneusement coordonnée pour empêcher le quorum de passer en dessous d’un nombre minimal de réplicas (habituellement 2). Ce modèle fonctionne bien pour les bases de données Premium, mais il requiert la redondance des composants de calcul et de stockage et entraîne un coût plus élevé.

## <a name="remote-storage"></a>Stockage étendu

Pour les configurations de stockage étendu (niveaux De base et Standard), une seule copie de la base de données est conservée dans le stockage étendu des objets blob. Il s’agit d’exploiter les fonctionnalités des systèmes de stockage en termes de durabilité, de redondance et de détection bit-rot. 

L’architecture de haute disponibilité est illustrée dans le diagramme suivant :
 
![Architecture de haute disponibilité](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>Détection des défaillances et récupération 
Un système distribué à grande échelle a besoin d’un système qui peut détecter les défaillances rapidement et de manière hautement fiable, mais aussi qui soit aussi proche que possible du client. Pour SQL Database, ce système s’appuie sur Azure Service Fabric. 

Avec un système utilisant le réplica principal, vous savez immédiatement si ce dernier a échoué et quand. En effet, vous êtes bloqué dans votre travail, car toutes les lectures et écritures interviennent en premier sur ce réplica. Le processus de promotion d’un réplica secondaire d’après l’état du réplica principal possède un objectif de temps de récupération (RTO) de 30 secondes et un objectif de point de récupération (RPO) de 0. Pour atténuer l’impact de ce RTO, la meilleure solution consiste à essayer de vous reconnecter plusieurs fois et de réduire le délai d’attente entre deux échecs de connexion.

Lorsqu’un réplica secondaire échoue, la base de données se rapproche du quorum minimal, sans aucun remplacement. Service Fabric initie le processus de reconfiguration en le calquant sur celui qui suit la défaillance du réplica principal. Par conséquent, après un court délai d’attente pour déterminer si l’échec est permanent, un autre réplica secondaire est créé. En cas de mise hors-service temporaire, par exemple une défaillance du système d’exploitation ou une mise à niveau, aucun nouveau réplica n’est pas généré immédiatement pour permettre au nœud défaillant de redémarrer à la place. 

Pour les configurations de stockage étendu, SQL Database utilise des fonctionnalités AlwaysON pour basculer les bases de données pendant les mises à niveau. Pour ce faire, il vous faut anticiper en préparant une nouvelle instance SQL dans le cadre de l’événement de mise à niveau planifiée. Elle se connecte et récupère le fichier de base de données à partir du stockage étendu. En cas de défaillance du processus ou autres événements non planifiés, Windows Fabric gère la disponibilité de l’instance et, au cours de la dernière étape de la récupération, attache le fichier de base de données distante.

## <a name="conclusion"></a>Conclusion
Azure SQL Database est étroitement intégré à la plateforme Azure et dépend très largement de Service Fabric pour la détection des défaillances et la récupération, mais aussi des objets blob de stockage Azure pour la protection des données. Parallèlement à cela, Azure SQL Database utilise la technologie AlwaysON intégrée à SQL Server pour la réplication et le basculement. La combinaison de ces technologies permet aux applications de profiter pleinement des avantages d’un modèle de stockage mixte et de prendre en charge les contrats de niveau de service les plus exigeants. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Service Fabric](/azure/service-fabric/service-fabric-overview.md)
- En savoir plus sur [Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md). 
