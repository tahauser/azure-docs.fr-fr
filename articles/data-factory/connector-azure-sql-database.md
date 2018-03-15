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
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: a4d2ccb4b4ba27983537f26e66b5c279f427d466
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copier des données depuis/vers Azure SQL Database en utilisant Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-azure-sql-connector.md)
> * [Version 2 - Préversion](connector-azure-sql-database.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers Azure SQL Database. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Azure SQL Database dans V1](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données depuis Azure SQL Database vers toute banque de données réceptrice prise en charge, ou copier des données depuis toute banque de données source prise en charge vers Azure SQL Database. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure SQL Database prend en charge ce qui suit :

- Copie de données à l’aide de l’**authentification SQL** et de l’**authentification du jeton de l’application Azure Active Directory** avec le principal du service ou Managed Service Identity (MSI).
- En tant que source, récupération de données à l’aide d’une requête SQL ou d’une procédure stockée.
- En tant que récepteur, l’ajout de données à une table de destination ou l’appel d’une procédure stockée avec une logique personnalisée pendant la copie.

> [!IMPORTANT]
> Si vous copiez des données à l’aide du runtime d’intégration Azure, configurez le [pare-feu du serveur SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) de façon à ce qu’il [autorise les services Azure à accéder au serveur](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Si vous copiez des données à l’aide d’un runtime d’intégration auto-hébergé, configurez le pare-feu du serveur SQL Azure de façon à ce qu’il autorise la plage d’adresses IP appropriée, notamment l’adresse IP de la machine qui est utilisée pour se connecter à Azure SQL Database.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory spécifiques du connecteur Azure SQL Database.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Azure SQL Database sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur : **AzureSqlDatabase** | OUI |
| connectionString |Spécifier les informations requises pour la connexion à l’instance de base de données SQL Azure pour la propriété connectionString. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. | Oui, lorsque vous utilisez l’authentification AAD avec le principal du service. |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui, lorsque vous utilisez l’authentification AAD avec le principal du service. |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | Oui, lorsque vous utilisez l’authentification AAD avec le principal du service. |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

Pour en savoir plus sur les autres types d’authentification, consultez les sections suivantes sur les prérequis et les exemples JSON :

- [Utilisation de l’authentification SQL](#using-sql-authentication)
- [Utilisation de l’authentification du jeton d’application AAD : principal du service](#using-service-principal-authentication)
- [Utilisation de l’authentification du jeton d’application AAD : Managed Service Identity](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Utilisation de l’authentification SQL

**Exemple de service lié à l’aide de l’authentification SQL :**

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

### <a name="using-service-principal-authentication"></a>Utilisation de l’authentification de principal de service

Pour utiliser l’authentification du jeton d’application AAD basée sur le principal du service, procédez comme suit :

1. **[Créez une application Azure Active Directory dans le portail Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Prenez note du nom de l’application et des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. **[Approvisionnez un administrateur Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** pour votre serveur SQL Azure sur le Portail Azure, si ce n’est pas déjà fait. L’administrateur AAD doit être un utilisateur AAD ou un groupe AAD, mais il ne peut pas être un principal de service. Vous devez effectuer cette étape pour qu’à l’étape suivante vous puissiez utiliser une identité AAD pour créer un utilisateur de base de données contenu pour le principal du service.

3. **Créez un utilisateur de base de données contenu pour le principal du service** en vous connectant à la base de données à partir de laquelle ou sur laquelle vous souhaitez copier des données à l’aide d’outils tels que SSMS, avec une identité AAD qui dispose au moins de l’autorisation MODIFIER UN UTILISATEUR et qui exécute le T-SQL suivant. En savoir plus sur l’utilisateur de base de données contenu [ici](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Accordez les autorisations requises par le principal du service** comme vous le feriez d’habitude pour les utilisateurs SQL, par exemple, en exécutant ce qui suit :

    ```sql
    EXEC sp_addrolemember '[your application name]', 'readonlyuser';
    ```

5. Dans ADF, configurez un service lié Azure SQL Database.


**Exemple de service lié utilisant l’authentification du principal du service :**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Une fabrique de données peut être associée à une [Managed Service Identity (MSI)](data-factory-service-identity.md) représentant la fabrique de données en question. Vous pouvez utiliser cette identité de service pour l’authentification Azure SQL Database, ce qui permet à la fabrique en question d’accéder à votre base de données et de copier des données.

Pour utiliser l’authentification du jeton d’application AAD basée sur une MSI, procédez comme suit :

1. **Créez un groupe dans Azure AD et faites entrer la MSI de la fabrique dans le groupe**.

    a. Trouvez l’identité du service de la fabrique de données sur le portail Azure. Accédez à votre fabrique de données -> Propriétés -> copiez le **ID DE L’IDENTITÉ DU SERVICE**.

    b. Installez le module [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2), connectez-vous à l’aide de la commande `Connect-AzureAD` et exécutez les commandes suivantes pour créer un groupe et ajouter la MSI de la fabrique de données en tant que membre.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Approvisionnez un administrateur Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** pour votre serveur SQL Azure sur le Portail Azure, si ce n’est pas déjà fait. L’administrateur AAD peut être un utilisateur AAD ou un groupe AAD. Si vous accordez au groupe avec MSI un rôle d’administrateur, ignorez les étapes 3 et 4 ci-dessous, car l’administrateur a un accès complet à la base de données.

3. **Créez un utilisateur de base de données contenu pour le groupe AAD** en vous connectant à la base de données à partir de laquelle ou sur laquelle vous souhaitez copier des données à l’aide d’outils tels que SSMS, avec une identité AAD qui dispose au moins de l’autorisation MODIFIER UN UTILISATEUR et qui exécute le T-SQL suivant. En savoir plus sur l’utilisateur de base de données contenu [ici](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Accordez les autorisations requises par le groupe AAD** comme vous le feriez d’habitude pour les utilisateurs SQL, par exemple, en exécutant ce qui suit :

    ```sql
    EXEC sp_addrolemember '[your AAD group name]', 'readonlyuser';
    ```

5. Dans ADF, configurez un service lié Azure SQL Database.

**Exemple de service lié à l’aide de l’authentification MSI :**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure SQL Database.

Pour copier des données depuis/vers Azure SQL Database, définissez la propriété type du jeu de données sur **AzureSqlTable**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **AzureSqlTable** | OUI |
| TableName |Nom de la table ou de la vue dans l’instance Azure SQL Database à laquelle le service lié fait référence. | OUI |

**Exemple :**

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

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **SqlSource** | OUI |
| SqlReaderQuery |Utiliser la requête SQL personnalisée pour lire les données. Exemple : `select * from MyTable`. |Non  |
| sqlReaderStoredProcedureName |Nom de la procédure stockée qui lit les données de la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. |Non  |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non  |

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

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **SqlSink**. | OUI |
| writeBatchSize |Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize<br/>Valeurs autorisées : integer (nombre de lignes). |Non (valeur par défaut : 10 000) |
| writeBatchTimeout |Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/>Valeurs autorisées : timespan. Exemple : « 00:30:00 » (30 minutes). |Non  |
| preCopyScript |Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans Azure SQL Database. Elle ne sera appelée qu’une seule fois par copie. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non  |
| sqlWriterStoredProcedureName |Nom de la procédure stockée qui définit comment appliquer les données sources dans la table cible, par exemple pour effectuer des upserts ou des transformations à l’aide de votre propre logique métier. <br/><br/>Notez que cette procédure stockée sera **appelée par lot**. Si vous souhaitez effectuer une opération qui ne s’exécute qu’une seule fois et n’a rien à faire avec les données sources, par exemple supprimer/tronquer, utilisez la propriété `preCopyScript`. |Non  |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non  |
| sqlWriterTableType |Spécifiez le nom du type de table à utiliser dans la procédure stockée. L’activité de copie place les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. |Non  |

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

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).