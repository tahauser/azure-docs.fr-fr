---
title: Gestion des statistiques sur les tables dans SQL Data Warehouse | Microsoft Docs
description: Prise en main des statistiques sur les tables dans Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 11/06/2017
ms.author: barbkess
ms.openlocfilehash: 5e7fd3c8790bb9a1a7ae8662f9a7047ae54892d2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Gestion des statistiques sur les tables dans SQL Data Warehouse
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

Plus Azure SQL Data Warehouse connaît vos données, plus il peut exécuter des requêtes sur celles-ci rapidement. La collecte de statistiques sur vos données et leur chargement dans SQL Data Warehouse est l’une des actions les plus importantes pour optimiser vos requêtes. En effet, l’optimiseur de requête SQL Data Warehouse est un optimiseur basé sur les coûts. Il compare le coût de différents plans de requête, puis choisit le plan avec le coût le plus bas, qui est le plus souvent celui dont l’exécution est la plus rapide. Par exemple, si l’optimiseur estime que la date que vous filtrez dans votre requête va renvoyer une ligne, il peut choisir un plan différent s’il estime que la date sélectionnée va renvoyer 1 million de lignes.

Actuellement, la création et la mise à jour des statistiques sont des processus manuels, mais simples à réaliser.  Vous serez bientôt en mesure de créer et mettre à jour automatiquement les statistiques sur des colonnes uniques et des index.  En utilisant les informations suivantes, vous pouvez considérablement automatiser la gestion des statistiques sur vos données. 

## <a name="getting-started-with-statistics"></a>Prise en main des statistiques
La création de statistiques échantillonnées sur chaque colonne est un bon moyen de bien démarrer. Les statistiques obsolètes entraînent des performances de requêtes non optimales. Cependant, la mise à jour des statistiques sur toutes les colonnes à mesure que vos données augmentent peut solliciter la mémoire. 

Voici quelques recommandations pour différents scénarios :
| **Scénario** | Recommandation |
|:--- |:--- |
| **Prise en main** | Mettre à jour toutes les colonnes après la migration vers SQL Data Warehouse |
| **Colonne la plus importante pour les statistiques** | Clé de distribution par hachage |
| **Deuxième colonne la plus importante pour les statistiques** | Clé de partition |
| **Autres colonnes importantes pour les statistiques** | Date, opérations JOIN fréquentes, GROUP BY, HAVING et WHERE |
| **Fréquence des mises à jour des statistiques**  | Classique : quotidienne <br></br> Après le chargement ou la transformation de données |
| **Échantillonnage** |  Moins de 1 milliard de lignes, utiliser le taux d’échantillonnage par défaut (20 pour cent) <br></br> Avec des tables contenant plus de 1 milliard de lignes, des statistiques sur une plage de 2 pour cent représentent un taux correct |

## <a name="updating-statistics"></a>Mettre à jour les statistiques

Une meilleure pratique consiste à mettre à jour les statistiques sur les colonnes de date à chaque fois qu’une date est ajoutée. Chaque fois que de nouvelles lignes sont chargées dans l’entrepôt, de nouvelles dates de transaction et de chargement sont également ajoutées. Ces dernières affectent la distribution des données et rendent les statistiques obsolètes. À l’inverse, vous n’aurez peut-être jamais à mettre à jour les statistiques d’une colonne de pays dans une table des clients, car la distribution des valeurs change rarement. Si l’on part du principe que la distribution des données est constante d’un client à l’autre, l’ajout de nouvelles lignes dans une table ne devrait pas affecter cette distribution. Toutefois, si votre entrepôt de données indique un seul pays et si vous importez des données d’un nouveau pays, ce qui entraîne donc le stockage de données de plusieurs pays, il est nécessaire de mettre à jour les statistiques de la colonne de pays.

L’une des premières questions que vous devez vous poser quand vous dépannez une requête est la suivante : **« Les statistiques sont-elles à jour ? »**

Or, vous ne pouvez pas répondre à cette question en vous appuyant sur l’âge des données. Un objet de statistiques à jour peut être ancien si aucune modification notable n’affecte les données sous-jacentes. Quand le nombre de lignes ou la distribution des valeurs change de manière substantielle dans une colonne, *alors* il est temps de mettre à jour les statistiques.

Dans la mesure où il n’existe aucune vue de gestion dynamique pour déterminer si les données de la table ont changé depuis la dernière mise à jour des statistiques, le fait de connaître l’ancienneté de vos statistiques peut vous donner un petit aperçu.  Vous pouvez utiliser la requête suivante pour déterminer la date de la dernière mise à jour des statistiques sur chaque table.  

