---
title: Copier des données dans le stockage Blob à l’aide d’Azure Data Factory | Microsoft Docs
description: Créez une fabrique de données Azure pour copier les données d’un emplacement dans le stockage Blob Azure vers un autre emplacement.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: a060ac1051f56b123f76606c771f0957096d2457
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Créer une fabrique de données Azure à l’aide de PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](quickstart-create-data-factory-powershell.md)

Ce guide de démarrage rapide explique comment utiliser PowerShell pour créer une fabrique de données Azure. Le pipeline que vous créez dans cette fabrique de données **copie** les données d’un dossier vers un autre dossier dans un stockage Blob Azure. Pour suivre un didacticiel sur la **transformation** des données à l’aide d’Azure Data Factory, consultez [Didacticiel : transformation des données à l’aide de Spark](transform-data-using-spark.md). 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Cet article ne fournit pas de présentation détaillée du service Data Factory. Pour une présentation du service Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Installez les modules Azure PowerShell les plus récents en suivant les instructions décrites dans [Comment installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps).

#### <a name="log-in-to-powershell"></a>Se connecter à PowerShell

1. Lancez **PowerShell** sur votre ordinateur. Gardez PowerShell ouvert jusqu’à la fin de ce guide de démarrage rapide. Si vous la fermez, puis la rouvrez, vous devez réexécuter ces commandes.
2. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe Azure que vous utilisez pour vous connecter au portail Azure :
       
    ```powershell
    Login-AzureRmAccount
    ```        
3. Exécutez la commande suivante pour afficher tous les abonnements de ce compte :

    ```powershell
    Get-AzureRmSubscription
    ```
4. Si plusieurs abonnements sont associés à votre compte, exécutez la commande suivante pour sélectionner l’abonnement avec lequel vous souhaitez travailler. Remplacez **SubscriptionId** par l’ID de votre abonnement Azure :

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Créer une fabrique de données
1. Définissez une variable pour le nom du groupe de ressources que vous utiliserez ultérieurement dans les commandes PowerShell. Copiez le texte de commande suivant dans PowerShell, spécifiez un nom pour le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) entre des guillemets doubles, puis exécutez la commande. Par exemple : `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$ResourceGroupName` et exécutez à nouveau la commande
2. Pour créer le groupe de ressources Azure, exécutez la commande suivante : 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ``` 
    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$ResourceGroupName` et exécutez à nouveau la commande. 
3. Définissez une variable pour le nom de la fabrique de données. 

    > [!IMPORTANT]
    >  Mettez à jour le nom de la fabrique de données afin qu’il soit globalement unique. Par exemple, ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

5. Pour créer la fabrique de données, exécutez l’applet de commande suivante **Set-AzureRmDataFactoryV2**, à l’aide des propriétés Location et ResourceGroupName à partir de la variable $ResGrp : 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Notez les points suivants :

* Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être un membre des rôles **contributeur** ou **propriétaire**, ou un **administrateur** de l’abonnement Azure.
* À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

## <a name="create-a-linked-service"></a>Créer un service lié

Créez des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans ce guide de démarrage rapide, vous allez créer un service lié Stockage Azure qui sera utilisé à la fois comme banque de données source et réceptrice. Le service lié comporte les informations de connexion utilisées par le service Data Factory lors de l’exécution pour s’y connecter.

1. Créez un fichier JSON nommé **AzureStorageLinkedService.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant : (créez le dossier ADFv2QuickStartPSH s’il n’existe pas déjà). 

    > [!IMPORTANT]
    > Remplacez &lt;accountName&gt; et &lt;accountKey&gt; par le nom et la clé de votre compte de stockage Azure avant d’enregistrer le fichier.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
    Si vous utilisez Notepad, sélectionnez **Tous les fichiers** comme **Type d’enregistrement sous** renseigné dans la boîte de dialogue **Enregistrer sous**. Sinon, l’extension `.txt` pourrait être ajoutée au fichier. Par exemple : `AzureStorageLinkedService.json.txt`. Si vous créez le fichier dans l’Explorateur de fichiers avant de l’ouvrir dans Notepad, il se peut que l’extension `.txt` ne s’affiche pas car l’option **Masquer les extensions des types de fichiers connus** est définie par défaut. Supprimez l’extension `.txt` avant de passer à l’étape suivante.
