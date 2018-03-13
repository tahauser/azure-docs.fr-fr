---
title: "Créer votre première fabrique de données Azure à l’aide d’un modèle Resource Manager | Microsoft Docs"
description: "Dans ce didacticiel, vous créez un exemple de pipeline Azure Data Factory en utilisant un modèle Azure Resource Manager."
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 50f6bebffbf9eb88ec2c725184793d1ea9291312
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Didacticiel : créer une fabrique de données Azure à l’aide du modèle Azure Resource Manager
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Version 2 - Préversion](quickstart-create-data-factory-resource-manager-template.md) 

Ce démarrage rapide vous montre comment utiliser un modèle Azure Resource Manager pour créer une fabrique de données Azure. Le pipeline que vous créez dans cette fabrique de données **copie** les données d’un dossier vers un autre dossier dans un stockage Blob Azure. Pour suivre un didacticiel sur la **transformation** des données à l’aide d’Azure Data Factory, consultez [Didacticiel : transformation des données à l’aide de Spark](transform-data-using-spark.md). 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, dont la disponibilité est générale (GA), consultez l’article [Créer votre première fabrique de données avec Data Factory version 1](v1/data-factory-build-your-first-pipeline-using-arm.md).
>
> Cet article ne fournit pas de présentation détaillée du service Data Factory. Pour une présentation du service Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Installez les modules Azure PowerShell les plus récents en suivant les instructions décrites dans [Comment installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="resource-manager-templates"></a>Modèles Resource Manager
Pour en savoir plus sur les modèles Azure Resource Manager, consultez l’article [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). 

La section suivante fournit le modèle Resource Manager complet permettant de définir des entités Data Factory pour que vous puissiez rapidement parcourir le didacticiel et tester le modèle. Pour comprendre comment chaque entité Data Factory est définie, consultez la section [Entités Data Factory dans le modèle](#data-factory-entities-in-the-template).

## <a name="data-factory-json"></a>JSON de la fabrique de données 
Créez un fichier JSON nommé **ADFTutorialARM.json** dans le dossier **C:\ADFTutorial** avec le contenu suivant :

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "dataFactoryName": {
            "type": "string",
            "metadata": {
                "description": "Name of the data factory. Must be globally unique."
            }
        },
        "dataFactoryLocation": {
            "type": "string",
            "allowedValues": [
                "East US",
                "East US 2",
                "West Europe"
            ],
            "defaultValue": "East US",
            "metadata": {
                "description": "Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey": {
            "type": "securestring",
            "metadata": {
                "description": "Key for the Azure storage account."
            }
        },
        "blobContainer": {
            "type": "string",
            "metadata": {
                "description": "Name of the blob container in the Azure Storage account."
            }
        },
        "inputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that has the input file."
            }
        },
        "inputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the input file/blob."
            }
        },
        "outputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that will hold the transformed data."
            }
        },
        "outputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the output file/blob."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },
    "variables": {
        "azureStorageLinkedServiceName": "ArmtemplateStorageLinkedService",
        "inputDatasetName": "ArmtemplateTestDatasetIn",
        "outputDatasetName": "ArmtemplateTestDatasetOut",
        "pipelineName": "ArmtemplateSampleCopyPipeline",
        "triggerName": "ArmTemplateTestTrigger"
    },
    "resources": [{
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "2017-09-01-preview",
        "type": "Microsoft.DataFactory/factories",
        "location": "[parameters('dataFactoryLocation')]",
        "properties": {
            "loggingStorageAccountName": "[parameters('storageAccountName')]",
            "loggingStorageAccountKey": "[parameters('storageAccountKey')]"
        },
        "resources": [{
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": {
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                            "type": "SecureString"
                        }
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('inputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
                        "fileName": "[parameters('inputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('outputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
                        "fileName": "[parameters('outputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "pipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "activities": [{
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "BlobSource"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "name": "MyCopyActivity",
                        "inputs": [{
                            "referenceName": "[variables('inputDatasetName')]",
                            "type": "DatasetReference"
                        }],
                        "outputs": [{
                            "referenceName": "[variables('outputDatasetName')]",
                            "type": "DatasetReference"
                        }]
                    }]
                }
            },
            {
                "type": "triggers",
                "name": "[variables('triggerName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]",
                    "[variables('pipelineName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "ScheduleTrigger",
                    "typeProperties": {
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1,
                            "startTime": "[parameters('triggerStartTime')]",
                            "endTime": "[parameters('triggerEndTime')]",
                            "timeZone": "UTC"
                        }
                    },
                    "pipelines": [{
                        "pipelineReference": {
                            "type": "PipelineReference",
                            "referenceName": "ArmtemplateSampleCopyPipeline"
                        },
                        "parameters": {}
                    }]
                }
            }
        ]
    }]
}
```

## <a name="parameters-json"></a>Paramètres JSON
Créez un fichier JSON nommé **ADFTutorialARM-Parameters** contient les paramètres du modèle Azure Resource Manager.  

> [!IMPORTANT]
> - Spécifiez le nom et la clé de votre compte Stockage Azure pour les paramètres **storageAccountName** et **storageAccountKey** dans ce fichier de paramètres. Vous avez créé le conteneur adftutorial et téléchargé l’exemple de fichier (emp.txt) dans le dossier d’entrée de ce stockage d’objets blob Azure. 
> - Spécifiez un nom global unique pour la fabrique de données pour le paramètre **dataFactoryName**. Par exemple : ARMTutorialFactoryJohnDoe11282017. 
> - Pour la valeur **triggerStartTime**, spécifiez la date du jour au format : `2017-11-28T00:00:00`.
> - Pour la valeur **triggerEndTime**, spécifiez la date du jour suivant au format : `2017-11-29T00:00:00`. Vous pouvez également vérifier l’heure UTC actuelle et spécifier l’heure suivante ou les deux heures suivantes comme heure de fin. Par exemple, si l’heure UTC actuelle est 1:32, spécifiez `2017-11-29:03:00:00` comme heure de fin. Dans ce cas, le déclencheur exécute le pipeline deux fois (à 2:00 et 3:00).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataFactoryName": {
      "value": "<datafactoryname>"
    },    
    "dataFactoryLocation": {
      "value": "East US"
    },
    "storageAccountName": {
      "value": "<yourstroageaccountname>"
    },
    "storageAccountKey": {
      "value": "<yourstorageaccountkey>"
    },
    "blobContainer": {
      "value": "adftutorial"
    },
    "inputBlobFolder": {
      "value": "input"
    },
    "inputBlobName": {
      "value": "emp.txt"
    },
    "outputBlobFolder": {
      "value": "output"
    },
    "outputBlobName": {
      "value": "emp.txt"
    },
    "triggerStartTime": {
        "value": "2017-11-28T00:00:00. Set to today"
    },
    "triggerEndTime": {
        "value": "2017-11-29T00:00:00. Set to tomorrow"
    }
  }
}
```