> [!NOTE]
> N’oubliez pas que si la distribution des valeurs d’une colonne a subi une modification significative, vous devez mettre à jour les statistiques, quelle que soit la date de la dernière mise à jour.  
> 
> 

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Par exemple, les statistiques des **colonnes de date** d’un entrepôt de données doivent souvent être mises à jour. Chaque fois que de nouvelles lignes sont chargées dans l’entrepôt, de nouvelles dates de transaction et de chargement sont également ajoutées. Ces dernières affectent la distribution des données et rendent les statistiques obsolètes.  À l’inverse, les statistiques d’une colonne indiquant le sexe d’un client dans une table n’auront peut-être jamais besoin d’être mises à jour. Si l’on part du principe que la distribution des données est constante d’un client à l’autre, l’ajout de nouvelles lignes dans une table ne devrait pas affecter cette distribution. Toutefois, si votre entrepôt de données ne fait mention que d’un seul sexe et qu’une nouvelle exigence nécessite le recours à plusieurs sexes, vous devez mettre à jour les statistiques de la colonne relative au sexe.

Pour en savoir plus, consultez la section [Statistiques][Statistics] de MSDN.

## <a name="implementing-statistics-management"></a>Implémentation de fonctions de gestion des statistiques
Il est souvent judicieux d’étendre le processus de chargement des données, afin de vérifier que les statistiques sont mises à jour à la fin du chargement. Le chargement des données se produit lorsque la taille et/ou la distribution des valeurs sont souvent modifiées dans les tables. Par conséquent, il est logique d’implémenter certains processus de gestion à ce niveau.

Les principes généraux suivants sont fournis afin de vous aider à mettre à jour vos statistiques pendant le processus de chargement :

* Assurez-vous que chaque table chargée présente au moins un objet de statistiques mis à jour. Cela met à jour les informations sur la taille des tables (nombre de lignes et de pages) dans le cadre du processus de mise à jour des statistiques.
* Concentrez-vous sur les colonnes participant aux clauses JOIN, GROUP BY, ORDER BY et DISTINCT.
* Envisagez de mettre plus souvent à jour les colonnes de clé croissante, comme celles des dates de transactions, car ces valeurs ne seront pas incluses dans l’histogramme des statistiques.
* Envisagez de mettre moins souvent à jour les colonnes de distribution statiques.
* N’oubliez pas que chaque objet de statistiques est mis à jour dans l’ordre. L’implémentation de l’élément `UPDATE STATISTICS <TABLE_NAME>` ne suffit pas toujours, notamment quand les tables sont volumineuses et incluent un grand nombre d’objets de statistiques.

Pour en savoir plus, consultez la section [Estimation de la cardinalité][Cardinality Estimation] de MSDN.

## <a name="examples-create-statistics"></a>Exemples de création de statistiques
Ces exemples indiquent comment utiliser différentes options pour créer des statistiques. Les options à utiliser pour chaque colonne dépendent des caractéristiques de vos données et de l’utilisation de la colonne dans les requêtes.

### <a name="create-single-column-statistics-with-default-options"></a>Créer des statistiques sur une colonne en utilisant les options par défaut
Pour créer des statistiques sur une colonne, il vous suffit d’indiquer le nom de l’objet de statistiques, ainsi que celui de la colonne.

Cette syntaxe a recours à toutes les options par défaut. Par défaut, SQL Data Warehouse échantillonne **20 pour cent** de la table quand il crée des statistiques.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Par exemple : 

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Créer des statistiques sur plusieurs colonnes en examinant chaque ligne
Le taux d’échantillonnage par défaut est de 20 %, ce qui est suffisant pour la plupart des situations. Toutefois, vous pouvez l’ajuster en fonction de vos besoins.

Pour échantillonner la table entière, utilisez la syntaxe suivante :

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Par exemple : 

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Créer des statistiques sur une colonne en spécifiant la taille de l’échantillon
Vous pouvez également spécifier cette taille sous la forme d’un pourcentage :

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Créer des statistiques sur une colonne sur certaines lignes uniquement
Vous pouvez également créer des statistiques sur une partie des lignes de votre table. On parle alors de « statistiques filtrées ».

Par exemple, vous pouvez utiliser les statistiques filtrées quand vous prévoyez d’interroger une partition spécifique dans une table partitionnée volumineuse. En créant des statistiques sur les valeurs des partitions uniquement, vous améliorez la précision des statistiques et, par conséquent, les performances des requêtes.

