---
title: "Copier de façon incrémentielle plusieurs tables en utilisant Azure Data Factory | Microsoft Docs"
description: "Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory qui copie de façon incrémentielle des données delta de plusieurs tables d’une base de données SQL Server locale dans une base de données Azure SQL."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 0a6bcf78561ef4f57d788ef19e132d1a855b8c2a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Charger de façon incrémentielle des données provenant de plusieurs tables de SQL Server vers une base de données SQL Azure
Dans ce didacticiel, vous allez créer une fabrique de données Azure Data Factory avec un pipeline qui charge les données delta de plusieurs tables d’une base de données SQL Server locale vers une base de données SQL Azure.    

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Préparer les magasins de données source et de destination.
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Installer le runtime d’intégration. 
> * créez des services liés. 
> * Créer des jeux de données source, récepteur et de filigrane.
> * Créez, exécutez et surveillez un pipeline.
> * Passez en revue les résultats.
> * Ajouter ou mettre à jour des données dans les tables source.
> * Réexécuter et surveiller le pipeline.
> * Passer en revue les résultats finaux.

> [!NOTE]
> Cet article s’applique à la version 2 d’Azure Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Vue d'ensemble
Voici les étapes importantes à suivre pour créer cette solution : 

1. **Sélectionner la colonne de limite**.
    Sélectionnez une colonne pour chaque table du magasin de données sources, qui peut servir à identifier les enregistrements nouveaux ou mis à jour pour chaque exécution. Normalement, les données contenues dans cette colonne sélectionnée (par exemple, last_modify_time ou ID) continuent de croître à mesure que des lignes sont créées ou mises à jour. La valeur maximale de cette colonne est utilisée comme limite.

2. **Préparer un magasin de données pour stocker la valeur de limite**.   
    Dans ce didacticiel, la valeur de filigrane est stockée dans une base de données SQL.

3. **Créer un pipeline avec les activités suivantes** : 
    
    a. Créez une activité ForEach qui effectue une itération dans une liste de noms de table source qui est transmise en tant que paramètre au pipeline. Pour chaque table source, elle appelle les activités suivantes pour effectuer le chargement delta pour cette table.

    b. Créez deux activités de recherche. Servez-vous de la première activité de recherche pour récupérer la dernière valeur de filigrane. Utilisez la deuxième activité de recherche pour récupérer la nouvelle valeur de filigrane. Ces valeurs de filigrane sont transmises à l’activité de copie.

    c. Créez une activité de copie qui copie les lignes du magasin de données source dont la valeur de la colonne de filigrane est supérieure à l’ancienne valeur de filigrane et inférieure à la nouvelle. Elle copie ensuite les données delta du magasin de données source dans un stockage Blob Azure sous la forme d’un nouveau fichier.

    d. Créez une activité StoredProcedure qui met à jour la valeur de filigrane pour le pipeline s’exécutant la prochaine fois. 

    Voici le diagramme général de la solution : 

    ![Chargement incrémentiel de données](media\tutorial-incremental-copy-multiple-tables-powershell\high-level-solution-diagram.png)


Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>configuration requise
* **SQL Server**. Dans le cadre de ce didacticiel, vous allez utiliser une base de données SQL Server locale comme magasin de données source. 
* **Base de données SQL Azure**. Vous allez utiliser une base de données SQL comme magasin de données récepteur. Si vous ne disposez pas d’une base de données SQL, consultez [Créer une base de données Azure SQL Database](../sql-database/sql-database-get-started-portal.md) pour connaître la procédure à suivre pour en créer une. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Créer des tables sources dans votre base de données SQL Server

1. Ouvrez SQL Server Management Studio, puis connectez-vous à votre base de données SQL Server locale.

2. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur la base de données et choisissez **Nouvelle requête**.

3. Exécutez la commande SQL suivante sur votre base de données pour créer des tables nommées `customer_table` et `project_table` :

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Créer des tables de destination dans votre base de données SQL Azure
1. Ouvrez SQL Server Management Studio, puis connectez-vous à votre base de données SQL Server.

2. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur la base de données et choisissez **Nouvelle requête**.

3. Exécutez la commande SQL suivante sur votre base de données SQL pour créer des tables nommées `customer_table` et `project_table` :  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Créer une autre table dans Azure SQL Database pour stocker la valeur de limite supérieure
1. Exécutez la commande SQL suivante sur votre base de données SQL pour créer une table sous le nom `watermarktable` pour stocker la valeur de filigrane : 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Insérer des valeurs de limite supérieure initiale pour les deux tables source dans la table de limite supérieure.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Créer une procédure stockée dans Azure SQL Database 

