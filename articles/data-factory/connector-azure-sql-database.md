---
title: "Copier des données depuis/vers Azure SQL Database à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers Azure SQL Database (ou) de SQL Database vers des banques de données réceptrices prises en charge."
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
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: a5d2994eb1203274454fc31c3ee9bf7a21562f75
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copier des données depuis/vers Azure SQL Database en utilisant Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-azure-sql-connector.md)
> * [Version 2 - Préversion](connector-azure-sql-database.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers Azure SQL Database. Il s’appuie sur l’article [présentant une vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Azure SQL Database dans V1](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données depuis Azure SQL Database vers toute banque de données réceptrice prise en charge, ou copier des données depuis toute banque de données source prise en charge vers Azure SQL Database. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure SQL Database prend en charge ce qui suit :

- Copie de données en utilisant une authentification SQL.
- En tant que source, récupération de données à l’aide d’une requête SQL ou d’une procédure stockée.
- En tant que récepteur, l’ajout de données à une table de destination ou l’appel d’une procédure stockée avec une logique personnalisée pendant la copie.

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie.

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory spécifiques du connecteur Azure SQL Database.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Azure SQL Database sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur : **AzureSqlDatabase** | Oui |
| connectionString |Spécifier les informations requises pour la connexion à l’instance de base de données SQL Azure pour la propriété connectionString. Seule l’authentification de base est prise en charge. Marquez ce champ comme SecureString. |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

> [!IMPORTANT]
> Configurez le [pare-feu Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) et le serveur de base de données pour [autoriser les services Azure à accéder au serveur](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). En outre, si vous copiez des données vers Azure SQL Database à partir d’un emplacement situé en dehors d’Azure, y compris à partir de sources de données locales avec le runtime d’intégration auto-hébergé de la fabrique de données, configurez la plage d’adresses IP appropriée pour l’ordinateur qui envoie des données à Azure SQL Database.

**Exemple :**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure SQL Database.

Pour copier des données depuis/vers Azure SQL Database, définissez la propriété type du jeu de données sur **AzureSqlTable**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **AzureSqlTable** | Oui |
| TableName |Nom de la table ou de la vue dans l’instance Azure SQL Database à laquelle le service lié fait référence. | Oui |

**Exemple :**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure SQL Database en tant que source et récepteur.

### <a name="azure-sql-database-as-source"></a>Azure SQL Database en tant que source

Pour copier des données d’Azure SQL Database, définissez **SqlSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **SqlSource** | Oui |
| SqlReaderQuery |Utiliser la requête SQL personnalisée pour lire les données. Exemple : `select * from MyTable`. |Non |
| sqlReaderStoredProcedureName |Nom de la procédure stockée qui lit les données de la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. |Non |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non |

**Points à noter :**

- Si **sqlReaderQuery** est spécifié pour SqlSource, l'activité de copie exécute cette requête sur la source Azure SQL Database pour obtenir les données. Vous pouvez également spécifier une procédure stockée en indiquant **sqlReaderStoredProcedureName** et **storedProcedureParameters** (si la procédure stockée accepte des paramètres).
- Si vous ne spécifiez pas « sqlReaderQuery » ou « sqlReaderStoredProcedureName », les colonnes définies dans la section « structure » du jeu de données JSON sont utilisées pour construire une requête (`select column1, column2 from mytable`) à exécuter sur Azure SQL Database. Si la définition du jeu de données ne possède pas de « structure », toutes les colonnes de la table sont sélectionnées.
- Quand vous utilisez **sqlReaderStoredProcedureName**, vous devez tout de même spécifier une propriété **tableName** factice dans le jeu de données JSON.

**Exemple : utilisation d’une requête SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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

### <a name="azure-sql-database-as-sink"></a>Azure SQL Database en tant que récepteur

Pour copier des données vers Azure SQL Database, définissez **SqlSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type du récepteur d’activité de copie doit être définie sur **SqlSink**. | Oui |
| writeBatchSize |Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize<br/>Valeurs autorisées : integer (nombre de lignes). |Non (valeur par défaut : 10 000) |
| writeBatchTimeout |Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/>Valeurs autorisées : timespan. Exemple : « 00:30:00 » (30 minutes). |Non |
| sqlWriterStoredProcedureName |Nom de la procédure stockée qui met à jour/insère les données dans la table cible. |Non |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non |
| sqlWriterTableType |Spécifiez le nom du type de table à utiliser dans la procédure stockée. L’activité de copie place les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. |Non |
| preCopyScript |Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans Azure SQL Database à chaque exécution. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non |

> [!TIP]
> Lors de la copie de données vers Azure SQL Database, l’activité de copie ajoute des données à la table du récepteur par défaut. Pour effectuer un UPSERT ou une logique métier supplémentaire, utilisez la procédure stockée dans SqlSink. Pour en savoir plus, consultez [Appel d’une procédure stockée pour un récepteur SQL](#invoking-stored-procedure-for-sql-sink).

**Exemple 1 : ajout de données**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemple 2 : appel d’une procédure stockée pendant la copie pour upsert**

Pour en savoir plus, consultez [Appel d’une procédure stockée pour un récepteur SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Colonnes d’identité dans la base de données cible

Cette section fournit un exemple pour copier des données d’une table source sans colonne d’identité vers une table de destination avec une colonne d’identité.

**Table source :**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Table de destination :**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Notez que la table cible possède une colonne d’identité.

**Définition du jeu de données JSON source**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Définition de jeu de données JSON de destination**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Notez que vos tables source et cible ont des schémas différents (la cible possède une colonne supplémentaire avec identité). Dans ce scénario, vous devez spécifier la propriété **structure** dans la définition du jeu de données cible, qui n’inclut pas la colonne d’identité.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Appel d’une procédure stockée pour un récepteur SQL

Quand vous copiez des données vers Azure SQL Database, une procédure stockée spécifiée par l’utilisateur peut être configurée et appelée avec des paramètres supplémentaires.

Une procédure stockée peut être utilisée à la place des mécanismes de copie intégrée. C’est généralement le cas quand une opération upsert (insertion + mise à jour) ou un traitement supplémentaire (fusion de colonnes, recherche de valeurs supplémentaires, insertion dans plusieurs tables, etc.) doivent être effectués avant l’insertion finale des données sources dans la table de destination.

L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération upsert simple dans une table d’Azure SQL Database. On part du principe que les données d’entrée et la table réceptrice « Marketing » ont trois colonnes : ProfileID, State et Category. On effectue une opération upsert basée sur la colonne « ProfileID » et on l’applique uniquement à une catégorie spécifique.

**Jeu de données de sortie**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Définissez la section « SqlSink » dans l’activité de copie comme suit.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Dans votre base de données, définissez la procédure stockée portant le même nom que « SqlWriterStoredProcedureName ». Elle gère les données d’entrée à partir de la source que vous avez spécifiée et les fusionne dans la table de sortie. Notez que le nom du paramètre de la procédure stockée doit être le même que le « tableName » défini dans le jeu de données.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

Dans votre base de données, définissez le type de table portant le même nom que SqlWriterTableType. Notez que le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

La fonction de procédure stockée tire parti des [paramètres Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Mappage de type de données pour Azure SQL Database

Lors de la copie de données à partir d’Azure SQL Database, les mappages suivants sont utilisés entre les types de données Azure Data Factory et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Azure SQL Database | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binaire |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
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
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Décimal |
| sql_variant |Objet * |
| texte |String, Char[] |
| time |intervalle de temps |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).