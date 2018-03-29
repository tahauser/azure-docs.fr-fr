---
title: Indexation de tables dans SQL Data Warehouse | Microsoft Azure
description: Prise en main de l’indexation de tables dans Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 03/15/2018
ms.author: barbkess
ms.openlocfilehash: 96d4bb91fabe6b962d1fe4d5b2dc26f6342012b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexation de tables dans SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Vue d’ensemble][Overview]
> * [Types de données][Data Types]
> * [Distribuer][Distribute]
> * [Index][Index]
> * [Partition][Partition]
> * [Statistiques][Statistics]
> * [Temporaire][Temporary]
> 
> 

SQL Data Warehouse propose plusieurs options d’indexation, notamment [les index columnstore en cluster][clustered columnstore indexes], [les index en cluster et les index non cluster][clustered indexes and nonclustered indexes].  En outre, il propose aussi une option sans index, également appelée [segment de mémoire][heap].  Cet article présente les avantages de chaque type d’index, ainsi que des conseils pour tirer les meilleures performances de vos index. Consultez la page [Syntaxe de création d’une table][create table syntax] pour plus d’informations sur la création d’une table dans SQL Data Warehouse.

## <a name="clustered-columnstore-indexes"></a>Index columnstore en cluster
Par défaut, SQL Data Warehouse crée un index columnstore en cluster lorsqu’aucune option d’index n’est spécifiée sur une table. Les tables columnstore en cluster offrent le plus haut niveau de compression de données ainsi que les meilleures performances de requête globales.  Les tables columnstore en cluster sont généralement plus performantes que les index en cluster ou les tables de segments de mémoire, et constituent généralement le meilleur choix pour les grandes tables.  Pour ces raisons, les index columnstore en cluster sont le meilleur endroit pour démarrer lorsque vous ne savez pas comment indexer votre table.  

Pour créer une table columnstore en cluster, spécifiez simplement CLUSTERED COLUMNSTORE INDEX dans la clause WITH, ou omettez la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Il existe quelques scénarios où un columnstore en cluster peut ne pas être pas une bonne option :

* Les tables columnstore ne prennent pas en charge varchar(max), nvarchar(max) et varbinary(max).  Envisagez plutôt les segments de mémoire ou les index en cluster.
* Les tables columnstore peuvent être moins efficaces pour les données temporaires.  Envisagez les segments de mémoire, voire les tables temporaires.
* Petites tables avec moins de 100 millions de lignes.  Envisagez les tables de segments de mémoire.

## <a name="heap-tables"></a>Tables de segments de mémoire
Lorsque vous envoyez des données dans SQL Data Warehouse, vous trouverez peut-être que l’utilisation d’une table de segments de mémoire accélère le processus global.  En effet, les charges sur les segments de mémoire sont plus rapides que sur les tables d’index et, dans certains cas, la lecture ultérieure peut être effectuée depuis le cache.  Si vous chargez des données uniquement pour les organiser avant d’exécuter d’autres transformations, le chargement de la table dans la table de segments de mémoire sera beaucoup plus rapide que le chargement de données dans une table columnstore en cluster. En outre, le chargement des données dans une [table temporaire][Temporary] sera également beaucoup plus rapide que le chargement d’une table dans un stockage permanent.  

Pour les petites tables de choix de moins de 100 millions de lignes, souvent, l’utilisation de tables de segments de mémoire est judicieuse.  Les tables columnstore en cluster commencent à atteindre une compression optimale une fois qu’elles comptent plus de 100 millions de lignes.

Pour créer une table de segments de mémoire, spécifiez simplement HEAP dans la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Index en cluster et non cluster
Les index en cluster peuvent être plus performants que les tables columnstore en cluster lorsqu’une seule ligne doit être récupérée rapidement.  Pour les requêtes où une seule ligne ou très peu de choix de lignes est requis pour des performances très rapides, envisagez un index de cluster ou un index secondaire non cluster.  L’inconvénient d’utiliser un index en cluster est que seules les requêtes qui utilisent un filtre hautement sélectif sur la colonne d’index en cluster en bénéficient.  Pour améliorer le filtre sur les autres colonnes, un index non cluster peut être ajouté aux autres colonnes.  Toutefois, chaque index qui est ajouté à une table ajoutera l’espace et le temps de traitement sur les charges.

