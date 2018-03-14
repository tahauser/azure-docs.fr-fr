---
title: "Créer votre première fabrique de données Azure (PowerShell) | Microsoft Docs"
description: "Dans ce didacticiel, vous allez créer un exemple de pipeline Azure Data Factory à l’aide d’Azure PowerShell."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: 
editor: 
ms.assetid: 22ec1236-ea86-4eb7-b903-0e79a58b90c7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: bea8f944e59774abdb70f0f1cad37cfe0749b195
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Didacticiel : Créer votre première fabrique de données Azure à l’aide d’Azure PowerShell
> [!div class="op_single_selector"]
> * [Vue d’ensemble et étapes préalables requises](data-factory-build-your-first-pipeline.md)
> * [Portail Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modèle Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). Si vous utilisez la version 2 du service Data Factory, qui est en préversion, consultez [Démarrage rapide : créer une fabrique de données à l’aide de la version 2 de Azure Data Factory](../quickstart-create-data-factory-powershell.md).

Dans cet article, vous utilisez Azure PowerShell pour créer votre première fabrique de données Azure. Pour suivre le didacticiel avec d’autres outils/Kits de développement logiciel (SDK), sélectionnez une des options dans la liste déroulante.

Le pipeline dans ce didacticiel a une activité : **Activité HDInsight Hive**. Cette activité exécute un script Hive sur un cluster HDInsight qui transforme des données d’entrée pour produire des données de sortie. Le pipeline est programmé pour s’exécuter une fois par mois entre les heures de début et de fin spécifiées. 

