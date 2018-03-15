---
title: "Pipelines et activités dans Azure Data Factory | Microsoft Docs"
description: "Découvrez les pipelines et les activités dans Azure Data Factory."
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
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: f5384b4fa0a1baaafd8b2dbf3ed4d7776f4d80ea
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines et activités dans Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-create-pipelines.md)
> * [Version 2 - Préversion](concepts-pipelines-activities.md)

Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory, et à les utiliser dans l’optique de créer des workflows pilotés par les données de bout en bout pour vos scénarios de déplacement des données et de traitement des données.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Pipelines dans Data Factory version 1](v1/data-factory-create-pipelines.md).
> 
> Cet article part du principe que vous avez lu la [présentation d’Azure Data Factory](introduction.md) et suivi le [didacticiel de démarrage rapide](quickstart-create-data-factory-powershell.md).

## <a name="overview"></a>Vue d’ensemble
Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline constitue un regroupement logique d’activités qui exécutent ensemble une tâche. Par exemple, un pipeline peut contenir un ensemble d’activités qui ingèrent et nettoient des données de journal, puis lancent un travail Spark sur un cluster HDInsight pour analyser les données de journal. L’avantage de cette opération, c’est que le pipeline vous permet de gérer les activités en tant que groupe et non individuellement. Par exemple, vous pouvez déployer et planifier le pipeline, plutôt que chaque activité séparément.  

Les activités d’un pipeline définissent les actions à effectuer sur les données. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’un serveur SQL Server local dans un stockage Blob Azure. Utilisez ensuite une activité Hive qui exécute un script Hive sur un cluster Azure HDInsight pour traiter/transformer les données du stockage Blob afin de produire des données de sortie. Enfin, utilisez une deuxième activité de copie pour copier les données de sortie dans un Azure SQL Data Warehouse sur lequel des solutions de génération de rapports décisionnelles sont développées.

Data Factory prend en charge trois types d’activités : les [activités de déplacement des données](copy-activity-overview.md), les [activités de transformation des données](transform-data.md) et les [activités de contrôle](control-flow-web-activity.md). Une activité peut inclure zéro ou plusieurs [jeux de données](concepts-datasets-linked-services.md) d’entrée et produire un ou plusieurs [jeux de données](concepts-datasets-linked-services.md) de sortie. Le diagramme suivant montre la relation entre le pipeline, l’activité et le jeu de données dans Data Factory :

![Relation entre le jeu de données, l’activité et le pipeline](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Un jeu de données d’entrée représente l’entrée d’une activité dans le pipeline, tandis qu’un jeu de données de sortie représente la sortie de l’activité. Les jeux de données identifient les données dans différents magasins de données, par exemple des tables, des fichiers, des dossiers et des documents. Après avoir créé un jeu de données, vous pouvez l’utiliser avec des activités d’un pipeline. Un jeu de données peut, par exemple, constituer un jeu de données d’entrée/sortie d’une activité de copie ou d’une activité HDInsightHive. Pour plus d’informations sur les jeux de données, consultez l’article [Jeux de données dans Azure Data Factory](concepts-datasets-linked-services.md).

## <a name="data-movement-activities"></a>Activités de déplacement des données
L’activité de copie dans Data Factory permet de copier les données d’un magasin de données source vers un magasin de données récepteur. Data Factory prend en charge les magasins de données répertoriés dans le tableau de cette section. Les données de n’importe quelle source peuvent être écrites dans n’importe quel récepteur. Cliquez sur une banque de données pour découvrir comment copier des données depuis/vers cette banque.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Pour plus d’informations, consultez l’article [Activité de copie - vue d’ensemble](copy-activity-overview.md).

## <a name="data-transformation-activities"></a>Activités de transformation des données
Azure Data Factory prend en charge les activités de transformation suivantes, qui peuvent être ajoutées à des pipelines, soit individuellement soit de façon chaînée avec une autre activité.

Activités de transformation des données | Environnement de calcul
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Activités Machine Learning : exécution de lot et mise à jour de ressource](transform-data-using-machine-learning.md) | Microsoft Azure
[Procédure stockée](transform-data-using-stored-procedure.md) | SQL Azure, Azure SQL Data Warehouse ou SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Service Analytique Azure Data Lake

Pour plus d’informations, consultez l’article [Activités de transformation des données](transform-data.md). 

## <a name="control-activities"></a>Activités de contrôle
Les activités de flux de contrôle suivantes sont prises en charge :

