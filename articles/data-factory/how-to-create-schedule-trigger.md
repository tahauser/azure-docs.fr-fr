---
title: "Guide pratique pour créer des déclencheurs de planification dans Azure Data Factory | Microsoft Docs"
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
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Guide pratique pour créer un déclencheur qui exécute un pipeline selon une planification
Cet article fournit des informations sur le déclencheur de planification et les étapes pour créer, démarrer et surveiller un déclencheur. Pour les autres types de déclencheurs, consultez [Exécution du pipeline et déclencheurs](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="schedule-trigger-json-definition"></a>Définition JSON du déclencheur de planification
Lorsque vous créez un déclencheur de planification, vous pouvez indiquer la planification et la périodicité en utilisant la définition JSON de l’exemple ci-dessous.

Pour que votre déclencheur de planification lance une exécution de pipeline, insérez la référence du pipeline concerné dans la définition du déclencheur. Les pipelines et les déclencheurs ont une relation plusieurs-à-plusieurs. Plusieurs déclencheurs peuvent exécuter le même pipeline. Un seul déclencheur peut déclencher plusieurs pipelines.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
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


### <a name="overview-schedule-trigger-schema"></a>Vue d’ensemble : schéma du déclencheur de planification
Le tableau suivant présente les principaux objets liés à la périodicité et à la planification d’un déclencheur :

Propriété JSON |     DESCRIPTION
------------- | -------------
startTime | startTime Correspond à la date/l’heure de début du lancement du déclencheur. Pour les planifications simples, l’heure de début correspond à la première exécution. Pour les planifications complexes, le déclencheur ne démarre pas avant l’heure de début indiquée.
endTime | Spécifie la date/l’heure de fin du lancement du déclencheur. Le déclencheur n’exécute plus le pipeline après ce délai. La valeur endTime ne peut pas se situer dans le passé. Propriété facultative.
timeZone | Actuellement, seul UTC est pris en charge.
recurrence | Spécifie les règles de périodicité pour le déclencheur. Il prend en charge les éléments suivants : frequency, interval, endTime, count et schedule. Si la périodicité est définie, la fréquence doit l’être aussi. Les autres éléments de la valeur recurrence sont facultatifs.
frequency | Représente l’unité de fréquence à laquelle le déclencheur se répète. Valeurs prises en charge : `minute`, `hour`, `day`, `week` ou `month`.
interval | Sa valeur doit être un entier positif. Il indique l’intervalle qui détermine la fréquence à laquelle le déclencheur s’exécute. Par exemple, si l’intervalle est défini sur 3 et la fréquence est définie sur « semaine », le déclencheur se répète toutes les 3 semaines.
schedule | Un déclencheur dont la fréquence est spécifiée modifie sa périodicité selon une planification périodique. Une planification contient des modifications basées sur des minutes, heures, jours de la semaine, jour du mois et numéro de semaine.


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>Vue d’ensemble : valeurs par défaut, limites et exemples du schéma du déclencheur de planification

Nom JSON | Type de valeur | Requis ? | Valeur par défaut | Valeurs valides | exemples
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Chaîne | Oui | Aucun | Dates-Heures ISO-8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Object | Oui | Aucun | Objet de périodicité | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | Non  | 1 | 1 à 1 000. | ```"interval":10```
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
minutes | Minutes d’exécution du déclencheur dans l’heure. | <ul><li>Entier </li><li>Tableau d’entiers</li></ul>
hours | Heures d’exécution du déclencheur dans la journée. | <ul><li>Entier </li><li>Tableau d’entiers</li></ul>
weekDays | Jours d’exécution du déclencheur dans la semaine. Peut uniquement être spécifié avec une fréquence hebdomadaire. | <ul><li>Lundi, mardi, mercredi, jeudi, vendredi, samedi ou dimanche</li><li>Tableau comprenant l’une des valeurs (taille de tableau maximale 7)</li></p>Ne respectent pas la casse</p>
monthlyOccurrences | Détermine les jours d’exécution du déclencheur dans le mois. Peut uniquement être spécifié avec une fréquence mensuelle. | Tableau d’objets monthlyOccurence : `{ "day": day,  "occurrence": occurence }`. <p> day correspond au jour d’exécution du déclencheur dans la semaine, par exemple, `{Sunday}` correspond à tous les dimanches du mois. Requis.<p>occurrence correspond à l’occurrence du jour dans le mois. Par exemple, `{Sunday, -1}` correspond au dernier dimanche du mois. facultatif.
monthDays | Jour d’exécution du déclencheur dans le mois. Peut uniquement être spécifié avec une fréquence mensuelle. | <ul><li>Toute valeur <= -1 et >= -31</li><li>Toute valeur >= 1 et <= 31</li><li>Tableau de valeurs</li>


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


## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Cette section montre comment utiliser Azure PowerShell pour créer, démarrer et effectuer le monitoring d’un déclencheur de planification. Si vous souhaitez voir cet exemple en fonctionnement, commencez par lire le [guide de démarrage rapide : créer une fabrique de données avec Azure PowerShell](quickstart-create-data-factory-powershell.md). Ensuite, ajoutez le code suivant à la méthode main : il crée et lance un déclencheur de planification qui s’exécute toutes les 15 minutes. Le déclencheur est associé à un pipeline (**Adfv2QuickStartPipeline**), créé dans le cadre du guide de démarrage rapide.

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

## <a name="next-steps"></a>étapes suivantes
Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#triggers).
