---
title: "Exécution et déclencheurs du pipeline dans Azure Data Factory | Microsoft Docs"
description: "Cet article fournit des informations sur l’exécution d’un pipeline dans Azure Data Factory, soit à la demande, soit en créant un déclencheur."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: 88ae5dfbf6246ecf92d6528ad3d9a8e5fb57e4b0
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Exécution et déclencheurs du pipeline dans Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-scheduling-and-execution.md)
> * [Version 2 - Préversion](concepts-pipeline-execution-triggers.md)

Une **exécution du pipeline** est un terme utilisé dans Azure Data Factory Version 2 qui définit une instance d’une exécution du pipeline. Par exemple, vous disposez d’un pipeline qui s’exécute à 8 h 00, 9 h 00 et 10 h 00. Il y aura trois exécutions de pipeline différentes. Chaque exécution du pipeline a une ID d’exécution de pipeline, correspondant à un GUID qui identifie de façon unique cette exécution de pipeline spécifique. Les exécutions de pipeline sont généralement instanciées en transmettant des arguments aux paramètres définis dans les pipelines. Il existe deux façons d’exécuter un pipeline : **manuellement** ou via un **déclencheur**. Cet article décrit ces deux méthodes d’exécution d’un pipeline.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la rubrique [Planification et exécution avec Data Factory](v1/data-factory-scheduling-and-execution.md).

## <a name="run-pipeline-on-demand"></a>Exécution du pipeline à la demande
Dans cette méthode, vous exécutez votre pipeline manuellement. Il s’agit d’une exécution à la demande.

Par exemple, vous souhaitez exécuter le pipeline **copyPipeline**. Ce pipeline est un pipeline simple avec une seule activité qui copie des données à partir d’un dossier source situé dans Stockage Blob Azure vers un dossier de destination situé au même endroit. Voici la définition de ce pipeline :

```json
{
  "name": "copyPipeline",
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
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```
Le pipeline accepte deux paramètres : sourceBlobContainer et sinkBlobContainer comme indiqué dans la définition JSON. Vous pouvez transmettre des valeurs à ces paramètres lors de l’exécution.

Pour exécuter le pipeline manuellement, vous pouvez utiliser une des méthodes suivantes : .NET, PowerShell, REST et Python.

### <a name="rest-api"></a>de l’API REST
Voici un exemple de commande REST :  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
Consultez la rubrique relative à la [création d’une fabrique de données à l’aide de l’API REST](quickstart-create-data-factory-rest-api.md) pour obtenir un exemple complet.

### <a name="powershell"></a>PowerShell
Voici un exemple de commande PowerShell :

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Vous pouvez transmettre des paramètres dans le corps de la charge utile de la demande. Dans .NET, Powershell et Python, les valeurs se transmettent dans un dictionnaire transmis sous forme d’argument à l’appel.

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

La charge utile de réponse est un identificateur unique de l’exécution du pipeline :

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


Consultez la rubrique relative à la [création d’une fabrique de données à l’aide de PowerShell](quickstart-create-data-factory-powershell.md) pour obtenir un exemple complet.

### <a name="net"></a>.NET
Voici un exemple d’appel .NET :

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Consultez la rubrique relative à la [création d’une fabrique de données à l’aide de .NET](quickstart-create-data-factory-dot-net.md) pour obtenir un exemple complet.

> [!NOTE]
> Vous pouvez utiliser l’API .NET pour appeler des pipelines Data Factory à partir d’Azure Functions, de vos services web, etc.

## <a name="triggers"></a>Déclencheurs
Les déclencheurs permettent aussi de lancer une exécution du pipeline. Ils correspondent à une unité de traitement qui détermine le moment où une exécution de pipeline doit être lancée. Actuellement, Data Factory prend en charge deux types de déclencheurs : 1)**le déclencheur Scheduler**, un déclencheur qui appelle un pipeline selon un planning horaire 2)**le déclencheur de fenêtre bascule** : pour les déclencheurs qui agissent sur un intervalle périodique tout en conservant leur état. Actuellement, Data Factory ne prend pas en charge les déclencheurs basés sur des événements, par exemple lors de la réception d’un fichier.

Les pipelines et les déclencheurs ont une relation plusieurs-à-plusieurs. Plusieurs déclencheurs peuvent lancer un même pipeline et un seul déclencheur peut lancer plusieurs pipelines. Dans cette définition JSON d’un déclencheur, la propriété **pipelines** fait référence à la liste des pipelines qui sont déclenchés par un déclencheur particulier, ainsi que les valeurs des paramètres du pipeline.