> [!NOTE]
> Dans ce didacticiel, le pipeline de données transforme les données d’entrée pour produire des données de sortie. Il ne copie pas les données d’un magasin de données source vers un magasin de données de destination. Pour un didacticiel sur la copie de données à l’aide d’Azure Data Factory, consultez [Copie de données Blob Storage vers une base de données SQL à l’aide de Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Un pipeline peut contenir plusieurs activités. En outre, vous pouvez chaîner deux activités (une après l’autre) en configurant le jeu de données de sortie d’une activité en tant que jeu de données d’entrée de l’autre activité. Pour plus d’informations, consultez [Planification et exécution dans Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Prérequis
* Lisez l’article [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md) et effectuez les **étapes préalables requises** .
* Suivez les instructions de l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) pour installer la dernière version d’Azure PowerShell sur votre ordinateur.
* (facultatif) Cet article ne couvre pas toutes les applets de commande de Data Factory. Consultez la [Référence des applets de commande Data Factory](/powershell/module/azurerm.datafactories) pour obtenir une documentation complète sur les applets de commande Data Factory.

## <a name="create-data-factory"></a>Créer une fabrique de données
Dans cette étape, vous utilisez Azure PowerShell pour créer une fabrique de données Azure nommée **FirstDataFactoryPSH**. Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline peut contenir une ou plusieurs activités. Par exemple, une activité de copie censée copier des données d’un magasin de données source vers un magasin de données de destination, et une activité Hive HDInsight pour exécuter un script Hive pour transformer des données d’entrée. Commençons par la création de la fabrique de données dans cette étape.

1. Démarrez Azure PowerShell et exécutez la commande suivante. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous la fermez, puis la rouvrez, vous devez réexécuter ces commandes.
   * Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure.
    ```PowerShell
    Login-AzureRmAccount
    ```    
   * Exécutez la commande suivante pour afficher tous les abonnements de ce compte.
    ```PowerShell
    Get-AzureRmSubscription 
    ```
   * Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Cet abonnement doit être identique à celui utilisé dans le portail Azure.
    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext
    ```     
2. Créez un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante :
    
    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé ADFTutorialResourceGroup. Si vous utilisez un autre groupe de ressources, vous devrez l’utiliser à la place d’ADFTutorialResourceGroup dans ce didacticiel.
3. Exécutez l’applet de commande **New-AzureRmDataFactory** qui crée une fabrique de données nommée **FirstDataFactoryPSH**.

    ```PowerShell
    New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
Notez les points suivants :

* Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur suivante s’affiche : **Le nom de la fabrique de données « FirstDataFactoryPSH » n’est pas disponible**, changez le nom (par exemple en votrenomFirstDataFactoryPSH). Utilisez ce nom à la place d’ADFTutorialFactoryPSH quand vous effectuez les étapes de ce didacticiel. Consultez la rubrique [Data Factory - Règles d’affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
* Pour créer des instances de fabrique de données, vous devez avoir le statut d’administrateur/collaborateur de l’abonnement Azure
* Le nom de la fabrique de données pourra être enregistré en tant que nom DNS et devenir ainsi visible publiquement.
* Si vous recevez le message d’erreur : «**L’abonnement n’est pas inscrit pour utiliser l’espace de noms Microsoft.DataFactory**», effectuez l’une des opérations suivantes et essayez de relancer la publication :

  * Dans Azure PowerShell, exécutez la commande suivante pour enregistrer le fournisseur Data Factory :

    ```PowerShell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Vous pouvez exécuter la commande suivante pour confirmer l’enregistrement du fournisseur Data Factory :

    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de l’abonnement Azure et accédez à un panneau Data Factory (ou) créez une fabrique de données dans le portail Azure. Cette action enregistre automatiquement le fournisseur.

Avant de créer un pipeline, vous devez d’abord créer quelques entités de la fabrique de données. Créez d’abord des services liés pour lier des magasins de données/calculs à votre magasin de données, définissez des jeux de données d’entrée et de sortie pour représenter les données d’entrée/sortie dans les magasins de données liés, puis créez le pipeline avec une activité qui utilise ces jeux de données.

## <a name="create-linked-services"></a>Créez des services liés
Dans cette étape, vous liez votre compte Stockage Azure et un cluster Azure HDInsight à la demande à votre fabrique de données. Le compte Stockage Azure contient les données d’entrée et de sortie pour le pipeline de cet exemple. Le service lié HDInsight est utilisé pour exécuter le script Hive spécifié dans l’activité du pipeline de cet exemple. Identifiez les services de magasin de données/de calcul qui sont utilisés dans votre scénario et les lier à la fabrique de données en créant des services liés.

### <a name="create-azure-storage-linked-service"></a>Créer le service lié Stockage Azure
Dans cette étape, vous liez votre compte Stockage Azure à votre fabrique de données. Vous utilisez le même compte Stockage Azure pour stocker les données d’entrée/sortie et le fichier de script HQL.

1. Créez un fichier JSON nommé StorageLinkedService.json dans le dossier C:\ADFGetStarted, avec le contenu suivant. Créez le dossier ADFGetStarted s’il n’existe pas encore.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }
    ```
    Remplacez **account name** par le nom de votre compte de stockage Azure et **account key** par sa clé d’accès. Pour savoir comment obtenir votre clé d’accès de stockage, reportez-vous aux informations sur l’affichage, la copie et la régénération de clés d’accès de stockage dans [Gérer votre compte de stockage](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).
2. Dans Azure PowerShell, accédez au dossier ADFGetStarted.
3. Vous pouvez utiliser l’applet de commande **New-AzureRmDataFactoryLinkedService** qui crée un service lié. Cette applet de commande et d’autres applets de commande Data Factory que vous utilisez dans ce didacticiel vous obligent à transmettre des valeurs aux paramètres *ResourceGroupName* et *DataFactoryName*. Vous pouvez également utiliser **Get-AzureRmDataFactory** pour obtenir un objet **DataFactory** et transmettre l’objet sans avoir à saisir *ResourceGroupName* et *DataFactoryName* chaque fois que vous exécutez une applet de commande. Exécutez la commande suivante pour affecter le résultat de l’applet de commande **Get-AzureRmDataFactory** à une variable **$df**.

    ```PowerShell
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Exécutez maintenant l’applet de commande **New-AzureRmDataFactoryLinkedService** qui crée le service **StorageLinkedService** lié.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    Si vous n’avez pas exécuté l’applet de commande **Get-AzureRmDataFactory** et affecté la sortie à la variable **$df**, vous devez spécifier des valeurs pour les paramètres *ResourceGroupName* et *DataFactoryName* comme suit.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Si vous fermez Azure PowerShell au milieu du didacticiel, vous devez exécuter l’applet de commande **Get-AzureRmDataFactory** au prochain démarrage d’Azure PowerShell pour terminer le didacticiel.

### <a name="create-azure-hdinsight-linked-service"></a>Créer le service lié Azure HDInsight
Dans cette étape, vous liez un cluster HDInsight à la demande à votre fabrique de données. Le cluster HDInsight est automatiquement créé lors de l’exécution, puis supprimé une fois le traitement effectué et au terme du délai d’inactivité spécifié. Vous pouvez utiliser votre propre cluster HDInsight au lieu d’utiliser un cluster HDInsight à la demande. Pour plus d’informations, consultez [Services de calcul liés](data-factory-compute-linked-services.md) .

1. Créez un fichier JSON nommé **HDInsightOnDemandLinkedService**.json dans le dossier **C:\ADFGetStarted** avec le contenu suivant.

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    Le tableau suivant décrit les propriétés JSON utilisées dans l'extrait de code :

   | Propriété | Description |
   |:--- |:--- |
   | ClusterSize |Spécifie la taille du cluster HDInsight. |
   | TimeToLive |Spécifie la durée d’inactivité du cluster HDInsight avant sa suppression. |
   | linkedServiceName |Spécifie le compte de stockage utilisé pour stocker les journaux générés par HDInsight |

    Notez les points suivants :

   * La fabrique de données crée pour vous un cluster HDInsight **Linux** avec le code JSON. Pour plus d’informations, voir [Service lié à la demande Azure HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .
   * Vous pouvez utiliser votre **propre cluster HDInsight** au lieu d’utiliser un cluster HDInsight à la demande. Pour plus d’informations, voir [Service lié Azure HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
   * Le cluster HDInsight crée un **conteneur par défaut** dans le stockage d’objets blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. Ce comportement est normal. Avec le service lié HDInsight disponible à la demande, un cluster HDInsight est créé dès qu’une tranche est traitée, à moins qu’il n’existe un cluster actif (**timeToLive**). Ce cluster est supprimé, une fois le traitement terminé.

       Comme un nombre croissant de tranches sont traitées, vous voyez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Le nom de ces conteneurs suit un modèle : « **nomdevotrefabriquededonnéesadf**-**nomduservicelié**-horodatage ». Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer des conteneurs dans votre stockage d’objets blob Azure.

     Pour plus d’informations, voir [Service lié à la demande Azure HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .
2. Exécutez l’applet de commande **New-AzureRmDataFactoryLinkedService** qui crée le service lié nommé HDInsightOnDemandLinkedService.
    
    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Créez les jeux de données
Dans cette étape, vous créez des jeux de données afin de représenter les données d’entrée et de sortie pour le traitement Hive. Ces jeux de données font référence au service **StorageLinkedService** que vous avez créé précédemment dans ce didacticiel. Le service lié pointe vers un compte de stockage Azure, et les jeux de données spécifient le conteneur, le dossier et le nom de fichier dans le stockage qui contient les données d’entrée et de sortie.

### <a name="create-input-dataset"></a>Créer le jeu de données d’entrée
1. Créez un fichier JSON nommé **InputTable.json** dans le dossier **C:\ADFGetStarted** avec le contenu suivant :

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Le code JSON définit un jeu de données nommé **AzureBlobInput**, qui représente les données d’entrée correspondant à une activité du pipeline. En outre, ce code spécifie que les données d’entrée figurent dans le conteneur d’objets blob **adfgetstarted** et dans le dossier **inputdata**.

    Le tableau suivant décrit les propriétés JSON utilisées dans l'extrait de code :

   | Propriété | Description |
   |:--- |:--- |
   | Type |La propriété type est définie sur AzureBlob, car les données se trouvent dans le stockage d’objets blob Azure. |
   | linkedServiceName |fait référence au service StorageLinkedService que vous avez créé précédemment. |
   | fileName |Cette propriété est facultative. Si vous omettez cette propriété, tous les fichiers spécifiés dans le paramètre folderPath sont récupérés. Dans le cas présent, seul le fichier input.log est traité. |
   | Type |Les fichiers journaux sont au format texte : nous utilisons donc TextFormat. |
   | columnDelimiter |Les colonnes des fichiers journaux sont délimitées par une virgule (,). |
   | frequency/interval |La fréquence est définie sur Mois et l’intervalle est 1, ce qui signifie que les segments d’entrée sont disponibles mensuellement. |
   | external |Cette propriété a la valeur true si les données d’entrée ne sont pas générées par le service Data Factory. |
2. Exécutez la commande suivante dans Azure PowerShell pour créer le jeu de données Data Factory :

    ```PowerShell
    New-AzureRmDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Créer un jeu de données de sortie
Vous allez maintenant créer le jeu de données de sortie pour représenter les données de sortie stockées dans le stockage d’objets blob Azure.

1. Créez un fichier JSON nommé **OutputTable.json** dans le dossier **C:\ADFGetStarted** avec le contenu suivant :

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Le code JSON définit un jeu de données nommé **AzureBlobOutput**, qui représente les données de sortie correspondant à une activité du pipeline. En outre, ce code spécifie que les résultats sont stockés dans le conteneur d’objets blob **adfgetstarted** et dans le dossier **partitioneddata**. La section **availability** spécifie que le jeu de données de sortie est produit tous les mois.
2. Exécutez la commande suivante dans Azure PowerShell pour créer le jeu de données Data Factory :

    ```PowerShell
    New-AzureRmDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>Création d’un pipeline
Dans cette étape, vous créez votre premier pipeline avec une activité **HDInsightHive** . La tranche d’entrée est disponible mensuellement (fréquence : Mois, intervalle : 1), la tranche de sortie est produite mensuellement et la propriété du planificateur pour l’activité est également définie sur Mensuellement. Les paramètres pour le jeu de données de sortie et le planificateur d’activité doivent correspondre. À ce stade, c'est le jeu de données de sortie qui pilote la planification : vous devez donc créer un jeu de données de sortie même si l’activité ne génère aucune sortie. Si l’activité ne prend aucune entrée, vous pouvez ignorer la création du jeu de données d’entrée. Les propriétés utilisées dans le code JSON suivant sont expliquées à la fin de cette section.

1. Créez un fichier JSON nommé MyFirstPipelinePSH.json dans le dossier C:\ADFGetStarted avec le contenu suivant :

   > [!IMPORTANT]
   > Dans le code JSON, remplacez **storageaccountname** par le nom de votre compte de stockage.
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```
    Dans l’extrait de code JSON, vous créez un pipeline qui se compose d’une seule activité utilisant Hive pour traiter des données sur un cluster HDInsight.

    Le fichier de script Hive, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par le service scriptLinkedService, appelé **StorageLinkedService**) et dans un dossier **script** du conteneur **adfgetstarted**.

    La section **defines** est utilisée pour spécifier les paramètres d’exécution passés au script Hive comme valeurs de configuration Hive (par exemple ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Les propriétés **start** et **end** du pipeline spécifient la période active du pipeline.

    Dans l’activité JSON, vous spécifiez que le script Hive s’exécute sur le calcul spécifié par le service **linkedServiceName** – **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Consultez « Pipeline JSON » dans [Pipelines et activités dans Azure Data Factory](data-factory-create-pipelines.md) pour plus d’informations sur les propriétés JSON utilisées dans l’exemple.

2. Vérifiez que le fichier **input.log** apparaît dans le dossier **adfgetstarted/inputdata** du Stockage Blob Azure, puis exécutez la commande suivante pour déployer le pipeline. Étant donné que les valeurs pour **start** et **end** sont définies sur des valeurs antérieures au moment actuel, et que **isPaused** est défini sur false, le pipeline (activité dans le pipeline) s’exécute immédiatement après le déploiement.

    ```PowerShell
    New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Félicitations, vous avez réussi à créer votre premier pipeline avec Azure PowerShell.

## <a name="monitor-pipeline"></a>Surveillance d’un pipeline
Au cours de cette étape, vous utilisez Azure PowerShell pour surveiller ce qui se passe dans une fabrique de données Azure.

1. Exécutez **Get-AzureRmDataFactory** et affectez le résultat à une variable **$df**.

    ```PowerShell
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. Exécutez **Get-AzureRmDataFactorySlice** pour obtenir des détails sur toutes les tranches de **EmpSQLTable**, qui correspond à la table de sortie du pipeline.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Notez que la valeur StartDateTime que vous spécifiez ici est identique à l’heure de début que vous avez spécifiée dans le pipeline de JSON. Voici l'exemple de sortie :

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Exécutez **Get-AzureRmDataFactoryRun** pour obtenir les détails des exécutions d’activité pour un segment spécifique.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Voici l'exemple de sortie : 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Vous pouvez continuer d’exécuter cette applet de commande jusqu’à ce que le segment apparaisse dans l’état **Prêt** ou **En échec**. Quand l’état du segment est Prêt, vérifiez la présence des données de sortie dans le dossier **partitioneddata** du conteneur **adfgetstarted** de votre stockage d’objets blob.  La création d’un cluster HDInsight à la demande prend généralement un certain temps.

    ![données de sortie](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> La création d’un cluster HDInsight à la demande prend généralement un certain temps (environ 20 minutes). Le pipeline devrait donc traiter la tranche en **30 minutes environ** .
>
> Le fichier d’entrée sera supprimé lorsque la tranche est traitée avec succès. Par conséquent, si vous souhaitez réexécuter la tranche ou refaire le didacticiel, chargez le fichier d’entrée (input.log) dans le dossier inputdata du conteneur adfgetstarted.
>
>

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous avez créé une fabrique de données Azure pour traiter des données en exécutant le script Hive sur un cluster Hadoop HDInsight. Vous avez effectué les étapes suivantes dans le portail Azure à l’aide de Data Factory Editor :

1. Création d’une **fabrique de données**Azure.
2. Création de deux **services liés**:
   1. **Azure Storage** pour lier à la fabrique de données votre stockage d’objets blob Azure contenant les fichiers d’entrée/sortie.
   2. **Azure HDInsight** à la demande pour lier à la fabrique de données un cluster Hadoop HDInsight à la demande. Azure Data Factory crée un cluster Hadoop HDInsight juste-à-temps pour traiter les données d’entrée et produire des données de sortie.
3. Création de deux **jeux de données**qui décrivent les données d’entrée et de sortie pour l’activité HDInsight Hive dans le pipeline.
4. Création d’un **pipeline** avec une activité **Hive HDInsight**.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une activité de transformation (Activité HDInsight) qui exécute un script Hive sur un cluster Azure HDInsight à la demande. Pour voir comment utiliser une activité de copie pour copier des données depuis un objet blob Azure vers Azure SQL, consultez le [Didacticiel : copie de données depuis un objet blob Azure vers Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Voir aussi
| Rubrique | Description |
|:--- |:--- |
| [Informations de référence sur les applets de commande de Data Factory](/powershell/module/azurerm.datafactories) |Consultez la documentation complète sur les applets de commande de Data Factory |
| [Pipelines](data-factory-create-pipelines.md) |Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory, et à les utiliser dans l’optique de créer des workflows pilotés par les données de bout en bout pour votre scénario ou votre entreprise. |
| [Groupes de données](data-factory-create-datasets.md) |Cet article vous aide à comprendre les jeux de données dans Azure Data Factory. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) |Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory. |
| [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion.](data-factory-monitor-manage-app.md) |Cet article décrit comment surveiller, gérer et déboguer les pipelines à l’aide de l’application de surveillance et gestion. |