2. Dans **PowerShell**, basculez vers le dossier **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```
3. Exécutez l’applet de commande **Set-AzureRmDataFactoryV2LinkedService** pour créer le service lié **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Voici l'exemple de sortie :

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Créer un jeu de données
Dans cette étape, vous allez définir un jeu de données qui représente les données à copier d’une source vers un récepteur. Le jeu de données est de type **AzureBlob**. Il fait référence au **service lié Stockage Azure** que vous avez créé à l’étape précédente. Il prend un paramètre pour construire la propriété **folderPath**. Pour un jeu de données d’entrée, l’activité de copie dans le pipeline transmet le chemin d’entrée en tant que valeur pour ce paramètre. De même, pour un jeu de données de sortie, l’activité de copie transmet le chemin de sortie en tant que valeur pour ce paramètre. 

1. Créez un fichier JSON nommé **BlobDataset.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant :

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "@{dataset().path}"
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Pour créer le jeu de données **BlobDataset**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Voici l'exemple de sortie :

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Créer un pipeline
  
Dans ce guide de démarrage rapide, vous allez créer un pipeline avec une activité qui accepte deux paramètres : le chemin de l’objet blob d’entrée et le chemin de l’objet blob de sortie. Les valeurs de ces paramètres sont définies quand le pipeline est déclenché/exécuté. L’activité de copie utilise le même jeu de données d’objet blob créé à l’étape précédente comme entrée et sortie. Quand le jeu de données est utilisé comme jeu de données d’entrée, le chemin d’entrée est spécifié. De même, quand le jeu de données est utilisé comme jeu de données de sortie, le chemin de sortie est spécifié. 

1. Créez un fichier JSON nommé **Adfv2QuickStartPipeline.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant :

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Pour créer le pipeline **Adfv2QuickStartPipeline**, exécutez l’applet de commande **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Voici l'exemple de sortie :

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline

Dans cette étape, vous définissez des valeurs pour les paramètres de pipeline : **inputPath** et **outputPath** avec les valeurs réelles des chemins des objets blob source et récepteur. Vous créez ensuite une exécution du pipeline à l’aide de ces arguments. 

1. Créez un fichier JSON nommé **PipelineParameters.json** dans le dossier **C:\ADFv2QuickStartPSH** avec le contenu suivant :

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Exécutez l’applet de commande **Invoke-AzureRmDataFactoryV2Pipeline** pour créer une exécution du pipeline et passer les valeurs des paramètres. L’applet de commande renvoie l’ID d’exécution du pipeline pour permettre une surveillance ultérieure.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1. Exécutez le script PowerShell suivant afin de vérifier continuellement l’état de l’exécution du pipeline jusqu’à la fin de la copie des données. Copiez/collez le script suivant dans la fenêtre PowerShell et appuyez sur ENTRÉE. 

    ```powershell
    while ($True) {
        $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output  "Pipeline is running...status: InProgress"
        }

        Start-Sleep -Seconds 10
    }   
    ```

    Voici l’exemple de sortie de l’exécution du pipeline :

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    Si vous recevez l’erreur :
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    Procédez comme suit : 
    1. Dans le fichier AzureStorageLinkedService.json, vérifiez que le nom et la clé de votre compte Stockage Azure sont corrects. 
    2. Vérifiez que le format de la chaîne de connexion est correct. Les propriétés (AccountName et AccountKey, par exemple) sont séparées par des points-virgules (`;`). 
    3. Si le nom et la clé du compte se trouvent entre crochets, supprimez ces derniers. 
    4. Voici un exemple de chaîne de connexion : 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. Recréez le service lié en suivant les étapes de la section [Créer un service lié](#create-a-linked-service). 
    6. Exécutez à nouveau le pipeline en suivant les étapes de la section [Créer une exécution du pipeline](#create-a-pipeline-run). 
    7. Exécutez à nouveau la commande de surveillance actuelle pour surveiller la nouvelle exécution du pipeline. 
1. Exécutez le script suivant pour récupérer les détails de l’exécution de l’activité de copie, par exemple la taille des données lues/écrites.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Vérifiez que la sortie est similaire à l’exemple de sortie suivant pour le résultat de l’exécution de l’activité :

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Consultez les [didacticiels](tutorial-copy-data-dot-net.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios. 
