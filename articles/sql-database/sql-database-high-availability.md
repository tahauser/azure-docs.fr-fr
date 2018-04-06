---
title: 'Haute disponibilité : service Azure SQL Database | Microsoft Docs'
description: En savoir plus sur les fonctionnalités de haute disponibilité du service Azure SQL Database
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 03/19/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: d26fe28d301cf563dc6bdb3d9e17903dea3e73fc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="high-availability-and-azure-sql-database"></a>Haute disponibilité et Azure SQL Database
Depuis la sortie de l’offre PaaS Azure SQL Database, Microsoft a promis à ses clients d’intégrer au service la haute disponibilité afin qu’ils n’aient plus à intervenir, à ajouter une logique particulière ou à prendre des décisions dans ce domaine. Microsoft offre aux clients un contrat de niveau de service (SLA) et conserve un contrôle total sur la configuration et l’utilisation du système de haute disponibilité. Le SLA relatif à la haute disponibilité s’applique à une base de données SQL dans une région et ne fournit aucune protection en cas de panne généralisée, dès lors que les raisons de cette panne échappent au contrôle raisonnable de Microsoft (catastrophe naturelle, guerre, actes de terrorisme, émeutes, action des pouvoirs publics, panne d’un réseau ou d’un appareil autre que celle des centres de données de Microsoft, notamment sur le site du client ou entre le site du client et le centre de données de Microsoft).

Pour simplifier le problème d’espace lié à la haute disponibilité, Microsoft part des hypothèses suivantes :
1.  Les défaillances matérielles et logicielles sont inévitables.
2.  Le personnel en charge de l’exploitation commet des erreurs qui entraînent des échecs.
3.  Les opérations de maintenance planifiées provoquent des pannes. 

Bien que de tels événements individuels soient rares, à une échelle du cloud, leur fréquence est hebdomadaire, parfois même quotidienne. 

## <a name="fault-tolerant-sql-databases"></a>Bases de données SQL tolérantes aux pannes
Les clients s’intéressent davantage à la résilience de leurs propres bases de données qu’à celle du service SQL Database dans son ensemble. La disponibilité d’un service a beau être de 99,99 %, cela est sans importance si « ma base de données» fait partie des 0,01 % de celles qui sont en panne. Chaque base de données doit être tolérante aux pannes et l’atténuation des risques ne doit jamais provoquer la perte d’une transaction validée. 

Pour les données, SQL Database utilise le stockage local (LS) basé sur des disques/disques durs virtuels à connexion directe et le stockage étendu (RS) basé sur des objets blob de pages de stockage Azure Premium. 
- Le stockage local est utilisé dans les bases de données et les pools Premium lesquels sont conçus pour les applications OLTP stratégiques ayant des exigences élevées en termes d’IOPS. 
- Le stockage étendu est utilisé pour les niveaux de service De base et Standard lesquels sont conçus pour les charges de travail orientées budget, ou encore les bases de données passives ou volumineuses qui requièrent une certaine puissance de stockage et de calcul pour être mises à l’échelle de manière indépendante. Nous utilisons un objet blob de page unique pour la base de données et les fichiers journaux, ainsi que des mécanismes intégrés de réplication et de basculement de stockage.

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

La solution de haute disponibilité dans SQL Database s’appuie sur la technologie [Groupes de disponibilité Always ON](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) de SQL Server et la rend disponible à la fois dans les bases de données LS et RS, sans grande différence. La technologie du groupe de disponibilité Always ON est utilisé pour la persistance dans la configuration LS tandis que dans la configuration RS elle est utilisée pour la disponibilité (RTO faible). 

## <a name="local-storage-configuration"></a>Configuration du stockage local

Dans cette configuration, chaque base de données est mise en ligne par le service de gestion (MS) dans l’anneau de contrôle. Un réplica principal et au moins deux réplicas secondaires (quorum) figurent dans une boucle client. Celle-ci s’étend sur trois sous-systèmes physiques indépendants, au sein du même centre de données. Toutes les lectures et écritures sont envoyées par la passerelle vers le réplica principal, et les écritures sont répliquées de manière asynchrone sur les réplicas secondaires. Azure SQL Database utilise un schéma de validation par quorum selon lequel les données sont écrites sur le réplica principal et sur au moins l’un des réplicas secondaires avant que la transaction ne soit validée.

Le système de basculement [Service Fabric](../service-fabric/service-fabric-overview.md) reconstruit automatiquement les réplicas à mesure que des nœuds tombent en panne et actualise l’appartenance au quorum à mesure que des nœuds quittent ou rejoignent le système. La maintenance planifiée est soigneusement coordonnée pour empêcher le quorum de passer en dessous d’un nombre minimal de réplicas (habituellement 2). Ce modèle fonctionne bien pour les bases de données Premium, mais il requiert la redondance des composants de calcul et de stockage et entraîne un coût plus élevé.

## <a name="remote-storage-configuration"></a>Configuration du stockage à distance

Pour les configurations de stockage étendu (niveaux De base et Standard), une seule copie est conservée dans le stockage étendu des objets blob. Il s’agit d’utiliser les fonctionnalités des systèmes de stockage en termes de durabilité, de redondance et de détection bit-rot. 

