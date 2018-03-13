---
title: "Copier des données depuis/vers Azure SQL Data Warehouse à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Factory copier des données de banques de données sources prises en charge vers SQL Data Warehouse (ou) à de SQL Data Warehouse vers des banques de données réceptrices prises en charge."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: 2601d386bdacbe005b2930a44db531a0b58fb7b5
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copier des données depuis/vers Azure SQL Data Warehouse à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Version 2 - Préversion](connector-azure-sql-data-warehouse.md)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’Azure SQL Data Warehouse. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur SQL Data Warehouse dans V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’Azure SQL Data Warehouse vers toute banque de données réceptrice prise en charge, ou copier des données de toute banque de données source prise en charge vers Azure SQL Data Warehouse. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure SQL Data Warehouse prend en charge ce qui suit :

- Copie des données à l’aide de **l’authentification SQL** et de **l’authentification du jeton de l’application Azure Active Directory** avec le principal de service ou Managed Service Identity (MSI).
- En tant que source, récupération de données à l’aide d’une requête SQL ou d’une procédure stockée.
- En tant que récepteur, chargement de données à l’aide de **PolyBase** ou d’une insertion en bloc. La première solution est **recommandée** pour améliorer les performances de copie.

> [!IMPORTANT]
> Veuillez noter que PolyBase prend uniquement en charge l’authentification SQL, pas l’authentification Azure Active Directory.

