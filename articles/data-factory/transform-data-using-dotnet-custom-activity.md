---
title: "Utilisation des activités personnalisées dans un pipeline Azure Data Factory"
description: "Découvrez comment créer des activités personnalisées et les utiliser dans un pipeline Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 4b9714bc456ad28d9dd46742ca16f52e68c61399
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilisation des activités personnalisées dans un pipeline Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-use-custom-activities.md)
> * [Version 2 - Préversion](transform-data-using-dotnet-custom-activity.md)

Vous pouvez utiliser deux types d’activités dans un pipeline Azure Data Factory.

- Les [activités de déplacement de données](copy-activity-overview.md) permettent de transférer des données entre les [magasins de données source et récepteur pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
- Les [activités de transformation de données](transform-data.md) permettent de transformer des données à l’aide de services de calcul, comme Azure HDInsight, Azure Batch et Azure Machine Learning. 

Pour déplacer des données vers ou depuis un magasin de données que Data Factory ne prend pas en charge, ou pour transformer et traiter les données d’une manière qui n’est pas prise en charge par Data Factory, créez une **Activité personnalisée** avec votre propre logique de déplacement ou de transformation des données, et utilisez cette activité dans un pipeline. L’activité personnalisée exécute votre logique de code personnalisé sur un pool de machines virtuelles **Azure Batch**.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, consultez la section relative à [l’activité DotNet (personnalisée) dans Azure Data Factory version 1](v1/data-factory-use-custom-activities.md).
 

Consultez les articles suivants si vous ne connaissez pas le service Azure Batch :

* [Présentation de base d’Azure Batch](../batch/batch-technical-overview.md) pour une vue d’ensemble du service Azure Batch.
* Applet de commande [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) pour créer un compte Azure Batch (ou) [Portail Azure](../batch/batch-account-create-portal.md) pour créer le compte Azure Batch à l’aide du portail Azure. Pour obtenir des instructions détaillées sur l’utilisation de la cmdlet, voir [Utilisation de PowerShell pour gérer un compte Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) pour créer un pool Azure Batch.

## <a name="azure-batch-linked-service"></a>Service lié Azure Batch 
L’extrait de code JSON suivant définit un exemple de service lié Azure Batch. Pour plus de détails, consultez [Environnements de calcul pris en charge par Azure Data Factory](compute-linked-services.md).

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Pour approfondir votre connaissance du service lié Azure Batch, consultez l’article [Services liés de calcul](compute-linked-services.md). 

## <a name="custom-activity"></a>Activité personnalisée

L’extrait de code JSON suivant définit un pipeline avec une activité personnalisée simple. La définition de l’activité comporte une référence au service lié Azure Batch. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

Dans cet exemple, le fichier helloworld.exe est une application personnalisée qui est stockée dans le dossier customactv2/helloworld du compte Stockage Azure utilisé dans le resourceLinkedService. L’activité personnalisée soumet cette application personnalisée pour qu’elle soit exécutée sur Azure Batch. Vous pouvez remplacer la commande par n’importe quelle application par défaut pouvant être exécutée sur le système d’exploitation cible des nœuds du pool Azure Batch. 

Le tableau suivant indique les noms et les descriptions des propriétés qui sont spécifiques à cette activité. 

| Propriété              | Description                              | Obligatoire |
| :-------------------- | :--------------------------------------- | :------- |
| Nom                  | Nom de l’activité dans le pipeline     | OUI      |
| description           | Texte décrivant l’activité.  | Non        |
| Type                  | Pour une activité personnalisée, le type d’activité est **Custom**. | OUI      |
| linkedServiceName     | Service lié sur Azure Batch. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md).  | OUI      |
| command               | Commande de l’application personnalisée à exécuter. Si l’application est déjà disponible sur le nœud du pool Azure Batch, resourceLinkedService et folderPath peuvent être ignorés. Par exemple, vous pouvez spécifier la commande pour qu’elle soit `cmd /c dir`, ce qui est pris en charge en mode natif par le nœud du pool Windows Batch. | OUI      |
| resourceLinkedService | Le service lié Stockage Azure sur le compte de stockage où l’application personnalisée est stockée. | Non        |
| folderPath            | Chemin du dossier de l’application personnalisée et de toutes ses dépendances. | Non        |
| referenceObjects      | Tableau des services liés et des jeux de données existants. Les services liés et les jeux de données référencés sont passés à l’application personnalisée au format JSON, votre code personnalisé peut ainsi référencer des ressources de la fabrique de données. | Non        |
| extendedProperties    | Propriétés définies par l’utilisateur qui peuvent être passées à l’application personnalisée au format JSON, votre code personnalisé peut ainsi référencer des propriétés supplémentaires. | Non        |

## <a name="executing-commands"></a>Exécution de commandes

Vous pouvez exécuter directement une commande à l’aide d’une activité personnalisée. Dans l’exemple suivant, la commande « echo hello world » est exécutée sur les nœuds de pool Azure Batch cibles, et la sortie est envoyée vers stdout. 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>Passage des objets et des propriétés