Exécutez la commande suivante pour créer une procédure stockée dans votre base de données SQL. Cette procédure stockée met à jour la valeur de filigrane après chaque exécution du pipeline. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Créer des types de données et des procédures stockées supplémentaires dans Azure SQL Database
Exécutez la requête suivante pour créer deux procédures stockées et deux types de données dans votre base de données SQL. Ils sont utilisés pour fusionner les données des tables source dans les tables de destination.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Installez les modules Azure PowerShell les plus récents en suivant les instructions décrites dans [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Créer une fabrique de données
1. Définissez une variable pour le nom du groupe de ressources que vous utiliserez ultérieurement dans les commandes PowerShell. Copiez le texte de commande suivant dans PowerShell, spécifiez un nom pour le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) entre des guillemets doubles, puis exécutez la commande. Par exemple `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Attribuez une valeur différente à la variable `$resourceGroupName`, puis réexécutez la commande.

2. Définissez une variable pour l’emplacement de la fabrique de données. 

    ```powershell
    $location = "East US"
    ```
3. Pour créer le groupe de ressources Azure, exécutez la commande suivante : 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Attribuez une valeur différente à la variable `$resourceGroupName`, puis réexécutez la commande.

4. Définissez une variable pour le nom de la fabrique de données. 

    > [!IMPORTANT]
    >  Mettez à jour le nom de la fabrique de données pour le rendre globalement unique. Par exemple, ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Pour créer la fabrique de données, exécutez l’applet de commande **Set-AzureRmDataFactoryV2** suivante : 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Notez les points suivants :

* Le nom de la fabrique de données doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez :

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pour créer des instances Data Factory, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être membre des rôles Contributeur ou Propriétaire, ou administrateur de l’abonnement Azure.
* À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, SQL Database, etc.) et les services de calcul (Azure HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Créez des services liés
Vous allez créer des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans cette section, vous allez créer des services liés à vos bases de données SQL Server et SQL locales. 

### <a name="create-the-sql-server-linked-service"></a>Créer le service lié SQL Server
Dans cette étape, vous liez votre base de données SQL Server locale à la fabrique de données.

1. Créez un fichier JSON sous le nom SqlServerLinkedService.json dans le dossier C:\ADFTutorials\IncCopyMultiTableTutorial avec le contenu suivant. Sélectionnez la section appropriée en fonction de l’authentification utilisée pour vous connecter à SQL Server. Créez les dossiers locaux s’ils n’existent pas encore. 

    > [!IMPORTANT]
    > Sélectionnez la section appropriée en fonction de l’authentification utilisée pour vous connecter à SQL Server.

    Si vous utilisez l’authentification SQL, copiez la définition JSON suivante :

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    
    Si vous utilisez l’authentification Windows, copiez la définition JSON suivante :

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```
    > [!IMPORTANT]
    > - Sélectionnez la section appropriée en fonction de l’authentification utilisée pour vous connecter à SQL Server.
    > - Remplacez &lt;integration runtime name> par le nom de votre runtime d’intégration.
    > - Remplacez &lt;servername>, &lt;databasename>, &lt;username> et &lt;password> par les valeurs de votre base de données SQL Server avant d’enregistrer le fichier.
    > - Si vous avez besoin d’utiliser une barre oblique (`\`) dans votre nom de serveur ou de compte d’utilisateur, utilisez le caractère d’échappement (`\`). Par exemple `mydomain\\myuser`.

2. Dans PowerShell, basculez vers le dossier C:\ADFTutorials\IncCopyMultiTableTutorial.

3. Exécutez la cmdlet **Set-AzureRmDataFactoryV2LinkedService** pour créer le service lié AzureStorageLinkedService. Dans l’exemple suivant, vous transmettez les valeurs des paramètres *ResourceGroupName* et *DataFactoryName* : 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Créer le service lié pour base de données SQL
1. Créez un fichier JSON sous le nom AzureSQLDatabaseLinkedService.json dans le dossier C:\ADFTutorials\IncCopyMultiTableTutorial avec le contenu suivant. (Créez le dossier ADF s’il n’existe pas déjà.) Remplacez &lt;server&gt;, &lt;database name&gt;, &lt;user id&gt; et &lt;password&gt; par le nom de votre base de données SQL Server, le nom de votre base de données, l’ID utilisateur et le mot de passe avant d’enregistrer le fichier. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. Dans PowerShell, exécutez la cmdlet **Set-AzureRmDataFactoryV2LinkedService** pour créer le service lié AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Créez les jeux de données
Dans cette étape, vous créez des jeux de données pour représenter la source de données, la destination des données et l’emplacement de stockage du filigrane.

### <a name="create-a-source-dataset"></a>Créer un jeu de données source

1. Créez un fichier JSON sous le nom SourceDataset.json dans le même dossier avec le contenu suivant : 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dummyName"
            },
            "linkedServiceName": {
                "referenceName": "SqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```

    Le nom de table est factice. L’activité de copie dans le pipeline utilise une requête SQL pour charger les données plutôt que de charger l’ensemble de la table.

2. Exécutez la cmdlet **Set-AzureRmDataFactoryV2Dataset** pour créer le jeu de données SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Voici l’exemple de sortie de l’applet de commande :
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Créer un jeu de données récepteur

1. Créez un fichier JSON sous le nom SinkDataset.json dans le même dossier avec le contenu suivant. L’élément tableName est défini par le pipeline de manière dynamique lors de l’exécution. L’activité ForEach du pipeline effectue une itération dans une liste de noms de table et transmet le nom de table à ce jeu de données à chaque itération. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().SinkTableName}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "SinkTableName": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Exécutez la cmdlet **Set-AzureRmDataFactoryV2Dataset** pour créer le jeu de données SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Voici l’exemple de sortie de l’applet de commande :
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Créer un jeu de données pour un filigrane
Dans cette étape, vous allez créer un jeu de données pour stocker une valeur de limite supérieure. 

