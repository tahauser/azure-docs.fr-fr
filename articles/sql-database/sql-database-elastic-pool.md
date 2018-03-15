---
title: "Gérer plusieurs bases de données SQL avec les pools élastiques - Azure | Microsoft Docs"
description: "Gérez et mettez à l’échelle plusieurs bases de données SQL (des centaines et des milliers) avec des pools élastiques. Un prix unique pour des ressources que vous pouvez distribuer là où vous en avez besoin."
keywords: "plusieurs bases de données, ressources des bases de données, performances des bases de données"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 03/02/2018
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.openlocfilehash: 917b5cdd82b44c1993eebd16634257d5a6518c96
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données Microsoft Azure SQL

Les pools élastiques SQL Database représentent une solution simple et rentable de gestion et de mise à l’échelle de plusieurs bases de données qui ont des demandes d’utilisation variables et imprévisibles. Les bases de données d’un pool élastique se trouvent sur un seul serveur Azure SQL Database et partagent un nombre défini de ressources ([DTU élastiques](sql-database-what-is-a-dtu.md) (eDTU)) à prix fixe. Les pools élastiques dans Azure SQL Database permettent aux développeurs SaaS d’optimiser le rapport performance/prix pour un groupe de bases de données dans un budget prescrit tout en offrant une élasticité des performances pour chaque base de données. 

## <a name="what-are-sql-elastic-pools"></a>Que sont les pools élastiques SQL ? 

Les développeurs SaaS créent des applications qui reposent sur des couches de données à grande échelle composées de plusieurs bases de données. Un modèle d’application courant consiste à approvisionner une base de données pour chaque client. Toutefois, les modèles d’utilisation sont souvent imprévisibles et varient d’un client à l’autre, et il est difficile de prévoir les besoins en ressources de chaque utilisateur de base de données spécifique. Vous disposez généralement de deux options : 

- Sur-approvisionner les ressources en fonction de l’utilisation maximale et payer trop, ou
- Sous-approvisionner pour réduire les coûts, au détriment des performances et de la satisfaction des clients au moment des pics d’utilisation. 

Les pools élastiques résolvent ce problème en vous assurant que les bases de données disposent des ressources de performances nécessaires au moment où elles en ont besoin. Ils représentent un mécanisme simple d’allocation de ressources avec un budget prévisible. Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Les pools élastiques permettent au développeur d’acheter des [unités de transaction de base de données élastiques](sql-database-what-is-a-dtu.md) (eDTU) pour un pool partagé par plusieurs bases de données afin de s’adapter aux périodes imprévisibles d’utilisation par les bases de données. Le besoin en eDTU d’un pool est déterminé par l'utilisation globale de ses bases de données. Le nombre d’eDTU disponible pour le pool est contrôlé par le budget du développeur. Le développeur ajoute simplement des bases de données au pool, définit le nombre minimal et maximal d’eDTU pour les bases de données, puis définit l’eDTU du pool en fonction de son budget. Un développeur peut utiliser des pools pour faire évoluer en toute transparence son service en passant d’une lean startup à une entreprise mature à une vitesse sans cesse croissante.

Au sein du pool, les différentes bases de données peuvent en toute souplesse s’adapter automatiquement en fonction des paramètres définis. Si la charge est élevée, une base de données peut consommer plus d’eDTU pour répondre à la demande. Les bases de données soumises à des charges légères en consomment moins, tandis que celles qui ne sont soumises à aucune charge n’en consomment pas du tout. L’approvisionnement des ressources pour l’ensemble du pool plutôt que pour des bases de données uniques simplifie vos tâches de gestion. En outre, vous disposez d’un budget prévisible pour le pool. Des eDTU peuvent être ajoutées à un pool existant sans temps d’arrêt de la base de données, à ceci près que les bases de données peuvent être déplacées pour fournir les ressources de calcul supplémentaires à la nouvelle réservation d’eDTU. De même, si les eDTU supplémentaires ne sont plus nécessaires, ils peuvent être supprimés à partir d’un pool existant à tout moment. De plus, vous pouvez ajouter des bases de données au pool ou en retirer. Si une base de données finit par sous-utiliser les ressources, retirez-la.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quand devez-vous envisager d’utiliser un pool élastique SQL Database ?