Pour créer une table d’index en cluster, spécifiez simplement CLUSTERED INDEX dans la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Pour ajouter un index non-cluster sur une table, utilisez simplement la syntaxe suivante :

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimisation des index columnstore en cluster
Les tables columnstore en cluster sont organisées en données dans les segments.  Une qualité de segment élevée est essentielle pour atteindre des performances des requêtes optimales sur une table columnstore.  La qualité du segment peut être mesurée par le nombre de lignes dans un groupe de lignes compressé.  La qualité de segment est optimale lorsqu’il existe au moins 100 000 lignes par groupe de lignes compressé, et ses performances vont croissant, car le nombre de lignes par groupe est proche de 1 048 576 lignes, soit le nombre maximum de lignes qu’un groupe de lignes peut contenir.

La vue ci-dessous peut être créée et utilisée sur votre système afin de calculer les lignes moyennes par groupe de lignes et d’identifier tous les index columnstore en cluster non optimaux.  La dernière colonne sur cette vue se génère en tant qu’instruction SQL qui peut être utilisée pour reconstruire vos index.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Maintenant que vous avez créé la vue, exécutez cette requête pour identifier les tables avec des groupes de lignes comportant moins de 100 000 lignes.  Bien sûr, vous souhaiterez peut-être augmenter le seuil de 100 000 si vous recherchez une qualité de segment supérieure. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Une fois que vous avez exécuté la requête, vous pouvez commencer à examiner les données et analyser vos résultats. Cette table explique ce que vous devez rechercher dans votre analyse de groupe de lignes.

