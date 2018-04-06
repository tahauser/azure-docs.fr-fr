---
title: Distribution mondiale des données avec Azure Cosmos DB | Microsoft Docs
description: Apprenez-en plus sur la géoréplication à l’échelle de la planète, le basculement et la récupération des données à l’aide de bases de données mondiales à partir d’Azure Cosmos DB, un service de base de données multimodèle distribué mondialement.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: jhubbard
editor: ''
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2018
ms.author: arramac
ms.openlocfilehash: e31b66f20c51e95dbc2d8285bb5602590abd4f4f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Comment distribuer des données mondialement avec Azure Cosmos DB
Avec plus de 30 régions géographiques, Azure est partout et continue de s’étendre. Présente dans le monde entier, l’une des fonctionnalités différenciées qu’Azure offre à ses développeurs est la possibilité de générer, de déployer et de gérer facilement des applications mondialement distribuées. 

[Azure Cosmos DB](../cosmos-db/introduction.md) est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Azure Cosmos DB fournit la distribution mondiale clés en main, la [mise à l’échelle élastique du débit et du stockage](../cosmos-db/partition-data.md), des latences de l’ordre de quelques millisecondes dans le monde entier dans plus de 99 pour cent des cas, [cinq niveaux de cohérence bien définis](consistency-levels.md) et la garantie d’une haute disponibilité, le tout soutenu par de [contrats SLA de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexe automatiquement les données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à s’occuper de la gestion des schémas et des index. Il est multi-modèle et prend en charge les modèles de données en colonnes, documents, graphes et clé-valeur. En tant que service cloud, Azure Cosmos DB est dès le départ conçu avec des fonctions d’architecture mutualisée et de distribution mondiale.

**Collection Azure Cosmos DB unique, partitionnée et distribuée dans plusieurs régions Azure**

![Collection Azure Cosmos DB partitionnée et distribuée dans trois régions](./media/distribute-data-globally/global-apps.png)

Comme nous l’avons appris lors de la création Azure Cosmos DB, l’ajout de la distribution globale ne peut pas venir après coup. Cette fonction ne peut pas être « rajoutée » sur un système de base de données à un seul site. Les possibilités offertes par une base de données mondialement distribuée s’étendent au-delà de la récupération d’urgence géographique traditionnelle proposée par les bases de données à un seul site. Les bases de données à un seul site offrant la fonctionnalité de récupération d’urgence géographique sont un sous-ensemble strict des bases de données mondialement distribuées. 

Avec la distribution mondiale clé en main d’Azure Cosmos DB, les développeurs n’ont pas à créer leur propre génération de modèles automatique de réplication en utilisant soit le modèle d’expression Lambda (par exemple, [réplication AWS DynamoDB](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) sur le journal de base de données ou en effectuant des « doubles écritures » dans plusieurs régions. Nous déconseillons ces approches, car il est impossible de garantir leur exactitude et de fournir des contrats de niveau de service fiables. 

Dans cet article, nous fournissons une vue d’ensemble des fonctionnalités de distribution mondiale d’Azure Cosmos DB. Nous décrivons également l’approche unique d’Azure Cosmos DB pour fournir des contrats SLA complets. 

## <a id="EnableGlobalDistribution"></a>Activation de la distribution mondiale clé en main
Azure Cosmos DB fournit les fonctionnalités suivantes pour vous permettre d’écrire facilement des applications à l’échelle planétaire. Ces fonctionnalités sont disponibles par l’intermédiaire des [API REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) basées sur le fournisseur de ressources d’Azure Cosmos DB, ainsi que sur le portail Azure.

Dans la vidéo suivante, le responsable du programme Azure Cosmos DB, Andrew Liu, illustre les fonctionnalités de la distribution globale clés en main.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Omniprésence régionale 
Azure étend constamment sa présence géographique en ajoutant de [nouvelles régions](https://azure.microsoft.com/regions/) en ligne. Azure Cosmos DB est disponible par défaut dans toutes les nouvelles régions Azure. Cela vous permet d’associer une région géographique à votre compte de base de données Azure Cosmos DB dès qu’Azure ouvre une nouvelle région aux entreprises.

### <a id="UnlimitedRegionsPerAccount"></a>Associer un nombre illimité de régions à votre compte de base de données Azure Cosmos DB
Azure Cosmos DB vous permet d’associer n’importe quel nombre de régions Azure à votre compte de base de données Azure Cosmos DB. En dehors des restrictions de délimitations géographiques (par exemple, pour la Chine et l’Allemagne), il n’existe aucune restriction quant au nombre de régions qui peuvent être associées à votre compte de base de données Azure Cosmos DB. La figure suivante illustre un compte de base de données configuré pour couvrir 25 régions Azure.  

**Un compte de base de données Azure Cosmos DB d’un locataire couvrant 25 régions Azure**

![Compte de base de données Azure Cosmos DB couvrant 25 régions Azure](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Délimitations géographiques basées sur des stratégies
Azure Cosmos DB est conçu pour disposer de fonctionnalités de délimitation géographique basée sur des stratégies. La délimitation géographique est un composant important pour garantir la gouvernance des données et les restrictions de conformité, des données, et peut empêcher l’association d’une région spécifique avec votre compte. Les exemples de délimitation géographique incluent (mais sans s’y limiter) l’étendue de la distribution mondiale aux régions dans un cloud souverain (par exemple, la Chine et l’Allemagne), ou dans les limites d’imposition du gouvernement (par exemple, l’Australie). Les stratégies sont contrôlées à l’aide des métadonnées de votre abonnement Azure.

### <a id="DynamicallyAddRegions"></a>Ajouter et supprimer des régions dynamiquement
Azure Cosmos DB vous permet d’ajouter (associer) ou de supprimer (dissocier) des régions de votre compte de base de données à tout moment (voir la [figure précédente](#UnlimitedRegionsPerAccount)). En vertu de la réplication de données à travers les partitions en parallèle, Azure Cosmos DB garantit une disponibilité dans les 30 minutes lorsqu’une nouvelle région est en ligne, n’importe où dans le monde et jusqu’à 100 To. 

### <a id="FailoverPriorities"></a>Priorités de basculement
Pour contrôler l’ordre exact des basculements régionaux lors d’une panne dans plusieurs régions, Azure Cosmos DB vous permet d’associer la priorité à différentes régions associées au compte de la base de données (voir la figure suivante). Azure Cosmos DB garantit que la séquence de basculement automatique a lieu dans l’ordre de priorité que vous avez spécifié. Pour plus d’informations sur les basculements régionaux, consultez [Basculements régionaux automatiques pour la continuité des activités dans Azure Cosmos DB](regional-failover.md).

**Un locataire d’Azure Cosmos DB peut configurer l’ordre de priorité de basculement (volet droit) pour les régions associées à un compte de base de données**

![Configuration des priorités de basculement avec Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Plusieurs modèles de cohérence bien définis pour les bases de données mondialement répliquées
Azure Cosmos DB expose [plusieurs niveaux de cohérence bien définis](consistency-levels.md) pris en charge par des contrats SLA. Vous pouvez choisir un modèle de cohérence spécifique (à partir de la liste des options disponibles) en fonction des charges de travail/scénarios. 

### <a id="TunableConsistency"></a>Cohérence ajustable pour les bases de données répliquées mondialement
Azure Cosmos DB vous permet de remplacer par programme et d’assouplir le choix de cohérence par défaut à la demande, lors de l’exécution. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Régions de lecture et d’écriture configurables de manière dynamique
Azure Cosmos DB vous permet de configurer les régions (associées à la base de données) en « lecture », en « écriture » ou en « lecture/écriture ». 

### <a id="ElasticallyScaleThroughput"></a>Mise à l’échelle flexible du débit à travers les régions Azure
Vous pouvez mettre à l’échelle une collection Azure Cosmos DB de manière flexible en configurant le débit par programme. Le débit est appliqué à toutes les régions dans lesquelles la collection est distribuée.

### <a id="GeoLocalReadsAndWrites"></a>Lectures et écritures géo-locales
Le principal avantage d’une base de données mondialement distribuée est de proposer un accès aux données ne présentant qu’une faible latence n’importe où dans le monde. Azure Cosmos DB offre des garanties de latence faible à 99,99 % pour diverses opérations de base de données. La solution garantit que toutes les lectures sont acheminées vers la région de lecture locale le plus proche. Pour répondre à une demande de lecture, le quorum se localise dans la région dans laquelle la lecture est émise et utilisé ; il en va de même pour les écritures. Une écriture est reconnue uniquement après qu’une majorité de réplicas ont durablement validé l’écriture localement, mais sans être contrôlés sur des réplicas à distance pour reconnaître les écritures. En d’autres termes, le protocole de réplication d’Azure Cosmos DB fonctionne en partant du principe que les quorums en lecture et en écriture sont toujours locaux pour les régions en lecture et en écriture, respectivement, dans lesquelles les demandes sont émises.

### <a id="ManualFailover"></a>Initier manuellement le basculement régional
Azure Cosmos DB vous permet de déclencher le basculement du compte de base de données pour valider les propriétés de disponibilité de l’application entière de *bout en bout* (au-delà de la base de données). Étant donné que les propriétés de sécurité et d’activité de la détection de défaillances et du choix de responsable sont garanties, Azure Cosmos DB garantit *l’absence de perte de données* lors des opérations de basculement manuelles initiées par les locataires.

### <a id="AutomaticFailover"></a>Basculement automatique
Azure Cosmos DB prend en charge le basculement automatique en cas d’une ou de plusieurs pannes régionales. Lors d’un basculement régional, Azure Cosmos DB gère la latence de lecture, la disponibilité, la cohérence et les contrats SLA de débit. Azure Cosmos DB fournit une limite supérieure pour le temps d’achèvement d’une opération de basculement automatique. Il s’agit de la fenêtre de perte de données potentielle pendant une panne régionale.

### <a id="GranularFailover"></a>Conçu pour différentes granularités de basculement
Actuellement, les fonctionnalités de basculement automatiques et manuelles sont exposées au niveau de granularité du compte de base de données. Notez que, en interne, Azure Cosmos DB est conçu pour offrir un basculement *automatique* au niveau de granularité le plus fin d’une base de données, d’une collection, voire d’une partition (d’une collection qui possède un éventail de clés). 

### <a id="MultiHomingAPIs"></a>API multihébergement dans Azure Cosmos DB
Azure Cosmos DB vous permet d’interagir avec la base de données à l’aide des points de terminaison logiques (sans région) ou physiques (spécifiques à une région). L’utilisation de points de terminaison logiques garantit que l’application peut en toute transparence être multihébergée en cas de basculement. Les points de terminaison physiques, quant à eux, fournissent un contrôle précis à l’application pour rediriger les lectures et écritures vers des régions spécifiques.

Vous trouverez plus d’informations sur la configuration des préférences de lecture de [l’API SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), de l’[API Graph](../cosmos-db/tutorial-global-distribution-graph.md), de [l’API Table](../cosmos-db/tutorial-global-distribution-table.md) et de [l’API MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) dans leurs articles respectifs.

### <a id="TransparentSchemaMigration"></a>Schéma de base de données et migration d’index transparents et cohérents 
Azure Cosmos DB est entièrement [sans schéma](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). La conception unique de son moteur de base de données permet d’indexer automatiquement et de manière synchrone toutes les données reçues sans nécessiter de schéma ou d’un index secondaire de votre part. Cela vous permet d’itérer votre application mondialement distribuée rapidement et sans soucis liés au schéma de base de données et à la migration d’index de base de données ou à la coordination des déploiements d’application en plusieurs phases de modifications de schéma. Azure Cosmos DB garantit que les modifications apportées à l’indexation de stratégies explicitement faites par vous n’entraînent pas de dégradation des performances ou de la disponibilité.  

### <a id="ComprehensiveSLAs"></a>Contrats de niveau de service complets (au-delà de la simple haute disponibilité)
En tant que service de base de données mondialement distribué, Azure Cosmos DB propose des contrats SLA bien définis pour la **perte de données**, la **disponibilité**, la **latence à 99,99 %**, le **débit** et la **cohérence** pour la base de données dans son ensemble, quel que soit le nombre de régions associées à celle-ci.  

## <a id="LatencyGuarantees"></a>Garanties de latence
Le principal avantage d’un service de base de données mondialement distribué comme Azure Cosmos DB est de proposer un accès à vos données ne présentant qu’une faible latence, n’importe où dans le monde. Azure Cosmos DB offre une latence faible garantie à 99,99 % pour diverses opérations de base de données. Le protocole de réplication qu’Azure Cosmos DB emploie garantit que les opérations de base de données (dans l’idéal, lectures et écritures) sont toujours effectuées dans la région locale du client. Le contrat SLA pour la latence Azure Cosmos DB inclut 99,99 % pour les lectures, les écritures indexées (de manière synchrone) et les requêtes pour différentes tailles de demande et de réponse. Les garanties de latence pour les écritures incluent les validations de quorum majoritaire durables dans le centre de données local.

### <a id="LatencyAndConsistency"></a>Relation de latence avec la cohérence 
Pour qu’un service mondialement distribué offre une cohérence forte dans une configuration mondialement distribuée, il doit effectuer une réplication synchrone des écritures ou des lectures interrégionales synchrones ; la vitesse de la lumière et la fiabilité du réseau étendu déterminent qu’une cohérence forte entraîne des latences élevées et une disponibilité réduite des opérations de base de données. Par conséquent, pour garantir des temps de latence faibles à 99 % et une disponibilité de 99,99 % sur tous les comptes à région unique et tous les comptes à plusieurs régions avec cohérence souple, ainsi qu’une disponibilité de lecture de 99,999 % sur tous les comptes de base de données à plusieurs régions, le service doit utiliser la réplication asynchrone. Cela nécessite que le service offre également des [choix de cohérence bien définis et flexibles](consistency-levels.md) – plus faibles que puissants (pour offrir des garanties de latence faible et de disponibilité) et, dans l’idéal, plus puissants que la cohérence « finale » (pour proposer un modèle de programmation intuitif).

Azure Cosmos DB garantit qu’une opération de lecture n’est pas requise pour contacter des réplicas dans plusieurs régions, afin de fournir la garantie de niveau de cohérence spécifique. De même, la solution garantit qu’une opération d’écriture n’est pas bloquée pendant que les données sont répliquées dans toutes les régions (par exemple, les écritures sont répliquées de manière asynchrone dans différentes régions). Dans le cas de comptes de base de données multirégions, plusieurs niveaux de cohérence flexibles sont disponibles. 

### <a id="LatencyAndAvailability"></a>Relation de la latence avec la disponibilité 
La latence et la disponibilité sont les deux faces d’une même pièce. Dans un état stable, nous parlons de latence de l’opération et, en cas de défaillance, nous parlons de disponibilité. Du point de vue de l’application, une opération de base de données lente ne peut pas être différenciée d’une base de données qui n’est pas disponible. 

Pour distinguer une latence élevée de l’indisponibilité, Azure Cosmos DB fournit une limite supérieure absolue sur la latence de diverses opérations de base de données. Si l’opération de base de données prend plus de temps que la limite supérieure pour se terminer, Azure Cosmos DB renvoie une erreur de délai d’expiration. Le contrat SLA de disponibilité Azure Cosmos DB garantit que les délais d’expiration sont imputés au contrat SLA de disponibilité. 

### <a id="LatencyAndThroughput"></a>Relation de latence avec le débit
Azure Cosmos DB ne vous fait pas choisir entre la latence et le débit. La solution respecte le contrat de niveau de service pour la latence à 99,99 % et vous fournit le débit que vous avez configuré. 

## <a id="ConsistencyGuarantees"></a>Garanties de cohérence
Même si le [modèle de cohérence forte](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) constitue la référence en matière de programmabilité, celui-ci entraîne une latence élevée (dans un état stable) et une perte de disponibilité (en cas de défaillance). 

Azure Cosmos DB vous offre un modèle de programmation bien défini vous permettant d’analyser la cohérence des données répliquées. Afin de vous permettre de créer des applications multihébergées, les modèles de cohérence exposés par Azure Cosmos DB sont conçus pour être sans région, et indépendants de la région où les lectures et les écritures sont fournies. 

Le contrat SLA de cohérence d’Azure Cosmos DB garantit que 100 % des demandes de lecture respectent la garantie de cohérence pour le niveau de cohérence que vous avez demandé (le niveau de cohérence par défaut sur le compte de base de données ou la valeur remplacée lors de la demande). Une demande de lecture est considérée comme respectant le contrat de niveau de service de cohérence si toutes les garanties de cohérence associées au niveau de cohérence sont satisfaites. Le tableau suivant répertorie les garanties de cohérence qui correspondent aux niveaux de cohérence spécifiques proposés par Azure Cosmos DB.

**Garanties de cohérence associées à un niveau de cohérence donné dans Azure Cosmos DB**

<table>
    <tr>
        <td><strong>Niveau de cohérence</strong></td>
        <td><strong>Caractéristiques de la cohérence</strong></td>
        <td><strong>CONTRAT SLA</strong></td>
    </tr>
    <tr>
        <td rowspan="3">session</td>
        <td>Lecture de votre propre écriture</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Lecture unitone</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Préfixe cohérent</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td rowspan="3">Bounded staleness (En fonction de l'obsolescence)</td>
        <td>Lecture unitone (au sein d’une région)</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Préfixe cohérent</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Obsolescence limitée &lt; K, T</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Préfixe cohérent</td>
        <td>Préfixe cohérent</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Remarque</td>
        <td>Linéarisable</td>
        <td>100 %</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Relation de la cohérence avec la disponibilité
Le [résultat d’impossibilité](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) du [théorème CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prouve qu’il est en effet impossible que le système reste disponible et offre une cohérence linéarisable en cas de défaillance. Les services de base de données doivent choisir d’être CP ou AP. Les systèmes CP renoncent à la disponibilité en faveur d’une cohérence linéarisable, tandis que les systèmes AP renoncent à la [cohérence linéarisable](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) en faveur de la disponibilité. Azure Cosmos DB n’enfreint jamais le niveau de cohérence demandé, ce qui en fait un système formellement CP. Toutefois, dans la pratique, la cohérence n’est pas une proposition à prendre ou à laisser : il existe plusieurs modèles de cohérence bien définis dans le spectre de cohérence entre linéarisable et cohérence finale. Dans Azure Cosmos DB, nous avons essayé d’identifier plusieurs des modèles de cohérence assouplis avec une applicabilité au monde réel et un modèle de programmation intuitif. Azure Cosmos DB vous évite de faire des compromis en matière de cohérence et de disponibilité en proposant [plusieurs niveaux de cohérence souples bien définis](consistency-levels.md), ainsi qu’une disponibilité de 99,99 % sur tous les comptes à région unique et à plusieurs régions avec cohérence souple, et une disponibilité de lecture de 99,999 % sur tous les comptes de base de données à plusieurs régions. 

### <a id="ConsistencyAndAvailability"></a>Relation de la cohérence avec la latence
Une variation plus complète du CAP a été proposée par le professeur Daniel Abadi ; elle est appelée [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), qui constitue également un compromis en termes de latence et de cohérence dans un état stable. Elle indique que, dans un état stable, le système de base de données doit choisir entre la cohérence et la latence. Avec plusieurs modèles de cohérence assouplis (pris en charge par la réplication asynchrone et les quorums de lecture et d’écriture locaux), Azure Cosmos DB garantit que toutes les lectures et écritures sont effectuées dans les régions de lecture et d’écriture locales, respectivement.  Ainsi, Azure Cosmos DB offre des garanties de faible latence dans la région pour les niveaux de cohérence.  

### <a id="ConsistencyAndThroughput"></a>Relation de la cohérence avec le débit
Étant donné que l’implémentation d’un modèle de cohérence spécifique varie selon le choix d’un [type de quorum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), le débit varie également selon le choix de la cohérence. Par exemple, dans Azure Cosmos DB, la charge d’unités de requête (RU) pour les lectures fortement cohérentes représente environ le double de celle des lectures cohérentes. Dans ce cas, vous devez provisionner le double de RU dans votre collection pour obtenir le même débit.
 
**Relation de la capacité de lecture pour un niveau de cohérence spécifique dans Azure Cosmos DB**

![Relation entre cohérence et débit](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Garanties de débit 
Azure Cosmos DB vous permet, en toute flexibilité, de mettre à l’échelle le débit, ainsi que le stockage, dans différentes régions en fonction de la demande. 

**Une collection Azure Cosmos DB unique, partitionnée (sur trois partitions) et distribuée dans trois régions Azure**

![Collections distribuées et partitionnées d’Azure Cosmos DB](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Une collection Azure Cosmos DB est distribuée dans deux dimensions : au sein d’une région, puis entre les régions. Voici comment procéder : 

* Dans une région unique, une collection Azure Cosmos DB est mise à l’échelle en termes de partitions de ressources. Chaque partition de ressources gère un ensemble de clés et est fortement cohérente et hautement disponible en vertu de la réplication de machines d’état parmi un jeu de réplicas. Azure Cosmos DB est un système entièrement régi par les ressources où une partition de ressources est chargée de fournir sa part du débit pour le budget de ressources système qui lui est alloué. La mise à l’échelle d’une collection Azure Cosmos DB est complètement transparente : Azure Cosmos DB gère les partitions de ressources, et les fractionne et les fusionne en fonction des besoins. 
* Chaque partition de ressources est ensuite distribuée dans plusieurs régions. Les partitions de ressources possédant le même jeu de clés dans différentes régions forment le jeu de partitions (voir [figure précédente](#ThroughputGuarantees)).  Les partitions de ressources au sein d’un jeu de partitions sont coordonnées à l’aide de la réplication de machines d’état dans les différentes régions. Selon le niveau de cohérence configuré, les partitions de ressources au sein d’un jeu de partitions sont configurées dynamiquement à l’aide de différentes topologies (étoile, cascade, arborescence, etc.). 

Grâce à une gestion des partitions très réactive, à l’équilibrage de charge et à une gouvernance stricte des ressources, Azure Cosmos DB vous permet des mises à l’échelle flexibles dans plusieurs régions Azure sur une collection Azure Cosmos DB. La modification du débit sur une collection est une opération d’exécution dans Azure Cosmos DB ; comme pour d’autres opérations de base de données, Azure Cosmos DB garantit la limite supérieure absolue de la latence pour votre demande de modification du débit. Par exemple, la figure suivante montre la collection d’un client dont le débit est configuré en toute flexibilité (1 à 10 millions de demandes/s entre deux régions) en fonction de la demande.
 
**Collection d’un client dont le débit est configuré en toute flexibilité (1 à 10 millions de demandes/s)**

![Débit configuré en toute flexibilité d’Azure Cosmos DB](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Relation du débit avec la cohérence 
Identique à la[Relation de la cohérence avec le débit](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relation du débit avec la disponibilité
Azure Cosmos DB maintient sa disponibilité lorsque des modifications sont apportées au débit. Azure Cosmos DB gère en toute transparence les partitions (notamment les opérations de fractionnement, de fusion et de clonage) et s’assure que les opérations n’altèrent pas la performance ou la disponibilité, tandis que l’application augmente ou diminue le débit en toute flexibilité. 

## <a id="AvailabilityGuarantees"></a>Garanties de disponibilité
Azure Cosmos DB propose un contrat SLA avec une disponibilité à 99,99 % pour tous les comptes à région unique et à plusieurs régions avec cohérence souple, ainsi qu’une disponibilité de lecture à 99,999 % pour tous les comptes de base de données à plusieurs régions. Comme indiqué précédemment, les garanties de disponibilité d’Azure Cosmos DB incluent une limite supérieure absolue de la latence pour toutes les opérations de données et de plan de contrôle. Les garanties de disponibilité sont fermes et ne changent pas avec le nombre de régions ou la distance géographique entre les régions. Les garanties de disponibilité s’appliquent au basculement manuel et automatique. Azure Cosmos DB offre des API multihébergement transparentes qui garantissent que votre application fonctionne sur les points de terminaison logiques et achemine en toute transparence les demandes vers la nouvelle région en cas de basculement. En d’autres termes, votre application ne doit pas être redéployée lors du basculement régional, et les contrats de niveau de service de disponibilité sont maintenus.

### <a id="AvailabilityAndConsistency"></a>Relation de la disponibilité avec la cohérence, la latence et le débit
La relation de la disponibilité avec la cohérence, la latence et le débit est décrite dans [Relation de la cohérence avec la disponibilité](#ConsistencyAndAvailability), [Relation de la latence avec la disponibilité](#LatencyAndAvailability) et [Relation du débit avec la disponibilité](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Garanties et comportement du système en cas de « perte de données »
Dans Azure Cosmos DB, chaque partition (d’une collection) est rendue hautement disponible par un certain nombre de réplicas, répartis entre 10 à 20 domaines d’erreur au moins. Toutes les écritures sont validées de façon synchrone et durablement par un quorum majoritaire de réplicas avant d’être confirmées au client. La réplication asynchrone est appliquée de manière coordonnée entre les partitions réparties dans plusieurs régions. Azure Cosmos DB garantit qu’il n’y a aucune perte de données en cas de basculement manuel initié par un locataire. Pendant le basculement automatique, Azure Cosmos DB garantit une limite supérieure égale à l’intervalle d’obsolescence limitée configuré pour la fenêtre de perte de données dans le cadre de son contrat SLA.

## <a id="CustomerFacingSLAMetrics"></a>Mesures de contrat de niveau de service orientées client
Azure Cosmos DB expose en toute transparence les mesures de débit, de latence, de cohérence et de disponibilité. Ces mesures sont accessibles par programme et dans le portail Azure (voir la figure suivante). Vous pouvez également définir des alertes sur différents seuils à l’aide d’Azure Application Insights.
 
**Mesures de cohérence, de latence, de débit et de disponibilité disponibles en toute transparence pour chaque locataire**

![Mesures du contrat SLA visible par le client d’Azure Cosmos DB](./media/distribute-data-globally/customer-slas.png)

## <a id="Next Steps"></a>Étapes suivantes
* Pour mettre en œuvre la réplication mondiale sur votre compte Azure Cosmos DB à l’aide du portail Azure, consultez [Configuration de la distribution mondiale d’Azure Cosmos DB à l’aide de l’API DocumentDB](tutorial-global-distribution-sql-api.md).
* Pour savoir comment implémenter des architectures multimaîtres avec Azure Cosmos DB, consultez [Architectures de base de données multimaîtres répliquées de façon globale avec Azure Cosmos DB](multi-region-writers.md).
* Pour plus d’informations sur les basculements automatiques et manuels de tâches dans Azure Cosmos DB, consultez [Basculements régionaux automatiques pour la continuité des activités dans Azure Cosmos DB](regional-failover.md).

## <a id="References"></a>Références
1. Eric Brewer. [Towards Robust Distributed Systems](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) (Vers des systèmes distribués robustes)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (CAP douze ans plus tard : en quoi les règles ont-elles changé ?)
3. Gilbert, Lynch. - [Brewer&amp;#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Conjecture et faisabilité de services web cohérents, disponibles et tolérant le partitionnement)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Compromis en termes de cohérence dans la conception des systèmes de base de données distribués modernes)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Arrêtez d’appeler les bases de données CP ou AP)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Probabilités en fonction de l’obsolescence limitée (PBS) pour les quorums partiels pratiques)
7. Naor and Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Charge, capacité et disponibilité dans les systèmes de quorum)
8. Herlihy and Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linéarisabilité : Une condition d’exactitude pour les objets simultanés)
9. [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