> [!IMPORTANT]
> Vous pouvez utiliser des fichiers JSON de paramètres distincts pour les environnements de développement, de test et de production avec le même modèle JSON Data Factory. En utilisant un script PowerShell, vous pouvez automatiser le déploiement des entités Data Factory dans ces environnements. 

## <a name="deploy-data-factory-entities"></a>Déployer des entités Data Factory 
Dans PowerShell, exécutez la commande suivante pour déployer des entités Data Factory à l’aide du modèle Resource Manager que vous avez créé précédemment dans ce démarrage rapide. 

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Une sortie similaire à l’exemple suivant s’affiche : 

```
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 11/29/2017 3:11:13 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                 Type            Value
                          ===============      ============    ==========
                          dataFactoryName      String          <data factory name>
                          dataFactoryLocation  String          East US
                          storageAccountName   String          <storage account name>
                          storageAccountKey    SecureString
                          blobContainer        String          adftutorial
                          inputBlobFolder      String          input
                          inputBlobName        String          emp.txt
                          outputBlobFolder     String          output
                          outputBlobName       String          emp.txt
                          triggerStartTime     String          11/29/2017 12:00:00 AM
                          triggerEndTime       String          11/29/2017 4:00:00 AM

Outputs                 :
DeploymentDebugLogLevel :
```

## <a name="start-the-trigger"></a>Démarrer le déclencheur

Le modèle déploie les entités Data Factory suivantes : 

- Service lié Stockage Azure
- Jeux de données Blob Azure (entrée et sortie)
- Pipeline avec une activité de copie
- Déclencheur pour déclencher le pipeline

Le déclencheur déployé est à l’arrêt. Une des méthodes pour démarrer le déclencheur consiste à utiliser l’applet de commande PowerShell **Start-AzureRmDataFactoryV2Trigger**. La procédure suivante fournit des étapes détaillées : 