### <a name="basic-trigger-definition"></a>Définition du déclencheur de base :
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="schedule-trigger"></a>Déclencheur de planification
Le déclencheur de planification exécute les pipelines selon un planning horaire. Ce déclencheur prend en charge les options de calendrier périodiques et avancées (toutes les semaines, le lundi à 17 h 00 et le jeudi à 21 h 00). Il est flexible car il n’a pas de modèle de jeu de données spécifique, et ne fait pas la distinction entre les données de série chronologique et non chronologique.

Pour plus d’informations spécifiques sur les déclencheurs de planification et obtenir des exemples, consultez la section [Comment : créer un déclencheur de planification](how-to-create-schedule-trigger.md)

## <a name="tumbling-window-trigger"></a>Déclencheur de fenêtre bascule
Les déclencheurs de fenêtre bascule sont un type de déclencheur qui s’active à un intervalle de temps périodique à partir d’une heure de début spécifiée, tout en conservant son état. Les fenêtres bascule sont une série d’intervalles de temps contigus fixes, qui ne se chevauchent pas.
Pour plus d’informations spécifiques sur les déclencheurs de fenêtre bascule et obtenir des exemples, consultez la section [Comment : créer un déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md)

### <a name="scheduler-trigger-json-definition"></a>Définition JSON du déclencheur Scheduler
Lorsque vous créez un déclencheur Scheduler, vous pouvez indiquer la planification et la périodicité en utilisant la définition JSON de l’exemple ci-dessous. 

Pour que votre déclencheur Scheduler lance une exécution de pipeline, insérez la référence du pipeline concerné dans la définition du déclencheur. Les pipelines et les déclencheurs ont une relation plusieurs-à-plusieurs. Plusieurs déclencheurs peuvent exécuter le même pipeline. Un seul déclencheur peut déclencher plusieurs pipelines.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // how often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  La propriété **parameters** est une propriété obligatoire de **pipelines**. Même si votre pipeline n’accepte aucun paramètre, incluez un json vide pour les paramètres, car la propriété doit exister.


### <a name="overview-scheduler-trigger-schema"></a>Vue d’ensemble : schéma du déclencheur Scheduler
Le tableau suivant présente les principaux objets liés à la périodicité et à la planification d’un déclencheur :

Propriété JSON |     DESCRIPTION
------------- | -------------
startTime | startTime Correspond à la date/l’heure de début du lancement du déclencheur. Pour les planifications simples, l’heure de début correspond à la première exécution. Pour les planifications complexes, le déclencheur ne démarre pas avant l’heure de début indiquée.
endTime | Spécifie la date/l’heure de fin du lancement du déclencheur. Le déclencheur n’exécute plus le pipeline après ce délai. La valeur endTime ne peut pas se situer dans le passé.
timeZone | Actuellement, seul UTC est pris en charge. 
recurrence | Spécifie les règles de périodicité pour le déclencheur. Il prend en charge les éléments suivants : frequency, interval, endTime, count et schedule. Si la périodicité est définie, la fréquence doit l’être aussi. Les autres éléments de la valeur recurrence sont facultatifs.
frequency | Représente l’unité de fréquence à laquelle le déclencheur se répète. Valeurs prises en charge : `minute`, `hour`, `day`, `week` ou `month`.
interval | Sa valeur doit être un entier positif. Il indique l’intervalle qui détermine la fréquence à laquelle le déclencheur s’exécute. Par exemple, si l’intervalle est défini sur 3 et la fréquence est définie sur « semaine », le déclencheur se répète toutes les 3 semaines.
schedule | Un déclencheur dont la fréquence est spécifiée modifie sa périodicité selon une planification périodique. Une planification contient des modifications basées sur des minutes, heures, jours de la semaine, jour du mois et numéro de semaine.


