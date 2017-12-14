---
title: "Guide de conception pour les tables répliquées - Azure SQL Data Warehouse | Microsoft Docs"
description: "Recommandations relatives à la conception de tables répliquées dans votre schéma Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/23/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: 575b3c5710d744e99c6e02439577a362eb17c67e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Guide de conception pour l’utilisation de tables répliquées dans Azure SQL Data Warehouse
Cet article vous fournit des recommandations relatives à la conception de tables répliquées dans votre schéma SQL Data Warehouse. Utilisez ces recommandations pour améliorer les performances des requêtes en réduisant le déplacement de données et la complexité des requêtes.

> [!NOTE]
> La fonctionnalité de table répliquée est actuellement disponible en préversion publique. Certains comportements sont susceptibles d’être modifiés.
> 

## <a name="prerequisites"></a>Composants requis
Cet article suppose que vous êtes familiarisé avec les concepts de distribution et de déplacement des données dans SQL Data Warehouse.  Pour plus d’informations, consultez l’article sur [l’architecture](massively-parallel-processing-mpp-architecture.md). 

Dans le cadre de la conception d’une table, essayez d’en savoir autant que possible sur vos données et la façon dont elles sont interrogées.  Considérez par exemple les questions suivantes :

- Quelle est la taille de la table ?   
- Quelle est la fréquence d’actualisation de la table ?   
- Est-ce que je dispose de tables de faits et de dimension dans un entrepôt de données ?   

## <a name="what-is-a-replicated-table"></a>Qu’est-ce qu’une table répliquée ?
Une table répliquée possède une copie complète de la table accessible sur chaque nœud de calcul. La réplication d’une table évite le transfert de données entre des nœuds de calcul avant une jointure ou une agrégation. Étant donné que la table possède plusieurs copies, le fonctionnement des tables répliquées est optimal lorsque la taille de la table est inférieure à 2 Go compressés.

Le diagramme suivant illustre une table répliquée accessible sur chaque nœud de calcul. Dans SQL Data Warehouse, la table répliquée est entièrement copiée dans une base de données de distribution sur chaque nœud de calcul. 

![Table répliquée](media/guidance-for-using-replicated-tables/replicated-table.png "Table répliquée")  

Le fonctionnement des tables répliquées est mieux adapté aux petites tables de dimension dans un schéma en étoile. Les tables de dimension ont généralement une taille qui permet de stocker et de gérer plusieurs copies. Les dimensions stockent des données descriptives qui se modifient lentement, comme le nom et l’adresse du client, ainsi que les détails sur le produit. La lenteur de la nature changeante des données entraîne moins de reconstructions de la table répliquée. 

Envisagez d’utiliser une table répliquée dans les cas suivants :