| Colonne | Utilisation de ces données |
| --- | --- |
| [table_partition_count] |Si la table est partitionnée, vous pouvez vous attendre à un nombre plus élevé de groupes de lignes ouverts. Chaque partition de la distribution peut, en théorie, avoir un groupe de lignes ouvert associé. Tenez-en compte dans votre analyse. Une petite table qui a été partitionnée peut être optimisée en supprimant complètement le partitionnement pour améliorer la compression. |
| [row_count_total] |Nombre total de lignes de la table. Par exemple, vous pouvez utiliser cette valeur pour calculer le pourcentage de lignes à l’état compressé. |
| [row_count_per_distribution_MAX] |Si toutes les lignes sont réparties uniformément, cette valeur est le nombre cible de lignes par distribution. Comparez cette valeur avec compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Nombre total de lignes au format columnstore pour la table |
| [COMPRESSED_rowgroup_rows_AVG] |Si le nombre moyen de lignes est considérablement inférieur au nombre maximal de lignes pour un groupe de lignes, utilisez CTAS ou ALTER INDEX REBUILD pour recompresser les données |
| [COMPRESSED_rowgroup_count] |Nombre de groupes de lignes au format columnstore. Si ce nombre est très élevé par rapport à la table, il indique que la densité de columnstore est faible. |
| [COMPRESSED_rowgroup_rows_DELETED] |Les lignes sont logiquement supprimées au format columnstore. Si le nombre est élevé par rapport à la taille de la table, recréez la partition ou reconstruisez l’index, car cette opération les supprime physiquement. |
| [COMPRESSED_rowgroup_rows_MIN] |Utilisez cette valeur avec les colonnes AVG et MAX pour comprendre la plage de valeurs des groupes de lignes dans votre columnstore. Un nombre bas au-dessus du seuil de chargement (102 400 par distribution alignée sur la partition) suggère que des optimisations sont disponibles dans le chargement des données |
| [COMPRESSED_rowgroup_rows_MAX] |Identique à ce qui précède |
| [OPEN_rowgroup_count] |Les groupes de lignes ouverts sont normaux. On peut raisonnablement s’attendre à un groupe de lignes ouvert par distribution de tables (60). Les nombres excessifs suggèrent un chargement des données sur plusieurs partitions de données. Vérifiez la stratégie de partitionnement pour vous assurer qu’elle est saine |
| [OPEN_rowgroup_rows] |Chaque groupe de lignes peut contenir 1 048 576 lignes maximum. Utilisez cette valeur pour connaître l’état de remplissage actuel des groupes de lignes ouverts |
| [OPEN_rowgroup_rows_MIN] |Les groupes ouverts indiquent que les données sont chargées de manière progressive dans la table ou que la charge précédente a débordé sur les lignes restantes de ce groupe de lignes. Utilisez les colonnes MIN, MAX et AVG pour afficher la quantité de données stockées dans les groupes de ligne ouverts. Pour les petites tables, il peut s’agir de 100 % des données. Dans ce cas, l’instruction ALTER INDEX REBUILD force les données dans le columnstore. |
| [OPEN_rowgroup_rows_MAX] |Identique à ce qui précède |
| [OPEN_rowgroup_rows_AVG] |Identique à ce qui précède |
| [CLOSED_rowgroup_rows] |Examinez les lignes des groupes de lignes fermés pour effectuer un contrôle de validité. |
| [CLOSED_rowgroup_count] |S’il existe des groupes de lignes fermés, ils doivent être en petit nombre. Les groupes de lignes fermés peuvent être convertis en groupes de lignes compressés à l’aide de ALTER INDEX ... REORGANISE. Toutefois, cela n’est généralement pas nécessaire. Les groupes fermés sont automatiquement convertis en groupes de lignes du columnstore par le processus « moteur de tuple » en arrière-plan. |
| [CLOSED_rowgroup_rows_MIN] |Les groupes de lignes fermés doivent avoir un taux de remplissage très élevé. Si le taux de remplissage d’un groupe de lignes fermé est faible, une analyse plus approfondie du columnstore est nécessaire. |
| [CLOSED_rowgroup_rows_MAX] |Identique à ce qui précède |
| [CLOSED_rowgroup_rows_AVG] |Identique à ce qui précède |
| [Rebuild_Index_SQL] |Reconstruction d’un index columnstore pour une table par SQL |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causes de la qualité médiocre des index columnstore
Si vous avez identifié des tables avec une qualité médiocre de segments, vous devez en déterminer la cause racine.  Vous trouverez ci-dessous d’autres causes communes de segments de qualité médiocre :

1. Saturation de la mémoire lors de la construction de l’index
2. Volume élevé d’opérations DML
3. Opérations de chargement progressives ou légères
4. Nombre trop important de partitions

En raison de ces facteurs, le nombre de lignes par groupe de lignes de l’index columnstore peut être très inférieur à 1 million (chiffre optimal).  Ces facteurs peuvent également entraîner le transfert des lignes vers un groupe de lignes delta au lieu d’un groupe de lignes compressé. 

### <a name="memory-pressure-when-index-was-built"></a>Saturation de la mémoire lors de la construction de l’index
Le nombre de lignes par groupe de lignes compressé est directement lié à la largeur de la ligne et à la quantité de mémoire disponible pour traiter le groupe de lignes.  Lorsque les lignes sont écrites dans les tables columnstore avec une mémoire insuffisante, la qualité du segment columnstore peut être affectée.  Par conséquent, la meilleure pratique consiste à accorder à la session qui écrit sur vos tables d’index columnstore l’accès à autant de mémoire que possible.  Dans la mesure où il existe un compromis entre la mémoire et l’accès concurrentiel, les conseils sur l’allocation de la mémoire appropriée dépendent des données dans chaque ligne de votre table, des unités d’entrepôts de données affectées à votre système et de la quantité d’emplacements de concurrence que vous pouvez donner à la session qui écrit les données sur votre table.  Nous vous recommandons la bonne pratique suivante : commencez avec xlargerc si vous utilisez DW300 ou une base de données inférieure, largerc si vous utilisez des bases de données DW400 à DW600, et mediumrc si vous utilisez DW1000 et des bases de données supérieures.