## <a name="tumbling-window-trigger-vs-schedule-trigger"></a>Déclencheur de fenêtre bascule vs. Déclencheur de planification
Étant donné que le déclencheur de fenêtre bascule et le déclencheur de planification fonctionnent tous les deux sur des pulsations de temps, en quoi sont-ils différents ?
Pour le déclencheur de fenêtre bascule :
* **Scénarios de renvoi** : les déclencheurs de fenêtre bascule prennent en charge les scénarios de renvoi, ayant eu la capacité de planifier des exécutions pour des fenêtres dans le passé. Le déclencheur de planification ne peut être exécuté que sur des périodes portant la date du jour ou une date ultérieure.
* **Fiabilité :** les déclencheurs de fenêtre bascule planifient des exécutions de pipeline pour toutes les fenêtres à partir d’une date de début sans interruption avec une fiabilité de 100 %.
* **Nouvelle tentative** : les déclencheurs de fenêtre bascule ont la capacité de faire une nouvelle tentative. Les exécutions de pipeline ayant échoué ont une stratégie de nouvelle tentative par défaut d’une valeur de 0 ou une spécifiée par l’utilisateur dans le cadre de la définition du déclencheur. Une nouvelle tentative se fait automatiquement sur les instances lorsque des exécutions échouent en raison de limites de concurrence/serveur/limitation. Cela inclut, par exemple, les codes d’état 400 (Erreur de l’utilisation), 429 (Trop de demandes), 500 (Erreur interne du serveur).
* **Concurrence** : les déclencheurs de fenêtre bascule permettent aux utilisateurs de définir explicitement les limites de concurrence pour le déclencheur (1 à 50 exécutions de pipeline simultanées déclenchées maximum)
* **Variables de début & de fin de fenêtre** : pour les déclencheurs de fenêtre bascule, les utilisateurs peuvent entrer triggerOutputs().windowStartTime et triggerOutputs().windowEndTime en tant que variables système de déclencheur dans la définition du déclencheur, qui seront, respectivement, les temps de début et de fin de la fenêtre. Par exemple, si un déclencheur de fenêtre bascule s’exécute toutes les heures, pour la fenêtre 1h00-2h00, triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z et triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z.
* **Relation du pipeline et du déclencheur** : les déclencheurs de planification ont une relation n:m avec les pipelines. Un déclencheur de planification peut déclencher plusieurs pipelines. Les déclencheurs de fenêtre bascule ont une relation 1:1 avec les pipelines. Un déclencheur de fenêtre bascule ne peut déclencher qu’un seul pipeline.

### <a name="schedule-trigger-example"></a>Exemple de déclencheur de planification

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>Vue d’ensemble : valeurs par défaut, limites et exemples du schéma du déclencheur Scheduler

Nom JSON | Type de valeur | Requis ? | Valeur par défaut | Valeurs valides | exemples
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Chaîne | Oui | Aucun | Dates-Heures ISO-8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Object | Oui | Aucun | Objet de périodicité | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | Oui | Aucun | 1 à 1 000. | ```"interval":10```
endTime | Chaîne | Oui | Aucun | Valeur date-heure représentant une heure dans le futur | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Object | Non  | Aucun | Objet de planification | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Présentation approfondie : startTime
Le tableau suivant décrit comment la valeur startTime contrôle la méthode d’exécution du déclencheur :

Valeur startTime | Périodicité sans planification | Périodicité avec planification
--------------- | --------------------------- | ------------------------
Heure de début dans le passé | Calcule la prochaine exécution initiale après l’heure de début, puis l’exécute à l’heure indiquée.<p>Lancez les exécutions suivantes à partir du calcul effectué lors de la dernière exécution.</p><p>Consultez l’exemple qui suit ce tableau.</p> | Le déclencheur _ne démarre pas avant_ l’heure de début spécifiée. La première occurrence dépend de la planification calculée à partir de l’heure de début. <p>Les exécutions suivantes sont basées sur la planification de périodicité</p>
Heure de début dans le futur ou au moment présent | Exécute une fois le déclencheur à l’heure de début spécifiée. <p>Lancez les exécutions suivantes à partir du calcul effectué lors de la dernière exécution.</p> | Le déclencheur _ne démarre pas avant_ l’heure de début spécifiée. La première occurrence dépend de la planification calculée à partir de l’heure de début.<p>Lancez les exécutions suivantes à partir de la planification de périodicité.</p>

Voyons ce qui se passe lorsque l’heure de début (startTime) se situe dans le passé, que la périodicité (recurrence) est définie et la planification (schedule) n’est pas renseignée. Supposons que la date et l’heure actuelle sont `2017-04-08 13:00`, l’heure de début est `2017-04-07 14:00` et la périodicité est tous les 2 jours (avec pour fréquence jour et pour intervalle  « 2 »). Notez que l’heure de début se situe dans le passé et se produit avant l’heure actuelle.