Les pools sont idéaux dans le cas de nombreuses bases de données avec des modèles d’utilisation spécifiques. Pour une base de données indiquée, ce modèle se caractérise par une faible utilisation moyenne avec des pics d'utilisation relativement rares.

Plus vous ajoutez de bases de données à un pool, plus vous faites d'économies. En fonction de votre modèle d’utilisation de l’application, il est possible de faire des économies avec seulement deux bases de données S3. 

Les sections suivantes vous aident à comprendre comment évaluer si votre collection de bases de données spécifique peut tirer profit de l’utilisation d’un pool. Les exemples utilisent les pools Standard, mais ces principes s’appliquent également aux pools De base et Premium.

### <a name="assessing-database-utilization-patterns"></a>Évaluation des modèles d'utilisation de base de données

La figure suivante montre l’exemple d'une base de données qui est très souvent inactive, mais qui connaît de temps en temps des pics d’activité. Il s’agit d’un modèle d’utilisation particulièrement adapté à un pool :

   ![une base de données unique adaptée à un pool](./media/sql-database-elastic-pool/one-database.png)

Pour la période de cinq minutes illustrée, DB1 culmine à 90 DTU, mais son utilisation moyenne totale est inférieure à cinq DTU. Un niveau de performance S3 est requis pour exécuter cette charge de travail dans une base de données unique. Cependant, cela laisse la plupart des ressources inutilisées pendant les périodes de faible activité.

Un pool permet de partager ces DTU inutilisées entre plusieurs bases de données, ce qui réduit la quantité totale de DTU nécessaires et le coût global.

En s'appuyant sur l'exemple précédent, supposons qu'il y ait des bases de données supplémentaires avec des modèles d'utilisation similaires comme DB1. Dans les deux figures ci-dessous, l’utilisation de quatre bases de données et celle de 20 bases de données sont représentées sur le même graphique pour montrer que leur utilisation ne se chevauche pas dans le temps :

   ![quatre bases de données avec un modèle d’utilisation adapté à un pool](./media/sql-database-elastic-pool/four-databases.png)

   ![vingt bases de données avec un modèle d’utilisation adapté à un pool](./media/sql-database-elastic-pool/twenty-databases.png)

L’utilisation globale des DTU pour les 20 bases de données est illustrée par la ligne noire dans la figure ci-dessus. Cela montre que l'utilisation globale des DTU ne dépasse jamais 100 DTU et que les 20 bases de données peuvent partager 100 eDTU sur cette période. Le nombre de DTU est diminué par 20 et le prix par 13 par rapport au placement de chacune des bases de données dans des niveaux de performances S3 pour les bases de données uniques.

Cet exemple est idéal pour les raisons suivantes :

* Il existe de grandes différences entre les pics d’utilisation et l'utilisation moyenne par base de données. 
* Les pics d’utilisation de chaque base de données se produisent à différents moments dans le temps.
* Les eDTU sont partagées entre plusieurs bases de données.

Le prix d’un pool dépend des eDTU du pool. Alors que le prix unitaire d’une eDTU pour un pool est 1,5 fois supérieur au prix unitaire d’une DTU pour une base de données unique, les **eDTU de pool peuvent être partagées avec de nombreuses bases de données ; un nombre moins important d’eDTU est donc requis au total**. Ces différences en matière de prix et de partage des eDTU constituent la base du potentiel d'économies que les pools peuvent présenter. 

Les règles élémentaires suivantes relatives au nombre de bases de données et à l’utilisation des bases de données permettent de s’assurer qu’un pool coûte moins cher que l’utilisation de niveaux de performances pour des bases de données uniques.

### <a name="minimum-number-of-databases"></a>Nombre minimal de bases de données

