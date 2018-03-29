---
title: Créer des déclencheurs de fenêtre bascule dans Azure Data Factory | Microsoft Docs
description: Découvrez comment créer un déclencheur dans Azure Data Factory qui exécute un pipeline sur une fenêtre bascule.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: 312072a5de21ff1c6b602fed93b77c564b15a9f1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Créer un déclencheur qui exécute un pipeline sur une fenêtre bascule
Cet article décrit les étapes permettant de créer, de démarrer et d’effectuer le monitoring d’un déclencheur de fenêtre bascule. Pour obtenir des informations générales sur les déclencheurs et les types pris en charge, consultez [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Cet article s’applique à Azure Data Factory version 2, actuellement en préversion. Si vous utilisez Azure Data Factory version 1, qui est généralement disponible, consultez [Bien démarrer avec Azure Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Les déclencheurs de fenêtre bascule sont un type de déclencheur qui s’active à un intervalle de temps périodique à partir d’une heure de début spécifiée, tout en conservant son état. Les fenêtres bascule sont une série d’intervalles de temps contigus fixes, qui ne se chevauchent pas. Un déclencheur de fenêtre bascule a une relation un à un avec un pipeline et ne peut référencer qu’un seul pipeline.

## <a name="tumbling-window-trigger-type-properties"></a>Propriétés de type de déclencheur de fenêtre bascule
Une fenêtre bascule a les propriétés de type de déclencheur suivantes :

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

Le tableau suivant présente les principaux éléments JSON liés à la périodicité et à la planification d’un déclencheur de fenêtre bascule :

| Élément JSON | Description | type | Valeurs autorisées | Obligatoire |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Type du déclencheur. Le type est la valeur fixe « TumblingWindowTrigger ». | Chaîne | « TumblingWindowTrigger » | OUI |
| **runtimeState** | État actuel du runtime du déclencheur.<br/>**Remarque**: Cet élément est \<readOnly>. | Chaîne | « Started », « Stopped », « Disabled » | OUI |
| **frequency** | Chaîne qui représente l’unité de fréquence (minutes ou heures) à laquelle le déclencheur doit être répété. Si les valeurs de date **startTime** sont plus précises que la valeur **frequency**, les dates **startTime** sont prises en compte quand les limites de la fenêtre sont calculées. Par exemple, si la valeur de **frequency** est horaire et que la valeur de **startTime** est 2016-04-01T10:10:10Z, la première fenêtre est (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Chaîne | « minute », « hour »  | OUI |
| **interval** | Un entier positif qui indique l’intervalle de la valeur **frequency**, qui détermine la fréquence d’exécution du déclencheur. Par exemple, si **interval** a la valeur 3 et que **frequency** est « hour », le déclencheur se répète toutes les trois heures. | Entier  | Entier positif. | OUI |
| **startTime**| Première occurrence, qui peut être dans le passé. Le premier intervalle de déclencheur est (**startTime**, **startTime** + **interval**). | Datetime | Valeur DateTime. | OUI |
| **endTime**| Dernière occurrence, qui peut être dans le passé. | Datetime | Valeur DateTime. | OUI |
| **delay** | Délai duquel différer le démarrage du traitement des données pour la fenêtre. L’exécution du pipeline est démarrée après l’heure d’exécution prévue + **delay**. **delay** définit la durée d’attente du déclencheur après l’heure d’échéance avant de déclencher une nouvelle exécution. **delay** ne modifie pas la valeur **startTime** de la fenêtre. Par exemple, une valeur **delay** de 00:10:00 indique un délai de 10 minutes. | Timespan  | Valeur d’heure où la valeur par défaut est 00:00:00. | Non  |
| **maxConcurrency** | Nombre d’exécutions simultanées du déclencheur qui sont déclenchées pour des fenêtres qui sont prêtes. Par exemple, pour un renvoi des exécutions qui ont eu lieu toutes les heures la veille, 24 fenêtres sont générées. Si **maxConcurrency** = 10, les événements du déclencheur sont déclenchés uniquement pour les 10 premières fenêtres (00:00-01:00 - 09:00-10:00). Une fois que les 10 premières exécutions déclenchées du pipeline sont terminées, les exécutions du déclencheur sont déclenchées pour les 10 fenêtres suivantes (10:00-11:00 - 19:00-20:00). Pour poursuivre avec l’exemple de **maxConcurrency** = 10, s’il y a 10 fenêtres prêtes, il y a au total 10 exécutions du pipeline. Si une seule fenêtre est prête, il n’y a qu’une seule exécution du pipeline. | Entier  | Entier compris entre 1 et 50. | OUI |
| **retryPolicy: Count** | Nombre de nouvelles tentatives avant que l’exécution du pipeline ne soit marquée comme « Failed » (Échec).  | Entier  | Nombre entier, où la valeur par défaut est 0 (aucune nouvelle tentative). | Non  |
| **retryPolicy: intervalInSeconds** | Délai en secondes entre chaque nouvelle tentative | Entier  | Nombre de secondes, où la valeur par défaut est 30. | Non  |

### <a name="windowstart-and-windowend-system-variables"></a>Variables système WindowStart et WindowEnd

Vous pouvez utiliser les variables système **WindowStart** et **WindowEnd** du déclencheur de fenêtre bascule dans votre définition du **pipeline** (autrement dit, pour une partie d’une requête). Passez les variables système en tant que paramètres à votre pipeline dans la définition du **déclencheur**. L’exemple suivant montre comment passer ces variables en tant que paramètres :

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

Pour utiliser les variables système **WindowStart** et **WindowEnd** dans la définition du pipeline, utilisez vos paramètres « MyWindowStart » et « MyWindowEnd ».

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ordre d’exécution des fenêtres dans un scénario de renvoi
Quand il y a plusieurs fenêtres pour exécution (surtout dans un scénario de renvoi), l’ordre d’exécution des fenêtres est déterministe (de l’intervalle le plus ancien au plus récent). Actuellement, ce comportement ne peut pas être modifié.

### <a name="existing-triggerresource-elements"></a>Éléments TriggerResource existants
Les points suivants s’appliquent aux éléments **TriggerResource** existants :

* Si la valeur de l’élément **frequency** (ou la taille de fenêtre) du déclencheur change, l’état des fenêtres qui ont déjà été traitées n’est *pas* réinitialisé. Le déclencheur continue de se déclencher pour les fenêtres à partir de la dernière exécutée avec la nouvelle taille de fenêtre.
* Si la valeur de l’élément **endTime** du déclencheur change (ajout ou mise à jour), l’état des fenêtres qui ont déjà été traitées n’est *pas* réinitialisé. Le déclencheur respecte la nouvelle valeur **endTime**. Si la nouvelle valeur **endTime** est antérieure aux fenêtres qui ont déjà été exécutées, le déclencheur s’arrête. Dans le cas contraire, le déclencheur s’arrête quand la nouvelle valeur **endTime** est rencontrée.

## <a name="sample-for-azure-powershell"></a>Exemple pour Azure PowerShell
Cette section montre comment utiliser Azure PowerShell pour créer, démarrer et effectuer le monitoring d’un déclencheur.

1. Créez un fichier JSON nommé **MyTrigger.json** dans le dossier C:\ADFv2QuickStartPSH\ avec le contenu suivant :

   > [!IMPORTANT]
   > Avant d’enregistrer le fichier JSON, affectez l’heure UTC actuelle comme valeur de l’élément **startTime**. Définissez la valeur de l’élément **endTime** sur une (1) heure après l’heure UTC actuelle.

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

2. Créez un déclencheur avec l’applet de commande **Set-AzureRmDataFactoryV2Trigger** :

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Vérifiez que l’état du déclencheur est **Stopped** avec la cmdlet **Get-AzureRmDataFactoryV2Trigger** :

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Démarrez le déclencheur avec la cmdlet **Start-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Vérifiez que l’état du déclencheur est **Started** avec la cmdlet **Get-AzureRmDataFactoryV2Trigger** :

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Récupérez les exécutions du déclencheur dans Azure PowerShell avec l’applet de commande **Get-AzureRmDataFactoryV2TriggerRun**. Pour obtenir plus d’informations sur les exécutions du déclencheur, exécutez la commande suivante régulièrement. Mettez à jour les valeurs **TriggerRunStartedAfter** et **TriggerRunStartedBefore** pour qu’elles correspondent aux valeurs spécifiées dans la définition du déclencheur :

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Pour effectuer la surveillance des exécutions du déclencheur et du pipeline dans le portail Azure, consultez [Surveiller des exécutions de pipelines](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Étapes suivantes
Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#triggers).
