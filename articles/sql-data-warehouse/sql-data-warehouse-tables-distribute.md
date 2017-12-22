---
title: Distribution de tables dans SQL Data Warehouse | Microsoft Docs
description: Prise en main de la distribution de tables dans Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 5ed4337f-7262-4ef6-8fd6-1809ce9634fc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 82e17e575cdb227af2fabf94f01e94df22994aac
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribution de tables dans SQL Data Warehouse
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

SQL Data Warehouse est un système de base de données distribuée à traitement massivement parallèle.  En répartissant les données et les fonctions de traitement sur plusieurs nœuds, SQL Data Warehouse propose une évolutivité immense, bien supérieure à ce qu’offre n’importe quel système unique.  Le fait de décider comment distribuer vos données dans votre SQL Data Warehouse est un des facteurs les plus importants pour obtenir des performances optimales.   L’élément clé permettant d’optimiser les performances est de réduire le déplacement des données et celui permettant de réduire le déplacement des données est de sélectionner la stratégie de distribution appropriée.

## <a name="understanding-data-movement"></a>Présentation du déplacement des données
Dans un système MPP, les données de chaque table sont réparties dans plusieurs bases de données sous-jacentes.  Les requêtes les plus optimisées sur un système MPP peuvent simplement être passées pour s’exécuter sur les différentes bases de données distribuées sans aucune interaction entre les autres bases de données.  Par exemple, supposons que vous disposez d’une base de données avec des données de ventes, qui contient deux tables, ventes et clients.  Si vous avez une requête qui doit joindre votre table de ventes à votre table de clients, et que vous divisez ces deux tables par numéro de client, en plaçant chaque client dans une base de données distincte, toutes les requêtes qui joignent des ventes à des clients peuvent être résolues dans chaque base de données sans connaissance des autres bases de données.  En revanche, si vous avez divisé vos données de vente par numéro de commande et vos données de clients par numéro de client, les bases de données ne disposeront pas des données correspondantes pour chaque client. Ainsi, si vous souhaitez joindre vos données de ventes à vos données de clients, vous devez obtenir les données relatives à chaque client à partir des autres bases de données.  Dans ce second exemple, un déplacement des données devra se produire pour déplacer les données des clients vers les données de ventes, afin que les deux tables puissent être jointes.  

Le déplacement des données n’est pas toujours une mauvaise chose. Il est parfois nécessaire pour résoudre une requête.  Mais quand cette étape supplémentaire peut être évitée, votre requête s’exécute naturellement plus rapidement.  Le déplacement des données se produit généralement en cas de jointure ou d’agrégation de tables.  Il arrive souvent que vous deviez effectuer ces deux opérations à la fois. Ainsi, même si vous pouvez optimiser pour un scénario (par exemple une jointure), vous devrez quand même déplacer des données pour vous aider à remplir les conditions de l’autre scénario (par exemple une agrégation).  L’astuce est de déterminer ce qui nécessite le moins de travail.  Dans la plupart des cas, la distribution de grandes tables de faits sur une colonne de jointure commune est la méthode la plus efficace pour réduire le déplacement des données.  La distribution de données sur des colonnes de jointure est une méthode de réduction du déplacement des données beaucoup plus courante que la distribution de données sur des colonnes impliquées dans une agrégation.

## <a name="select-distribution-method"></a>Sélectionner la méthode de distribution
En arrière-plan, SQL Data Warehouse divise vos données en 60 bases de données.  Chaque base de données est considérée comme une **distribution**.  Lorsque les données sont chargées dans chaque table, SQL Data Warehouse doit savoir comment répartir vos données parmi ces 60 distributions.  

La méthode de distribution est définie au niveau de la table et actuellement, il existe deux possibilités :

1. **Tourniquet (round robin)** , qui distribue les données de manière équitable mais aléatoire.
2. **distribution par hachage** , qui distribue les données en fonction des valeurs de hachage d’une colonne unique.

Par défaut, quand vous ne définissez pas de méthode de distribution de données, votre table est distribuée à l’aide de la méthode de distribution du **tourniquet (round robin)** .  Toutefois, à mesure que vous affinez votre implémentation, vous voudrez peut-être utiliser des tables à **distribution par hachage** afin de réduire le déplacement des données, ce qui optimise les performances des requêtes.

