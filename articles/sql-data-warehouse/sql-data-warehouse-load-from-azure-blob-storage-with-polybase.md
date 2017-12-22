---
title: "Charger à partir d’Azure Blob vers l’entrepôt de données Azure| Microsoft Docs"
description: "Apprenez à utiliser PolyBase pour charger des données de stockage d’objets blob Azure dans SQL Data Warehouse. Chargez quelques tables à partir de données publiques dans le schéma d’entrepôt de données Contoso Retail."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: barbkess
editor: 
ms.assetid: faca0fe7-62e7-4e1f-a86f-032b4ffcb06e
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: barbkess
ms.openlocfilehash: 4221bcd5a50fad680427a500e32837c1e75dd990
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Charger les données de stockage d’objets blob Azure dans SQL Data Warehouse (PolyBase)
> [!div class="op_single_selector"]
> * [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
> * [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
> 
> 

Utilisez PolyBase et les commandes T-SQL pour charger les données de stockage d’objets blob Azure dans Azure SQL Data Warehouse. 

En quelques mots, ce didacticiel charge deux tables de stockage d’objets blob Azure public dans le schéma d’entrepôt de données Contoso Retail. Pour charger le jeu de données, exécutez l’exemple [Load the full Contoso Retail Data Warehouse][Load the full Contoso Retail Data Warehouse] (Charger l’ensemble de l’entrepôt de données Contoso Retail) à partir du référentiel d’exemples Microsoft SQL Server.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

1. Configurer PolyBase pour effectuer un chargement à partir du stockage d’objets blob Azure
2. Charger des données publiques dans votre base de données
3. Procéder à des optimisations une fois le chargement terminé

## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter ce didacticiel, vous avez besoin d’un compte Azure qui possède déjà une base de données SQL Data Warehouse. Si vous n’en disposez pas, consultez la page [Créer un entrepôt de données SQL][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Configurer la source de données
PolyBase utilise des objets externes T-SQL pour définir l’emplacement et les attributs des données externes. Les définitions d’objet externe sont stockées dans SQL Data Warehouse. Les données elles-mêmes sont stockées en externe.

### <a name="11-create-a-credential"></a>1.1. Créer des informations d’identification
**Ignorez cette étape** si vous chargez les données publiques de Contoso. Vous n’avez pas besoin d’un accès sécurisé aux données publiques, car ces dernières sont accessibles à tous.

**N’ignorez pas cette étape** si vous utilisez ce didacticiel comme modèle pour le chargement de vos propres données. Pour accéder aux données par le biais d’informations d’identification, utilisez le script suivant afin de créer des informations d’identification de niveau base de données, puis définissez l’emplacement de la source de données.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Passez à l’étape 2.

### <a name="12-create-the-external-data-source"></a>1.2. Créer la source de données externe
Utilisez la commande [CREATE EXTERNAL DATA SOURCE][CREATE EXTERNAL DATA SOURCE] pour stocker l’emplacement des données et le type de données. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Si vous choisissez de rendre publics vos conteneurs de stockage d’objets blob Azure, n’oubliez pas qu’en tant que propriétaire des données vous devez vous acquitter des frais d’acheminement lorsque des données quittent le centre de données. 
> 
> 

## <a name="2-configure-data-format"></a>2. Configurer le format des données
Les données sont stockées dans des fichiers texte dans le stockage d’objets blob Azure, et chaque champ est séparé par un délimiteur. Exécutez cette commande [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT] pour spécifier le format des données dans les fichiers texte. Les données Contoso ne sont pas compressées et elles sont séparées par des barres verticales.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. Créer les tables externes
Maintenant que vous avez spécifié la source des données et le format de fichier, vous êtes prêt à créer les tables externes. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Créez un schéma pour les données.
Pour créer un emplacement de stockage des données Contoso dans votre base de données, créez un schéma.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Créez les tables externes.
Exécutez ce script pour créer les tables externes DimProduct et FactOnlineSales. Nous essayons ici de définir les noms de colonnes et les types de données, et de les lier à l’emplacement et au format des fichiers de stockage d’objets blob Azure. La définition est stockée dans SQL Data Warehouse et les données se trouvent toujours dans le stockage d’objets blob Azure.

Le paramètre **LOCATION** désigne le dossier qui se situe sous le dossier racine du stockage d’objets blob Azure. Chaque table se trouve dans un dossier spécifique.

```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. Chargement des données
Il existe plusieurs façons d’accéder aux données externes.  Vous pouvez interroger les données directement à partir de la table externe, charger les données dans de nouvelles tables de base de données, ou ajouter des données externes à des tables de base de données existantes.  

### <a name="41-create-a-new-schema"></a>4.1. Créer un schéma
CTAS crée une table qui contient des données.  Commencez par créer un schéma pour les données Contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Charger les données dans de nouvelles tables
Pour charger des données à partir d’Azure Blob Storage et pour les enregistrer dans une table au sein de votre base de données, utilisez l’instruction [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]. Le chargement avec CTAS s’appuie sur les tables externes fortement typées que vous venez de créer. Pour charger les données dans de nouvelles tables, utilisez une instruction [CTAS][CTAS] par table. 
 
CTAS crée une table et la remplit avec les résultats d’une instruction select. CTAS définit la nouvelle table de manière à proposer les mêmes colonnes et les mêmes types de données que les résultats de l’instruction select. Si vous sélectionnez toutes les colonnes d’une table externe, la nouvelle table est un réplica des colonnes et des types de données dans la table externe.

Dans cet exemple, nous créons la table de dimension et la table de faits qui joueront le rôle de tables distribuées par hachage. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 Suivre la progression du chargement
Vous pouvez suivre la progression de votre chargement à l’aide des vues de gestion dynamique (DMV). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. Optimiser la compression columnstore
Par défaut, SQL Data Warehouse stocke la table comme un index columnstore en cluster. Après un chargement, certaines lignes de données peuvent ne pas être compressées dans le columnstore.  Cela peut être dû à diverses raisons. Pour plus d’informations, consultez [Gérer les index Columnstore][manage columnstore indexes].

Pour optimiser les performances des requêtes et la compression du columnstore après un chargement, reconstruisez la table afin de forcer l’index columnstore à compresser toutes les lignes. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Pour plus d’informations sur la maintenance des index Columnstore, consultez l’article [Gérer les index Columnstore][manage columnstore indexes].

## <a name="6-optimize-statistics"></a>6. Optimiser les statistiques
Il est préférable de créer des statistiques sur une colonne immédiatement après un chargement. Les statistiques offrent plusieurs possibilités. Par exemple, si vous créez des statistiques sur une colonne pour chaque colonne, il faudra peut-être beaucoup de temps pour reconstruire toutes les statistiques. S’il est certain que des colonnes ne se trouveront pas dans les prédicats de requête, vous pouvez ignorer la création des statistiques sur ces colonnes.

Si vous décidez de créer des statistiques sur une colonne pour chaque colonne de chaque table, vous pouvez utiliser l’exemple de code de procédure stockée `prc_sqldw_create_stats` dans l’article portant sur les [statistiques][statistics].

L’exemple suivant est un bon point de départ pour la création de statistiques. Il permet de créer des statistiques sur une colonne pour chaque colonne de la table de dimension, et chaque colonne de jointure des tables de faits. Vous pouvez toujours ajouter ultérieurement des statistiques sur une ou plusieurs colonnes dans d’autres colonnes de table de faits.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Et voilà !
Vous avez correctement chargé les données publiques dans Azure SQL Data Warehouse. Bon travail !

Vous pouvez maintenant interroger les tables à l’aide de requêtes comme celle-ci :

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Étapes suivantes
Pour charger l’ensemble des données de l’entrepôt de données Contoso Retail, utilisez le script. Pour d’autres conseils de développement, consultez [Vue d’ensemble sur le développement SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
