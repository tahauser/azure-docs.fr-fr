---
title: Partitionnement de tables dans SQL Data Warehouse | Microsoft Docs
description: Prise en main du partitionnement de tables dans Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: f94bc3770fbd7e707194032cb99c67b09f8a0618
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partitionnement de tables dans SQL Data Warehouse
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

Le partitionnement est pris en charge sur tous les types de table SQL Data Warehouse, notamment columnstore en cluster, index en cluster et segment de mémoire.  Le partitionnement est également pris en charge sur tous les types de distribution, notamment le hachage ou le tourniquet (round robin) distribué.  Le partitionnement vous permet de diviser vos données en groupes de données plus restreints et, dans la plupart des cas, il est effectué sur une colonne de date.

## <a name="benefits-of-partitioning"></a>Avantages du partitionnement
Le partitionnement peut bénéficier de la maintenance des données et des performances des requêtes.  Le fait qu’il bénéficie à ces deux éléments ou à un seul dépend de la façon dont les données sont chargées et du fait que la même colonne puisse être utilisée ou non dans les deux cas, étant donné que le partitionnement peut uniquement être effectué sur une colonne.

### <a name="benefits-to-loads"></a>Avantages pour les charges
Le principal avantage du partitionnement dans SQL Data Warehouse est d’améliorer l’efficacité et les performances de chargement de données en utilisant la suppression, le basculement et la fusion de partitions.  Dans la plupart des cas, les données sont partitionnées sur une colonne de dates étroitement liée à l’ordre selon lequel les données sont chargées dans la base de données.  L’un des avantages majeurs de l’utilisation de partitions pour la maintenance des données et qu’elle évite la journalisation des transactions.  Bien que l’insertion, la mise à jour ou la suppression de données constituent l’approche la plus simple, avec un peu de réflexion et d’efforts, l’utilisation du partitionnement pendant votre processus de chargement peut considérablement améliorer les performances.

Le basculement de partitions peut servir à supprimer ou à remplacer rapidement une section d’une table.  Par exemple, une table de faits des ventes peut contenir seulement des données pour les 36 derniers mois.  À la fin de chaque mois, le mois de données de ventes le plus ancien est supprimé de la table.  Ces données ont pu être supprimées à l’aide d’une instruction delete pour supprimer les données pour le mois plus ancien.  Toutefois, la suppression d’une grande quantité de données ligne par ligne avec une instruction DELETE peut prendre beaucoup de temps, ainsi que créer le risque de transactions volumineuses qui pourraient nécessiter un temps de restauration considérable en cas de problème.  Une approche plus optimale consiste à supprimer la partition de données la plus ancienne.  Alors que la suppression des lignes individuelle peut prendre des heures, la suppression d’une partition complète ne prend que secondes.

### <a name="benefits-to-queries"></a>Avantages pour les requêtes
Le partitionnement peut également servir à améliorer les performances des requêtes.  Une requête qui applique un filtre de données partitionnées peut limiter l’analyse aux seules partitions éligibles. Cette méthode de filtrage peut éviter l’analyse de table complète et n’analyser qu’un sous-ensemble de données moins conséquent. Avec l’introduction des index columnstore en cluster, les avantages des performances de l’élimination de prédicats sont moins notables, mais dans certains cas, il peut exister un avantage pour les requêtes.  Par exemple, si la table de faits de ventes est partagée en 36 mois grâce au champ de date des ventes, alors les requêtes filtrées par date de vente peuvent ignorer la recherche dans les partitions de traitement qui ne correspondent pas au filtre.

## <a name="partition-sizing-guidance"></a>Guide de dimensionnement de partition
Tandis que le partitionnement peut être utilisé pour améliorer les performances de certains scénarios, la création d’une table avec **trop** de partitions peut nuire aux performances dans certaines circonstances.  Ces inquiétudes sont particulièrement avérées pour les tables columnstore en cluster.  Pour que le partitionnement soit utile, il est important de savoir quand utiliser le partitionnement et le nombre de partitions à créer.  Il n’existe aucune règle absolue concernant le nombre de partitions ; cela dépend de vos données et du nombre de partitions que vous chargez simultanément.  En règle générale, un schéma de partition réussi n’a qu’entre dix et cent partitions, pas mille.