### <a name="round-robin-tables"></a>Tables avec distribution par tourniquet (round robin)
L’utilisation de la méthode du tourniquet (round Robin) pour la distribution des données fonctionne comme suit :  Lors du chargement de vos données, chaque ligne est simplement envoyée à la distribution suivante.  Cette méthode distribue toujours aléatoirement les données de manière très uniforme sur toutes les distributions.  Autrement dit, aucun tri n’est effectué pendant le processus de tourniquet (round robin) qui place vos données.  C’est pour cette raison que ce type de distribution est parfois appelé « hachage aléatoire ».  Dans le cas d’une table avec distribution par tourniquet (round robin), il n’est pas nécessaire de comprendre la nature des données.  Pour cette raison, ce type de table représente souvent une cible de chargement adéquate.

Par défaut, si aucune méthode de distribution n’est choisie, la méthode de distribution par tourniquet (round robin) sera utilisée.  Toutefois, alors que les tables avec distribution par tourniquet (round robin) sont faciles à utiliser, dans la mesure où les données sont distribuées de manière aléatoire sur le système, cela signifie que le système ne peut pas garantir sur quelle distribution se trouve chaque ligne.  Par conséquent, le système doit parfois appeler une opération de déplacement des données pour mieux organiser vos données avant de pouvoir résoudre une requête.  Cette étape supplémentaire peut ralentir vos requêtes.

Vous pouvez envisager une distribution par tourniquet (round robin) des données de votre table dans les cas suivants :

* lors de la mise en route sous forme de point de départ simple ;
* s’il n’existe aucune clé de jointure évidente ;
* s’il n’existe aucune colonne adaptée à la distribution par hachage de la table ;
* si la table ne partage aucune clé de jointure avec d’autres tables ;
* si la jointure est moins importante que d’autres dans la requête ;
* lorsque la table est une table temporaire intermédiaire ;

Ces exemples créent une table à distribution par tourniquet (round robin) :

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [!NOTE]
> Bien que le tourniquet (round robin) soit le type de table par défaut, le fait qu’il soit explicite dans votre DDL est considéré comme étant une bonne pratique afin que les intentions de disposition de votre table soient claires pour les autres.
>
>

### <a name="hash-distributed-tables"></a>Tables à distribution par hachage
Le fait d’utiliser un algorithme de **distribution par hachage** pour distribuer vos tables peut améliorer les performances pour de nombreux scénarios en réduisant le déplacement des données au moment de la requête.  Ces tables sont réparties entre les bases de données distribuées à l’aide d’un algorithme de hachage portant sur une colonne unique que vous avez sélectionnée.  La colonne de distribution détermine comment les données sont réparties sur vos bases de données distribuées.  La fonction de hachage utilise la colonne de distribution pour affecter des lignes aux distributions.  L’algorithme de hachage et la distribution qui en résulte sont déterministes.  Autrement dit, la même valeur avec le même type de données aura toujours la même distribution.    

Cet exemple crée une table distribuée en fonction de l’ID :

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Sélectionner la colonne de distribution
Quand vous choisissez de **distribuer une table par hachage** , vous devez sélectionner une seule colonne de distribution.  Lorsque vous sélectionnez une colonne de distribution, vous devez prendre en compte trois facteurs essentiels.  

Sélectionnez une colonne unique qui :

1. ne sera pas mise à jour ;
2. distribuera les données de manière uniforme en évitant le décalage des données ;
3. réduira le nombre de déplacements des données.

### <a name="select-distribution-column-which-will-not-be-updated"></a>Sélectionner la colonne de distribution qui ne sera pas mise à jour
Les colonnes de distribution ne peuvent pas être mises à jour, par conséquent, sélectionnez une colonne avec des valeurs statiques.  Si une colonne doit être mise à jour, celle-ci n’est généralement pas une bonne candidate pour la distribution.  Si vous devez mettre à jour une colonne de distribution, il est possible d’effectuer cette opération en commençant par la suppression de la ligne et l’insertion d’une nouvelle.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Sélectionner la colonne de distribution qui distribuera les données de manière uniforme
Dans la mesure où un système distribué s’exécute à la vitesse de sa distribution la plus lente, il est important de travailler uniformément sur les distributions afin d’équilibrer l’exécution sur le système.  La manière dont le travail est réparti sur le système distribué repose sur l’endroit où résident les données de chaque distribution.  Il est donc très important de sélectionner la colonne de distribution de droite pour la distribution des données afin que chaque point de distribution ait la même quantité de travail et qu’il mette le même temps pour effectuer cette dernière.  Lorsque le travail est réparti sur le système, les données sont équilibrées entre les distributions.  Lorsque les données ne sont pas équilibrées, c’est ce que nous appelons le **décalage des données**.  