Cet exemple montre comment vous pouvez utiliser les referenceObjects et les extendedProperties pour passer des objets Data Factory et des propriétés définies par l’utilisateur sur votre application personnalisée. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

Lorsque l’activité est exécutée, les éléments referenceObjects et extendedProperties sont stockés dans les fichiers suivants qui sont déployés vers le même dossier d’exécution de l’application SampleApp.exe : 

- activity.json

  Stocke extendedProperties et les propriétés de l’activité personnalisée. 

- linkedServices.json

  Stocke un tableau des services liés définis dans la propriété referenceObjects. 

- datasets.json

  Stocke un tableau de jeux de données défini dans la propriété referenceObjects. 

L’exemple de code suivant montre comment SampleApp.exe peut accéder aux informations nécessaires à partir de fichiers JSON : 

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Récupérer les sorties de l’exécution

  Vous pouvez démarrer une exécution de pipeline à l’aide de la commande PowerShell suivante : 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  Quand le pipeline s’exécute, vous pouvez vérifier la sortie de l’exécution à l’aide des commandes suivantes : 

  ```.powershell
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

  Les éléments **stdout** et **stderr** de votre application personnalisée sont enregistrés dans le conteneur **adfjobs** du service lié Stockage Azure que vous avez défini lors de la création du service lié Azure Batch à l’aide d’un GUID de la tâche. Vous pouvez obtenir le chemin détaillé à partir de la sortie de l’exécution de l’activité, comme indiqué dans l’extrait de code suivant : 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Si vous souhaitez consommer le contenu de stdout.txt dans des activités en aval, vous pouvez obtenir le chemin du fichier stdout.txt dans l’expression « @activity(’MyCustomActivity’).output.outputs [0] ». 

  > [!IMPORTANT]
  > - Les fichiers activity.json, linkedServices.json et datasets.json sont stockés dans le dossier d’exécution de la tâche de traitement par lots. Pour cet exemple, le chemin où sont stockés les fichiers activity.json, linkedServices.json et datasets.json est le suivant : https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/. Si nécessaire, vous devez les nettoyer séparément. 
  > - Pour que les services liés utilisent le runtime d’intégration auto-hébergé, les informations sensibles, telles que les clés ou les mots de passe, sont chiffrées par le runtime d’intégration auto-hébergé, afin de s’assurer que les informations d’identification restent dans l’environnement de réseau privé défini par le client. Certains champs sensibles peuvent manquer lorsqu’ils sont référencés par votre code d’application personnalisé de cette façon. Au besoin, utilisez SecureString dans extendedProperties au lieu d’utiliser une référence de service lié. 

## <a name="difference-between-custom-activity-in-azure-data-factory-version-2-and-custom-dotnet-activity-in-azure-data-factory-version-1"></a>Différence entre une activité personnalisée dans Azure Data Factory version 2 et une activité DotNet (personnalisée) dans Azure Data Factory version 1

  Dans la version 1, vous implémentez le code de l’activité DotNet (personnalisée) en créant un projet de bibliothèque de classes .Net avec une classe qui implémente la méthode Execute de l’interface IDotNetActivity. Les services liés, les jeux de données et les propriétés étendues dans la charge utile JSON d’activité DotNet (personnalisée) sont passés à la méthode d’exécution en tant qu’objets fortement typés. Pour en savoir plus, consultez la rubrique relative à [DotNet (personnalisé) dans la version 1](v1/data-factory-use-custom-activities.md). En raison de cette implémentation, votre code personnalisé doit être écrit dans le .Net Framework 4.5.2 et être exécuté sur des nœuds de pool Azure Batch basés sur Windows. 

  Dans une activité personnalisée Azure Data Factory V2, vous n’êtes pas obligé d’implémenter une interface .Net. Vous pouvez maintenant exécuter directement des commandes et des scripts, et exécuter votre propre code compilé comme exécutable. Pour cela, vous devez spécifier la propriété Command avec la propriété folderPath. L’activité personnalisée charge le fichier exécutable et les dépendances dans folderpath et exécute la commande pour vous. 

  Les services liés, les jeux de données (définis dans referenceObjects) et les propriétés étendues définis dans la charge utile JSON de l’activité personnalisée sont accessibles par votre fichier exécutable en tant que fichiers JSON. Vous pouvez accéder aux propriétés requises à l’aide du sérialiseur JSON comme indiqué dans l’exemple de code SampleApp.exe précédent. 

  Avec les modifications introduites dans l’activité personnalisée Azure Data Factory V2, vous êtes libre d’écrire votre logique de code personnalisée dans le langage de votre choix et de l’exécuter sur les systèmes d’exploitation Windows et Linux pris en charge par Azure Batch. 

  Le tableau suivant décrit les différences qui existent entre l’activité personnalisée Data Factory version 2 et l’activité DotNet (personnalisée) Data Factory version 1 : 


|Différences      |Activité personnalisée de la version 2      | Activité DotNet (personnalisée) de la version 1      |
| ---- | ---- | ---- |
|Mode de définition de la logique personnalisée      |En exécutant n’importe quel fichier exécutable (existant ou implémentant votre propre fichier exécutable)      |En implémentant une DLL .NET      |
|Environnement d’exécution de la logique personnalisée      |Windows ou Linux      |Windows (.NET Framework 4.5.2)      |
|Exécution des scripts      |Prise en charge de l’exécution directe des scripts (par exemple « cmd /c echo hello world » sur une machine virtuelle Windows)      |Nécessite une implémentation dans la DLL .NET      |
|Jeu de données nécessaire      |Facultatif      |Nécessaire pour chaîner des activités et passer des informations      |
|Passer des informations entre l’activité et la logique personnalisée      |Via ReferenceObjects (LinkedServices et Datasets) et ExtendedProperties (propriétés personnalisées)      |et via ExtendedProperties (propriétés personnalisées), Input et Output Datasets      |
|Récupération des informations dans la logique personnalisée      |Analyse des fichiers activity.json, linkedServices.json et datasets.json stockés dans le même dossier que l’exécutable      |Via .NET SDK (.NET Framework 4.5.2)      |
|Journalisation      |Écrit directement dans STDOUT      |Implémentation de l’enregistreur d’événements dans la DLL .NET      |


  Si vous avez du code .Net écrit pour une activité DotNet (personnalisée) de la version 1, vous devez le modifier pour qu’il fonctionne avec une activité personnalisée de la version 2, en respectant les instructions générales suivantes :  

   - Changez le projet pour qu’il s’agisse non plus d’une bibliothèque de classes .Net, mais d’une application console. 
   - Démarrez votre application avec la méthode Main. La méthode Execute de l’interface IDotNetActivity n’est plus nécessaire. 
   - Lisez et analysez les services liés, les jeux de données et l’activité avec le sérialiseur JSON plutôt qu’en tant qu’objets fortement typés, et passez les valeurs des propriétés requises à votre logique de code personnalisé principale. Voir l’exemple de code SampleApp.exe plus haut. 
   - L’objet d’enregistreur d’événements n’est plus pris en charge. Les sorties de fichiers exécutables peuvent être imprimées dans la console et enregistrées dans stdout.txt. 
   - Le package NuGet Microsoft.Azure.Management.DataFactories n’est plus nécessaire. 
   - Compilez votre code, chargez le fichier exécutable et les dépendances dans le Stockage Azure et définissez le chemin dans la propriété folderPath. 

Pour obtenir un exemple complet de la façon dont l’exemple de DLL et de pipeline de bout en bout décrit dans le document Data Factory version 1 ([Utilisation des activités personnalisées dans un pipeline Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities)) peut être réécrit dans le style de l’activité personnalisée Data Factory V2, reportez-vous à [l’exemple d’activité personnalisée Data Factory version 2](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

## <a name="auto-scaling-of-azure-batch"></a>Mise à l’échelle automatique d’Azure Batch
Vous pouvez aussi créer un pool Azure Batch avec la fonctionnalité **autoscale** . Par exemple, vous pouvez créer un pool Azure Batch avec 0 machine virtuelle dédiée et une formule de mise à l’échelle automatique en fonction du nombre de tâches en attente. 

Cet exemple de formule permet d’obtenir le comportement suivant : quand le pool est créé, il commence avec 1 machine virtuelle. La métrique $PendingTasks définit le nombre de tâches dans l’état En cours d’exécution + Actif (en file d’attente).  Cette formule recherche le nombre moyen de tâches en attente au cours des 180 dernières secondes et définit TargetDedicated en conséquence. Elle garantit que TargetDedicated ne va jamais au-delà de 25 machines virtuelles. Par conséquent, à mesure que de nouvelles tâches sont envoyées, le pool s’accroît automatiquement et, au fil de la réalisation des tâches, les machines virtuelles se libèrent une à une et la mise à l’échelle automatique réduit ces machines virtuelles. Vous pouvez ajuster startingNumberOfVMs et maxNumberofVMs selon vos besoins.

Formule de mise à l’échelle automatique :

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Pour plus d’informations, consultez [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](../batch/batch-automatic-scaling.md) .

Si le pool utilise la valeur par défaut du paramètre [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), le service Batch peut mettre 15 à 30 minutes à préparer la machine virtuelle avant d’exécuter l’activité personnalisée.  Si le pool utilise une autre valeur pour autoScaleEvaluationInterval, le service Batch peut prendre la durée d’autoScaleEvaluationInterval + 10 minutes.


## <a name="next-steps"></a>étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens : 

* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité Hive](transform-data-using-hadoop-hive.md)
* [Activité pig](transform-data-using-hadoop-pig.md)
* [Activité MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Activité de diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité d’exécution du lot Machine Learning](transform-data-using-machine-learning.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