Dans cet exemple, des statistiques sont créées sur une plage de valeurs. Les valeurs peuvent facilement être définies de manière à correspondre à la plage de valeurs d’une partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Pour que l’optimiseur de requête envisage d’utiliser les statistiques filtrées lorsqu’il choisit le plan de requête distribuée, il faut que cette requête soit suffisamment petite pour pouvoir s’insérer dans la définition de l’objet de statistiques. Selon l’exemple précédent, la clause WHERE de la requête doit indiquer des valeurs incluses entre 2000101 et 20001231 dans la colonne col1.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Créer des statistiques sur une colonne en utilisant toutes les options
Vous pouvez aussi combiner les options. L’exemple suivant permet de créer un objet de statistiques filtrées avec une taille d’échantillon personnalisée :

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Pour accéder à la référence complète, consultez la section [CREATE STATISTICS][CREATE STATISTICS] de MSDN.

### <a name="create-multi-column-statistics"></a>Créer des statistiques sur plusieurs colonnes
Pour créer un objet de statistiques sur plusieurs colonnes, il vous suffit d’utiliser les exemples précédents, en spécifiant davantage de colonnes.

> [!NOTE]
> L’histogramme, qui est utilisé pour estimer le nombre de lignes dans le résultat d’une requête, est uniquement disponible pour la première colonne répertoriée dans la définition d’objet de statistiques.
> 
> 

Dans cet exemple, l’histogramme concerne l’élément *product\_category*. Les statistiques portant sur différentes colonnes sont calculées sur la base des éléments *product\_category* et *product\_sub_category* :

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Dans la mesure où il existe une corrélation entre les éléments *product\_category* et *product\_sub\_category*, un objet de statistiques sur plusieurs colonnes peut être utile quand le système accède à ces colonnes en même temps.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Créer des statistiques sur toutes les colonnes d’une table
Pour créer des statistiques, vous pouvez par exemple émettre des commandes CREATE STATISTICS après avoir créé la table :

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Utiliser une procédure stockée pour créer des statistiques sur toutes les colonnes d’une base de données
SQL Data Warehouse n’inclut pas de procédure stockée par le système équivalente à sp_create_stats dans SQL Server. Cette procédure stockée crée un objet de statistiques sur une colonne portant sur chaque colonne de la base de données non pourvue de statistiques.

