---
title: Transformer des données à l’aide de Hive dans un réseau virtuel Azure | Microsoft Docs
description: Ce didacticiel fournit des instructions détaillées de transformation des données à l’aide de l’activité Hive dans Azure Data Factory.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shengc
ms.openlocfilehash: fda9cab53290d7af69e243ce47df702b25d1de67
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformer des données dans un réseau virtuel Azure à l’aide de l’activité Hive dans Azure Data Factory
Dans ce didacticiel, vous utilisez Azure PowerShell pour créer un pipeline Azure Data Factory qui transforme des données à l’aide d’une activité Hive sur un cluster HDInsight qui se trouve dans un réseau virtuel Azure (VNet). Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données. 
> * Créer et configurer un runtime d’intégration autohébergé.
> * Créer et déployer des services liés.
> * Créer et déployer un pipeline qui contient une activité Hive.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline. 
> * Vérifier la sortie. 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- **Compte Stockage Azure**. Vous créez un script Hive et le téléchargez vers le stockage Azure. La sortie du script Hive est stockée dans ce compte de stockage. Dans cet exemple, le cluster HDInsight utilise ce compte de stockage Azure en tant que stockage principal. 
- **Réseau virtuel Azure.** Si vous ne disposez pas d’un réseau virtuel Azure, créez-le en suivant [ces instructions](../virtual-network/quick-create-portal.md). Dans cet exemple, HDInsight est dans un réseau virtuel Azure. Voici un exemple de configuration du réseau virtuel Azure. 

    ![Création d’un réseau virtuel](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **Cluster HDInsight.** Créez un cluster HDInsight et joignez-le au réseau virtuel que vous avez créé à l’étape précédente en suivant cet article : [Étendre HDInsight à l’aide d’un réseau virtuel Azure](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Voici un exemple de configuration de HDInsight dans un réseau virtuel. 

    ![HDInsight dans un réseau virtuel](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **Azure PowerShell**. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Téléchargez le script Hive sur votre compte de stockage Blob

1. Créez un fichier SQL Hive nommé **hivescript.hql** avec le contenu suivant :

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Dans votre stockage Blob Azure, créez un conteneur nommé **adftutorial** s’il n’existe pas.
3. Créez un dossier nommé **hivescripts**.
4. Téléchargez le fichier **hivescript.hql** dans le sous-dossier **hivescripts**.

 

## <a name="create-a-data-factory"></a>Créer une fabrique de données


1. Définissez le nom du groupe de ressources. Vous créez un groupe de ressources dans le cadre de ce didacticiel. Vous pouvez toutefois utiliser un groupe de ressources existant si vous le souhaitez. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```
2. Spécifiez le nom de la fabrique de données. Il doit être globalement unique.

    ```powershell
    $dataFactoryName = "MyDataFactory09142017"
    ```
3. Spécifiez le nom du pipeline. 

    ```powershell
    $pipelineName = "MyHivePipeline" # 
    ```
4. Spécifiez le nom du runtime d’intégration autohébergé. Vous avez besoin d’un runtime d’intégration autohébergé lorsque Data Factory doit accéder à des ressources (comme Azure SQL Database) dans un réseau virtuel. 
    ```powershell
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" 
    ```
2. Lancez **PowerShell**. Gardez Azure PowerShell ouvert jusqu’à la fin de ce guide de démarrage rapide. Si vous fermez puis rouvrez Azure PowerShell, vous devez réexécuter ces commandes. À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

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
3. Créez le groupe de ressources ADFTutorialResourceGroup s’il n’existe pas déjà dans votre abonnement. 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Créez la fabrique de données. 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Exécutez la commande suivante pour afficher la sortie : 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>Créer un runtime d’intégration autohébergé
Dans cette section, vous créez un runtime d’intégration autohébergé et l’associez à une machine virtuelle Azure dans le même réseau virtuel Azure que celui où se trouve votre cluster HDInsight.

1. Créez le runtime d’intégration autohébergé. Utilisez un nom unique au cas où il existe déjà un autre runtime d’intégration portant le même nom.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    Cette commande crée une inscription logique du runtime d’intégration autohébergé. 
2. Utilisez PowerShell pour récupérer les clés d’authentification pour inscrire le runtime d’intégration autohébergé. Copiez l’une des clés pour inscrire le runtime d’intégration auto-hébergé.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Voici l'exemple de sortie : 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    Notez la valeur de **AuthKey1** sans guillemets. 
3. Créez une machine virtuelle Azure et joignez-la au réseau virtuel qui contient votre cluster HDInsight. Pour plus d’informations, reportez-vous à [Créer des machines virtuelles](../virtual-network/quick-create-portal.md#create-virtual-machines). Joignez-les à un réseau virtuel Azure. 
4. Sur la machine virtuelle Azure, téléchargez le [runtime d’intégration autohébergé](https://www.microsoft.com/download/details.aspx?id=39717). Utilisez la clé d’authentification obtenue à l’étape précédente pour inscrire manuellement le runtime d’intégration autohébergé. 

   ![Inscrire le runtime d’intégration](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   Le message suivant s’affiche lorsque le runtime d’intégration autohébergé est inscrit correctement : ![Le nœud Runtime d'intégration (autohébergé) a été inscrit](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png)

   La page suivante apparaît lorsque le nœud est connecté au service cloud : ![Le nœud est connecté au service cloud.](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png)

## <a name="author-linked-services"></a>Créer des services liés

Cette section explique comment créer et déployer deux services liés :
- Un service lié Azure Storage qui relie un compte de stockage Azure à la fabrique de données. Il s’agit du stockage principal utilisé par votre cluster HDInsight. Dans ce cas, nous utilisons également ce compte de stockage Azure pour conserver le script Hive et la sortie du script.
- Un service lié HDInsight. Azure Data Factory soumet le script Hive à ce cluster HDInsight en vue de son exécution.

### <a name="azure-storage-linked-service"></a>Service lié Stockage Azure

Créez un fichier JSON à l’aide de votre éditeur favori, copiez la définition JSON suivante d’un service lié au stockage Azure, puis enregistrez le fichier sous **MyStorageLinkedService.json**.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

Remplacez **&lt;accountname&gt; et &lt;accountkey&gt;** par le nom et la clé de votre compte de stockage Azure.

### <a name="hdinsight-linked-service"></a>Service lié HDInsight

Créez un fichier JSON à l’aide de votre éditeur favori, copiez la définition JSON suivante d’un service lié HDInsight Azure et enregistrez le fichier sous **MyHDInsightLinkedService.json**.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

Mettez à jour les valeurs des propriétés suivantes dans la définition de service lié :

- **userName**. Nom de l’utilisateur de la connexion de cluster que vous avez spécifié lors de la création du cluster. 
- **password**. Mot de passe de l’utilisateur.
- **clusterUri**. Spécifiez l’URL de votre cluster HDInsight au format suivant : https://<clustername>.azurehdinsight.net.  Cet article part du principe que vous avez accès au cluster via Internet. Par exemple, vous pouvez vous connecter au cluster à l’adresse `https://clustername.azurehdinsight.net`. Cette adresse utilise la passerelle publique qui n’est pas disponible si vous avez utilisé des groupes de sécurité réseau ou des itinéraires définis par l’utilisateur pour restreindre l’accès à partir d’Internet. Pour que la fabrique de données envoie des travaux aux clusters HDInsight sur le réseau virtuel Azure, votre réseau virtuel Azure doit être configuré de telle sorte que l’URL puisse être résolue sur l’adresse IP privée de la passerelle utilisée par HDInsight.

  1. À partir du portail Azure, ouvrez le réseau virtuel dans lequel HDInsight se trouve. Ouvrez l’interface réseau dont le nom commence par `nic-gateway-0`. Notez son adresse IP privée. Par exemple, 10.6.0.15. 
  2. Si votre réseau virtuel Azure contient un serveur DNS, mettez à jour l’enregistrement DNS pour que l’URL du cluster HDInsight `https://<clustername>.azurehdinsight.net` puisse être résolu en `10.6.0.15`. Il s’agit de l’approche recommandée. Si vous n’avez pas de serveur DNS dans votre réseau virtuel Azure, vous pouvez utiliser une solution de contournement temporaire en modifiant le fichier hosts (C:\Windows\System32\drivers\etc) de toutes les machines virtuelles inscrites en tant que nœuds runtime d’intégration autohébergés en ajoutant une entrée semblable à la suivante : 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-linked-services"></a>Créez des services liés
Dans PowerShell, basculez vers le dossier dans lequel vous avez créé des fichiers JSON et exécutez la commande suivante pour déployer les services liés : 

1. Dans PowerShell, basculez vers le dossier dans lequel vous avez créé des fichiers JSON.
2. Exécutez la commande suivante pour créer un service lié Stockage Azure. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
3. Exécutez la commande suivante pour créer un service lié Azure HDInsight. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDInsightLinkedService" -File "MyHDInsightLinkedService.json"
    ```

## <a name="author-a-pipeline"></a>Créer un pipeline
Au cours de cette étape, vous allez créer un pipeline avec une activité Hive. L’activité exécute le script Hive en vue de renvoyer des données à partir d’un exemple de table et de les enregistrer sur un chemin d’accès que vous avez défini. Créez un fichier JSON dans votre éditeur favori, copiez la définition JSON suivante d’une définition de pipeline et enregistrez-la sous **MyHivePipeline.json**.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}

```

Notez les points suivants :

- **scriptPath** pointe vers le chemin d’accès au script Hive sur le compte de stockage Azure que vous avez utilisé pour MyStorageLinkedService. Le chemin respecte la casse.
- **Output** est un argument utilisé dans le script Hive. Utilisez le format `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` pour le faire pointer vers un dossier existant de votre stockage Azure. Le chemin respecte la casse. 

Basculez sur le dossier dans lequel vous avez créé des fichiers JSON et exécutez la commande suivante pour déployer le pipeline : 


```powershell
Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>Lancer le pipeline 

1. Démarrez une exécution de pipeline. Cette opération capture également l’ID d’exécution du pipeline pour une surveillance ultérieure.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. Exécutez le script suivant pour vérifier en permanence l’état de l’exécution du pipeline jusqu’à la fin.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }
    
    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

   Voici la sortie de l’exemple d’exécution :

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Recherchez dans le dossier `outputfolder` le fichier créé en tant que résultat de la requête Hive. Le résultat doit ressembler à l’exemple suivant : 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données. 
> * Créer et configurer un runtime d’intégration autohébergé.
> * Créer et déployer des services liés.
> * Créer et déployer un pipeline qui contient une activité Hive.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline. 
> * Vérifier la sortie. 

Passez au didacticiel suivant pour en savoir plus sur la transformation des données à l’aide d’un cluster Spark sur Azure :

> [!div class="nextstepaction"]
>[Création de branche et chaînage du flux de contrôle Data Factory](tutorial-control-flow.md)