Si la somme des DTU des niveaux de performances pour une base de données unique est égale à plus de 1,5 x les eDTU nécessaires pour le pool, un pool élastique est plus rentable. Pour plus d’informations sur les tailles disponibles, consultez l’article [Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

***Exemple***<br>
Au moins deux bases de données S3 ou au moins 15 bases de données S0 sont nécessaires pour qu’un pool de 100 eDTU soit plus rentable que l’utilisation de niveaux de performances pour des bases de données uniques.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Nombre maximal de bases de données connaissant un pic simultané

En partageant les eDTU, toutes les bases de données d’un pool ne peuvent pas utiliser simultanément les eDTU jusqu'à la limite disponible lors de l'utilisation de niveaux de performances pour les bases de données uniques. Plus le nombre de bases de données connaissant un pic simultané est faible, plus le nombre d’eDTU du pool peut être revu à la baisse et plus le pool devient rentable. En général, pas plus de 2/3 (ou 67 %) des bases de données du pool doivent connaître un pic simultané à leur limite d’eDTU.

***Exemple***<br>
Pour réduire les coûts pour trois bases de données S3 dans un pool de 200 eDTU, au moins deux de ces bases de données peuvent connaître un pic simultané au niveau de leur utilisation. Sinon, si plus de deux de ces quatre bases de données S3 connaissent un pic simultané, le pool devra être redimensionné à plus de 200 eDTU. Si le pool est redimensionné à plus de 200 eDTU, vous devez ajouter plusieurs bases de données S3 au pool pour maintenir des coûts inférieurs aux niveaux de performances pour les bases de données uniques.

Notez que cet exemple ne tient pas compte de l'utilisation des autres bases de données dans le pool. Si toutes les bases de données connaissent une utilisation à un moment donné, moins de 2/3 (ou 67 %) des bases de données peuvent connaître un pic simultané.

### <a name="dtu-utilization-per-database"></a>Utilisation de DTU par base de données
Une différence importante entre le pic d’utilisation et l'utilisation moyenne d’une base de données indique de longues périodes de faible utilisation et de courtes périodes d'utilisation intensive. Ce modèle d'utilisation est idéal pour partager des ressources entre les bases de données. Une base de données doit être envisagée pour un pool quand son pic d’utilisation est environ 1,5 fois supérieur à son utilisation moyenne.

***Exemple***<br>
Une base de données S3 qui culmine à 100 DTU et qui utilise en moyenne 67 DTU ou moins est un bon candidat pour le partage des eDTU dans un pool. Sinon, une base de données S1 qui culmine à 20 DTU et qui utilise en moyenne 13 DTU ou moins est un bon candidat à un pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Comment choisir la bonne taille de pool ?

La taille optimale pour un pool dépend du nombre global d’eDTU et des ressources de stockage nécessaires pour toutes les bases de données du pool. Cela implique de déterminer la plus grande des deux quantités suivantes :

* Nombre maximal de DTU que se partagent toutes les bases de données du pool.
* Nombre maximal d’octets de stockage que se partagent toutes les bases de données du pool.

Pour plus d’informations sur les tailles disponibles, consultez l’article [Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

La base de données SQL évalue automatiquement l’historique d’utilisation en ressources des bases de données dans un serveur de base de données SQL existant et recommande la configuration de pool appropriée dans le portail Azure. En plus des recommandations, une expérience intégrée estime l’utilisation d’eDTU pour un groupe personnalisé de bases de données sur le serveur. Cela vous permet de faire une analyse de scénarios en ajoutant de façon interactive des bases de données au pool et en les supprimant pour obtenir l’analyse de l’utilisation des ressources et des conseils de dimensionnement avant de valider vos modifications. Pour afficher une procédure, consultez [Surveiller, gérer et dimensionner un pool élastique](sql-database-elastic-pool-manage-portal.md).

Dans les cas où vous ne pouvez pas utiliser les outils, la procédure pas à pas suivante peut vous aider à estimer si un pool est plus économique que les bases de données uniques :

1. Estimez les eDTU nécessaires pour le pool comme suit :

   MAX(<*nombre total de bases de données* X *utilisation DTU moyenne par base de données*>,<br>
   <*Nombre de bases de données connaissant un pic simultané* X *utilisation DTU maximale par base de données*)
2. Estimez l’espace de stockage nécessaire pour le pool en ajoutant le nombre d’octets nécessaires pour toutes les bases de données du pool. Déterminez ensuite la taille du pool d’eDTU qui fournit cette quantité de stockage. Pour connaître les limites de stockage du pool en fonction de la taille du pool d’eDTU, consultez l’article [Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).
3. Prenez la plus grande des estimations d’eDTU de l’étape 1 et de l’étape 2.
4. Consultez la [page de tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/) et recherchez la plus petite taille du pool d’eDTU qui est supérieure à l’estimation de l’étape 3.
5. Comparez le prix du pool trouvé à l’étape 5 à celui de l’utilisation des niveaux de performances appropriés pour les bases de données uniques.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Utilisation d’autres fonctionnalités SQL Database avec des pools élastiques

### <a name="elastic-jobs-and-elastic-pools"></a>Travaux élastiques et pools élastiques

Un pool simplifie les tâches de gestion grâce à l’exécution des scripts dans des **[tâches élastiques](sql-database-elastic-jobs-overview.md)**. Un travail élastique élimine pratiquement le caractère fastidieux d’un nombre élevé de bases de données. Pour commencer, consultez l’article [Prise en main de Tâches de bases de données élastiques](sql-database-elastic-jobs-getting-started.md).

Pour en savoir plus sur les autres outils de base de données permettant d’utiliser plusieurs bases de données, consultez l’article [Montée en charge avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Options de continuité de l’activité pour les bases de données d’un pool élastique
Les bases de données regroupées prennent généralement en charge les mêmes [fonctionnalités de continuité d’activité](sql-database-business-continuity.md) que celles disponibles pour les bases de données uniques.

- **Limite de restauration dans le temps** : la limite de restauration dans le temps utilise les sauvegardes automatiques de base de données pour récupérer une base de données d’un pool à un moment précis dans le temps. Voir [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Géorestauration** : la géorestauration constitue l’option de récupération par défaut lorsqu’une base de données est indisponible en raison d’un incident survenu dans la région où elle est hébergée. Voir [Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire](sql-database-disaster-recovery.md)

- [Géoréplication active](sql-database-geo-replication-overview.md) : pour les applications qui ont des exigences de récupération plus agressives que ce qu’offre la géorestauration, configurez la **géoréplication active**.

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Gérer des pools élastiques et des bases de données à l’aide du portail Azure

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Création d’un pool élastique SQL Database à l’aide du portail Azure

Vous pouvez créer un pool élastique dans le portail Azure de deux façons. Vous pouvez le faire à partir de zéro si vous connaissez la configuration de pool souhaitée ou si vous commencez par une recommandation issue du service. La base de données SQL est dotée d’une intelligence intégrée qui recommande une configuration de pool élastique si cela semble plus économique pour vous en fonction de la dernière télémétrie d’utilisation de vos bases de données. 

Créer un pool élastique à partir d’une page de serveur existant dans le portail est le moyen le plus simple pour déplacer des bases de données existantes dans un pool élastique. Vous pouvez également créer un pool élastique en recherchant **pool élastique SQL** sur la **Place de marché** ou en cliquant sur **+Ajouter** dans la page Pools élastiques SQL. Ce workflow d’approvisionnement de pool vous permet d’indiquer un nouveau serveur ou un serveur existant.

> [!NOTE]
> Vous pouvez créer plusieurs pools sur un serveur, mais il est impossible d’ajouter des bases de données de différents serveurs dans le même pool.
> 

Le niveau de tarification du pool détermine les fonctionnalités disponibles pour les bases de données élastiques dans le pool, ainsi que le nombre maximal d'eDTU (eDTU MAX) et le stockage (Go) disponibles pour chaque base de données. Pour plus d’informations, voir [Limites de ressources pour les pools élastiques](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Pour modifier le niveau tarifaire du pool, cliquez sur **Niveau tarifaire**, sur le niveau tarifaire de votre choix, puis sur **Sélectionner**.

> [!IMPORTANT]
> Une fois que vous avez choisi le niveau tarifaire et validé vos modifications en cliquant sur **OK** à la dernière étape, vous ne pouvez plus modifier le niveau tarifaire du pool. Pour modifier le niveau de tarification d’un pool élastique existant, créez un pool élastique dans le niveau de tarification souhaité et migrez les bases de données vers ce nouveau pool.
>

Si les bases de données que vous utilisez contiennent suffisamment de données de télémétrie d’historique d’utilisation, le graphique **Utilisation estimée des eDTU et des Go** et le graphique à barres **Utilisation effective des eDTU** sont mis à jour pour vous aider à prendre des décisions en termes de configuration. Le service peut également vous envoyer un message de recommandation pour vous aider à rectifier la taille du pool.

Le service SQL Database évalue l’historique d’utilisation et recommande un ou plusieurs pools lorsque cela est plus rentable que d’utiliser des bases de données uniques. Chaque recommandation est configurée avec un sous-ensemble unique de bases de données du serveur qui correspond le mieux au pool.

![pool recommandé](./media/sql-database-elastic-pool-create-portal/recommended-pool.png) 

La recommandation relative au pool comprend les éléments suivants :

- Niveau de tarification du pool (De base, Standard ou Premium)
- Valeur **eDTU du pool** appropriée (également appelée eDTU max par pool)
- Paramètres **eDTU max** et **eDTU min** par base de données
- Liste des bases de données recommandées pour le pool

> [!IMPORTANT]
> Le service prend en compte les 30 derniers jours de télémétrie lors de la recommandation de pools. Pour qu’une base de données soit considérée comme candidate à un pool élastique, elle doit exister depuis au moins 7 jours. Les bases de données qui figurent déjà dans un pool élastique ne sont pas considérées comme candidates pour les recommandations de pool élastique.
>

Le service évalue les besoins en ressources et la rentabilité du déplacement des bases de données uniques dans chaque niveau de service vers des pools du même niveau. Par exemple, toutes les bases de données Standard d’un serveur sont évaluées pour leur compatibilité avec un pool élastique Standard. Cela signifie que le service n'effectue aucune recommandation multiniveau telle que le déplacement d'une base de données Standard dans un pool Premium.

Après avoir ajouté des bases de données au pool, des recommandations sont générées de façon dynamique en fonction de l’historique d’utilisation des bases de données que vous avez sélectionnées. Ces recommandations sont affichées dans le graphique d’utilisation d’eDTU et de Go, ainsi que dans la bannière de recommandation en haut de la page **Configurer le pool**. Ces recommandations sont destinées à vous aider à créer un pool élastique optimisé pour vos bases de données spécifiques.

![Recommandations dynamiques](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

### <a name="manage-and-monitor-an-elastic-pool"></a>Gérer et surveiller un pool élastique

Dans le portail Azure, vous pouvez surveiller l’utilisation d’un pool élastique et des bases de données que contient ce pool. Vous pouvez également apporter un ensemble de modifications à votre pool élastique et soumettre toutes les modifications en même temps. Ces modifications incluent l’ajout ou la suppression de bases de données, ainsi que le changement des paramètres du pool élastique ou des bases de données.

Le graphique suivant montre un exemple de pool élastique. L’affichage inclut :

* Des graphiques pour la surveillance de l’utilisation des ressources du pool élastique et des bases de données que contient ce pool.
* Le bouton **Configurer le pool** pour apporter des modifications au pool élastique.
* Le bouton **Créer une base de données**, qui crée une base de données et l’ajoute au pool élastique actuel.
* Des tâches élastiques, qui vous aident à gérer un grand nombre de bases de données en exécutant des scripts Transact SQL sur toutes les bases de données d’une liste.

![Affichage du pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

Vous pouvez accéder à un pool particulier pour consulter l’utilisation des ressources de ce dernier. Par défaut, le pool est configuré pour afficher l’utilisation du stockage et des eDTU au cours de la dernière heure. Le graphique peut être configuré pour afficher d’autres métriques sur différentes plages de temps. Cliquez sur le graphique **Utilisation des ressources** sous **Surveillance du pool élastique** pour afficher une vue détaillée des métriques spécifiées par rapport à la fenêtre de temps indiquée.

![Surveillance du pool élastique](./media/sql-database-elastic-pool-manage-portal/basic-2.png)

![Page Métrique](./media/sql-database-elastic-pool-manage-portal/metric.png)

### <a name="to-customize-the-chart-display"></a>Pour personnaliser l’affichage du graphique

Vous pouvez modifier le graphique et la page Métrique pour afficher d’autres métriques, telles que le pourcentage d’UC, le pourcentage d’E/S des données et le pourcentage d’E/S des fichiers journaux utilisés.

![Cliquez sur Modifier](./media/sql-database-elastic-pool-manage-portal/edit-metric.png)

Sur le formulaire **Modifier le graphique**, vous pouvez sélectionner une plage de temps (dernière heure, aujourd’hui ou semaine dernière) ou cliquer sur **Personnalisé** pour sélectionner la plage de temps de votre choix au cours des deux dernières semaines. Vous pouvez choisir entre un graphique en courbes ou un graphique à barres, puis sélectionner les ressources à surveiller.

> [!Note]
> Seules les mesures présentant la même unité peuvent figurer simultanément dans le graphique. Par exemple, si vous sélectionnez « Pourcentage eDTU », vous pouvez sélectionner d’autres mesures de pourcentage.
>

![Cliquez sur Modifier](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

### <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Gérer et surveiller des bases de données dans un pool élastique

Il est également possible de surveiller des bases de données individuelles pour identifier des problèmes potentiels. Sous **Surveillance de la base de données élastique**, vous trouverez un graphique qui affiche les métriques de cinq bases de données. Par défaut, le graphique affiche les 5 premières bases de données du pool en termes d’utilisation moyenne des eDTU au cours de la dernière heure. 

![Surveillance du pool élastique](./media/sql-database-elastic-pool-manage-portal/basic-3.png)

Cliquez sur **l’utilisation des eDTU pour les bases de données au cours de la dernière heure** sous **Surveillance de la base de données élastique**. Cette opération ouvre **Utilisation des ressources de base de données** et fournit une vue détaillée de l’utilisation de la base de données dans le pool. À l’aide de la grille située dans la partie inférieure de la page, vous pouvez sélectionner les bases de données de votre choix dans le pool (jusqu’à 5 bases de données) pour afficher leur utilisation dans le graphique. Vous pouvez également personnaliser les métriques et la plage de temps affichées dans le graphique en cliquant sur **Modifier le graphique**.

![Page Utilisation des ressources de base de données](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-view"></a>Pour personnaliser l’affichage

Vous pouvez modifier le graphique pour sélectionner une plage de temps (dernière heure ou dernières 24 heures) ou cliquer sur **Personnalisé** pour sélectionner un autre jour au cours des 2 dernières semaines.

![Cliquez sur Modifier le graphique](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

![Cliquez sur Personnalisé](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

Vous pouvez également cliquer sur la liste déroulante **Comparer les bases de données par** pour modifier la métrique à utiliser lors de la comparaison des bases de données.

![Modifiez le graphique](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Pour sélectionner les bases de données à surveiller

Dans la liste des bases de données de la page **Utilisation des ressources de base de données**, vous pouvez rechercher des bases de données spécifiques en parcourant les différentes pages de la liste ou en tapant leur nom. Activez la case à cocher pour sélectionner la base de données.

![Recherchez les bases de données à surveiller](./media/sql-database-elastic-pool-manage-portal/select-dbs.png)


### <a name="add-an-alert-to-an-elastic-pool-resource"></a>Ajouter une alerte à une ressource de pool élastique

Vous pouvez ajouter des règles à un pool élastique qui envoient des messages électroniques à des personnes ou des chaînes d’alertes à des points de terminaison d’URL quand le pool élastique atteint un seuil d’utilisation que vous configurez.

**Pour ajouter une alerte à une ressource :**

1. Cliquez sur le graphique **Utilisation des ressources** pour ouvrir la page **Métrique**. Cliquez sur **Ajouter une alerte**, puis renseignez les informations dans la page **Ajouter une règle d’alerte** (la valeur **Ressource** est automatiquement définie comme étant le pool que vous utilisez).
2. Saisissez un **nom** et une **description** qui identifient l’alerte pour vous et les destinataires.
3. Choisissez une **métrique** pour l’alerte dans la liste.

   Le graphique affiche dynamiquement l’utilisation des ressources pour cette métrique pour vous aider à choisir un seuil.

4. Choisissez une **condition** (supérieur à, inférieur à, etc.) et un **seuil**.
5. Choisissez une **Période** de temps pendant laquelle la règle de métrique doit être satisfaite pour que l’alerte se déclenche.
6. Cliquez sur **OK**.

Pour plus d'informations, voir [Créer des alertes SQL Database dans le portail Azure](sql-database-insights-alerts-portal.md).

### <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique

Vous pouvez ajouter ou supprimer des bases de données dans un pool existant. Les bases de données peuvent se trouver dans d’autres pools. Toutefois, vous pouvez uniquement ajouter des bases de données qui se trouvent sur le même serveur logique.

 ![Cliquez sur Configurer le pool](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

![Cliquez sur Ajouter au pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

![Sélectionnez les bases de données à ajouter](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

![Ajouts de pool en attente](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

![Cliquez sur Enregistrer.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

### <a name="move-a-database-out-of-an-elastic-pool"></a>Déplacer une base de données hors d’un pool élastique

![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

### <a name="change-performance-settings-of-an-elastic-pool"></a>Modifier les paramètres de performances d'un pool élastique

Lorsque vous surveillez l’utilisation des ressources d’un pool élastique, vous pouvez constater que certains ajustements sont nécessaires. Les limites de performances ou de stockage du pool ont peut-être besoin d’être modifiées. Vous pouvez aussi vouloir modifier les paramètres des bases de données du pool. Vous pouvez modifier la configuration du pool à tout moment pour obtenir le meilleur compromis entre les performances et le coût. Pour plus d’informations, consultez l’article [Quand utiliser un pool élastique ?](sql-database-elastic-pool.md).

Pour modifier les eDTU ou les limites par pool et les eDTU par base de données :

![Utilisation des ressources du pool élastique](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Gérer des pools élastiques et des bases de données à l’aide de PowerShell

Pour créer et gérer les pools élastiques SQL Database avec Azure PowerShell, utilisez les cmdlets PowerShell suivants. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps). Pour créer et gérer des bases de données, des serveurs et des règles de pare-feu, consultez [Gérer les serveurs, les bases de données et les pare-feu SQL Azure à l’aide de PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell). 

> [!TIP]
> Pour obtenir des exemples de scripts PowerShell, consultez [Créer des pools élastiques et déplacer les bases de données entre les pools et en dehors d’un pool à l’aide de PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) et [Utiliser PowerShell pour surveiller et mettre à l’échelle un pool élastique SQL dans Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Crée un pool de bases de données élastique sur un serveur SQL logique.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Obtient les pools élastiques et leurs valeurs de propriété sur un serveur SQL logique.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modifie les propriétés d’un pool de bases de données élastique sur un serveur SQL logique. Par exemple, utilisez la propriété **StorageMB** pour modifier le stockage maximal d’un pool élastique.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Supprime un pool de bases de données élastique sur un serveur SQL logique.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Obtient l’état des opérations sur un pool élastique sur un serveur SQL logique.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crée une base de données dans un pool existant ou en tant que base de données seule. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtient une ou plusieurs bases de données.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique, en dehors de celui-ci ou entre des pools élastiques.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Supprime une base de données.|


> [!TIP]
> La création d’un grand nombre de bases de données dans un pool élastique peut prendre du temps si elle se fait par le biais du portail ou d’applets de commande PowerShell qui créent une seule base de données à la fois. Pour automatiser la création dans un pool élastique, consultez [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Gérer des pools élastiques et des bases de données à l’aide de l’interface de ligne de commande Azure

Pour créer et gérer un serveur des pools élastiques avec [Azure CLI](/cli/azure/overview), utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows. 

> [!TIP]
> Pour obtenir des exemples de scripts Azure CLI , consultez [Utiliser l’interface CLI afin de déplacer une base de données SQL Azure dans un pool élastique SQL](scripts/sql-database-move-database-between-pools-cli.md) et [Utiliser Azure CLI pour mettre un pool élastique SQL à l’échelle dans Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Applet de commande | Description |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Crée un pool élastique.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Renvoie une liste de pools élastiques dans un serveur.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Renvoie une liste des bases de données dans un pool élastique.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Inclut également les paramètres DTU de pool, les limites de stockage et les paramètres par base de données disponibles. Pour réduire les détails, les limites de stockage supplémentaires et les paramètres par base de données sont masqués par défaut.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Met à jour un pool élastique.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Supprime le pool élastique.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Gérer des bases de données dans les pools élastiques à l’aide de Transact-SQL

Pour créer et déplacer des bases de données dans les pools élastiques existants ou pour renvoyer des informations sur un pool élastique SQL Database avec Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. Pour créer et gérer des bases de données, des serveurs et des règles de pare-feu, consultez [Gérer les serveurs, les bases de données et les pare-feu SQL Azure à l’aide de Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Vous ne pouvez pas créer, mettre à jour ou supprimer un pool élastique SQL Database à l’aide de Transact-SQL. Vous pouvez ajouter ou supprimer des bases de données à partir d’un pool élastique, et vous pouvez utiliser des vues de gestion dynamiques pour renvoyer des informations sur les pools élastiques existants.
>

| Commande | Description |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crée une base de données dans un pool existant ou en tant que base de données seule. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Déplace une base de données dans un pool élastique, en dehors de celui-ci ou entre des pools élastiques.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Supprime une base de données.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Renvoie les statistiques d’utilisation de ressources pour tous les pools de bases de données élastiques dans un serveur logique. Pour chaque pool de bases de données élastique, il existe une ligne pour chaque fenêtre de création de rapports de 15 secondes (quatre lignes par minute). Cela inclut la consommation de stockage, le journal, les E/S, l’UC et l’utilisation de session/requête simultanée par toutes les bases de données du pool.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, le cas échéant, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, renvoie les informations au sujet de toutes les bases de données. Pour Azure SQL Data Warehouse, vous devez être connecté à la base de données MASTER.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Gérer des pools élastiques et des bases de données à l’aide de l’API REST

Pour créer et gérer des pools élastiques SQL Database, utilisez ces demandes d’API REST.

| Commande | Description |
| --- | --- |
|[Pools élastiques - Créer ou mettre à jour](/rest/api/sql/elasticpools/createorupdate)|Crée un pool élastique ou met à jour un pool élastique existant.|
|[Pools élastiques - Supprimer](/rest/api/sql/elasticpools/delete)|Supprime le pool élastique.|
|[Pools élastiques - Obtenir](/rest/api/sql/elasticpools/get)|Obtenir un pool élastique.|
|[Pools élastiques - Lister par serveur](/rest/api/sql/elasticpools/listbyserver)|Renvoie une liste de pools élastiques dans un serveur.|
|[Pools élastiques - Mettre à jour](/rest/api/sql/elasticpools/update)|Met à jour un pool élastique existant.|
|[Pools élastiques recommandés - Obtenir](/rest/api/sql/recommendedelasticpools/get)|Obtient un pool élastique recommandé.|
|[Pools élastiques recommandés - Lister par serveur](/rest/api/sql/recommendedelasticpools/listbyserver)|Retourne les pools élastiques recommandés.|
|[Pools élastiques recommandés - Lister par métrique](/rest/api/sql/recommendedelasticpools/listmetrics)|Retourne les métriques des pools élastiques recommandés.|
|[Activités de pool élastique](/rest/api/sql/elasticpoolactivities)|Retourne les activités de pool élastique.|
|[Activités de bases de données du pool élastique](/rest/api/sql/elasticpooldatabaseactivities)|Retourne l’activité sur les bases de données à l’intérieur d’un pool élastique.|
|[Bases de données - Créer ou mettre à jour](/rest/api/sql/databases/createorupdate)|Crée une base de données ou met à jour une base de données existante.|
|[Bases de données - Obtenir](/rest/api/sql/databases/get)|Obtient une base de données.|
|[Bases de données - Obtenir par pool élastique](/rest/api/sql/databases/getbyelasticpool)|Obtient une base de données à l’intérieur d’un pool élastique.|
|[Bases de données - Obtenir par pool élastique recommandé](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtient une base de données à l’intérieur d’un pool élastique recommandé.|
|[Bases de données - Lister par pool élastique](/rest/api/sql/databases/listbyelasticpool)|Renvoie une liste des bases de données dans un pool élastique.|
|[Bases de données - Lister par pool élastique recommandé](/rest/api/sql/databases/listbyrecommendedelasticpool)|Retourne une liste de bases de données à l’intérieur d’un pool élastique recommandé.|
|[Bases de données - Lister par serveur](/rest/api/sql/databases/listbyserver)|Retourne une liste de bases de données d’un serveur.|
|[Bases de données - Mettre à jour](/rest/api/sql/databases/update)|Met à jour une base de données existante.|

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez aussi regarder la vidéo [Formation vidéo Microsoft Virtual Academy sur les fonctions de bases de données élastiques dans Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Modèles de conception pour les applications SaaS mutualisées avec la base de données SQL Azure).
* Pour obtenir un didacticiel SaaS utilisant des pools élastiques, consultez [Présentation de l’application SaaS Wingtip](sql-database-wtp-overview.md).
