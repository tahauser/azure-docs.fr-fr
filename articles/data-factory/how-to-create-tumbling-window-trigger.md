---
title: "Guide pratique pour créer des déclencheurs de fenêtre bascule dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment créer un déclencheur dans Azure Data Factory qui exécute un pipeline sur une fenêtre bascule."
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
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Guide pratique pour créer un déclencheur qui exécute un pipeline sur une fenêtre bascule
Cet article décrit les étapes permettant de créer, de démarrer et d’effectuer le monitoring d’un déclencheur de fenêtre bascule. Vous trouverez des informations générales sur les déclencheurs et les types que nous prenons en charge sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Les déclencheurs de fenêtre bascule sont un type de déclencheur qui s’active à un intervalle de temps périodique à partir d’une heure de début spécifiée, tout en conservant son état. Les fenêtres bascule sont une série d’intervalles de temps contigus fixes, qui ne se chevauchent pas. Un déclencheur de fenêtre bascule a une relation 1:1 avec un pipeline et ne peut référencer qu’un pipeline unique.

## <a name="tumbling-window-trigger-type-properties"></a>Propriétés de type de déclencheur de fenêtre bascule
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

Le tableau suivant présente les principaux objets liés à la périodicité et à la planification d’un déclencheur de fenêtre bascule.

| **Nom JSON** | **Description** | **Valeurs autorisées** | **Obligatoire** |
|:--- |:--- |:--- |:--- |
| **type** | Type du déclencheur. Celui-ci est fixe et défini sur « TumblingWindowTrigger ». | Chaîne | Oui |
| **runtimeState** | <readOnly> Valeurs possibles : Démarré, Arrêté, Désactivé | Chaîne | Oui, lecture seule |
| **frequency** |La chaîne *frequency* représente l’unité de fréquence selon laquelle le déclencheur se répète. Les valeurs prises en charge sont « minute » et « heure ». Si l’heure de début comporte des parties de date qui sont plus précises que la fréquence, elles seront prises en considération pour calculer les limites de la fenêtre. Par exemple : si la fréquence est horaire et si l’heure de début est 2016-04-01T10:10:10Z, la première fenêtre est (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z.)  | Chaîne. Types pris en charge « minute », « heure » | Oui |
| **interval** |La valeur *interval* est un entier positif et indique l’intervalle de la valeur *frequency* qui détermine la fréquence d’exécution du déclencheur. Par exemple, si *l’intervalle* est défini sur 3 et *la fréquence* est définie sur « heure », le déclencheur se répète toutes les 3 heures. | Entier  | Oui |
| **startTime**|*startTime* est une Date-Heure. *startTime* est la première occurrence et peut se situer dans le passé. Le premier intervalle de déclencheur sera (startTime ou heure de début, startTime ou heure de début + interval ou intervalle). | Datetime | Oui |
| **endTime**|*endTime* est une valeur Date-Heure. *endTime* est la dernière occurrence et peut se situer dans le passé. | Datetime | Oui |
| **delay** | Spécifie le délai avant le début du traitement des données de la fenêtre. L’exécution du pipeline est démarrée après l’heure d’exécution prévue + délai. Le délai définit la durée d’attente du déclencheur après l’heure d’échéance avant de déclencher une nouvelle exécution. Il ne modifie l’heure de début de la fenêtre. | Timespan (ou Intervalle de temps) (exemple : 00:10:00, implique un délai de 10 minutes) |  Non. La valeur par défaut est « 00:00:00 » |
| **max concurrency** | Nombre d’exécutions simultanées du déclencheur qui sont déclenchées pour des fenêtres qui sont prêtes. Exemple : si nous essayons de renvoyer toutes les heures d’hier, cette valeur serait égale à 24 fenêtres. Si concurrency (accès concurrentiel) = 10, les événements du déclencheur sont déclenchés uniquement pour les 10 premières fenêtres (00:00-01:00 - 09:00-10:00). Une fois que les 10 premières exécutions déclenchées du pipeline sont terminées, les exécutions du déclencheur sont déclenchées pour les 10 suivantes (10:00-11:00 - 19:00-20:00). Pour poursuivre avec l’exemple d’accès concurrentiel = 10, s’il y a 10 fenêtres prêtes, il y aura 10 exécutions du pipeline. S’il n’y a que 1 fenêtre prête, il n’y aura que 1 exécution du pipeline. | Entier  | Oui. Valeurs possibles 1-50 |
| **retryPolicy: Count** | Le nombre de nouvelles tentatives avant que l’exécution du pipeline ne soit marquée comme « Failed » (Échec)  | Entier  |  Non. La valeur par défaut est de 0 tentative |
| **retryPolicy: intervalInSeconds** | Délai en secondes entre chaque nouvelle tentative | Entier  |  Non. La valeur par défaut est de 30 secondes |

### <a name="using-system-variables-windowstart-and-windowend"></a>Utilisation de variables système : WindowStart et WindowEnd

Si vous souhaitez utiliser WindowStart et WindowEnd du déclencheur de fenêtre bascule dans votre définition de **pipeline** (par exemple, dans le cadre d’une requête), vous devez transmettre les variables en tant que paramètres à votre pipeline dans la définition du **déclencheur**, comme suit :
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

Puis, dans la définition du pipeline, pour utiliser les valeurs WindowStart et WindowEnd, utilisez vos paramètres conformément à « MyWindowStart » et « MyWindowEnd »

### <a name="notes-on-backfill"></a>Remarques sur le renvoi
Lorsqu’il y a plusieurs fenêtres pour exécution (surtout dans un scénario de renvoi), l’ordre d’exécution des fenêtres est déterministe et va de l’intervalle le plus ancien au plus récent. Il n’existe aucun moyen de modifier ce comportement pour le moment.

### <a name="updating-an-existing-triggerresource"></a>Mise à jour d’une valeur TriggerResource existante
* Si la fréquence (ou taille de fenêtre) du déclencheur est modifiée, l’état des fenêtres déjà traitées ne sera *pas* réinitialisé. Le déclencheur continuera de se déclencher pour les fenêtres à partir de la dernière exécutée avec la nouvelle taille de fenêtre.
* Si l’heure de fin du déclencheur est modifiée (ajoutée ou mise à jour), l’état des fenêtres déjà traitées ne sera *pas* réinitialisé. Le déclencheur respectera simplement la nouvelle heure de fin. Si l’heure de fin est antérieure aux fenêtres déjà exécutées, le déclencheur s’arrête. Sinon, il s’arrête lorsque la nouvelle heure de fin est atteinte.

## <a name="sample-using-azure-powershell"></a>Exemple avec Azure PowerShell
Cette section montre comment utiliser Azure PowerShell pour créer, démarrer et effectuer le monitoring d’un déclencheur.

1. Créez un fichier JSON nommé MyTrigger.json dans le dossier C:\ADFv2QuickStartPSH\ avec le contenu suivant :

   > [!IMPORTANT]
   > Définissez **startTime** sur l’heure UTC actuelle et **endTime** sur une heure après l’heure UTC actuelle avant d’enregistrer le fichier JSON.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  
2. Créez un déclencheur avec la cmdlet **Set-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

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

## <a name="next-steps"></a>étapes suivantes
Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#triggers).