- La taille de la table sur le disque est inférieure à 2 Go, quel que soit le nombre de lignes. Pour connaître la taille d’une table, vous pouvez utiliser la commande [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) : `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- La table est utilisée dans des jointures qui requièrent normalement un déplacement des données. Par exemple, une jointure sur des tables distribuées par hachage requiert le déplacement des données lorsque les colonnes de jointure ne correspondent pas à la colonne de distribution. Si l’une des tables distribuées par hachage est de petite taille, pensez à utiliser une table répliquée. Une jointure sur une table de distribution par tourniquet (round-robin) requiert le déplacement des données. Nous vous recommandons d’utiliser des tables répliquées au lieu de tables de distribution par tourniquet dans la plupart des cas. 


Envisagez de convertir une table distribuée existante en table répliquée dans les cas suivants :

- Les plans de requêtes utilisent des opérations de déplacement des données qui diffusent les données sur tous les nœuds de calcul. L’opération BroadcastMoveOperation est coûteuse et ralentit les performances de requête. Pour consulter les opérations de déplacement des données dans les plans de requêtes, utilisez [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).
 
Les tables répliquées ne produisent sans doute pas les meilleurs résultats dans les cas suivants :

- La table est l’objet d’opérations d’insertion, de mise à jour et de suppression fréquentes. Ces opérations DLM (langage de manipulation de données) nécessitent une regénération de la table répliquée. La reconstruction fréquente peut diminuer les performances.
- L’entrepôt de données est souvent mis à l’échelle. La mise à l’échelle d’un entrepôt de données modifie le nombre de nœuds de calcul, ce qui entraîne une reconstruction.
- La table comporte un grand nombre de colonnes, mais les opérations de données n’accèdent généralement qu’à un nombre restreint de colonnes. Dans ce scénario, au lieu de répliquer la table entière, il peut s’avérer plus efficace de distribuer la table par hachage et de créer ensuite un index sur les colonnes fréquemment sollicitées. Lorsqu’une requête requiert le déplacement des données, SQL Data Warehouse déplace uniquement les données dans les colonnes demandées. 



## <a name="use-replicated-tables-with-simple-query-predicates"></a>Utiliser des tables répliquées avec des prédicats de requête simples
Avant de décider de distribuer ou de répliquer une table, pensez aux types de requêtes que vous projetez d’exécuter sur la table. Lorsque possible,

- Utilisez des tables répliquées pour les requêtes avec des prédicats de requête simples, comme l’égalité ou l’inégalité.
- Utilisez des tables distribuées pour les requêtes avec des prédicats de requête complexes, comme LIKE ou NOT LIKE.

Les requêtes sollicitant beaucoup le processeur fonctionnent de manière optimale lorsque le travail est réparti entre tous les nœuds de calcul. Par exemple, les requêtes qui exécutent des calculs sur chaque ligne d’une table fonctionnent mieux sur les tables distribués que sur les tables répliquées. Étant donné qu’une table répliquée est entièrement stockée sur chaque nœud de calcul, une requête sollicitant beaucoup le processeur sur une table répliquée s’exécute sur la table entière sur chaque nœud de calcul. Le calcul supplémentaire peut ralentir les performances des requêtes.

Par exemple, cette requête comporte un prédicat complexe.  Elle s’exécute plus rapidement lorsque le fournisseur est une table distribuée au lieu d’une table répliquée. Dans cet exemple, le fournisseur peut être distribué par hachage ou distribué par tourniquet.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Convertir les tables de distribution par tourniquet (round-robin) en tables répliquées
Si vous avez déjà des tables de distribution par tourniquet, nous vous recommandons de les convertir en tables répliquées si elles sont conformes aux critères décrits dans cet article. Les tables répliquées ont de meilleures performances que les tables de distribution par tourniquet, car elles éliminent le déplacement des données.  Une table de distribution par tourniquet requiert toujours le déplacement des données pour les jointures. 

Cet exemple utilise [CTAS](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) pour modifier la table DimSalesTerritory en une table répliquée. Cet exemple fonctionne que la table DimSalesTerritory soit distribuée par hachage ou par tourniquet.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exemple de performances de requête pour une table de distribution par tourniquet et une table répliquée 

Une table répliquée ne nécessite pas le déplacement des données pour les jointures, car la table entière est déjà présente sur chaque nœud de calcul. Si les tables de dimension sont des tables distribuées par tourniquet, une jointure copie entièrement la table de dimension sur chaque nœud de calcul. Pour déplacer les données, le plan de requête contient une opération appelée BroadcastMoveOperation. Ce type d’opération de déplacement des données ralentit les performances des requêtes. Il n’est pas utilisé par les tables répliquées. Pour afficher les étapes relatives au plan de requête, utilisez la vue catalogue système [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql). 

Par exemple, dans la requête suivante sur le schéma AdventureWorks, la table ` FactInternetSales` est distribuée par hachage. Les tables `DimDate` et `DimSalesTerritory` sont des tables de dimension plus petites. Cette requête retourne le total des ventes en Amérique du Nord pour l’année fiscale 2004 :
 
```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
Nous avons recréé les tables `DimDate` et `DimSalesTerritory` en tant que tables de distribution par tourniquet. Par conséquent, la requête affichait le plan de requête suivant, avec plusieurs opérations de déplacement pour la diffusion : 
 