Lorsque vous créez des partitions sur des tables **columnstore en cluster**, il est important de prendre en compte le nombre de lignes dans chaque partition.  Pour une compression et des performances des tables columnstore en cluster optimales, un minimum de 1 million de lignes par partition et par distribution est nécessaire.  Avant la création des partitions, SQL Data Warehouse divise déjà chaque table en 60 bases de données distribuées.  Tout partitionnement ajouté à une table est en plus des distributions créées en arrière-plan.  Dans cet exemple, si la table de faits de ventes contient 36 partitions mensuelles, et étant donné que SQL Data Warehouse comporte 60 distributions, la table de faits de ventes doit contenir 60 millions de lignes par mois, ou 2,1 milliards de lignes lorsque tous les mois sont remplis.  Si une table contient moins de lignes que le nombre minimum recommandé par partition, envisagez d’utiliser moins de partitions pour augmenter le nombre de lignes par partition.  Consultez également l’article [Indexation][Index] qui comporte les requêtes pouvant être exécutées sur SQL Data Warehouse afin d’évaluer la qualité des index columnstore en cluster.

## <a name="syntax-difference-from-sql-server"></a>Différence de syntaxe à partir de SQL Server
SQL Data Warehouse présente un mode de définition des partitions qui est plus simple que SQL Server.  Les fonctions et les schémas de partitionnement ne sont pas utilisés dans SQL Data Warehouse, car ils se trouvent dans SQL Server.  À la place, il vous suffit d’identifier la colonne partitionnée et les points de limite.  Bien que la syntaxe de partitionnement puisse être légèrement différente de SQL Server, les concepts de base sont les mêmes.  SQL Server et SQL Data Warehouse prennent en charge une colonne de partition par table, qui peut être une partition par plage.  Pour en savoir plus sur le partitionnement, consultez [Tables et index partitionnés][Partitioned Tables and Indexes].

Exemple ci-dessous d’une instruction [CREATE TABLE][CREATE TABLE] partitionnée par SQL Data Warehouse, partitions de la table FactInternetSales sur la colonne OrderDateKey :

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migration du partitionnement dans SQL Server
Pour migrer les définitions des partitions SQL Server vers SQL Data Warehouse, procédez simplement comme suit :

* Supprimez le [schéma de partition][partition scheme] SQL Server.
* Ajoutez la définition de la [fonction de partition][partition function] à votre instruction CREATE TABLE.

Si vous migrez une table partitionnée à partir d’une instance SQL Server, le SQL ci-dessous peut vous aider à déterminer le nombre de lignes se trouvant dans chaque partition.  N’oubliez pas que si la même granularité de partitionnement est utilisée sur SQL Data Warehouse, le nombre de lignes par partition diminue d’un facteur de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gestion des charges de travail
Dernier facteur à prendre en compte en ce qui concerne les partitions de tables : la [gestion des charges de travail][workload management].  La gestion des charges de travail dans SQL Data Warehouse consiste essentiellement en la gestion de mémoire et d’accès concurrentiel.  Dans SQL Data Warehouse, la quantité de mémoire maximale allouée à chaque distribution lors de l’exécution des requêtes est définie par les classes de ressources.  Dans l’idéal, vos partitions sont dimensionnées en tenant compte d’autres facteurs tels que les besoins en mémoire pour la création d’index columnstore en cluster.  Les index columnstore en cluster présentent des avantages non négligeables lorsque davantage de mémoire leur est allouée.  Vous devez donc vous assurer que la mémoire disponible pour une reconstruction d’index de partition est suffisante. Pour augmenter la quantité de mémoire disponible pour votre requête, vous devez passer du rôle par défaut, smallrc, à d’autres rôles, par exemple, largerc.

