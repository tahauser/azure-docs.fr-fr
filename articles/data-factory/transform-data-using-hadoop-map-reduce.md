---
title: "Transformer des données à l’aide d’une activité Hadoop MapReduce dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment traiter des données en exécutant des programmes Hadoop MapReduce sur un cluster Azure HDInsight à partir d’une fabrique de données Azure."
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
ms.openlocfilehash: c1fbb6864629874ef116cdf81d48df4a9ed5af1f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformer des données à l’aide d’une activité Hadoop MapReduce dans Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-map-reduce.md)
> * [Version 2 : préversion](transform-data-using-hadoop-map-reduce.md)


L’activité MapReduce de HDInsight dans un [pipeline](concepts-pipelines-activities.md) Data Factory appelle un programme MapReduce sur [votre propre](compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight ou [à la demande](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, consultez [Activité MapReduce dans V1](v1/data-factory-map-reduce.md).


Si vous découvrez Azure Data Factory, lisez la [Présentation d’Azure Data Factory](introduction.md) et suivez le [Didacticiel : Transformer des données](tutorial-transform-data-spark-powershell.md) avant de lire cet article. 

Consultez [Pig](transform-data-using-hadoop-pig.md) et [Hive](transform-data-using-hadoop-hive.md) pour plus d’informations sur l’exécution de scripts Pig/Hive sur un cluster HDInsight à partir d’un pipeline à l’aide des activités Pig et Hive de HDInsight. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
          "-SampleHadoopJobArgument1"
        ],
        "defines": {
          "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Détails de la syntaxe

| Propriété          | Description                              | Obligatoire |
| ----------------- | ---------------------------------------- | -------- |
| Nom              | Nom de l’activité                     | OUI      |
| description       | Texte décrivant la raison motivant l’activité. | Non        |
| Type              | Pour l’activité MapReduce, le type d’activité est HDinsightMapReduce. | OUI      |
| linkedServiceName | Référence au cluster HDInsight enregistré en tant que service lié dans Data Factory. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md). | OUI      |
| className         | Nom de la classe à exécuter         | OUI      |
| jarLinkedService  | Référence à un service lié Stockage Azure utilisé pour stocker les fichiers Jar. Si vous ne spécifiez pas ce service lié, le service lié Stockage Azure défini dans le service lié HDInsight est utilisé. | Non        |
| jarFilePath       | Indiquez le chemin des fichiers Jar stockés dans le stockage Azure référencé par jarLinkedService. Le nom de fichier respecte la casse. | OUI      |
| jarlibs           | Le tableau de chaînes du chemin des fichiers de bibliothèque Jar référencés par le travail stocké dans le stockage Azure défini dans jarLinkedService. Le nom de fichier respecte la casse. | Non        |
| getDebugInfo      | Spécifie quand les fichiers journaux sont copiés vers le stockage Azure utilisé par le cluster HDInsight (ou) spécifié par jarLinkedService. Valeurs autorisées : Aucun, Toujours ou Échec. Valeur par défaut : Aucun. | Non        |
| arguments         | Spécifie un tableau d’arguments pour un travail Hadoop. Les arguments sont passés sous la forme d’arguments de ligne de commande à chaque tâche. | Non        |
| defines           | Spécifier les paramètres sous forme de paires clé/valeur pour le référencement au sein du script Hive. | Non        |



## <a name="example"></a>Exemple
Vous pouvez utiliser l’activité MapReduce de HDInsight pour exécuter un fichier jar MapReduce dans un cluster HDInsight. Dans l'exemple suivant de définition JSON d'un pipeline, l'activité HDInsight est configurée pour exécuter un fichier JAR Mahout.

```json   
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
Vous pouvez spécifier les arguments du programme MapReduce dans la section **arguments**. Lors de l’exécution, vous verrez quelques arguments supplémentaires (par exemple : mapreduce.job.tags) à partir de l’infrastructure MapReduce. Pour différencier vos arguments avec les arguments MapReduce, envisagez d’utiliser l’option et la valeur en tant qu’arguments comme indiqué dans l’exemple suivant (-s, --entrée, --sortie etc. sont des options immédiatement suivies par leurs valeurs).

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens : 

* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité Hive](transform-data-using-hadoop-hive.md)
* [Activité Pig](transform-data-using-hadoop-pig.md)
* [Activité de streaming Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité personnalisée .NET](transform-data-using-dotnet-custom-activity.md)
* [Activité d’exécution du lot Machine Learning](transform-data-using-machine-learning.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