Dans ces conditions, la première exécution aura lieu le `2017-04-09 at 14:00`. Le moteur d'Azure Scheduler calcule les occurrences de l'exécution à partir de l'heure de début. Toutes les instances dans le passé sont ignorées. Le moteur utilise l'instance suivante qui se produit dans le futur. Dans ce cas, l’heure de début étant `2017-04-07 at 2:00pm`, l’instance suivante aura lieu 2 jours après cette date, c’est-à-dire le `2017-04-09 at 2:00pm`.

La première heure d’exécution est la même si l’heure de début est le `2017-04-05 14:00` ou `2017-04-01 14:00`. Après la première exécution, les exécutions suivantes sont calculées à partir de la planification. Par conséquent, elles auront lieu le `2017-04-11 at 2:00pm`, puis le `2017-04-13 at 2:00pm`, puis le `2017-04-15 at 2:00pm`, etc.

Enfin, lorsqu’un déclencheur a une planification, si les heures et/ou les minutes ne sont pas définies dans la planification, elles prennent la valeur par défaut des heures et/ou minutes de la première exécution, respectivement.

### <a name="deep-dive-schedule"></a>Présentation approfondie : schedule
D’une part, une planification peut limiter le nombre d’exécutions du déclencheur. Par exemple, si un déclencheur avec une fréquence « mois » a une planification qui s’exécute uniquement le 31, le déclencheur s’exécute uniquement pendant les mois qui comptent un 31e jour.

Une planification peut, quant à elle, également augmenter le nombre d’exécutions du déclencheur. Par exemple, si un déclencheur avec une fréquence « mois » a une planification qui s’exécute le 1 et le 2 de chaque mois, le déclencheur s’exécute le 1er et le 2e jours du mois au lieu d’une fois par mois.

Si plusieurs éléments de planification sont spécifiés, l’ordre d’évaluation va du plus grand au plus petit : numéro de semaine, jour du mois, jour de la semaine, heure et minute.

Le tableau suivant décrit les éléments schedule en détail :


Nom JSON | DESCRIPTION | Valeurs valides
--------- | ----------- | ------------
minutes | Minutes d’exécution du déclencheur dans l’heure. | <ul><li>Tableau d’entiers</li></ul>
hours | Heures d’exécution du déclencheur dans la journée. | <ul><li>Tableau d’entiers</li></ul>
weekDays | Jours d’exécution du déclencheur dans la semaine. Peut uniquement être spécifié avec une fréquence hebdomadaire. | <ul><li>Tableau comprenant l’une des valeurs ci-dessous (taille de tableau maximale 7)<ul><li>Lundi</li><li>Mardi</li><li>Mercredi</li><li>Jeudi</li><li>Vendredi</li><li>Samedi</li><li>Dimanche</li></ul></li></p>Ne respectent pas la casse</p>
monthlyOccurrences | Détermine les jours d’exécution du déclencheur dans le mois. Peut uniquement être spécifié avec une fréquence mensuelle. | Tableau d’objets monthlyOccurence : `{ "day": day,  "occurrence": occurence }`. <p> day correspond au jour d’exécution du déclencheur dans la semaine, par exemple, `{Sunday}` correspond à tous les dimanches du mois. Requis.<p>occurrence correspond à l’occurrence du jour dans le mois. Par exemple, `{Sunday, -1}` correspond au dernier dimanche du mois. facultatif.
monthDays | Jour d’exécution du déclencheur dans le mois. Peut uniquement être spécifié avec une fréquence mensuelle. | <ul><li>Un tableau des valeurs ci-dessous</li><ul><li>Toute valeur <= -1 et >= -31</li><li>Toute valeur >= 1 et <= 31</li></ul></ul> |


## <a name="examples-recurrence-schedules"></a>Exemples : planifications de périodicité
Vous trouverez ici plusieurs exemples de planifications de périodicité qui se penchent tout particulièrement sur l’objet de planification et ses sous-éléments.

Les exemples de planifications supposent que l’intervalle est défini sur 1. Vous devez également prendre en compte la fréquence qui est indiquée dans la planification. Par exemple, vous ne pouvez pas utiliser la fréquence « day » et effectuer une modification « monthDays » dans la planification. Ces restrictions sont mentionnées dans le tableau de la section précédente. 