### <a name="high-volume-of-dml-operations"></a>Volume élevé d’opérations DML
Un volume élevé d’opérations DML qui mettent à jour et suppriment des lignes peut provoquer l’inefficacité du columnstore. Cela est particulièrement vrai lorsque la majorité des lignes dans un groupe de lignes est modifiée.

* La suppression d’une ligne dans un groupe de lignes compressé ne fait que marquer logiquement la ligne comme étant supprimée. La ligne reste dans le groupe de lignes compressé jusqu’à ce que la partition ou la table soit reconstruite.
* L’insertion d’une ligne ajoute cette dernière à une table de groupe de lignes interne appelée groupe de lignes delta. La ligne insérée n’est pas convertie en columnstore jusqu’à ce que le groupe de lignes delta soit rempli et marqué comme étant fermé. Les groupes de lignes sont fermés lorsqu’ils atteignent leur capacité maximale de 1 048 576 lignes. 
* La mise à jour d’une ligne au format columnstore est traitée en tant que suppression logique, puis en tant qu’insertion. La ligne insérée peut être stockée dans le deltastore.

Les opérations de mise à jour par lot et d’insertion qui dépassent le seuil de 102 400 lignes par distribution alignée sur la partition seront écrites directement au format columnstore. Toutefois, pour que cela se produise et en supposant que la répartition soit uniforme, vous devriez modifier plus de 6,144 millions de lignes en une seule opération. Si le nombre de lignes d’une distribution alignée sur la partition donnée est inférieur à 102 400, les lignes seront transférées au deltastore et y resteront jusqu’à ce que l’une des trois conditions suivantes ait été remplie : un nombre suffisant de lignes a été inséré ; un nombre suffisant de lignes a été modifié pour pouvoir fermer le groupe de lignes ; l’index a été reconstruit.

### <a name="small-or-trickle-load-operations"></a>Opérations de chargement progressives ou légères
Les charges légères entrant dans SQL Data Warehouse sont parfois appelées charges progressives. Elles représentent généralement un flux presque constant de données que le système reçoit. Toutefois, comme ce flux est presque continu, le volume des lignes n’est pas forcément important. La plupart du temps, les données sont bien inférieures au seuil requis pour une charge directe au format columnstore.

Dans ces situations, il est souvent préférable d’envoyer les données dans Azure Blob Storage et de les laisser s’accumuler avant le chargement. Cette technique est souvent appelée *micro-batching*ou micro-traitement par lots.

### <a name="too-many-partitions"></a>Nombre trop important de partitions
Autre élément à prendre en compte : l’impact du partitionnement sur vos tables columnstore en cluster.  Avant de partitionner, SQL Data Warehouse divise déjà vos données en 60 bases de données.  Un partitionnement plus approfondi divise vos données.  Si vous partitionnez vos données, vous souhaiterez peut-être estimer que **chaque** partition nécessitera au moins 1 million de lignes pour bénéficier d’un index columnstore en cluster.  Si vous partitionnez votre table en 100 partitions, celle-ci devra compter au moins 6 milliards de lignes pour bénéficier d’un index columnstore en cluster (60 distributions * 100 partitions * 1 million de lignes). Si votre table de 100 partitions ne possède pas 6 milliards de lignes, réduisez le nombre de partitions ou envisagez plutôt d’utiliser une table de segments de mémoire.