Pour procéder à une répartition uniforme et éviter le décalage des données, tenez compte des éléments suivants lors de la sélection de votre colonne de distribution :

1. Sélectionnez une colonne contenant un nombre important de valeurs distinctes.
2. Évitez la distribution des données sur des colonnes avec peu de valeurs distinctes.
3. Évitez de distribuer les données sur des colonnes dont la fréquence de valeurs NULL est élevée.
4. Évitez de distribuer les données sur des colonnes de date.

Étant donné que chaque valeur est hachée sur 1 à 60 distributions, vous devez sélectionner une colonne particulièrement unique contenant plus de 60 valeurs uniques pour obtenir une distribution uniforme.  Pour illustrer cela, prenons un cas où une colonne contient seulement 40 valeurs uniques.  Si cette colonne a été sélectionnée en tant que clé de répartition, les données de cette table seraient réparties sur 40 distributions au plus, avec 20 distributions sans aucune donnée et aucun traitement à effectuer.  À l’inverse, les 40 autres distributions auraient plus de travail à effectuer si les données étaient réparties de manière uniforme sur plus de 60 distributions.  Ce scénario est un exemple de décalage de données.

Dans un système MPP, chaque étape de requête attend que toutes les distributions terminent leur part du travail.  Si une distribution effectue plus de travail que les autres, les ressources des autres distributions sont gaspillées à attendre la distribution occupée.  Lorsque le travail n’est pas réparti uniformément sur toutes les distributions, c’est ce que nous appelons le **décalage de traitement**.  Le décalage de traitement entraîne une exécution plus lente des requêtes par rapport à une répartition uniforme de la charge de travail sur les distributions.  Un décalage de données entraîne un décalage de traitement.

Évitez une distribution sur des colonnes contenant de nombreuses valeurs Null, car elles sont placées sur la même distribution. La distribution sur une colonne de date peut également entraîner un décalage de traitement, car toutes les données d’une date donnée sont placées sur la même distribution. Si plusieurs utilisateurs exécutent des requêtes toutes filtrées à la même date, seule 1 des 60 distributions réalise tout le travail, car une date donnée ne se trouvera que sur une seule distribution. Dans ce scénario, les requêtes s’exécuteront probablement 60 fois plus lentement que si les données avaient été réparties équitablement sur toutes les distributions.

Lorsqu’il n’existe aucune colonne appropriée, envisagez d’utiliser la méthode de distribution du tourniquet (round robin).

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Sélectionner la colonne de distribution qui réduira le déplacement des données
La réduction du déplacement des données par la sélection de la colonne de distribution adaptée est une des stratégies les plus importantes pour optimiser les performances de votre SQL Data Warehouse.  Le déplacement des données se produit généralement en cas de jointure ou d’agrégation de tables.  Les colonnes utilisées dans les clauses `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` et `HAVING` sont toutes **adéquates** pour une distribution par hachage.

En revanche, les colonnes de la clause `WHERE` ne sont **pas** adéquates pour une distribution par hachage, car elles limitent les distributions pouvant participer à la requête, ce qui entraîne un décalage de traitement.  Une colonne de date est un bon exemple d’une colonne pouvant être utilisée pour la distribution, mais qui peut entraîner ce décalage de traitement.

En règle générale, si deux grandes tables de faits sont souvent impliquées dans une jointure, la distribution des deux tables sur l’une des colonnes de jointure permet d’obtenir les meilleures performances.  Si vous disposez d’une table qui n’est jamais jointe à une autre grande table de faits, examinez les colonnes se trouvant fréquemment dans la clause `GROUP BY` .

