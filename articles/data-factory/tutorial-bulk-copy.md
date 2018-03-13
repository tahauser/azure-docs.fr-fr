---
title: "Copier des données en bloc à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Factory et Copier l’activité	 pour copier en bloc les données d’une banque de données source dans une banque de données de destination."
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
ms.openlocfilehash: 96a5caca1f00b46330c839ef3380d58cf9913ad5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copier plusieurs tables en bloc à l’aide d’Azure Data Factory
Ce didacticiel montre **comment copier des tables d’Azure SQL Database dans Azure SQL Data Warehouse**. Vous pouvez appliquer le même modèle à d’autres scénarios de copie. Par exemple : copie de tables à partir de SQL Server/Oracle dans Azure SQL Database/Data Warehouse/Azure Blob, copie de différents chemins à partir de Blob dans des tables Azure SQL Database.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

À un niveau élevé, ce didacticiel implique les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer des services liés à Azure SQL Database, Azure SQL Data Warehouse, et Stockage Azure.
> * Créer des jeux de données Azure SQL Database et Azure SQL Data Warehouse.
> * Créer un pipeline pour rechercher les tables à copier et un autre pipeline pour effectuer l’opération de copie. 
> * Démarrer une exécution de pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

Ce didacticiel utilise Azure PowerShell. Pour en savoir plus sur l’utilisation d’autres outils/SDK pour créer une fabrique de données, consultez [Démarrages rapides](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Workflow de bout en bout
Dans ce scénario, nous disposons d’un certain nombre de tables dans Azure SQL Database que nous souhaitons copier dans SQL Data Warehouse. Voici l’ordre logique des étapes du workflow qui se produit dans les pipelines :

![Workflow](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* Le premier pipeline recherche la liste des tables à copier dans les banques de données du récepteur.  Vous pouvez également conserver une table de métadonnées qui répertorie toutes les tables à copier dans la banque de données du récepteur. Le pipeline déclenche ensuite un autre pipeline qui itère chaque table dans la base de données et effectue l’opération de copie de données.
* Le second pipeline effectue la copie. Il prend la liste des tables comme paramètre. Pour chaque table dans la liste, copiez la table spécifique d’Azure SQL Database dans la table correspondante de SQL Data Warehouse à l’aide d’une [copie intermédiaire par le biais de Stockage Blob et PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) afin d’obtenir de meilleurs résultats. Dans cet exemple, le premier pipeline passe la liste des tables comme valeur pour le paramètre. 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>configuration requise

* **Azure PowerShell**. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps).
* **Compte Stockage Azure**. Le compte Stockage Azure est utilisé comme stockage d’objets blob intermédiaire dans l’opération de copie en bloc. 
* **Base de données SQL Azure**. Cette base de données contient les données sources. 
* **Azure SQL Data Warehouse**. Cet entrepôt de données conserve les données copiées à partir de SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Préparer SQL Database et SQL Data Warehouse

**Préparer la base de données SQL Azure source** :

Créez une base de données SQL Azure avec l’exemple de données Adventure Works LT. Pour cela, suivez les instructions de l’article [Créer une base de données SQL Azure](../sql-database/sql-database-get-started-portal.md). Ce didacticiel copie toutes les tables de cet exemple de base de données dans un entrepôt de données SQL.

**Préparer le récepteur Azure SQL Data Warehouse** :

1. Si vous n’avez pas d’entrepôt de données Azure SQL Data Warehouse, consultez l’article [Créer un entrepôt de données SQL](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) pour la procédure à suivre.

2. Créez les schémas de table correspondants dans SQL Data Warehouse. Vous pouvez utiliser l’[utilitaire de migration](https://www.microsoft.com/download/details.aspx?id=49100) pour **migrer le schéma** d’Azure SQL Database vers Azure SQL Data Warehouse. Plus tard, vous utiliserez Azure Data Factory pour migrer/copier les données.

## <a name="azure-services-to-access-sql-server"></a>Services Azure pour accéder au serveur SQL

Pour SQL Database et SQL Data Warehouse, autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est **activé** pour votre serveur SQL Azure. Ce paramètre permet au service Data Factory de lire les données de votre base de données SQL Azure et de les écrire dans votre entrepôt de données SQL Azure. Pour vérifier et activer ce paramètre, procédez comme suit :

1. Cliquez sur **Tous les services** sur la gauche, puis sur **Serveurs SQL**.
2. Sélectionnez votre serveur, puis cliquez sur **Pare-feu** sous **PARAMÈTRES**.
3. Dans la page **Paramètres de pare-feu**, cliquez sur **ACTIVER** pour **Autoriser l’accès aux services Azure**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez **PowerShell**. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous fermez puis rouvrez Azure PowerShell, vous devez réexécuter ces commandes.

    Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure :
        
    ```powershell
    Login-AzureRmAccount
    ```
    Exécutez la commande suivante pour afficher tous les abonnements de ce compte :

    ```powershell
    Get-AzureRmSubscription
    ```
    Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **SubscriptionId** par l’ID de votre abonnement Azure :

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Exécutez l’applet de commande **Set-AzureRmDataFactoryV2** pour créer une fabrique de données. Avant d’exécuter la commande, remplacez les espaces réservés par vos propres valeurs. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Notez les points suivants :

    * Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Pour créer des instances de fabrique de données, vous devez être un administrateur/collaborateur de l’abonnement Azure.
    * À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

## <a name="create-linked-services"></a>Créez des services liés

Dans ce didacticiel, vous allez créer trois services liés (un pour la source, un pour le récepteur et un pour l’objet blob intermédiaire), avec des connexions à vos magasins de données :

### <a name="create-the-source-azure-sql-database-linked-service"></a>Créer le service lié Azure SQL Database pour la source

1. Créez un fichier JSON nommé **AzureSqlDatabaseLinkedService.json** dans le dossier **C:\ADFv2TutorialBulkCopy** avec le contenu suivant (créez le dossier ADFv2TutorialBulkCopy s’il n’existe pas) :

    > [!IMPORTANT]
    > Remplacez &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; et &lt;password&gt; par les valeurs de votre base de données SQL Azure avant d’enregistrer le fichier.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. Dans **Azure PowerShell**, passez au dossier **ADFv2TutorialBulkCopy**.

3. Exécutez l’applet de commande **Set-AzureRmDataFactoryV2LinkedService** pour créer le service lié : **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Créer le service lié Azure SQL Data Warehouse pour le récepteur

1. Créez un fichier JSON nommé **AzureSqlDWLinkedService.json** dans le dossier **C:\ADFv2TutorialBulkCopy** avec le contenu suivant :

    > [!IMPORTANT]
    > Remplacez &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; et &lt;password&gt; par les valeurs de votre base de données SQL Azure avant d’enregistrer le fichier.

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
            }
        }
    }
    ```

2. Pour créer le service lié **AzureSqlDWLinkedService**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2LinkedService**.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Créer le service lié Stockage Azure intermédiaire

Dans ce didacticiel, vous allez utiliser Stockage Blob Azure comme zone intermédiaire pour améliorer les performances de copie de PolyBase.

1. Créez un fichier JSON nommé **AzureSqlDWLinkedService.json** dans le dossier **C:\ADFv2TutorialBulkCopy** avec le contenu suivant :

    > [!IMPORTANT]
    > Remplacez &lt;accountName&gt; et &lt;accountKey&gt; par le nom et la clé de votre compte de stockage Azure avant d’enregistrer le fichier.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. Pour créer le service lié **AzureStorageLinkedService**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2LinkedService**.

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>Créez les jeux de données

Dans ce didacticiel, vous créez des jeux de données (source et récepteur) qui spécifient l’emplacement de stockage des données :

### <a name="create-a-dataset-for-source-sql-database"></a>Créer un jeu de données pour la base de données SQL source

1. Créez un fichier JSON nommé **AzureSqlDatabaseDataset.json** dans le dossier **C:\ADFv2TutorialBulkCopy** avec le contenu suivant : « tableName » est un nom factice. Plus tard, vous utiliserez la requête SQL dans l’activité de copie pour récupérer des données.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Pour créer le jeu de données **AzureSqlDatabaseDataset**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Voici l'exemple de sortie :

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Créer un jeu de données pour le récepteur SQL Data Warehouse

1. Créez un fichier JSON nommé **AzureSqlDWDataset.json** dans le dossier **C:\ADFv2TutorialBulkCopy** avec le contenu suivant : « tableName » est défini en tant que paramètre. Plus tard, l’activité de copie qui référence ce jeu de données passera la valeur réelle au jeu de données.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Pour créer le jeu de données **AzureSqlDWDataset**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Voici l'exemple de sortie :

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Créer des pipelines

Dans ce didacticiel, vous allez créer deux pipelines :

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Créer le pipeline « IterateAndCopySQLTables »

Ce pipeline prend une liste de tables comme paramètre. Pour chaque table dans la liste, il copie les données de la table dans Azure SQL Database vers Azure SQL Data Warehouse en utilisant la copie intermédiaire et PolyBase.

1. Créez un fichier JSON nommé **IterateAndCopySQLTables.json** dans le dossier **C:\ADFv2TutorialBulkCopy** avec le contenu suivant :

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
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
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Pour créer le pipeline **IterateAndCopySQLTables**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Voici l'exemple de sortie :

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Créer le pipeline « GetTableListAndTriggerCopyData »

Ce pipeline exécute deux étapes :

* Recherche la table système Azure SQL Database pour obtenir la liste des tables à copier.
* Déclenche le pipeline « IterateAndCopySQLTables » pour copier les données.

1. Créez un fichier JSON nommé **GetTableListAndTriggerCopyData.json** dans le dossier **C:\ADFv2TutorialBulkCopy** avec le contenu suivant :

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Pour créer le pipeline **GetTableListAndTriggerCopyData**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Voici l'exemple de sortie :

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Démarrer et surveiller une exécution de pipeline

1. Démarrez une exécution de pipeline pour le pipeline principal « GetTableListAndTriggerCopyData » et capturez l’ID d’exécution du pipeline pour une analyse ultérieure. Ceci déclenche l’exécution du pipeline « IterateAndCopySQLTables », comme indiqué dans l’activité ExecutePipeline.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Exécutez le script suivant pour vérifier en permanence l’état de l’exécution du pipeline **GetTableListAndTriggerCopyData**, puis imprimez le résultat final de l’exécution de pipeline et de l’exécution d’activité.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Voici la sortie de l’exemple d’exécution :

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Vous pouvez obtenir l’ID d’exécution du pipeline « **IterateAndCopySQLTables** », puis vérifier le résultat détaillé de l’exécution d’activité comme suit.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Voici la sortie de l’exemple d’exécution :

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Connectez-vous à votre récepteur Azure SQL Data Warehouse et vérifiez que les données ont bien été copiées à partir d’Azure SQL Database.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer des services liés à Azure SQL Database, Azure SQL Data Warehouse, et Stockage Azure.
> * Créer des jeux de données Azure SQL Database et Azure SQL Data Warehouse.
> * Créer un pipeline pour rechercher les tables à copier et un autre pipeline pour effectuer l’opération de copie. 
> * Démarrer une exécution de pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

Passez au didacticiel suivant pour découvrir comment copier des données de manière incrémentielle d’une source vers une destination :
> [!div class="nextstepaction"]
>[Copier des données de façon incrémentielle](tutorial-incremental-copy-powershell.md)