1. Créez un fichier JSON sous le nom WatermarkDataset.json dans le même dossier avec le contenu suivant : 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2. Exécutez la cmdlet **Set-AzureRmDataFactoryV2Dataset** pour créer le jeu de données WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Voici l’exemple de sortie de l’applet de commande :
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : <data factory name>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Créer un pipeline
Ce pipeline prend une liste de noms de tables comme paramètre. L’activité ForEach effectue une itération dans la liste de noms de table et effectue les opérations suivantes : 

1. Utilisez l’activité de recherche pour récupérer l’ancienne valeur de filigrane (valeur initiale ou valeur utilisée dans la dernière itération).

2. Utilisez l’activité de recherche pour récupérer la nouvelle valeur de filigrane (valeur maximale de la colonne de filigrane de la table source).

3. Utilisez l’activité de copie pour copier des données entre ces deux valeurs de filigrane depuis la base de données source vers la base de données de destination.

4. Utilisez l’activité StoredProcedure pour mettre à jour l’ancienne valeur de filigrane à utiliser au cours de la première étape de l’itération suivante. 

### <a name="create-the-pipeline"></a>Créer le pipeline
1. Créez un fichier JSON sous le nom IncrementalCopyPipeline.json dans le même dossier avec le contenu suivant : 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [{
    
                "name": "IterateSQLTables",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "items": {
                        "value": "@pipeline().parameters.tableList",
                        "type": "Expression"
                    },
    
                    "activities": [
                        {
                            "name": "LookupOldWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'"
                                },
    
                                "dataset": {
                                    "referenceName": "WatermarkDataset",
                                    "type": "DatasetReference"
                                }
                            }
                        },
                        {
                            "name": "LookupNewWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}"
                                },
    
                                "dataset": {
                                    "referenceName": "SourceDataset",
                                    "type": "DatasetReference"
                                }
                            }
                        },
    
                        {
                            "name": "IncrementalCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                                },
                                "sink": {
                                    "type": "SqlSink",
                                    "SqlWriterTableType": "@{item().TableType}",
                                    "SqlWriterStoredProcedureName": "@{item().StoredProcedureNameForMergeOperation}"
                                }
                            },
                            "dependsOn": [{
                                    "activity": "LookupNewWaterMarkActivity",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                },
                                {
                                    "activity": "LookupOldWaterMarkActivity",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
    
                            "inputs": [{
                                "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                            }],
                            "outputs": [{
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference",
                                "parameters": {
                                    "SinkTableName": "@{item().TABLE_NAME}"
                                }
                            }]
                        },
    
                        {
                            "name": "StoredProceduretoWriteWatermarkActivity",
                            "type": "SqlServerStoredProcedure",
                            "typeProperties": {
    
                                "storedProcedureName": "sp_write_watermark",
                                "storedProcedureParameters": {
                                    "LastModifiedtime": {
                                        "value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                        "type": "datetime"
                                    },
                                    "TableName": {
                                        "value": "@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                        "type": "String"
                                    }
                                }
                            },
    
                            "linkedServiceName": {
                                "referenceName": "AzureSQLDatabaseLinkedService",
                                "type": "LinkedServiceReference"
                            },
    
                            "dependsOn": [{
                                "activity": "IncrementalCopyActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }]
                        }
    
                    ]
    
                }
            }],
    
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```
2. Exécutez la cmdlet **Set-AzureRmDataFactoryV2Pipeline** pour créer le pipeline IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Voici l'exemple de sortie : 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Exécuter le pipeline

1. Créez un fichier de paramètres sous le nom Parameters.json dans le même dossier avec le contenu suivant :

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
            }
        ]
    }
    ```
2. Exécutez le pipeline IncrementalCopyPipeline en utilisant la cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Remplacez les espaces réservés par les noms de votre groupe de ressources et de votre fabrique de données.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Tous les services**, effectuez une recherche avec le mot clé *Fabriques de données*, puis sélectionnez **Fabriques de données**. 

    ![Menu Fabriques de données](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-data-factories-menu-1.png)

3. Recherchez votre fabrique de données dans la liste des fabriques de données, puis sélectionnez-la pour ouvrir la page **Fabrique de données**. 

    ![Rechercher votre fabrique de données](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-search-data-factory-2.png)

4. Dans la page **Fabrique de données**, sélectionnez **Surveiller et gérer**. 

    ![Vignette Surveiller et gérer](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-monitor-manage-tile-3.png)

5. L’**application d’intégration de données** s’ouvre dans un onglet distinct. Vous pouvez voir toutes les exécutions de pipeline et leurs états. Notez que dans l’exemple suivant, l’état d’exécution de pipeline est **Réussite**. Pour vérifier les paramètres transmis au pipeline, sélectionnez le lien dans la colonne **Paramètres**. Si une erreur s’est produite, un lien figure dans la colonne **Erreur**. Sélectionnez le lien dans la colonne **Actions**. 

    ![Exécutions de pipeline](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-4.png)    
6. Si vous sélectionnez le lien dans la colonne **Actions**, la page suivante affiche toutes les exécutions d’activité du pipeline : 

    ![Exécutions d’activité](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-activity-runs-5.png)

7. Pour revenir à la vue **Exécutions de pipeline**, sélectionnez **Pipelines** comme illustré sur l’image. 

## <a name="review-the-results"></a>Passer en revue les résultats.
Dans SQL Server Management Studio, exécutez les requêtes suivantes sur la base de données SQL cible pour vérifier que les données ont été copiées à partir des tables source vers les tables de destination : 

**Requête** 
```sql
select * from customer_table
```

**Sortie**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Requête**

```sql
select * from project_table
```

**Sortie**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Requête**

```sql
select * from watermarktable
```

**Sortie**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Notez que les valeurs de filigrane des deux tables ont été mises à jour. 

## <a name="add-more-data-to-the-source-tables"></a>Ajouter plus de données aux tables sources

Exécutez la requête suivante sur la base de données SQL Server source pour mettre à jour une ligne existante dans customer_table. Insérez une nouvelle ligne dans project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Exécutez à nouveau le pipeline

1. Maintenant, exécutez à nouveau le pipeline à l’aide de la commande PowerShell suivante :

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Analysez les exécutions du pipeline en suivant les instructions indiquées dans la section [Surveiller le pipeline](#monitor-the-pipeline). Comme l’état du pipeline est **En cours**, un autre lien d’action s’affiche sous **Actions** pour annuler l’exécution du pipeline. 

    ![Exécutions du pipeline en cours](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-6.png)

3. Sélectionnez **Actualiser** pour actualiser la liste jusqu’à ce que l’exécution du pipeline réussisse. 

    ![Actualiser les exécutions du pipeline](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-succeded-7.png)

4. Sélectionnez éventuellement le lien **Afficher les exécutions du pipeline** sous **Actions** pour voir toutes les exécutions d’activité associées à l’exécution de ce pipeline. 

## <a name="review-the-final-results"></a>Passer en revue les résultats finaux
Dans SQL Server Management Studio, exécutez les requêtes suivantes sur la base de données cible pour vérifier que les données nouvelles/mises à jour ont été copiées à partir des tables sources vers les tables de destination. 

**Requête** 
```sql
select * from customer_table
```

**Sortie**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Notez les nouvelles valeurs de **Name** et **LastModifytime** pour le **PersonID** du numéro 3. 

**Requête**

```sql
select * from project_table
```

**Sortie**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Notez que l’entrée **NewProject** a été ajoutée à project_table. 

**Requête**

```sql
select * from watermarktable
```

**Sortie**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Notez que les valeurs de filigrane des deux tables ont été mises à jour.
     
## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Préparer les magasins de données source et de destination.
> * Créer une fabrique de données.
> * Créer un runtime d’intégration auto-hébergé (IR).
> * Installer le runtime d’intégration.
> * créez des services liés. 
> * Créer des jeux de données source, récepteur et de filigrane.
> * Créez, exécutez et surveillez un pipeline.
> * Passez en revue les résultats.
> * Ajouter ou mettre à jour des données dans les tables source.
> * Réexécuter et surveiller le pipeline.
> * Passer en revue les résultats finaux.

Passez au didacticiel suivant pour en savoir plus sur la transformation des données en utilisant un cluster Spark sur Azure :

> [!div class="nextstepaction"]
>[Charger de façon incrémentielle des données d’Azure SQL Database dans le stockage Blob Azure à l’aide de la technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)