Activité de contrôle | Description
---------------- | -----------
[Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md) | L’activité d’exécution du pipeline permet à un pipeline Data Factory d’appeler un autre pipeline.
[ForEachActivity](control-flow-for-each-activity.md) | L’activité ForEach définit un flux de contrôle répétitif dans votre pipeline. Elle permet d’effectuer une itération sur une collection, et exécute des activités spécifiées dans une boucle. L’implémentation en boucle de cette activité est semblable à la structure d’exécution en boucle de Foreach dans les langages de programmation.
[WebActivity](control-flow-web-activity.md) | L’activité web peut être utilisée pour appeler un point de terminaison REST personnalisé à partir d’un pipeline Data Factory. Vous pouvez transmettre des jeux de données et des services liés que l’activité peut utiliser et auxquels elle peut accéder. 
[Activité de recherche](control-flow-lookup-activity.md) | L’activité de recherche peut être utilisée pour lire ou rechercher un enregistrement, un nom de table ou une valeur à partir de n’importe quelle source externe. Cette sortie peut être référencée par des activités complémentaires. 
[Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md) | L’activité d’obtention des métadonnées peut être utilisée pour récupérer les métadonnées de n’importe quelle donnée dans Azure Data Factory. 
[Activité jusqu’à](control-flow-until-activity.md) | Implémente une boucle Exécuter jusqu’à semblable à la structure Do-Until des langages de programmation. Elle exécute un ensemble d’activités dans une boucle jusqu’à ce que la condition associée à l’activité retourne la valeur true. Vous pouvez spécifier une valeur de délai d’attente pour l’activité Until dans Data Factory.
[Activité IfCondition](control-flow-if-condition-activity.md) | L’activité IfCondition peut être utilisée pour créer une branche en fonction de l’évaluation d’une condition par la valeur true ou false. L’activité IfCondition fournit les mêmes fonctionnalités qu’une instruction «if » dans les langages de programmation. La condition évalue un ensemble d’activités si l’expression retourne `true` et un autre ensemble d’activités si elle retourne `false`.
[Activité d’attente](control-flow-wait-activity.md) | Quand vous utilisez une activité Wait dans un pipeline, celui-ci attend pendant la période spécifiée avant de poursuivre l’exécution des activités suivantes. 