Quelques critères clés doivent être remplis pour éviter le déplacement des données lors d’une jointure :

1. Les tables impliquées dans la jointure doivent être distribuées par hachage sur **l’une** des colonnes participant à la jointure.
2. Les types de données des colonnes de jointure doivent être identiques dans les deux tables.
3. Les colonnes doivent être jointes avec un opérateur d’égalité.
4. Le type de jointure ne peut pas être `CROSS JOIN`.

## <a name="troubleshooting-data-skew"></a>Résolution des problèmes de décalage de données
Quand les données de la table sont distribuées à l’aide de la méthode de distribution par hachage, il est probable que certaines distributions seront décalées pour avoir proportionnellement plus de données que d’autres. Un décalage excessif des données peut avoir un impact sur les performances des requêtes, car le résultat final d’une requête distribuée doit attendre la fin de la distribution dont l’exécution est la plus longue. En fonction du degré de décalage des données, vous devrez peut-être le résoudre.

### <a name="identifying-skew"></a>Identification du décalage
Un moyen simple d’identifier une table ayant subi un décalage consiste à utiliser `DBCC PDW_SHOWSPACEUSED`.  Il s’agit d’une solution simple et rapide pour afficher le nombre de lignes de la table stockées dans chacune des 60 distributions de votre base de données.  N’oubliez que pour obtenir les performances les plus équilibrées, les lignes de votre table distribuée doivent être partagées uniformément entre toutes les distributions.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Toutefois, si vous interrogez les vues de gestion dynamique (DMV) Azure SQL Data Warehouse, vous pouvez effectuer une analyse plus détaillée.  Pour commencer, créez la vue [dbo.vTableSizes][dbo.vTableSizes] en utilisant le SQL de l’article [Vue d’ensemble des tables][Overview].  Une fois la vue créée, exécutez cette requête pour identifier les tables dont le décalage des données est supérieur à 10 %.

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Résolution du décalage des données
Tous les décalages ne sont pas suffisants pour garantir un correctif.  Dans certains cas, les performances d’une table dans certaines requêtes peuvent annuler les dommages liés au décalage des données.  Pour déterminer si vous devez résoudre un décalage des données dans une table, vous devez comprendre au mieux les volumes de données et les requêtes dans votre charge de travail.   Vous pouvez suivre les étapes de l’article relatif à la [surveillance des requêtes][Query Monitoring] pour analyser l’impact du décalage sur les performances des requêtes, en particulier sur leur durée d’exécution sur chaque distribution.

La distribution de données consiste à trouver le juste équilibre entre la réduction du décalage des données et la réduction du déplacement des données. Ces buts peuvent s’opposer, et parfois, vous pouvez conserver le décalage des données afin de réduire le déplacement des données. Par exemple, quand la colonne de distribution est souvent la colonne partagée dans les jointures et les agrégations, vous devez minimiser le déplacement des données. L’avantage d’un déplacement minimal des données peut compenser l’impact d’un décalage des données.

La méthode classique permettant de résoudre un décalage des données consiste à recréer la table avec une colonne de distribution différente. Dans la mesure où il n’existe aucun moyen de modifier la colonne de distribution d’une table existante, vous devez recréer la distribution d’une table avec un [CTAS][].  Voici deux exemples illustrant comment résoudre le décalage de données :

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Exemple 1 : Recréer la table avec une nouvelle colonne de distribution
Cet exemple utilise [CTAS][] pour recréer une table avec une colonne de distribution par hachage différente.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Exemple 2 : Recréer la table à l’aide de la distribution par tourniquet (round robin)
Cet exemple utilise [CTAS][] pour recréer une table avec une distribution par tourniquet (round robin) et non par hachage. Cette modification génère une répartition uniforme des données au prix d’un déplacement des données plus important.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la conception des tables, consultez les articles [Distribuer][Distribute], [Index][Index], [Partition][Partition], [Types de données][Data Types], [Statistiques][Statistics] et [Tables temporaires][Temporary].

Pour obtenir une vue d’ensemble des bonnes pratiques, consultez [Bonnes pratiques pour Azure SQL Data Warehouse][SQL Data Warehouse Best Practices].

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
[Query Monitoring]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