Une fois que les tables ont été chargées avec des données, suivez les étapes ci-dessous pour identifier et reconstruire des tables avec des index columnstore en cluster non optimaux.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Reconstruire des index pour améliorer la qualité de segment
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Étape 1 : Identifier ou créer un utilisateur qui utilise la classe de ressources appropriée
Un moyen rapide d’améliorer immédiatement la qualité de segment consiste à reconstruire l’index.  La requête SQL renvoyée par la vue ci-dessus renvoie une instruction ALTER INDEX REBUILD, qui peut être utilisée pour reconstruire vos index.  Lors de la reconstruction de vos index, veillez à allouer suffisamment de mémoire à la session qui va reconstruire votre index.  Pour ce faire, augmentez la classe de ressources d’un utilisateur qui dispose des autorisations pour reconstruire l’index sur cette table conformément aux valeurs minimum recommandées.  La classe de ressources de l’utilisateur propriétaire de la base de données ne peut pas être modifiée, donc si vous n’avez pas créé d’utilisateur sur le système, vous devrez le faire en premier.  Le minimum recommandé est xlargerc si vous utilisez DW300 ou une base de données inférieure, largerc si vous utilisez des bases de données DW400 à DW600, et mediumrc si vous utilisez DW1000 et des bases de données supérieures.

Voici un exemple montrant comment allouer davantage de mémoire à un utilisateur en augmentant sa classe de ressources.  Des informations sur les classes de ressources et sur la création d’un utilisateur sont disponibles dans l’article [Gestion de la concurrence et des charges de travail][Concurrency].

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Étape 2 : Reconstruire les index columnstore en cluster avec un utilisateur de la classe de ressources la plus élevée
Connectez-vous en tant qu’utilisateur à l’étape 1 (p. ex., LoadUser), qui utilise maintenant une classe de ressources supérieure, puis exécutez les instructions ALTER INDEX.  N’oubliez pas que cet utilisateur possède l’autorisation ALTER sur les tables où l’index est reconstruit.  Ces exemples illustrent comment reconstruire l’index columnstore entier ou comment reconstruire une partition unique. Sur des tables volumineuses, il est plus pratique de reconstruire les index une seule partition à la fois.

Au lieu de reconstruire l’index, vous pouvez également copier la table dans une nouvelle table à l’aide de [CTAS][CTAS].  Quelle méthode est la meilleure ? Pour les gros volumes de données, [CTAS][CTAS] est généralement plus rapide que [ALTER INDEX][ALTER INDEX]. Pour les volumes de données plus restreints, [ALTER INDEX][ALTER INDEX] est plus facile à utiliser et ne vous oblige pas à permuter la table.  Consultez **Reconstruire des index avec CTAS et le basculement de partitions** ci-dessous pour plus d’informations sur la façon de reconstruire les index avec CTAS.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

La reconstruction d’un index dans SQL Data Warehouse est une opération hors connexion.  Pour plus d’informations sur la reconstruction d’index, consultez la section ALTER INDEX REBUILD dans [Columnstore Indexes Defragmentation][Columnstore Indexes Defragmentation] (Défragmentation d’index columnstore) et [ALTER INDEX][ALTER INDEX].

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Étape 3 : Vérifier que la qualité de segment columnstore en cluster a été améliorée
Réexécutez la requête qui a identifié la table présentant une qualité de segment médiocre, et vérifiez que la qualité de segment a été améliorée.  Si la qualité de segment n’a pas été améliorée, cela peut signifier que les lignes de votre table sont très larges.  Utilisez une classe de ressources supérieure ou une base de données DWU lors de la reconstruction de vos index.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Reconstruire des index avec CTAS et le basculement de partitions
Cet exemple utilise [CTAS][CTAS] et le basculement de partitions pour reconstruire une partition de table. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Pour plus d’informations sur la recréation de partitions à l’aide de `CTAS`, consultez l’article [Partition][Partition].

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les articles [Vue d’ensemble des tables][Overview], [Types de données de table][Data Types], [Distribution d’une table][Distribute], [Partitionnement d’une table][Partition], [Maintenance des statistiques de table][Statistics] et [Tables temporaires][Temporary].  Pour en savoir plus sur les bonnes pratiques, consultez la page [Meilleures pratiques relatives à SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./resource-classes-for-workload-management.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[clustered indexes and nonclustered indexes]: https://msdn.microsoft.com/library/ms190457.aspx
[create table syntax]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore Indexes Defragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[clustered columnstore indexes]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