## <a name="pipeline-json"></a>Pipeline JSON
Voici comment un pipeline est défini au format JSON : 

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Tag | Description | type | Obligatoire
--- | ----------- | ---- | --------
Nom | Nom du pipeline. Spécifiez un nom qui représente l’action effectuée par le pipeline. <br/><ul><li>Nombre maximal de caractères : 260</li><li>Doit commencer par une lettre, un chiffre ou un trait de soulignement (_)</li><li>•   Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \ »</li></ul> | Chaîne | OUI
description | Spécifiez le texte décrivant la raison motivant l’utilisation du pipeline. | Chaîne | Non 
activités | La section **Activités** peut contenir une ou plusieurs activités définies. Consultez la section [JSON d’activité](#activity-json) pour plus d’informations sur l’élément JSON des activités. | Tableau | OUI
parameters | La section **Paramètres** peut comporter un ou plusieurs des paramètres définis dans le pipeline, afin de rendre votre pipeline plus flexible en vue de sa réutilisation. | Liste | Non 

## <a name="activity-json"></a>Activité JSON
La section **Activités** peut contenir une ou plusieurs activités définies. Il existe deux principaux types d’activités : l’exécution et les activités de contrôle.

### <a name="execution-activities"></a>Activités d’exécution
Les activités d’exécution incluent le [déplacement des données](#data-movement-activities) et la [transformation des données](#data-transformation-activities). Elles possèdent la structure de niveau supérieur suivante :

```json
{
    "name": "Execution Activity Name",
    "description": "description", 
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

Le tableau suivant décrit des propriétés de la définition JSON de l’activité :

Tag | Description | Obligatoire
--- | ----------- | ---------
Nom | Nom de l’activité. Spécifiez un nom qui représente l’action effectuée par l’activité. <br/><ul><li>Nombre maximal de caractères : 260</li><li>Doit commencer par une lettre, un chiffre ou un trait de soulignement (_)</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \ » | OUI</li></ul>
description | Texte décrivant la raison motivant l’activité ou son utilisation | OUI
Type | Type de l’activité. Consultez les sections [Activités de déplacement des données](#data-movement-activities), [Activités de transformation des données](#data-transformation-activities) et [Activités de contrôle](#control-activities) pour en savoir plus sur les différents types d’activités. | OUI
linkedServiceName | Nom du service lié utilisé par l’activité.<br/><br/>Une activité peut nécessiter que vous spécifiiez le service lié à l’environnement de calcul requis. | Oui pour l’activité HDInsight, l’activité de calcul de score du lot Azure Machine Learning et l’activité de procédure stockée. <br/><br/>Non pour toutes les autres
typeProperties | Les propriétés de la section typeProperties dépendent de chaque type d’activité. Pour afficher les propriétés de type d’une activité, cliquez sur les liens vers l’activité dans la section précédente. | Non 
policy | Stratégies affectant le comportement d’exécution de l’activité. Cette propriété inclut le comportement de délai d’expiration et de nouvelle tentative. Si aucune valeur n’est spécifiée, les valeurs par défaut sont utilisées. Pour plus d’informations, consultez la section [Stratégie d’activité](#activity-policy). | Non 
dependsOn | Cette propriété est utilisée pour définir des dépendances des activités, et la manière dont les activités suivantes dépendent des activités précédentes. Pour plus d’informations, consultez l’article [Dépendance des activités](#activity-dependency) | Non 

### <a name="activity-policy"></a>Stratégie d’activité
Les stratégies ont un impact sur le comportement d’exécution d’une activité, et fournissent des options de configuration. Les stratégies d’activité sont uniquement disponibles pour les activités d’exécution. 

### <a name="activity-policy-json-definition"></a>Définition de la JSON Stratégie d’activité

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```
Nom JSON | Description | Valeurs autorisées | Obligatoire
--------- | ----------- | -------------- | --------
timeout | Spécifie le délai d’expiration d’exécution de l’activité. | Timespan | Non. Le délai d’expiration par défaut est de 7 jours.
retry | Nombre maximal de nouvelles tentatives | Entier  | Non. La valeur par défaut est 0
retryIntervalInSeconds | Délai en secondes entre chaque nouvelle tentative | Entier  | Non. La valeur par défaut est de 20 secondes

### <a name="control-activity"></a>Activité de contrôle
Les activités de contrôle ont la structure de niveau supérieur suivante :

```json
{
    "name": "Control Activity Name",
    "description": "description", 
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Tag | Description | Obligatoire
--- | ----------- | --------
Nom | Nom de l’activité. Spécifiez un nom qui représente l’action effectuée par l’activité.<br/><ul><li>Nombre maximal de caractères : 260</li><li>Doit commencer par une lettre, un chiffre ou un trait de soulignement (_)</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \ » | OUI</li><ul> 
description | Texte décrivant la raison motivant l’activité ou son utilisation | OUI
Type | Type de l’activité. Consultez les sections [Activités de déplacement des données](#data-movement-activities), [Activités de transformation des données](#data-transformation-activities) et [Activités de contrôle](#control-activities) pour en savoir plus sur les différents types d’activités. | OUI
typeProperties | Les propriétés de la section typeProperties dépendent de chaque type d’activité. Pour afficher les propriétés de type d’une activité, cliquez sur les liens vers l’activité dans la section précédente. | Non 
dependsOn | Cette propriété est utilisée pour définir la dépendance des activités, et la manière dont les activités suivantes dépendent des activités précédentes. Pour plus d’informations, consultez l’article [Dépendance des activités](#activity-dependency). | Non 

### <a name="activity-dependency"></a>Dépendance des activités
La dépendance des activités définit la manière dont les activités suivantes dépendent des activités précédentes, et détermine ainsi s’il faut poursuivre l’exécution de la tâche suivante. Une activité peut dépendre d’une ou de plusieurs activités précédentes avec différentes conditions de dépendance. 

Les différentes conditions de dépendance sont : Opération réussie, En échec, Ignoré, Terminé.

Par exemple, si un pipeline contient Activité A -> Activité B, les différents scénarios qui peuvent se produire sont les suivants :

- Activité B a une condition de dépendance sur Activité A avec **Opération réussie** : Activité B s’exécute uniquement si Activité A présente l’état final Opération réussie
- Activité B a une condition de dépendance sur Activité A avec **En échec** : Activité B s’exécute uniquement si Activité A présente l’état final En échec
- Activité B a une condition de dépendance sur Activité A avec **Terminé** : Activité B s’exécute si Activité A présente l’état final Opération réussie ou En échec
- Activité B a une condition de dépendance sur Activité A avec **Ignoré** : Activité B s’exécute si Activité A présente l’état final Ignoré. Ignoré se produit dans le scénario Activité X -> Activité Y -> Activité Z, où chaque activité s’exécute uniquement si l’activité précédente réussit. Si l’activité X échoue, Activité Y prend l’état « Ignoré », car elle ne s’exécute jamais. De même, Activité Z présente également l’état « Ignoré ».

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Exemple : Activité 2 dépend de la succession d’Activité 1

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Exemple de pipeline de copie
Dans l’exemple de pipeline suivant, il existe une activité de type **Copy** in the **d’activités** . Dans cet exemple, l’[activité de copie](copy-activity-overview.md) copie des données d’un stockage Blob Azure vers une base de données SQL Azure.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
} 
```
Notez les points suivants :

- Dans la section des activités, il existe une seule activité dont le **type** a la valeur **Copy**.
- L’entrée de l’activité est définie sur **InputDataset** et sa sortie, sur **OutputDataset**. Consultez l’article [Jeux de données](concepts-datasets-linked-services.md) pour en savoir plus sur la définition de jeux de données dans JSON.
- Dans la section **typeProperties**, **BlobSource** est spécifié en tant que type de source et **SqlSink**, en tant que type de récepteur. Dans la section [Activités de déplacement des données](#data-movement-activities), cliquez sur le magasin de données que vous souhaitez utiliser comme source ou récepteur pour en savoir plus sur le déplacement des données vers/depuis ce magasin de données.

Pour obtenir une description complète de la création de ce pipeline, consultez l’article [Démarrage rapide : créer une fabrique de données](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Exemple de pipeline de transformation
Dans l’exemple de pipeline suivant, il existe une activité de type **HDInsightHive** in the **d’activités** . Dans cet exemple, [l’activité Hive HDInsight](transform-data-using-hadoop-hive.md) transforme les données d’un stockage blob Azure en exécutant un fichier de script Hive sur un cluster Hadoop Azure HDInsight.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
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
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Notez les points suivants :

- Dans la section des activités, il existe une seule activité dont le **type** a la valeur **HDInsightHive**.
- Le fichier de script Hive, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par le service scriptLinkedService, appelé AzureStorageLinkedService) et dans le dossier script du conteneur `adfgetstarted`.
- La section `defines` est utilisée pour spécifier les paramètres d’exécution transmis au script Hive comme valeurs de configuration Hive (p. ex. $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

La section **typeProperties** est différente pour chaque activité de transformation. Pour en savoir plus sur les propriétés de type prises en charge pour une activité de transformation, cliquez sur l’activité de transformation dans la table [Activités de transformation des données](#data-transformation-activities).

Pour obtenir une description complète de la création de ce pipeline, consultez l’article [Didacticiel : transformation des données à l’aide de Spark](tutorial-transform-data-spark-powershell.md). 

## <a name="multiple-activities-in-a-pipeline"></a>Plusieurs activités à l’intérieur d’un pipeline
Les deux exemples de pipelines précédents ne contiennent qu’une seule activité. Un pipeline peut toutefois contenir plusieurs activités. Si un pipeline contient plusieurs activités et que les activités suivantes ne sont pas dépendantes d’activités précédentes, les activités peuvent s’exécuter en parallèle. 

Vous pouvez enchaîner deux activités à l’aide de la [dépendance des activités](#activity-dependency), qui définit la manière dont les activités suivantes dépendent des activités précédentes, et détermine ainsi s’il faut poursuivre l’exécution de la tâche suivante. Une activité peut dépendre d’une ou de plusieurs activités précédentes avec différentes conditions de dépendance. 

## <a name="scheduling-pipelines"></a>Planification des pipelines
Les pipelines sont planifiés par des déclencheurs. Il existe différents types de déclencheurs (déclencheur planificateur, qui permet aux pipelines d’être déclenchés selon une planification de durée chronométrée, mais aussi déclencheur manuel, qui déclenche les pipelines à la demande). Pour plus d’informations sur les déclencheurs, consultez l’article [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md). 

Pour que votre déclencheur lance l’exécution d’un pipeline, vous devez inclure une référence à ce pipeline spécifique dans la définition du déclencheur. Les pipelines et les déclencheurs ont une relation n-m. Plusieurs déclencheurs peuvent lancer un même pipeline et un même déclencheur peut lancer plusieurs pipelines. Une fois que le déclencheur est défini, vous devez le démarrer pour qu’il commence à déclencher le pipeline. Pour plus d’informations sur les déclencheurs, consultez l’article [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md). 

Imaginons, par exemple, un déclencheur de planificateur, « Déclencheur A », que je souhaite supprimer de mon pipeline « MyCopyPipeline ». Vous définissez le déclencheur comme indiqué dans l’exemple suivant :

### <a name="trigger-a-definition"></a>Définition du déclencheur A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Étapes suivantes
Consultez les didacticiels suivants pour obtenir des instructions pas à pas de création de pipelines avec des activités : 

- [Créer un pipeline avec une activité de copie](quickstart-create-data-factory-powershell.md)
- [Créer un pipeline avec une activité de transformation des données](tutorial-transform-data-spark-powershell.md)