L’architecture de haute disponibilité est illustrée dans le diagramme suivant :
 
![Architecture de haute disponibilité](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>Détection des défaillances et récupération 
Un système distribué à grande échelle a besoin d’un système qui peut détecter les défaillances rapidement et de manière hautement fiable, mais aussi qui soit aussi proche que possible du client. Pour SQL Database, ce système s’appuie sur Azure Service Fabric. 

Avec un système utilisant le réplica principal, vous savez immédiatement si ce dernier a échoué et quand. En effet, vous êtes bloqué dans votre travail, car toutes les lectures et écritures interviennent en premier sur ce réplica. Le processus de promotion d’un réplica secondaire d’après l’état du réplica principal possède un objectif de temps de récupération (RTO) de 30 secondes et un objectif de point de récupération (RPO) de 0. Pour atténuer l’impact de ce RTO, la meilleure solution consiste à essayer de vous reconnecter plusieurs fois et de réduire le délai d’attente entre deux échecs de connexion.

Lorsqu’un réplica secondaire échoue, la base de données se rapproche du quorum minimal, sans aucun remplacement. Service Fabric initie le processus de reconfiguration en le calquant sur celui qui suit la défaillance du réplica principal. Par conséquent, après un court délai d’attente pour déterminer si l’échec est permanent, un autre réplica secondaire est créé. En cas de mise hors-service temporaire, par exemple une défaillance du système d’exploitation ou une mise à niveau, aucun nouveau réplica n’est pas généré immédiatement pour permettre au nœud défaillant de redémarrer à la place. 

Pour les configurations de stockage étendu, SQL Database utilise des fonctionnalités AlwaysON pour basculer les bases de données pendant les mises à niveau. Pour ce faire, il vous faut anticiper en préparant une nouvelle instance SQL dans le cadre de l’événement de mise à niveau planifiée. Elle se connecte et récupère le fichier de base de données à partir du stockage étendu. En cas de défaillance du processus ou autres événements non planifiés, Windows Fabric gère la disponibilité de l’instance et, au cours de la dernière étape de la récupération, attache le fichier de base de données distante.

## <a name="zone-redundant-configuration-preview"></a>Configuration de zone redondante (préversion)

Par défaut, les réplicas du jeu de quorum pour les configurations de stockage local sont créés dans le même centre de données. Avec l’introduction des [Zones de disponibilité Azure](../availability-zones/az-overview.md), vous avez la possibilité de placer les différents réplicas dans les jeux de quorum sur des zones de haute disponibilité distinctes dans la même région. Pour éliminer un point de défaillance unique, l’anneau de contrôle est également dupliqué sur plusieurs fuseaux horaires sous forme de trois anneaux de passerelle (GW). Le routage vers un anneau de passerelle spécifique est contrôlé par [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Étant donné que la configuration de la redondance dans une zone ne crée pas de redondance de base de données supplémentaire, l’utilisation des zones de disponibilité dans le niveau de service Premium est disponible sans coût supplémentaire. En sélectionnant une base de données redondante dans une zone, vous pouvez rendre vos bases de données Premium résistantes à un plus grand éventail d’échecs, notamment les pannes graves de centre de données, sans aucune modification de la logique d’application. Vous pouvez également convertir vos bases de données ou pools Premium en configuration avec redondance dans une zone.

L’ensemble du jeu de quorums de redondance de zone ayant des réplicas dans différents centres de données avec une certaine distance entre eux, la latence accrue du réseau peut augmenter le temps de validation et ainsi avoir un impact sur les performances de certaines charges de travail OLTP. Vous pouvez toujours revenir à la configuration de zone unique en désactivant le paramètre de redondance de zone. Ce processus dépend de la taille des données et est semblable à la mise à jour des objectifs de niveau de service (SLO) ordinaires. À la fin du processus, la base de données ou le pool est migré à partir d’un anneau de redondance de zone vers un anneau de zone unique, ou vice versa.

> [!IMPORTANT]
> Les bases de données avec redondance de zone et les pools élastiques sont uniquement pris en charge dans le niveau de service Premium. Pour la version préliminaire publique, les sauvegardes et enregistrements d’audit sont stockés dans le stockage de RA-GRS, et ne sont donc pas automatiquement disponibles en cas d’une panne à l’échelle de la zone. 

La version avec redondance de zone de l’architecture de haute disponibilité est illustrée dans le diagramme suivant :
 
![architecture haute disponibilité avec redondance de zone](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="conclusion"></a>Conclusion
Azure SQL Database est étroitement intégré à la plateforme Azure et dépend très largement de Service Fabric pour la détection des défaillances et la récupération, mais aussi des objets blob de stockage Azure pour la protection des données et des zones de disponibilité. Parallèlement à cela, Azure SQL Database utilise pleinement la technologie AlwaysON intégrée à SQL Server pour la réplication et le basculement. La combinaison de ces technologies permet aux applications de profiter pleinement des avantages d’un modèle de stockage mixte et de prendre en charge les contrats de niveau de service les plus exigeants. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Zones de disponibilité Azure](../availability-zones/az-overview.md)
- En savoir plus sur [Service Fabric](../service-fabric/service-fabric-overview.md)
- En savoir plus sur [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