Si vous voulez obtenir des informations sur l’allocation de la mémoire pour chaque distribution, interrogez les vues de gestion dynamique de Resource Governor. En réalité, votre allocation de mémoire est inférieure aux résultats de la requête suivante. Toutefois, cette requête peut vous aider à dimensionner vos partitions lors d’opérations de gestion des données.  Évitez de dimensionner vos partitions en affectant une taille supérieure à la mémoire octroyée que fournit la classe de ressources de très grande taille. Si la taille de vos partitions dépasse cette valeur, vous risquez de solliciter la mémoire de manière trop intensive, ce qui peut nuire à l’efficacité de la compression.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Basculement de partitions
SQL Data Warehouse prend en charge le fractionnement, la fusion et le basculement de partition. Chacune de ces fonctions est exécutée à l’aide de l’instruction [ALTER TABLE][ALTER TABLE].

Pour faire basculer une partition d’une table à une autre, vous devez vous assurer que les partitions s’alignent sur leurs limites respectives et que les définitions de tables correspondent. Comme aucune contrainte de validation n’est disponible pour appliquer la plage de valeurs dans une table, la table source doit contenir les mêmes limites de partition que la table cible. Si les limites de partition sont différentes, le basculement de la partition échoue, car les métadonnées de celle-ci ne sont pas synchronisées.

### <a name="how-to-split-a-partition-that-contains-data"></a>Fractionnement d’une partition contenant des données
Pour fractionner une partition qui contient déjà des données, la méthode la plus efficace consiste à utiliser une instruction `CTAS` . Si la table partitionnée est une table columnstore en cluster, la partition de table doit être vide pour pouvoir être fractionnée.

L’exemple suivant crée une table columnstore partitionnée. Il insère une ligne dans chaque partition :

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> En créant l’objet de statistiques, les métadonnées des tables sont plus précises. Si vous omettez ces statistiques, SQL Data Warehouse utilise les valeurs par défaut. Pour en savoir plus sur les statistiques, consultez la rubrique [Statistiques][statistics].
> 
> 

La requête suivante recherche le nombre de lignes à l’aide de la vue catalogue `sys.partitions` :

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

La commande de fractionnement suivante génère un message d’erreur :

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Le message 35346, au niveau 15, état 1, ligne 44, indique que la clause SPLIT de l’instruction ALTER PARTITION a échoué, car la partition n’est pas vide.  Seules les partitions vides peuvent faire l’objet d’un fractionnement lorsque la table inclut un index columnstore. Vous pouvez envisager de désactiver l’index columnstore avant de lancer l’instruction ALTER PARTITION, puis de reconstruire l’index columnstore une fois l’opération ALTER PARTITION terminée.

Cependant, vous pouvez utiliser la commande `CTAS` pour créer une table afin de stocker les données.

```sql
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
FROM    FactInternetSales
WHERE   1=2
;
```

Comme les limites de la partition sont alignées, le basculement est autorisé. La table source présente donc une partition vide, que vous pouvez par la suite fractionner.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Il ne reste plus qu’à aligner les données en fonction des limites de la nouvelle partition, via la commande `CTAS`, et à faire basculer les données une nouvelle fois vers la table principale.

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Une fois le déplacement des données effectué, il est judicieux d’actualiser les statistiques sur la table cible. La mise à jour des statistiques permet de vérifier qu’elles reflètent de manière précise la nouvelle distribution des données sur leurs partitions respectives.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Contrôle de code source dans le cadre du partitionnement d’une table
Pour éviter la **détérioration** de la définition de votre table dans le système de contrôle du code source, vous pouvez envisager l’approche suivante :

1. Créez la table en tant que table partitionnée, mais sans ajouter de valeurs de partition.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. Appliquez à la table la commande `SPLIT` lors du processus de déploiement :

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Grâce à cette méthode, le code faisant l’objet d’un contrôle de code source reste statique. Quant aux valeurs limites du partitionnement, elles peuvent rester dynamiques et continuer à évoluer au fil du temps.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les articles [Vue d’ensemble des tables][Overview], [Types de données de table][Data Types], [Distribution d’une table][Distribute], [Indexation d’une table][Index], [Maintenance des statistiques de table][Statistics] et [Tables temporaires][Temporary].  Pour en savoir plus sur les meilleures pratiques, consultez [Meilleures pratiques relatives à SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./resource-classes-for-workload-management.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