exemples | DESCRIPTION
------- | -----------
`{"hours":[5]}` | Exécution à 5 h tous les jours
`{"minutes":[15], "hours":[5]}` | Exécution à 5 h 15 tous les jours
`{"minutes":[15], "hours":[5,17]}` | Exécution à 5 h 15 et 17 h 15 tous les jours
`{"minutes":[15,45], "hours":[5,17]}` | Exécution à 5 h 15, 5 h 45, 17 h 15 et 17 h 45 tous les jours
`{"minutes":[0,15,30,45]}` | Exécution toutes les 15 minutes
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Exécution toutes les heures. Ce déclencheur s’exécute toutes les heures. La minute dépend de la valeur indiquée dans startTime, si elle est spécifiée, sinon par l’heure de création. Par exemple, si l’heure de début ou l’heure de création (selon le cas) est 00 h 25, le déclencheur sera exécuté à 00 h 25, 01 h 25, 02 h 25, ..., 23 h 25. C’est comme si le déclencheur avait une fréquence « hour », un intervalle de 1 et aucune planification. À la différence près que cette planification peut être utilisée avec une fréquence et un intervalle différents pour créer d’autres déclencheurs. Par exemple, si la fréquence est définie sur « month », la planification serait exécutée une seule fois par mois, au lieu de tous les jours si la fréquence était définie sur « day ».
`{"minutes":[0]}` | Exécution toutes les heures sur l’heure. Ce déclencheur s’exécute également toutes les heures, mais sur l’heure (par exemple, minuit, 1 h, 2 h, etc.). C’est comme si le déclencheur avait une fréquence « hour », une heure de début sans minute et aucune planification si la fréquence était définie sur « day ». En revanche, si la fréquence était définie sur « week » ou « month », la planification ne s’exécuterait qu’un seul jour par semaine ou par mois, respectivement.
`{"minutes":[15]}` | Exécution 15 minutes après l’heure, toutes les heures. Exécution toutes les heures, à partir de 00h15, 1h15, 2h15, etc. et se terminant à 22h15 et 23h15.
`{"hours":[17], "weekDays":["saturday"]}` | Exécution à 17 h le samedi chaque semaine
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Exécution à 17 h le lundi, mercredi et vendredi chaque semaine
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Exécution à 17 h 15 et 17 h 45 le lundi, mercredi et vendredi chaque semaine
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Exécution toutes les 15 minutes les jours de semaine.
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Exécution toutes les 15 minutes les jours de semaine entre 9 h et 16 h 45
`{"weekDays":["tuesday", "thursday"]}` | Exécution le mardi et le jeudi à l’heure de début spécifiée
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Exécution à 6 h le 28e jour de chaque mois (en supposant une fréquence mensuelle)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Exécution à 6 h le dernier jour du mois. Si vous souhaitez exécuter un déclencheur le dernier jour du mois, utilisez -1 au lieu de jour 28, 29, 30 ou 31.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Exécution à 6 h le premier et le dernier jours de chaque mois
`{monthDays":[1,14]}` | Exécution le premier et le quatorzième jours de chaque mois à l’heure de début spécifiée
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Exécution le premier vendredi de chaque mois à 5 h
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Exécution le premier vendredi de chaque mois à l’heure de début spécifiée
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Exécution le troisième vendredi à partir de la fin du mois, chaque mois, à l’heure de début
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Exécution le premier et le dernier vendredi de chaque mois à 5 h 15
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Exécution le premier et le dernier vendredi de chaque mois à l’heure de début spécifiée
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Exécution le cinquième vendredi de chaque mois à l’heure de début. S’il n’existe pas de cinquième vendredi dans un mois, le pipeline n’est pas exécuté, dans la mesure où il est planifié pour s’exécuter uniquement le cinquième vendredi du mois.  Si vous souhaitez exécuter le déclencheur le dernier vendredi du mois, indiquez -1 au lieu de 5 pour l’occurrence.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Exécution toutes les 15 minutes le dernier vendredi du mois
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Exécution à 5 h 15, 5 h 45, 17 h 15 et 17 h 45 le troisième mercredi de chaque mois




## <a name="next-steps"></a>étapes suivantes
Consultez les didacticiels suivants :

- [Démarrage rapide : Créer une fabrique de données avec .NET](quickstart-create-data-factory-dot-net.md)
- [Comment : créer un déclencheur de planification](how-to-create-schedule-trigger.md)
- [Comment : créer un déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md)