L’exemple suivant vous aidera à commencer à concevoir votre base de données. N’hésitez pas à l’adapter à vos besoins :

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Pour créer des statistiques sur toutes les colonnes de la table avec cette procédure, il vous suffit de l’appeler.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```
Pour créer des statistiques sur toutes les colonnes de la table avec une analyse complète (fullscan), appelez cette procédure :

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```
Pour créer des échantillons de statistiques sur toutes les colonnes de la table, entrez 3 et le pourcentage de l’échantillon.  Cette procédure utilise un taux d’échantillonnage de 20 pour cent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```


Pour créer des échantillons de statistiques sur toutes les colonnes 

## <a name="examples-update-statistics"></a>Exemples de mises à jour des statistiques
Pour effectuer cette opération, vous avez différentes possibilités :

- Mettez à jour un objet de statistiques. Spécifiez le nom de l’objet de statistiques que vous souhaitez mettre à jour.
- Mettez à jour tous les objets de statistiques sur une table. Spécifiez le nom de la table, et non un objet de statistiques spécifique.

### <a name="update-one-specific-statistics-object"></a>Mettre à jour un objet de statistiques spécifique
Pour réaliser cette opération, utilisez la syntaxe suivante :

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Par exemple : 

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

En mettant à jour des objets de statistiques spécifiques, vous pouvez réduire les ressources et le temps requis pour gérer les statistiques. La sélection des objets de statistiques les plus appropriés pour une mise à jour prend du temps.

### <a name="update-all-statistics-on-a-table"></a>Mettre à jour tous les objets de statistiques dans une table
Voici une méthode simple pour mettre à jour tous les objets de statistiques dans une table :

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Par exemple : 

```sql
UPDATE STATISTICS dbo.table1;
```

Cette instruction est facile à utiliser. N’oubliez pas que cette action met à jour *toutes* les statistiques dans la table et, par conséquent, peut effectuer davantage de tâches que nécessaire. Si les performances ne sont pas un problème, il s’agit de la méthode la plus simple et la plus exhaustive pour garantir que les statistiques sont à jour.

> [!NOTE]
> Lors de la mise à jour de toutes les statistiques d’une table, SQL Data Warehouse procède à une analyse pour échantillonner la table, afin de rechercher chaque objet de statistiques. Si la table est volumineuse et comprend un grand nombre de colonnes et de statistiques, il peut s’avérer plus efficace de mettre à jour les statistiques individuellement, en fonction des besoins.
> 
> 

Pour obtenir une implémentation d’une procédure `UPDATE STATISTICS`, consultez [Tables temporaires][Temporary]. La méthode d’implémentation est légèrement différente de celle de la procédure `CREATE STATISTICS` précédente, mais le résultat est le même.

Pour accéder à la syntaxe complète, consultez la section [Mise à jour des statistiques][Update Statistics] de MSDN.

## <a name="statistics-metadata"></a>Métadonnées de statistiques
Vous pouvez utiliser plusieurs fonctions et vues système pour rechercher des informations sur des statistiques. Par exemple, vous pouvez voir si un objet de statistiques peut être obsolète à l’aide de la fonction stats-date (qui permet de connaître la date de création ou de dernière mise à jour des statistiques).

### <a name="catalog-views-for-statistics"></a>Vues de catalogue des statistiques
Ces vues système fournissent des informations sur les statistiques :

| Vue de catalogue | Description |
|:--- |:--- |
| [sys.columns][sys.columns] |Une ligne pour chaque colonne. |
| [sys.objects][sys.objects] |Une ligne pour chaque objet de la base de données. |
| [sys.schemas][sys.schemas] |Une ligne pour chaque schéma de la base de données. |
| [sys.stats][sys.stats] |Une ligne pour chaque objet de statistiques. |
| [sys.stats_columns][sys.stats_columns] |Une ligne pour chaque colonne de l’objet de statistiques. Paramètre lié à l’élément « sys.columns ». |
| [sys.tables][sys.tables] |Une ligne pour chaque table (y compris les tables externes). |
| [sys.table_types][sys.table_types] |Une ligne pour chaque type de données. |

### <a name="system-functions-for-statistics"></a>Fonctions système relatives aux statistiques
Ces fonctions système sont utiles lorsque vous gérez des statistiques :

| Fonction système | Description |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |Date de la dernière mise à jour de l’objet de statistiques. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Informations détaillées et récapitulatives sur la distribution des valeurs, telles que l’objet de statistiques la comprend. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combiner des fonctions et des colonnes de statistiques en une seule vue
Cette vue regroupe les colonnes portant sur les statistiques et les résultats de la fonction STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Exemples portant sur la fonction DBCC SHOW_STATISTICS()
La fonction DBCC SHOW_STATISTICS() présente les données contenues dans un objet de statistiques. Ces données sont affichées en trois parties :

- En-tête
- Vecteur de densité
- Histogramme

Métadonnées de l’en-tête sur les statistiques. L’histogramme affiche la distribution des valeurs dans la première colonne de l’objet de statistiques. Le vecteur de densité mesure la corrélation entre les colonnes. SQL Data Warehouse calcule les évaluations de cardinalité avec certaines données dans l’objet de statistiques.

### <a name="show-header-density-and-histogram"></a>Afficher l’en-tête, la densité et l’histogramme
Cet exemple simple illustre les trois parties d’un objet de statistiques :

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Par exemple : 

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Afficher une ou plusieurs parties de la fonction DBCC SHOW_STATISTICS()
Si vous êtes uniquement intéressé par l’affichage de certaines parties, utilisez la clause `WITH` et spécifiez les parties que vous voulez voir :

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Par exemple : 

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Différences liées à la fonction DBCC SHOW_STATISTICS()
La fonction DBCC SHOW_STATISTICS() est implémentée de manière plus stricte dans SQL Data Warehouse que dans SQL Server :

- Les fonctionnalités non documentées ne sont pas prises en charge.
- Impossible d’utiliser le paramètre Stats_stream.
- Impossible de joindre les résultats de sous-ensembles spécifiques de données de statistiques, par exemple (STAT_HEADER JOIN DENSITY_VECTOR).
- L’élément NO_INFOMSGS ne peut pas être défini pour la suppression des messages.
- Vous ne pouvez pas placer de crochets autour des noms de statistiques.
- Vous ne pouvez pas utiliser les noms de colonnes pour identifier les objets de statistiques.
- L’erreur personnalisée 2767 n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus, consultez la section [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] de MSDN.

  Pour plus d’informations, consultez les articles [Vue d’ensemble des tables][Overview], [Types de données de table][Data Types], [Distribution d’une table][Distribute], [Indexation d’une table][Index], [Partitionnement d’une table][Partition] et [Tables temporaires][Temporary].
  
   Pour en savoir plus sur les meilleures pratiques, consultez [Meilleures pratiques relatives à SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
