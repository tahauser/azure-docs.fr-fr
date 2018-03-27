---
title: Meilleures pratiques pour Azure SQL Data Warehouse | Microsoft Docs
description: Recommandations et meilleures pratiques que vous devez connaître pour développer des solutions pour Azure SQL Data Warehouse. Elles vous aideront à réussir.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 03/15/2018
ms.author: barbkess
ms.openlocfilehash: 53ad9f654c498f562d66de461a2a489895d0a46b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Meilleures pratiques pour Azure SQL Data Warehouse
Cet article rassemble les nombreuses meilleures pratiques qui vous permettront d’obtenir des performances optimales de votre Azure SQL Data Warehouse.  Certains des concepts de cet article sont basiques et faciles à expliquer, d’autres concepts sont plus avancés et nous ne faisons que les survoler dans cet article.  L’objectif de cet article est de vous donner quelques conseils de base et de vous informer des points importants à prendre en compte lorsque vous créez votre entrepôt de données.  Chaque section présente un concept et vous dirige ensuite vers des articles plus détaillés qui expliquent davantage le concept.

Si vous êtes novice avec Azure SQL Data Warehouse, ne laissez pas cet article vous submerger.  Les rubriques sont principalement présentées dans l’ordre d’importance.  Si vous commencez par vous intéresser aux premiers concepts, vous serez en bonne voie.  Dès que vous vous sentirez plus à l’aise avec SQL Data Warehouse, vous pourrez revenir consulter d’autres concepts.  Il faut peu de temps pour que tout devienne clair.

Pour le chargement des instructions, consultez [Guidance for loading data](guidance-for-loading-data.md) (Conseils relatifs au chargement des données).

## <a name="reduce-cost-with-pause-and-scale"></a>Réduire les coûts avec les opérations de suspension et de mise à l’échelle
Pour plus d’informations sur la réduction des coûts à l’aide de la suspension et de la mise à l’échelle, consultez la section [Gérer le calcul](sql-data-warehouse-manage-compute-overview.md). 


## <a name="maintain-statistics"></a>Mettre à jour les statistiques
Contrairement à SQL Server, qui détecte et crée ou met à jour les statistiques automatiquement dans les colonnes, SQL Data Warehouse nécessite une maintenance manuelle des statistiques.  Nous envisageons de changer ce comportement à l’avenir, mais en attendant, mettez à jour vos statistiques afin de garantir l’optimisation des plans SQL Data Warehouse.  Les plans créés par l’optimiseur sont aussi bons que les statistiques disponibles.  **La création de statistiques échantillonnées est un bon moyen de se familiariser avec la notion de statistiques.**  Il est également important de mettre à jour les statistiques car des modifications significatives affectent vos données.  Une méthode plus classique serait peut-être de mettre à jour vos statistiques tous les jours ou après chaque charge.  Lorsque vous créez et mettez des statistiques à jour, vous devez toujours faire un compromis entre les performances et les coûts. Si vous trouvez que la mise à jour de toutes vos statistiques est trop longue, vous souhaiterez peut-être sélectionner les colonnes qui possèdent des statistiques ou celles nécessitant une mise à jour fréquente.  Par exemple, vous pouvez mettre à jour des colonnes de date, où de nouvelles valeurs peuvent être ajoutées de façon quotidienne. **Vous bénéficierez de performances optimales en lançant des statistiques sur les colonnes impliquées dans les jointures, celles utilisées dans la clause WHERE et celles figurant dans GROUP BY.**

