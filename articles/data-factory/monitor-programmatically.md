---
title: "Surveiller par programmation une fabrique de données Azure | Microsoft Docs"
description: "Découvrez comment surveiller un pipeline dans une fabrique de données à l’aide de différents kits de développement logiciels (SDK)."
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 95934de9b84270ff112489a7b67f622dad609970
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Surveiller par programmation une fabrique de données Azure
Cet article explique comment surveiller un pipeline dans une fabrique de données à l’aide de différents kits de développement logiciels (SDK). 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la page [Surveiller et gérer les pipelines dans Data Factory version 1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="net"></a>.NET
Pour obtenir une description complète de la création et de la surveillance d’un pipeline à l’aide du kit de développement logiciel .NET, consultez [Créer une fabrique de données et un pipeline avec .NET](quickstart-create-data-factory-dot-net.md).

1. Ajoutez le code suivant afin de vérifier en permanence l’état de l’exécution du pipeline jusqu’à la fin de la copie des données.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Ajoutez le code suivant qui récupère les détails de l’exécution de l’activité de copie, par exemple la taille des données lues/écrites.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Pour une documentation complète sur le SDK .NET, consultez la [référence au SDK .NET de Data Factory](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Pour obtenir une description complète de la création et de la surveillance d’un pipeline à l’aide du kit de développement logiciel Python, consultez [Créer une fabrique de données et un pipeline à l’aide de Python](quickstart-create-data-factory-python.md).

Pour surveiller l’exécution du pipeline, ajoutez le code suivant :

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Pour une documentation complète sur le SDK Python, consultez la [référence au SDK Python de Data Factory](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>de l’API REST
Pour obtenir une description complète de la création et de la surveillance d’un pipeline à l’aide d’une API REST, consultez [Créer une fabrique de données Azure et un pipeline à l’aide de l’API REST](quickstart-create-data-factory-rest-api.md).
 
1. Exécutez le script suivant afin de vérifier en permanence l’état de l’exécution du pipeline jusqu’à la fin de la copie des données.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Exécutez le script suivant pour récupérer les détails de l’exécution de l’activité de copie, par exemple la taille des données lues/écrites.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Pour obtenir une documentation complète sur les API REST, consultez la [référence à l’API REST Data Factory](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Pour obtenir une description complète de la création et de la surveillance d’un pipeline à l’aide de PowerShell, consultez [Créer une fabrique de données et un pipeline à l’aide de PowerShell](quickstart-create-data-factory-powershell.md).

1. Exécutez le script suivant afin de vérifier en permanence l’état de l’exécution du pipeline jusqu’à la fin de la copie des données.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Exécutez le script suivant pour récupérer les détails de l’exécution de l’activité de copie, par exemple la taille des données lues/écrites.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Pour obtenir une documentation complète sur les applets de commande PowerShell, consultez la [référence aux applets de commande PowerShell de Data Factory](/powershell/module/azurerm.datafactoryv2/?view=azurermps-4.4.1).

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article [Surveiller les pipelines à l’aide d’Azure Monitor](monitor-using-azure-monitor.md) pour découvrir comment utiliser Azure Monitor dans le cadre de la surveillance des pipelines Data Factory. 

