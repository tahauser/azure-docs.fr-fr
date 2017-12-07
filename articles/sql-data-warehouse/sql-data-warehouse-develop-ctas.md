---
title: Instruction Create Table As Select (CTAS) dans SQL Data Warehouse | Microsoft Docs
description: "Conseils de codage à l’aide de l’instruction Create Table As Select (CTAS) dans Azure SQL Data Warehouse pour le développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 68ac9a94-09f9-424b-b536-06a125a653bd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: a885ba4f455fecd158696faaee38c83c1e4ec0bf
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Instruction Create Table As Select (CTAS) dans SQL Data Warehouse
L’instruction Create Table As Select ou `CTAS` est l’une des fonctionnalités T-SQL les plus importantes. Il s’agit d’une opération entièrement parallélisée qui crée une table en fonction de la sortie d’une instruction SELECT. `CTAS` est le moyen le plus simple et le plus rapide de créer une copie d’une table. Ce document fournit des exemples et les meilleures pratiques pour `CTAS`.

## <a name="selectinto-vs-ctas"></a>SELECT..INTO et CTAS
`CTAS` est en quelque sorte une version améliorée de `SELECT..INTO`.

Vous trouverez ci-dessous un exemple simple d’une instruction `SELECT..INTO` :

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Dans l’exemple ci-dessus, `[dbo].[FactInternetSales_new]` est créé sous la forme d’une table distribuée ROUND_ROBIN avec un index CLUSTERED COLUMNSTORE, conformément aux options par défaut des tables dans Azure SQL Data Warehouse.

Toutefois, `SELECT..INTO` ne vous permet pas de modifier la méthode de distribution ou le type d’index dans le cadre de l’opération. C’est là où `CTAS` entre en jeu.

La conversion de cette instruction en `CTAS` se révèle assez simple :

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

`CTAS` vous permet de modifier la distribution des données de table et le type de table. 

> [!NOTE]
> Si vous essayez uniquement de modifier l’index dans votre opération `CTAS` et que la table source est distribuée par hachage, vous pouvez optimiser les performances de votre opération `CTAS` en conservant la même colonne de distribution et le même type de données. Ceci permet d’éviter le déplacement des données entre les distributions pendant l’opération, d’où une efficacité accrue.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>Utilisation de CTAS pour copier une table
L’une des utilisations les plus courantes de `CTAS` consiste sans doute à créer une copie d’une table afin que vous puissiez modifier la DDL. Si, par exemple, vous avez à l'origine créé votre table en tant que `ROUND_ROBIN` et que vous voulez maintenant la remplacer par une table distribuée sur une colonne, `CTAS` vous permet de modifier la colonne de distribution. `CTAS` peut également servir à changer le partitionnement, l’indexation ou les types de colonnes.