Voir aussi [Gestion des statistiques sur les tables][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="group-insert-statements-into-batches"></a>Regrouper des instructions INSERT dans des lots
Une charge unique dans une petite table à l’aide d’une instruction INSERT ou même un rechargement périodique d’une recherche peut répondre parfaitement à vos besoins grâce à une instruction comme `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Toutefois, si vous avez besoin charger des milliers ou des millions de lignes sur une même journée, vous constaterez que les instructions INSERTS singleton sont inadaptées.  Au lieu de cela, développez vos processus afin qu’ils écrivent dans un fichier et un autre processus arrive régulièrement pour charger ce fichier.

Voir aussi [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Utiliser PolyBase pour charger et exporter rapidement des données
SQL Data Warehouse prend en charge le chargement et l’exportation de données via plusieurs outils dont Azure Data Factory, PolyBase et BCP.  Pour les petits volumes de données où les performances ne sont pas essentielles, n’importe quel outil peut suffire à vos besoins.  Toutefois, lorsque vous chargez ou exportez de gros volumes de données ou si des performances de vitesse sont nécessaires, PolyBase représente le meilleur choix.  PolyBase est conçu pour tirer parti de l’architecture MPP (Massively Parallel Processing) de SQL Data Warehouse et, par conséquent, chargera et exportera les magnitudes des données plus rapidement que n’importe quel autre outil.  Les charges PolyBase peuvent être exécutées à l’aide de CTAS ou d’INSERT INTO.  **L’utilisation de CTAS permet de minimiser la journalisation des transactions et constitue le moyen le plus rapide de charger vos données.**  Azure Data Factory prend également en charge les charges PolyBase.  PolyBase prend en charge une variété de formats de fichiers, y compris les fichiers Gzip.  **Pour maximiser le débit lors de l’utilisation de fichiers texte gzip, divisez les fichiers en 60 fichiers ou plus pour optimiser le parallélisme de votre charge.**  Pour un débit total plus rapide, envisagez le chargement simultané des données.

Consultez aussi [Chargement de données][Load data], [Guide d’utilisation de PolyBase][Guide for using PolyBase], [Modèles et stratégies de chargement Azure SQL Data Warehouse][Azure SQL Data Warehouse loading patterns and strategies], [Téléchargement de données avec Azure Data Factory][Load Data with Azure Data Factory], [Déplacer des données à l’aide d’Azure Data Factory][Move data with Azure Data Factory], [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Charger, puis interroger les tables externes
Si Polybase, également appelé tables externes, peut être le moyen le plus rapide pour charger les données, il n’est pas optimal pour les requêtes. Les tables SQL Data Warehouse Polybase ne prennent actuellement en charge que les fichiers d’objets blob Azure et le stockage Azure Data Lake. Aucune ressource de calcul ne se charge de la sauvegarde de ces fichiers.  Par conséquent, SQL Data Warehouse ne peut pas décharger ce travail et doit donc lire le fichier entier en le chargeant dans tempdb pour lire les données.  Donc, si vous disposez de plusieurs requêtes qui vont interroger ces données, il est préférable de charger ces données une fois et que les requêtes utilisent la table locale.

Voir aussi [Guide d’utilisation de PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Hacher et distribuer de grandes tables
Par défaut, les tables sont distribuées par tourniquet (Round Robin).  Cela aide les utilisateurs à commencer la création de leurs tables sans avoir à déterminer comment les tables doivent être distribuées.  Les tables distribuées par tourniquet (Round Robin) peuvent offrir des performances suffisantes pour certaines charges de travail, mais souvent la sélection d’une colonne de distribution s’avérera plus efficace.  L’exemple le plus courant de meilleures performances observées avec une table distribuée par une colonne par rapport à une table Round Robin est lorsque deux grandes tables de faits sont jointes.  Par exemple, si vous avez une table de commandes, qui est distribuée par order_id, et une table de transactions, également distribuée par order_id, lorsque vous joignez votre table de commandes à votre table de transactions sur order_id, cette requête devient une requête directe, ce qui signifie que nous éliminons les opérations de déplacement de données.  Moins d’étapes signifie une requête plus rapide.  Moins de déplacement des données permet également d’obtenir des requêtes plus rapides.  Cette explication ne fait que survoler le sujet. Lors du chargement d’une table distribuée, assurez-vous que vos données entrantes ne sont pas triées sur la clé de distribution car cela ralentit vos charges.  Consultez les liens ci-dessous pour des explications très détaillées sur la façon dont la sélection d’une colonne de distribution peut améliorer les performances et pour apprendre à définir une table distribuée dans la clause WITH de l’instruction CREATE TABLES.

Voir aussi [Vue d’ensemble des tables][Table overview], [Distribution de tables][Table distribution], [Sélection d’une distribution de tables][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Ne pas créer trop de partitions
Bien que le partitionnement des données peut être très efficace pour mettre à jour vos données grâce au basculement de partitions ou à l’optimisation des analyses avec élimination de partition, avoir un trop grand nombre de partitions peut ralentir vos requêtes.  Souvent une stratégie de partitionnement à granularité élevée qui peut fonctionner correctement sur SQL Server peut poser des problèmes sur SQL Data Warehouse.  Un trop grand nombre de partitions peut également réduire l’efficacité des index columnstore en cluster si chaque partition possède moins d’1 million de lignes.  N’oubliez pas que, en arrière-plan, SQL Data Warehouse partitionne vos données en 60 bases de données, donc si vous créez une table avec 100 partitions, cela produit en réalité 6 000 partitions.  Chaque charge de travail est différente ; par conséquent, le meilleur conseil serait d’expérimenter le partitionnement pour voir ce qui fonctionne le mieux pour votre charge de travail.  Envisagez d’abaisser le niveau de granularité à un niveau inférieur à celui qui a fonctionné pour vous dans SQL Server.  Par exemple, utilisez plutôt des partitions hebdomadaires ou mensuelles plutôt que des partitions quotidiennes.

Voir aussi [Partitionnement de table][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Minimiser la taille des transactions
Les instructions INSERT, UPDATE et DELETE s’exécutent dans une transaction, et en cas d’échec elles doivent être restaurées.  Pour minimiser le risque d’une restauration longue, réduisez si possible les tailles de transactions.  Pour ce faire, vous pouvez diviser les instructions INSERT, UPDATE et DELETE en plusieurs parties.  Par exemple, si vous disposez d’une instruction INSERT qui devrait prendre une heure, décomposez si possible l’insertion en 4 parties, qui seront chacune exécutées en 15 minutes.  Exploitez des cas spéciaux de journalisation minimale, tels que CTAS, TRUNCATE, DROP TABLE ou INSERT, dans des tables vides, afin de réduire le risque de restauration.  Un autre moyen d’éliminer les restaurations consiste à utiliser des opérations de métadonnées uniquement comme le basculement de partitions pour la gestion des données.  Par exemple, plutôt que d’exécuter une instruction DELETE pour supprimer toutes les lignes d’une table où order_date était octobre 2001, vous pouvez partitionner vos données tous les mois et ensuite extraire la partition contenant les données vers une partition vide à partir d’une autre table (voir les exemples ALTER TABLE).  Pour les tables non partitionnées, utilisez une instruction CTAS pour écrire les données que vous souhaitez conserver dans une table plutôt que l’instruction DELETE.  Si une instruction CTAS prend le même laps de temps, elle permet une opération beaucoup plus sûre car elle offre une journalisation des transactions très minime et peut être annulée rapidement si nécessaire.

Voir aussi [Transactions][Understanding transactions], [Optimisation des transactions][Optimizing transactions], [Partitionnement de table][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>Utiliser la plus petite taille de colonne possible
Lorsque vous définissez votre commande DDL, l’utilisation du plus petit type de données prenant en charge vos données améliore les performances de requête.  Ceci est particulièrement important pour les colonnes CHAR et VARCHAR.  Si la valeur la plus longue dans une colonne est de 25 caractères, définissez la colonne en tant que VARCHAR(25).  Évitez de définir toutes les colonnes de caractères sur une grande longueur par défaut.  En outre, définissez des colonnes VARCHAR lorsque cela suffit, au lieu d’utiliser NVARCHAR.

Voir aussi [Vue d’ensemble des tables][Table overview], [Types de données des tables][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Utiliser des tables de segments de mémoire temporaires pour les données temporaires
Lorsque vous envoyez des données dans SQL Data Warehouse, vous trouverez peut-être que l’utilisation d’une table de segments de mémoire accélère le processus global.  Si vous chargez des données uniquement pour les organiser avant d’exécuter d’autres transformations, le chargement de la table dans la table de segments de mémoire sera beaucoup plus rapide que le chargement de données dans une table columnstore en cluster.  En outre, le chargement des données dans une table temporaire sera également beaucoup plus rapide que le chargement d’une table dans un stockage permanent.  Les tables temporaires commencent par le signe « # » et sont accessibles uniquement par la session qui les a créées ; par conséquent, il se peut qu’elles ne fonctionnent que dans des scénarios limités.   Les tables de segments de mémoire sont définies dans la clause WITH d’une instruction CREATE TABLE.  Si vous utilisez une table temporaire, n’oubliez pas de créer des statistiques dans la table temporaire également.

Voir aussi [Tables temporaires][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Optimiser les tables columnstore en clusters
Les index columnstore en cluster sont l’une des méthodes les plus efficaces pour stocker vos données dans SQL Data Warehouse.  Par défaut, les tables dans SQL Data Warehouse sont créées en tant que ColumnStore en cluster.  Pour obtenir les meilleures performances pour les requêtes sur les tables columnstore, la qualité du segment est importante.  Lorsque les lignes sont écrites dans les tables columnstore avec une mémoire insuffisante, la qualité du segment columnstore peut être affectée.  La qualité du segment peut être mesurée par le nombre de lignes dans un groupe de lignes compressé.  Consultez la section [Causes de la qualité médiocre des index columnstore][Causes of poor columnstore index quality] dans l’article [Index de table][Table indexes] pour obtenir des instructions étape par étape sur la détection et l’amélioration de la qualité de segment pour les tables columnstore en cluster.  Étant donné que la qualité des segments columnstore est importante, nous vous conseillons d’utiliser des ID d’utilisateurs qui se trouvent dans la classe de ressource de moyenne ou grande taille pour le chargement des données. L’utilisation de faibles [niveaux de service](performance-tiers.md#service-levels) signifie que vous souhaitez assigner une classe de ressource plus grande à votre utilisateur de chargement.

Étant donné que les tables columnstore ne transmettent généralement pas de données dans un segment columnstore compressé s’il existe moins d’1 million de lignes par table et si chaque table SQL Data Warehouse est partitionnée en 60 tables, en règle générale, les tables columnstore ne tireront aucun profit d’une requête, sauf si la table comporte plus de 60 millions de lignes.  Pour une table comportant moins de 60 millions de lignes, il ne sera peut-être pas judicieux d’avoir un index columnstore.  Mais cela ne peut pas nuire non plus.  En outre, si vous partitionnez vos données, vous souhaiterez peut-être estimer que chaque partition nécessitera 1 million de lignes pour bénéficier d’un index columnstore en cluster.  Si une table possède 100 partitions, elle devra avoir au moins 6 milliards de lignes pour bénéficier d’une banque de colonnes en cluster (60 distributions * 100 partitions * 1 million de lignes).  Si votre table ne possède pas six milliards de lignes dans cet exemple, réduisez le nombre de partitions ou envisagez plutôt d’utiliser une table de segment de mémoire.  Il peut être également intéressant de tester pour voir si de meilleures performances peuvent être obtenues avec une table de segment de mémoire ayant des index secondaires plutôt qu’avec une table columnstore.

Lorsque vous interrogez une table columnstore, les requêtes s’exécutent plus vite si vous sélectionnez uniquement les colonnes dont vous avez besoin.  

Voir aussi [Index de table][Table indexes], [Guide des index columnstore][Columnstore indexes guide], [Reconstruction des index columnstore][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Utiliser une classe de ressource plus grande pour améliorer les performances des requêtes
SQL Data Warehouse utilise des groupes de ressources pour allouer de la mémoire aux requêtes.  Dès le départ, tous les utilisateurs sont affectés à la petite classe de ressource qui accorde 100 Mo de mémoire par distribution.  Dans la mesure où il existe toujours 60 distributions et que chaque distribution reçoit un minimum de 100 Mo au niveau du système, l’allocation de mémoire totale est de 6 000 Mo, ou juste en dessous de 6 Go.  Certaines requêtes, telles que des grandes jointures ou des charges dans des tables columnstore en cluster, bénéficieront d’allocations de mémoire supérieures.  Certaines requêtes, comme les analyses pures, ne tireront aucun avantage.  En revanche, l’utilisation de classes de ressource plus grandes affecte l’accès concurrentiel ; par conséquent, vous devez prendre ce point en considération avant de déplacer tous les utilisateurs vers une grande classe de ressource.

Voir également [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Utiliser une classe ressource plus petite pour augmenter l’accès concurrentiel
Si vous constatez que les requêtes utilisateur semblent avoir un délai trop long, cela peut signifier que vos utilisateurs s’exécutent dans des classes de ressource plus grandes et consomment beaucoup d’emplacements de concurrence entraînant la mise en file d’attente des autres requêtes.  Pour voir si les requêtes des utilisateurs sont en attente, exécutez `SELECT * FROM sys.dm_pdw_waits` pour déterminer si des lignes sont renvoyées.

Voir également [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Utiliser des DMV pour surveiller et optimiser vos requêtes
SQL Data Warehouse dispose de plusieurs DMV qui peuvent être utilisées pour surveiller l’exécution de la requête.  L’article ci-dessous sur la surveillance fournit des instructions étape par étape sur la façon d’examiner les détails d’une requête en cours d’exécution.  Pour trouver rapidement des requêtes dans ces DMV, l’utilisation de l’option LABEL avec vos requêtes peut aider.

Voir aussi [Surveiller votre charge de travail à l’aide de vues de gestion dynamique][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Autres ressources
Consultez également notre article [Dépannage][Troubleshooting] concernant les problèmes courants et leurs solutions.

Si vous ne trouvez pas ce que vous recherchez dans cet article, essayez d’utiliser la fonction de recherche de documents située sur le côté gauche de cette page pour rechercher tous les documents relatifs à Azure SQL Data Warehouse.  Le [Forum MSDN Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN Forum] a été créé pour vous permettre de poser des questions à d’autres utilisateurs et au groupe de produits SQL Data Warehouse.  Nous suivons activement ce forum pour vous assurer que vos questions sont traitées par un autre utilisateur ou un membre de notre équipe.  Si vous préférez poser vos questions sur Stack Overflow, nous avons également un [Forum Azure SQL Data Warehouse Stack Overflow][Azure SQL Data Warehouse Stack Overflow Forum].

Enfin, utilisez la page des [commentaires relatifs à Azure SQL Data Warehouse][Azure SQL Data Warehouse Feedback] afin de faire des demandes de fonctionnalités.  L’ajout de vos demandes ou la confirmation des autres demandes nous permet vraiment de hiérarchiser les fonctions.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ./sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/