![Plan de requête de type tourniquet (round robin)](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Nous avons recréé les tables `DimDate` et `DimSalesTerritory` en tant que tables répliquées et réexécuté la requête. Le plan de requête qui en résulte est beaucoup plus court et ne contient pas de mouvements de diffusion.

![Plan de requête répliqué](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considérations relatives aux performances pour la modification des tables répliquées
SQL Data Warehouse implémente une table répliquée en conservant une version principale de la table. Il copie la version principale dans une base de données de distribution sur chaque nœud de calcul. En cas de modification, SQL Data Warehouse met d’abord à jour la table principale. Puis, il demande une reconstruction des tables sur chaque nœud de calcul. Une reconstruction d’une table répliquée implique la copie de la table sur chaque nœud de calcul et la reconstruction des index.

Les reconstructions sont requises après les événements suivants :
- Des données sont chargées ou modifiées
- L’entrepôt de données est mis à l’échelle avec un [niveau de service](performance-tiers.md#service-levels) différent
- La définition de la table est mise à jour

Les reconstructions ne sont pas requises après les événements suivants :
- Opération de suspension
- Opération de reprise

La reconstruction ne se produit pas immédiatement après la modification des données. Au lieu de cela, la reconstruction est déclenchée la première fois qu’une requête est sélectionnée à partir de la table.  L’instruction select initiale de la table contient les étapes pour reconstruire la table répliquée.  Étant donné que la reconstruction est effectuée dans la requête, l’impact sur l’instruction select initiale peut être significatif selon la taille de la table.  Si plusieurs tables répliquées nécessitant une reconstruction sont impliquées, chaque copie est reconstruite en série en fonction des étapes de l’instruction.  Pour maintenir la cohérence des données pendant la reconstruction de la table répliquée, un verrou exclusif est placé sur la table.  Le verrou empêche tout accès à la table pendant la durée de la reconstruction. 

### <a name="use-indexes-conservatively"></a>Utilisation restrictive des index
Les pratiques d’indexation standard s’appliquent aux tables répliquées. SQL Data Warehouse reconstruit chaque index de table répliquée dans le cadre de la reconstruction. Utilisez les index uniquement lorsque le gain de performances compense le coût de reconstruction des index.  
 
### <a name="batch-data-loads"></a>Chargements de données par lots
Lors du chargement de données dans des tables répliquées, essayez de réduire les reconstructions en regroupant les chargements par lots. Effectuez tous les chargements par lots avant d’exécuter des instructions select.

Par exemple, ce modèle de chargement charge les données à partir de quatre sources et appelle quatre reconstructions. 

- Charger à partir de la source 1.
- Instruction select qui déclenche la reconstruction 1.
- Charger à partir de la source 2.
- Instruction select qui déclenche la reconstruction 2.
- Charger à partir de la source 3.
- Instruction select qui déclenche la reconstruction 3.
- Charger à partir de la source 4.
- Instruction select qui déclenche la reconstruction 4.

Par exemple, ce modèle de chargement charge les données à partir de quatre sources mais n’appelle qu’une seule reconstruction.

- Charger à partir de la source 1.
- Charger à partir de la source 2.
- Charger à partir de la source 3.
- Charger à partir de la source 4.
- Instruction select qui déclenche la reconstruction.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Reconstruire une table répliquée après un chargement par lots
Pour garantir des temps d’exécution de requête cohérents, nous vous recommandons de forcer une actualisation des tables répliquées après un chargement par lots. Sinon, la première requête doit attendre que les tables s’actualisent, ce qui implique la reconstruction des index. Selon la taille et le nombre des tables répliquées affectées, l’impact sur les performances peut être important.  

Cette requête utilise le DMV [sys.pdw_replicated_table_cache_state](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) pour répertorier les tables répliquées qui ont été modifiées mais pas reconstruites.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Pour forcer une reconstruction, exécutez l’instruction suivante sur chaque table dans la sortie précédente. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Étapes suivantes 
Pour créer une table répliquée, utilisez l’une de ces instructions :

- [CREATE TABLE (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE AS SELECT (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Pour une vue d’ensemble des tables distribuées, consultez [Tables distribuées](sql-data-warehouse-tables-distribute.md).



