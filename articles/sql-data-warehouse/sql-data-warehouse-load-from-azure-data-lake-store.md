---
title: Chargement de données Azure Data Lake Store dans SQL Data Warehouse | Microsoft Docs
description: Découvrez comment utiliser des tables externes PolyBase pour charger des données Azure Data Lake Store dans Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 3/14/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: f8cd293236255e227f80a42e78d25aebd8789bdd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>Chargement de données Azure Data Lake Store dans SQL Data Warehouse
Ce document vous indique toutes les étapes nécessaires pour charger des données d’Azure Data Lake Store (ADLS) dans SQL Data Warehouse à l’aide de PolyBase.
Même s’il est possible d’exécuter des requêtes ad hoc sur les données stockées en ADLS à l’aide de tables externes, nous vous recommandons d’importer les données dans SQL Data Warehouse pour obtenir de meilleures performances.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

1. Créer les objets de base de données devant être chargés à partir d’Azure Data Lake Store.
2. Se connecter à un répertoire Azure Data Lake Store.
3. Charger des données dans Azure SQL Data Warehouse.

## <a name="before-you-begin"></a>Avant de commencer
Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Une application Azure Active Directory pour l’authentification de service à service. Pour créer, suivez la procédure [Authentification Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)

>[!NOTE] 
> Vous avez besoin de l’ID client, de la clé et de la valeur du point de terminaison du jeton OAuth2.0 de votre application Active Directory pour vous connecter à Azure Data Lake depuis SQL Data Warehouse. Pour savoir comment obtenir ces valeurs, cliquez sur le lien ci-dessus.
>Remarque pour l’inscription à l’application Azure Active Directory : utilisez l’ID d’application en tant qu’ID de client.

* SQL Server Management Studio ou SQL Server Data Tools, pour télécharger SSMS et se connecter, consultez [Interroger SSMS](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)

* Un Azure SQL Data Warehouse, pour créer un suivi : https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision

* Une Azure Data Lake Store, pour créer un suivi : https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal


###  <a name="create-a-credential"></a>Créer des informations d’identification
Pour accéder à votre Azure Data Lake Store, vous devez créer une clé principale de base de données afin de chiffrer les informations secrètes d’identification au cours de l’étape suivante.
Créez ensuite des informations d’identification incluses dans l’étendue de la base de données, qui stockent les informations d’identification du principal du service configurées dans AAD. Pour ceux d'entre vous qui ont utilisé PolyBase pour se connecter à des objets blob Windows Azure Storage, notez que la syntaxe des informations d’identification est différente.
Pour vous connecter à Azure Data Lake Store, vous devez **tout d’abord** créer une application Azure Active Directory, créer une clé d’accès et accorder l’accès aux applications à la ressource Azure Data Lake. Vous trouverez [ici](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory) des instructions pour effectuer ces étapes.

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```


### <a name="create-the-external-data-source"></a>Créer la source de données externe
Utilisez la commande [CRÉER UNE SOURCE DE DONNÉES EXTERNE][CREATE EXTERNAL DATA SOURCE] pour stocker l’emplacement des données. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Configurer le format des données
Pour importer les données depuis ADLS, vous devez spécifier le format de fichier externe. Cet objet définit la manière dont les fichiers sont écrits en ADLS.
Consultez notre documentation T-SQL pour obtenir une liste complète [CRÉER UN FORMAT DE FICHIERS EXTERNES][CREATE EXTERNAL FILE FORMAT]

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

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

## <a name="create-the-external-tables"></a>Créer les tables externes
Maintenant que vous avez spécifié la source des données et le format de fichier, vous êtes prêt à créer les tables externes. Les tables externes vous permettent d’interagir avec des données externes. Le paramètre d’emplacement peut spécifier un fichier ou un répertoire. Si il spécifie un répertoire, tous les fichiers contenus dans le répertoire seront chargés.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Considérations relatives aux tables externes
La création d’une table externe est facile, mais il existe quelques nuances qui doivent être abordées.

Les tables externes sont fortement typés. Cela signifie que chaque ligne de données reçue doit respecter la définition du schéma de la table.
Si une ligne ne respecte pas la définition du schéma, cette ligne est exclue du chargement.

Les options REJECT_TYPE et REJECT_VALUE vous permettent de définir le nombre de lignes ou le pourcentage de données à inclure dans la table finale. Au cours du chargement, si la valeur à rejeter est atteinte, le chargement échoue. La principale cause de rejet de lignes est une incompatibilité de définition de schéma. Par exemple, si une colonne reçoit par erreur le schéma int alors que les données dans le fichier représentent une chaîne, aucune ligne ne sera chargée.

 Azure Data Lake utilise le contrôle d’accès en fonction du rôle (RBAC) pour contrôler l’accès aux données. Cela signifie que le principal de service doit disposer des autorisations de lecture pour les répertoires définis dans le paramètre d’emplacement, ainsi que pour les enfants des fichiers et du répertoire final. Cela permet à PolyBase d’authentifier et de charger ces données. 

## <a name="load-the-data"></a>Chargement des données
Pour charger des données depuis Azure Data Lake Store, utilisez l’instruction [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]. 

CTAS crée une table et la remplit avec les résultats d’une instruction select. CTAS définit la nouvelle table de manière à proposer les mêmes colonnes et les mêmes types de données que les résultats de l’instruction select. Si vous sélectionnez toutes les colonnes d’une table externe, la nouvelle table est un réplica des colonnes et des types de données dans la table externe.

Dans cet exemple, nous créons une table de hachage distribuée appelée DimProduct à partir de notre table externe DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimiser la compression columnstore
Par défaut, SQL Data Warehouse stocke la table comme un index columnstore en cluster. Après un chargement, certaines lignes de données peuvent ne pas être compressées dans le columnstore.  Cela peut être dû à diverses raisons. Pour plus d’informations, consultez [Gérer les index Columnstore][manage columnstore indexes].

Pour optimiser les performances des requêtes et la compression du columnstore après un chargement, reconstruisez la table afin de forcer l’index columnstore à compresser toutes les lignes.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

Pour plus d’informations sur la maintenance des index Columnstore, consultez l’article [Gérer les index Columnstore][manage columnstore indexes].

## <a name="optimize-statistics"></a>Optimiser les statistiques
Il est préférable de créer des statistiques sur une colonne immédiatement après un chargement. Les statistiques offrent plusieurs possibilités. Par exemple, si vous créez des statistiques sur une colonne pour chaque colonne, il faudra peut-être beaucoup de temps pour reconstruire toutes les statistiques. S’il est certain que des colonnes ne se trouveront pas dans les prédicats de requête, vous pouvez ignorer la création des statistiques sur ces colonnes.

Si vous décidez de créer des statistiques sur une colonne pour chaque colonne de chaque table, vous pouvez utiliser l’exemple de code de procédure stockée `prc_sqldw_create_stats` dans l’article portant sur les [statistiques][statistics].

L’exemple suivant est un bon point de départ pour la création de statistiques. Il permet de créer des statistiques sur une colonne pour chaque colonne de la table de dimension, et chaque colonne de jointure des tables de faits. Vous pouvez toujours ajouter ultérieurement des statistiques sur une ou plusieurs colonnes dans d’autres colonnes de table de faits.


## <a name="achievement-unlocked"></a>Et voilà !
Vous avez correctement chargé les données dans Azure SQL Data Warehouse. Bon travail !

## <a name="next-steps"></a>Étapes suivantes
Le chargement des données est la première étape du développement d’une solution d’entreposage des données à l’aide de SQL Data Warehouse. Découvrez nos ressources de développement dans les sections [Tables](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview) et [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops).


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
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