Supposons que vous avez créé cette table en utilisant le type de distribution par défaut `ROUND_ROBIN` distribué, car aucune colonne de distribution n’a été spécifiée dans `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Maintenant, vous souhaitez créer une copie de cette table avec un index Columnstore en cluster afin de tirer parti des performances de tables Columnstore en cluster. Vous souhaitez également distribuer cette table sur ProductKey dans la mesure où vous anticipez des jointures sur cette colonne et vous souhaitez éviter le déplacement de données au cours des jointures sur ProductKey. Enfin, vous souhaitez également ajouter le partitionnement sur OrderDateKey pour supprimer rapidement les anciennes données en supprimant les anciennes partitions. Voici l'instruction CTAS qui copie votre ancienne table dans une nouvelle table.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Pour finir, vous pouvez renommer vos tables pour intégrer votre nouvelle table et supprimer votre ancienne table.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [!NOTE]
> Azure SQL Data Warehouse ne prend pas encore en charge les statistiques à création ou mise à jour automatique.  Pour optimiser les performances de vos requêtes, il est important de créer les statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données.  Pour obtenir une explication détaillée des statistiques, consultez la rubrique [Statistiques][Statistics] dans le groupe de rubriques sur le développement.
> 
> 

## <a name="using-ctas-to-work-around-unsupported-features"></a>Utilisation de CTAS pour contourner les fonctionnalités non prises en charge
`CTAS` permet également de contourner un certain nombre de fonctionnalités non prises en charge qui sont répertoriées ci-après. L’utilisation de cette instruction se révèle doublement avantageuse, car elle assure non seulement la conformité de votre code, mais également une vitesse d’exécution accrue de ce dernier sur SQL Data Warehouse. Ces deux avantages découlent de sa conception entièrement parallélisée. Les scénarios qui peuvent être contournés avec CTAS comprennent notamment :

* Jointures ANSI sur les opérations UPDATE
* Jointures ANSI sur les opérations DELETE
* Instruction MERGE

> [!NOTE]
> Envisagez toujours de recourir à l’instruction CTAS en premier lieu. Si vous pensez pouvoir résoudre un problème en utilisant une instruction `CTAS` , il s’agit généralement de la meilleure approche à adopter, même si cela vous oblige à écrire davantage de données.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>Remplacement de jointures ANSI pour les instructions de mise à jour
Vous disposez peut-être d’une mise à jour complexe qui joint plus de deux tables à l’aide d’une syntaxe de jointure ANSI pour l’exécution d’une opération UPDATE ou DELETE.

Imaginons que vous souhaitiez mettre à jour la table suivante :

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

La requête d’origine pourrait ressembler à ceci :

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Étant donné que SQL Data Warehouse ne prend pas en charge les jointures ANSI dans la clause `FROM` d’une instruction `UPDATE`, vous ne pouvez pas copier ce code sans le modifier légèrement.

Vous pouvez remplacer ce code en combinant une instruction `CTAS` et une jointure implicite :

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Remplacement de jointures ANSI pour les instructions de suppression
L’utilisation de l’instruction `CTAS`constitue parfois la meilleure approche pour supprimer des données. Plutôt que de supprimer les données, vous pouvez simplement sélectionner les données à conserver. Cette approche est particulièrement recommandée dans le cas des instructions `DELETE` qui utilisent une syntaxe de jointure ANSI, car cette dernière n’est pas prise en charge par SQL Data Warehouse dans la clause `FROM` d’une instruction `DELETE`.

Voici un exemple d’instruction DELETE convertie :

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Remplacement d’instructions MERGE
Vous pouvez remplacer les instructions MERGE, du moins partiellement, à l’aide de `CTAS`. Vous pouvez consolider les instructions `INSERT` et `UPDATE` dans une seule et même instruction. Tous les enregistrements supprimés doivent être isolés dans une seconde instruction.

Voici un exemple d’utilisation d’une instruction consolidée `UPSERT` :

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Recommandation CTAS : déclarer explicitement le type de données et la possibilité de valeur NULL de la sortie
Lorsque vous procédez à la migration de votre code, vous pouvez constater que vous exécutez le type de modèle de codage suivant :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinctivement, vous pourriez envisager de convertir ce code en instruction CTAS, et vous auriez raison. Toutefois, un problème se dissimule derrière ce scénario.

Le code suivant ne génère PAS le même résultat :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Notez que la colonne « result » indique le type de données et les valeurs de possibilité de valeur NULL de l’expression. De légers écarts de valeurs peuvent en découler si vous n’y prenez pas garde.

Essayez l’exemple de code suivant :

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

La valeur stockée en guise de résultat diffère. Étant donné que la valeur persistante dans la colonne de résultat est utilisée dans d’autres expressions, l’erreur devient encore plus significative.

![][1]

Ceci se révèle particulièrement important dans le cas des migrations de données. Bien que la seconde requête soit effectivement plus précise, il existe un problème. Les données obtenues diffèrent de celles du système source, ce qui compromet l’intégrité de la migration. Il s’agit là de l’un des rares cas dans lesquels la « mauvaise » réponse est en réalité la bonne !

Cet écart entre les deux résultats est dû à la conversion de type (transtypage) implicite. Dans le premier exemple, la table définit la définition de colonne. Lorsque la ligne est insérée, une conversion de type implicite est effectuée. Dans le second exemple, aucune conversion de type implicite ne se produit, car l’expression définit le type de données de la colonne. Notez également que la colonne figurant dans le second exemple a été définie comme une colonne Nullable, ce qui n’est pas son cas dans le premier exemple. Lorsque la table a été créée dans le premier exemple, la possibilité de valeur Null de la colonne a été définie explicitement. Dans le second exemple, cet aspect est déterminé par l’expression et entraîne par défaut une définition de valeur Null.  

Pour résoudre ces problèmes, vous devez explicitement définir la conversion de type et la possibilité de valeur Null dans la partie `SELECT` de l’instruction `CTAS`. Vous ne pouvez pas définir ces propriétés dans la partie CREATE TABLE.

L’exemple ci-dessous indique comme corriger le code :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Notez les points suivants :

* Nous aurions pu utiliser CAST ou CONVERT.
* La possibilité de valeur Null est forcée avec ISNULL, et non avec COALESCE.
* ISNULL est la fonction située la plus à l’extérieur.
* La seconde partie de l’instruction ISNULL est une constante, c’est-à-dire 0.

> [!NOTE]
> La possibilité de valeur Null doit être impérativement définie avec `ISNULL`, et non avec `COALESCE`. `COALESCE` n’est pas une fonction déterministe et par conséquent, le résultat de l’expression sera toujours NULLable. `ISNULL` est différent. Cette fonction est déterministe. Par conséquent, lorsque la seconde partie de la fonction `ISNULL` est une constante ou un littéral, la valeur résultante est NOT NULL.
> 
> 

Ce conseil ne vous sert pas seulement à assurer l’intégrité de vos calculs. Il est également important dans le cadre du basculement de partition de table. Imaginons que vous ayez défini la table suivante :

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Toutefois, le champ de valeur est une expression calculée qui ne fait pas partie des données sources.

Pour créer votre jeu de données partitionné, vous pouvez exécuter le code suivant :

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Cette requête s’exécute parfaitement. Toutefois, un problème se pose lorsque vous essayez de procéder au basculement de partition. Les définitions de table ne correspondent pas. Vous devez donc modifier l’instruction CTAS pour faire correspondre ces définitions.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Vous pouvez alors constater que la cohérence des types et le maintien des propriétés de possibilité de valeur Null sur une instruction CTAS constituent des approches d’ingénierie judicieuses. Ces opérations contribuent à garantir l’intégrité de vos calculs, ainsi que la possibilité de basculement de partition.

Pour plus d’informations sur l’utilisation de [CTAS][CTAS], consultez le site MSDN. Cette instruction est l’une des plus importantes d’Azure SQL Data Warehouse. Vous devez donc faire en sorte d’en comprendre les moindres aspects.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez la [vue d’ensemble du développement][development overview].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