> [!IMPORTANT]
> Si vous copiez des données à l’aide du runtime d’intégration Azure, configurez le [pare-feu Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) de façon à ce qu’il [autorise les services Azure à accéder au serveur](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Si vous copiez des données à l’aide d’un runtime d’intégration auto-hébergé, configurez le pare-feu Azure SQL Server de façon à ce qu’il autorise la plage d’adresses IP appropriée, notamment l’adresse IP de la machine qui est utilisée pour se connecter à Azure SQL Database.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Azure SQL Data Warehouse sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AzureSqlDW** | OUI |
| connectionString |Spécifier les informations requises pour la connexion à l’instance Azure SQL Data Warehouse pour la propriété connectionString. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. | Oui, lorsque vous utilisez l’authentification AAD avec le principal de service. |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui, lorsque vous utilisez l’authentification AAD avec le principal de service. |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | Oui, lorsque vous utilisez l’authentification AAD avec le principal de service. |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

Pour en savoir plus sur les autres types d’authentification, consultez les sections suivantes sur les prérequis et les exemples JSON, respectivement :

- [Utilisation de l’authentification SQL](#using-sql-authentication)
- [Utilisation de l’authentification du jeton d’application AAD : principal de service](#using-service-principal-authentication)
- [Utilisation de l’authentification du jeton d’application AAD : Managed Service Identity](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Utilisation de l’authentification SQL

**Exemple de service lié utilisant l’authentification SQL :**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-principal-authentication"></a>Utilisation de l’authentification de principal de service

Pour utiliser l’authentification du jeton d’application AAD basée sur le principal de service, procédez comme suit :

1. **[Créez une application Azure Active Directory dans le portail Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Prenez note du nom de l’application et des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. **[Approvisionnez un administrateur Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** pour votre Azure SQL Server sur le portail Azure, si ce n’est pas déjà fait. L’administrateur AAD peut être un utilisateur AAD ou un groupe AAD. Si vous accordez au groupe avec MSI un rôle d’administrateur, ignorez les étapes 3 et 4 ci-dessous, car l’administrateur a un accès total à la base de données.

3. **Créez un utilisateur de base de données à relation contenant-contenu pour le principal de service** en vous connectant à l’entrepôt de données à partir duquel ou sur lequel vous souhaitez copier des données à l’aide d’outils tels que SSMS, avec une identité AAD qui dispose au moins de l’autorisation MODIFIER UN UTILISATEUR et qui exécute le T-SQL suivant. En savoir plus sur l’utilisateur de base de données à relation contenant-contenu [ici](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Accordez les autorisations requises par le principal de service** comme vous le feriez d’habitude pour les utilisateurs SQL, par exemple, en exécutant ce qui suit :

    ```sql
    EXEC sp_addrolemember '[your application name]', 'readonlyuser';
    ```

5. Dans ADF, configurez un service lié Azure SQL Data Warehouse.


**Exemple de service lié utilisant l’authentification du principal de service :**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>Utilisation de l’authentification MSI (Managed Service Identity)

Une fabrique de données peut être associée à une [identité de service managé (Managed Service Identity, MSI)](data-factory-service-identity.md) représentant la fabrique de données en question. Vous pouvez utiliser cette identité de service pour l’authentification Azure SQL Data Warehouse, ce qui permet à la fabrique en question d’accéder à votre entrepôt de données et de copier des données depuis ou vers celui-ci.

Pour utiliser l’authentification du jeton d’application AAD basée sur une MSI, procédez comme suit :

1. **Créez un groupe dans Azure AD et ajoutez la MSI de la fabrique comme membre du groupe**.

    a. Recherchez l’identité du service de la fabrique de données sur le portail Azure. Accédez à votre fabrique de données -> Propriétés -> copiez **l’ID DE L’IDENTITÉ DU SERVICE**.

    b. Installez le module [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2), connectez-vous à l’aide de la commande `Connect-AzureAD` et exécutez les commandes suivantes pour créer un groupe et ajouter la MSI de la fabrique de données en tant que membre.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Approvisionnez un administrateur Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** pour votre Azure SQL Server sur le portail Azure, si ce n’est pas déjà fait.

3. **Créez un utilisateur de base de données à relation contenant-contenu pour le groupe AAD** en vous connectant à l’entrepôt de données à partir duquel ou sur lequel vous souhaitez copier des données à l’aide d’outils tels que SSMS, avec une identité AAD qui dispose au moins de l’autorisation MODIFIER UN UTILISATEUR et qui exécute le T-SQL suivant. En savoir plus sur l’utilisateur de base de données à relation contenant-contenu [ici](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Accordez les autorisations requises par le groupe AAD** comme vous le feriez d’habitude pour les utilisateurs SQL, par exemple, en exécutant ce qui suit :

    ```sql
    EXEC sp_addrolemember '[your AAD group name]', 'readonlyuser';
    ```

5. Dans ADF, configurez un service lié Azure SQL Data Warehouse.

**Exemple de service lié à l’aide de l’authentification MSI :**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure SQL Data Warehouse.

Pour copier des données depuis/vers Azure SQL Data Warehouse, définissez la propriété type du jeu de données sur **AzureSqlDWTable**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **AzureSqlDWTable** | OUI |
| TableName |Nom de la table ou de la vue dans l’instance Azure SQL Data Warehouse à laquelle le service lié fait référence. | OUI |

**Exemple :**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure SQL Data Warehouse en tant que source et récepteur.

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL Data Warehouse en tant que source

Pour copier des données d’Azure SQL Data Warehouse, définissez **SqlDWSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **SqlDWSource** | OUI |
| SqlReaderQuery |Utiliser la requête SQL personnalisée pour lire les données. Exemple : `select * from MyTable`. |Non  |
| sqlReaderStoredProcedureName |Nom de la procédure stockée qui lit les données de la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. |Non  |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non  |

**Points à noter :**

- Si **sqlReaderQuery** est spécifié pour SqlSource, l'activité de copie exécute cette requête en fonction de la source Azure SQL Data Warehouse pour obtenir les données. Vous pouvez également spécifier une procédure stockée en indiquant **sqlReaderStoredProcedureName** et **storedProcedureParameters** (si la procédure stockée accepte des paramètres).
- Si vous ne spécifiez pas « sqlReaderQuery » ou « sqlReaderStoredProcedureName », les colonnes définies dans la section « structure » du jeu de données JSON sont utilisées pour construire une requête (`select column1, column2 from mytable`) à exécuter sur Azure SQL Data Warehouse. Si la définition du jeu de données ne possède pas de « structure », toutes les colonnes de la table sont sélectionnées.
- Quand vous utilisez **sqlReaderStoredProcedureName**, vous devez tout de même spécifier une propriété **tableName** factice dans le jeu de données JSON.

**Exemple : utilisation d’une requête SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemple : utilisation d’une procédure stockée**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Définition de la procédure stockée :**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse en tant que récepteur

Pour copier des données vers Azure SQL Data Warehouse, définissez **SqlDWSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **SqlDWSink**. | OUI |
| allowPolyBase |Indique s’il faut utiliser PolyBase (le cas échéant) au lieu du mécanisme BULKINSERT. <br/><br/> **L’utilisation de PolyBase est la méthode recommandée pour charger des données dans SQL Data Warehouse.** Reportez-vous à la section [Utiliser PolyBase pour charger des données dans Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) pour connaître les contraintes et les détails.<br/><br/>Valeurs autorisées : **True** (par défaut), et **False**.  |Non  |
| polyBaseSettings |Groupe de propriétés pouvant être spécifié lorsque la propriété **allowPolybase** est définie sur **true**. |Non  |
| rejectValue |Spécifie le nombre ou le pourcentage de lignes pouvant être rejetées avant l’échec de la requête.<br/><br/>Pour en savoir plus sur les options de rejet de PolyBase dans la section **Arguments** de la rubrique [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) (Créer une table externe (Transact-SQL)). <br/><br/>Valeurs autorisées : 0 (par défaut), 1, 2,... |Non  |
| rejectType |Spécifie si l’option rejectValue est spécifiée comme une valeur littérale ou un pourcentage.<br/><br/>Valeurs autorisées : **Value** (par défaut) et **Percentage**. |Non  |
| rejectSampleValue |Détermine le nombre de lignes à extraire avant que PolyBase recalcule le pourcentage de lignes rejetées.<br/><br/>Valeurs autorisées : 1, 2, … |Oui, si le **rejectType** est **percentage** |
| useTypeDefault |Spécifie comment gérer les valeurs manquantes dans les fichiers texte délimités lorsque PolyBase extrait des données à partir du fichier texte.<br/><br/>Pour plus d’informations sur cette propriété, consultez la section Arguments dans [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Valeurs autorisées : **True**, **False** (par défaut). |Non  |
| writeBatchSize |Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize S’applique uniquement quand PolyBase n’est pas utilisé.<br/><br/>Valeurs autorisées : integer (nombre de lignes). |Non (valeur par défaut : 10 000) |
| writeBatchTimeout |Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer. S’applique uniquement quand PolyBase n’est pas utilisé.<br/><br/>Valeurs autorisées : timespan. Exemple : « 00:30:00 » (30 minutes). |Non  |
| preCopyScript |Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans Azure SQL Data Warehouse à chaque exécution. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non  |(#repeatability-during-copy). |Une instruction de requête. |Non  |

**Exemple :**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Apprenez-en davantage sur la façon d’utiliser PolyBase pour charger efficacement dans SQL Data Warehouse en lisant la section suivante.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Utiliser PolyBase pour charger des données dans Azure SQL Data Warehouse

L’utilisation de **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** est un moyen efficace de charger de grandes quantités de données dans Azure SQL Data Warehouse avec un débit élevé. Vous pouvez profiter d’un gain important de débit en utilisant PolyBase au lieu du mécanisme BULKINSERT par défaut. Consultez [copier le numéro de référence des performances](copy-activity-performance.md#performance-reference) qui contient une comparaison détaillée. Consultez [Charger 1 To dans Azure SQL Data Warehouse en moins de 15 minutes avec Azure Data Factory](connector-azure-sql-data-warehouse.md) pour obtenir une procédure pas à pas avec un cas d’utilisation.

* Si votre source de données se trouve dans **Stockage Blob Azure ou Azure Data Lake Store**, et si le format est compatible avec PolyBase, vous pouvez la copier directement vers Azure SQL Data Warehouse à l’aide de PolyBase. Consultez **[Copie directe à l’aide de PolyBase](#direct-copy-using-polybase)**.
* Si votre banque de données sources et son format ne sont pas pris en charge à l’origine par PolyBase, vous pouvez utiliser la fonctionnalité **[Copie intermédiaire avec PolyBase](#staged-copy-using-polybase)** à la place. Elle propose également un meilleur débit en convertissant les données dans un format compatible avec PolyBase et en stockant les données dans le stockage Blob Azure automatiquement. Il charge ensuite les données dans SQL Data Warehouse.

> [!IMPORTANT]
> Veuillez noter que PolyBase prend uniquement en charge l’authentification Azure SQL Data Warehouse, pas l’authentification Azure Active Directory.

### <a name="direct-copy-using-polybase"></a>Copie directe à l’aide de PolyBase

SQL Data Warehouse PolyBase prend directement en charge Stockage Blob Azure et Azure Data Lake Store (à l’aide du principal du service) en tant que source et avec des exigences de format de fichier spécifiques. Si vos données source répondent aux critères décrits dans cette section, vous pouvez les copier directement du magasin de données source vers Azure SQL Data Warehouse à l’aide de PolyBase. Sinon, vous pouvez utiliser la méthode [Copie intermédiaire à l’aide de PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Pour copier des données de Data Lake Store vers SQL Data Warehouse efficacement, voir [Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/) (Azure Data Factory facilite et rend plus pratique la découverte d’informations à partir de données lors de l’utilisation de Data Lake Store avec SQL Data Warehouse).

Si les critères ne sont pas remplis, Azure Data Factory contrôle les paramètres et rétablit automatiquement le mécanisme BULKINSERT pour le déplacement des données.

1. Le **service lié de la source** est de type **AzureStorage** ou **AzureDataLakeStore avec authentification du principal de service**.
2. Le **jeu de données d’entrée** est de type **Azure Blob** ou **AzureDataLakeStoreFile** et le type de format dans les propriétés `type` est **OrcFormat**, **ParquetFormat** ou **TextFormat** avec les configurations suivantes :

   1. `rowDelimiter` doit être **\n**.
   2. `nullValue` est défini sur **chaîne vide** ("") ou `treatEmptyAsNull` est défini sur **true**.
   3. `encodingName` est défini sur **utf-8**, qui est la valeur **par défaut**.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` et `skipLineCount` ne sont pas spécifiés.
   5. `compression` peut être **aucune compression**, **GZip** ou **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Il n’y a aucun paramètre `skipHeaderLineCount` sous **BlobSource** ou **AzureDataLakeStore** pour l’activité de copie dans le pipeline.
4. Il n’y a aucun paramètre `sliceIdentifierColumnName` sous **SqlDWSink** pour l’activité de copie dans le pipeline. (PolyBase garantit que toutes les données sont mises à jour ou que rien n’est mis à jour en une seule exécution. Pour définir la **répétabilité**, vous pouvez utiliser `sqlWriterCleanupScript`.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>Copie intermédiaire à l’aide de PolyBase

Lorsque vos données source ne répondent pas aux critères présentés dans la section précédente, vous pouvez activer la copie des données par le biais d’un Stockage Blob Azure intermédiaire (il ne peut pas s’agir d’une offre Stockage Premium). Dans ce cas, Azure Data Factory effectue automatiquement des transformations sur les données pour répondre aux exigences de format de données de PolyBase, utilise PolyBase pour charger les données dans SQL Data Warehouse, puis supprime vos données temporaires du Stockage Blob. Consultez la rubrique [Copie intermédiaire](copy-activity-performance.md#staged-copy) pour plus d’informations sur le fonctionnement général de la copie des données par le biais d’un Blob Azure.

Pour utiliser cette fonctionnalité, vous devez créer un [service lié Azure Storage](connector-azure-blob-storage.md#linked-service-properties) qui fait référence au compte de stockage Azure qui comprend le stockage d’objets blob intermédiaire, puis spécifier les propriétés `enableStaging` et `stagingSettings` de l’activité de copie, comme indiqué dans le code suivant :

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>Meilleures pratiques lors de l’utilisation de PolyBase

Les sections suivantes contiennent des bonnes pratiques qui s’ajoutent à celles mentionnées dans [Bonnes pratiques pour Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Autorisation de base de données requise

Pour utiliser PolyBase, il est nécessaire que l’utilisateur utilisé pour charger des données dans SQL Data Warehouse ait [l’autorisation « CONTROL »](https://msdn.microsoft.com/library/ms191291.aspx) sur la base de données cible. Vous pouvez, pour cela, ajouter l’utilisateur en tant que membre du rôle « db_owner ». Découvrez comment procéder dans [cette section](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Limitations en matière de taille de ligne et de type de données

Les charges Polybase sont limitées au chargement de lignes de moins de **1 Mo** et ne peuvent pas charger vers VARCHR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX). Consultez cette [section](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Si les données source dont vous disposez ont des lignes d’une taille supérieure à 1 Mo, vous pouvez fractionner verticalement les tables source en plusieurs tables plus petites dans lesquelles la taille de ligne maximale ne dépasse pas la limite. Vous pouvez ensuite charger les tables plus petites à l’aide de PolyBase et les fusionner dans Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de ressources SQL Data Warehouse

Pour obtenir le meilleur débit possible, envisagez d’attribuer une classe de ressources plus volumineuse à l’utilisateur utilisé pour charger des données dans SQL Data Warehouse via PolyBase. Découvrez comment procéder en consultant [Exemple de modification d’une classe de ressources utilisateur](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName dans Azure SQL Data Warehouse

Le tableau suivant fournit des exemples sur la façon de spécifier la propriété **tableName** dans le jeu de données JSON pour différentes combinaisons de schémas et noms de table.

| Schéma BD | Nom de la table | Propriété JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable ou dbo.MyTable ou [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable ou [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] ou [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Si vous voyez l’erreur suivante, il peut s’agir d’un problème avec la valeur spécifiée pour la propriété tableName. Consultez le tableau pour savoir comment spécifier des valeurs pour la propriété JSON tableName.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colonnes avec des valeurs par défaut

Actuellement, la fonctionnalité PolyBase dans Data Factory accepte seulement le même nombre de colonnes que la table cible. Par exemple, vous avez une table avec quatre colonnes et l’une d’elles est définie avec une valeur par défaut. Les données d’entrée doivent toujours contenir quatre colonnes. La fourniture d’un jeu de données d’entrée de 3 colonnes produirait une erreur semblable au message suivant :

```
All columns of the table must be specified in the INSERT BULK statement.
```

La valeur NULL est une forme spéciale de valeur par défaut. Si la colonne accepte la valeur Null, les données d’entrée (dans l’objet blob) de cette colonne peuvent être vides (ne peuvent pas être absentes du jeu de données d’entrée). PolyBase insère NULL pour ces données dans Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mappage de type de données pour Azure SQL Data Warehouse

Lors de la copie de données à partir d’Azure SQL Data Warehouse, les mappages suivants sont utilisés entre les types de données Azure SQL Data Warehouse et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Azure SQL Data Warehouse | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binaire |Byte[] |
| bit |Booléen |
| char |String, Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DatetimeOffset |
| Décimal |Décimal |
| Attribut FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Décimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérique |Décimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Décimal |
| sql_variant |Objet * |
| text |String, Char[] |
| time |intervalle de temps |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).