1. Dans la fenêtre PowerShell, créez une variable pour contenir le nom du groupe de ressources. Copiez la commande suivante dans la fenêtre PowerShell et appuyez sur ENTRÉE. Si vous avez spécifié un nom de groupe de ressources différent pour la commande New-AzureRmResourceGroupDeployment, mettez à jour la valeur ici. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ``` 
1. Créez une variable pour contenir le nom de la fabrique de données. Spécifiez le même nom que vous avez spécifié dans le fichier ADFTutorialARM-Parameters.json.   

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Définissez une variable pour le nom du déclencheur. Le nom du déclencheur est codé en dur dans le fichier de modèle Resource Manager (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Obtenez l’**état du déclencheur** en exécutant la commande PowerShell suivante après avoir spécifié le nom de votre fabrique de données et de votre déclencheur :

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Voici l'exemple de sortie : 

    ```json
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Notez que l’état d’exécution du déclencheur est **arrêté**. 
5. **Démarrez le déclencheur**. Le déclencheur exécute le pipeline défini dans le modèle à l’heure suivante. Ainsi, si vous avez exécuté cette commande à 14:25, le déclencheur exécute le pipeline à 15:00 pour la première fois. Ensuite, il exécute le pipeline toutes les heures jusqu’à l’heure de fin que vous avez spécifiée pour le déclencheur. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Voici l'exemple de sortie : 
    
    ```
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Vérifiez que le déclencheur a été démarré en exécutant à nouveau la commande Get-AzureRmDataFactoryV2Trigger.  

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Voici l'exemple de sortie :
    
    ```
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline
1. Après vous être connecté au [portail Azure](https://portal.azure.com/), cliquez sur **Tous les services**, faites une recherche avec le mot clé **data fa**, puis sélectionnez **Fabriques de données**.

    ![Parcourir le menu Fabriques de données](media/quickstart-create-data-factory-resource-manager-template/browse-data-factories-menu.png)
2. Sur la page **Fabriques de données**, cliquez sur la fabrique de données que vous avez créée. Si nécessaire, filtrez la liste avec le nom de votre fabrique de données.  

    ![Sélectionner une fabrique de données](media/quickstart-create-data-factory-resource-manager-template/select-data-factory.png)
3. Sur la page Fabrique de données, cliquez sur la vignette **Surveiller et gérer**. 

    ![Vignette Surveiller et gérer](media/quickstart-create-data-factory-resource-manager-template/monitor-manage-tile.png)
4. L’**application d’intégration de données** devrait s’ouvrir dans un onglet distinct dans le navigateur web. Si l’onglet Surveiller n’est pas actif, basculez vers l’**onglet Surveiller**. Notez que l’exécution du pipeline a été déclenchée par un **déclencheur Scheduler**. 

    ![Surveillance de l’exécution du pipeline](media/quickstart-create-data-factory-resource-manager-template/monitor-pipeline-run.png)    

    > [!IMPORTANT]
    > Les exécutions du pipeline s’affichent uniquement par heure (par exemple : 4:00, 5:00, 6:00, etc.). Cliquez sur **Actualiser** dans la barre d’outils pour actualiser la liste lorsque l’heure atteint l’heure suivante. 
5. Cliquez sur le lien dans les colonnes **Actions**. 

    ![Lien Actions de pipeline](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)
6. Vous voyez les exécutions d’activités associées avec l’exécution du pipeline. Dans ce guide de démarrage rapide, le pipeline n’a qu’une seule activité de type : Copie. Par conséquent, vous observez une exécution de cette activité. 

    ![Exécutions d’activités](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
1. Cliquez sur le lien situé sous la colonne **Sortie**. Vous voyez la sortie de l’opération de copie dans une fenêtre **Sortie**. Cliquez sur le bouton Agrandir pour afficher la sortie complète. Vous pouvez fermer la fenêtre de sortie agrandie ou la fermer. 

    ![Fenêtre Sortie](media/quickstart-create-data-factory-resource-manager-template/output-window.png)
7. Arrêtez le déclencheur une fois que vous voyez une exécution réussie ou un échec. Le déclencheur exécute le pipeline une fois par heure. Le pipeline copie le même fichier à partir du dossier d’entrée dans le dossier de sortie à chaque exécution. Pour arrêter le déclencheur, exécutez la commande suivante dans la fenêtre PowerShell. 
    
    ```powershell
    Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="json-definitions-for-entities"></a>Définitions JSON pour les entités
Les entités Data Factory suivantes sont définies dans le modèle JSON : 

- [Service lié Azure Storage](#azure-storage-linked-service)
- [Jeu de données d'entrée d'objet Blob Azure](#azure-blob-input-dataset)
- [Jeu de données de sortie d'objet Blob Azure](#azure-blob-output-dataset)
- [Pipeline de données avec une activité de copie](#data-pipeline)
- [Déclencheur](#trigger)

#### <a name="azure-storage-linked-service"></a>Service lié Stockage Azure
AzureStorageLinkedService relie votre compte de stockage Azure à la fabrique de données. Vous avez créé un conteneur et chargé des données dans ce compte de stockage en remplissant les conditions préalables. Vous spécifiez le nom et la clé de votre compte Stockage Azure dans cette section. Consultez [Service lié Stockage Azure](connector-azure-blob-storage.md#linked-service-properties) pour en savoir plus sur les propriétés JSON utilisées pour définir un service lié Stockage Azure. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": {
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                "type": "SecureString"
            }
        }
    }
}
```

La propriété connectionString utilise les paramètres storageAccountName et storageAccountKey. Les valeurs de ces paramètres sont transmises à l’aide d’un fichier de configuration. La définition utilise également les variables azureStroageLinkedService et dataFactoryName, définies dans le modèle. 

#### <a name="azure-blob-input-dataset"></a>Jeu de données d'entrée d'objet Blob Azure
Le service lié Stockage Azure spécifie la chaîne de connexion que le service Data Factory utilise au moment de l’exécution pour se connecter à votre compte de stockage Azure. Dans la définition du jeu de données d’objets blob Azure, vous spécifiez les noms du conteneur d’objets blob, du dossier et du fichier contenant les données d’entrée. Consultez [Propriétés du jeu de données d’objet blob Azure](connector-azure-blob-storage.md#dataset-properties) pour en savoir plus sur les propriétés JSON permettant de définir un jeu de données d’objets blob Azure. 

```json
{
    "type": "datasets",
    "name": "[variables('inputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
        "fileName": "[parameters('inputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
},

```

#### <a name="azure-blob-output-dataset"></a>Jeu de données de sortie d’objet Blob Azure
Vous spécifiez le nom du dossier dans le Stockage Blob Azure contenant les données copiées à partir du dossier d’entrée. Consultez [Propriétés du jeu de données d’objet blob Azure](connector-azure-blob-storage.md#dataset-properties) pour en savoir plus sur les propriétés JSON permettant de définir un jeu de données d’objets blob Azure. 

```json
{
    "type": "datasets",
    "name": "[variables('outputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
        "fileName": "[parameters('outputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
}
```

#### <a name="data-pipeline"></a>Pipeline de données
Vous définissez un pipeline qui copie les données d’un jeu de données d’objet blob Azure vers un autre jeu de données d’objet blob Azure. Consultez [Pipeline JSON](concepts-pipelines-activities.md#pipeline-json) pour obtenir des descriptions des éléments JSON permettant de définir un pipeline dans cet exemple. 

```json
{
    "type": "pipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('inputDatasetName')]",
    "[variables('outputDatasetName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "activities": [
        {
        "type": "Copy",
        "typeProperties": {
            "source": {
            "type": "BlobSource"
            },
            "sink": {
            "type": "BlobSink"
            }
        },
        "name": "MyCopyActivity",
        "inputs": [
            {
            "referenceName": "[variables('inputDatasetName')]",
            "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
            "referenceName": "[variables('outputDatasetName')]",
            "type": "DatasetReference"
            }
        ]
        }
    ]
    }
}
```

#### <a name="trigger"></a>Déclencheur
Vous définissez un déclencheur qui exécute le pipeline une fois par heure. Le déclencheur déployé est à l’arrêt. Démarrez le déclencheur à l’aide de l’applet de commande **Start-AzureRmDataFactoryV2Trigger**. Pour plus d’informations sur les déclencheurs, consultez l’article [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#triggers). 

```json
{
    "type": "triggers",
    "name": "[variables('triggerName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('inputDatasetName')]",
        "[variables('outputDatasetName')]",
        "[variables('pipelineName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-28T00:00:00",
                "endTime": "2017-11-29T00:00:00",
                "timeZone": "UTC"               
            }
        },
        "pipelines": [{
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "ArmtemplateSampleCopyPipeline"
            },
            "parameters": {}
        }]
    }
}
```

## <a name="reuse-the-template"></a>Réutiliser le modèle
Dans ce didacticiel, vous avez créé un modèle pour définir des entités Data Factory et un modèle pour transmettre les valeurs des paramètres. Pour utiliser le même modèle afin de déployer des entités Data Factory dans des environnements différents, vous créez un fichier de paramètres pour chaque environnement et l’utiliser lors du déploiement de cet environnement.     

Exemple :  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Notez que la première commande utilise le fichier de paramètres pour l’environnement de développement, la deuxième pour l’environnement de test et la troisième pour l’environnement de production.  

Vous pouvez également réutiliser le modèle pour effectuer des tâches répétitives. Par exemple, créer plusieurs fabriques de données avec un ou plusieurs pipelines qui implémentent la même logique, mais chaque fabrique de données utilise des comptes Stockage Azure différents. Dans ce scénario, vous utilisez le même modèle dans le même environnement (développement, test ou production) avec différents fichiers de paramètres pour créer des fabriques de données. 


## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Consultez les [didacticiels](tutorial-copy-data-dot-net.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios. 
