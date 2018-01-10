---
title: "Guide pratique pour créer des déclencheurs dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment créer un déclencheur dans Azure Data Factory qui exécute un pipeline selon une planification."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Guide pratique pour créer un déclencheur qui exécute un pipeline selon une planification
Cet article décrit les étapes permettant de créer, de démarrer et d’effectuer le monitoring d’un déclencheur. Vous trouverez des informations conceptuelles sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Cette section montre comment utiliser Azure PowerShell pour créer, démarrer et effectuer le monitoring d’un déclencheur. Si vous souhaitez voir cet exemple en fonctionnement, commencez par lire le [guide de démarrage rapide : créer une fabrique de données avec Azure PowerShell](quickstart-create-data-factory-powershell.md). Ensuite, ajoutez le code suivant à la méthode main : il crée et lance un déclencheur de planification qui s’exécute toutes les 15 minutes. Le déclencheur est associé à un pipeline (**Adfv2QuickStartPipeline**), créé dans le cadre du guide de démarrage rapide.

1. Créez un fichier JSON nommé MyTrigger.json dans le dossier C:\ADFv2QuickStartPSH\ avec le contenu suivant : 

    > [!IMPORTANT]
    > Définissez **startTime** sur l’heure UTC actuelle et **endTime** sur une heure après l’heure UTC actuelle avant d’enregistrer le fichier JSON.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```
    
    Dans l’extrait de code JSON : 
    - Le **type** du déclencheur est défini sur **ScheduleTrigger**. 
    - **frequency** est défini sur **Minute** et **interval** sur **15**. Par conséquent, le déclencheur exécute le pipeline toutes les 15 minutes entre l’heure de début et l’heure de fin. 
    - **endTime** correspond à une heure après **startTime** ; ainsi, le déclencheur exécute le pipeline 15 minutes, 30 minutes et 45 minutes après startTime. N’oubliez pas de définir startTime sur l’heure UTC actuelle et endTime sur une heure après startTime.  
    - Le déclencheur est associé au pipeline **Adfv2QuickStartPipeline**. Pour associer plusieurs pipelines à un déclencheur, ajoutez d’autres sections **pipelineReference**. 
    - Le pipeline du guide de démarrage rapide prend deux **paramètres**. Par conséquent, les valeurs de ces paramètres sont transmises à partir du déclencheur. 
2. Créez un déclencheur avec la cmdlet **Set-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Vérifiez que l’état du déclencheur est **Arrêté** avec la cmdlet **Get-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
4. Démarrez le déclencheur avec la cmdlet **Start-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
5. Vérifiez que l’état du déclencheur est **Démarré** avec la cmdlet **Get-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
6.  Récupérez les exécutions du déclencheur en utilisant PowerShell avec la cmdlet **Get-AzureRmDataFactoryV2TriggerRun**. Pour obtenir les détails des exécutions du déclencheur, exécutez régulièrement la commande suivante : modifiez les valeurs **TriggerRunStartedAfter** et **TriggerRunStartedBefore** afin qu’elles correspondent à celles de la définition du déclencheur. 

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    Pour effectuer le monitoring des exécutions du déclencheur/du pipeline sur le Portail Azure, consultez la page [Effectuer le monitoring des exécutions du pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="use-net-sdk"></a>Utilisation du Kit de développement logiciel (SDK) .NET
Cette section montre comment utiliser le Kit SDK .NET pour créer, démarrer et effectuer le monitoring d’un déclencheur. Si vous souhaitez voir ce code en fonctionnement, commencez par lire le [guide de démarrage rapide : créer une fabrique de données avec le Kit SDK .NET](quickstart-create-data-factory-dot-net.md). Ensuite, ajoutez le code suivant à la méthode main : il crée et lance un déclencheur de planification qui s’exécute toutes les 15 minutes. Le déclencheur est associé à un pipeline (**Adfv2QuickStartPipeline**), créé dans le cadre du guide de démarrage rapide. 

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method. 
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Pour effectuer le monitoring d’une exécution du déclencheur, ajoutez le code suivant avant la dernière instruction `Console.WriteLine` : 

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Pour effectuer le monitoring des exécutions du déclencheur/du pipeline sur le Portail Azure, consultez la page [Effectuer le monitoring des exécutions du pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="use-python-sdk"></a>Utilisation du Kit de développement logiciel (SDK) Python
Cette section montre comment utiliser le Kit SDK Python pour créer, démarrer et effectuer le monitoring d’un déclencheur. Si vous souhaitez voir ce code en fonctionnement, commencez par lire le [guide de démarrage rapide : créer une fabrique de données avec le Kit SDK Python](quickstart-create-data-factory-python.md). Ensuite, ajoutez le bloc de code suivant après le bloc de code « effectuer le monitoring de l’exécution du déclencheur » dans le script Python. Ce code crée un déclencheur de planification qui s’exécute toutes les 15 minutes entre l’heure de début et l’heure de fin spécifiées. Définissez start_time sur l’heure UTC actuelle et end_time sur une heure après l’heure UTC actuelle. 

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC') 
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Pour effectuer le monitoring des exécutions du déclencheur/du pipeline sur le Portail Azure, consultez la page [Effectuer le monitoring des exécutions du pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="use-resource-manager-template"></a>Utilisation d’un modèle Resource Manager
Vous pouvez utiliser un modèle Azure Resource Manager pour créer un déclencheur. Vous trouverez des instructions pas à pas sur la page [Créer une fabrique de données Azure avec un modèle Resource Manager](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Transmettre l’heure de début du déclencheur à un pipeline
Dans la version 1, Azure Data Factory prenait en charge la lecture et l’écriture de données partitionnées à l’aide des variables système SliceStart/SliceEnd/WindowStart/WindowEnd. Dans la version 2, ce comportement est obtenu à l’aide d’un paramètre de pipeline ayant comme valeur une heure de début ou une heure planifiée de déclencheur. Dans l’exemple suivant, l’heure planifiée du déclencheur est transmise comme valeur au paramètre de pipeline scheduledRunTime. 

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
Pour plus d’informations, consultez la page [Guide pratique pour lire ou écrire des données partitionnées](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Étapes suivantes
Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#triggers).