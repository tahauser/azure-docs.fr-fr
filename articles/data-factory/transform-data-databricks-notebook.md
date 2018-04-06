---
title: Transformer des données avec Databricks Notebook - Azure | Microsoft Docs
description: Découvrez comment traiter ou transformer des données en exécutant un bloc-notes Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: d4a57e45d5ddf55906fcf575df39135a227418ec
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformer des données en exécutant un bloc-notes Databricks

L’activité Azure Databricks Notebook dans un [pipeline Data Factory](concepts-pipelines-activities.md) exécute un bloc-notes Databricks dans votre espace de travail Azure Databricks. Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge. Azure Databricks est une plateforme gérée pour exécuter Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Définition de l’activité Databricks Notebook

Voici l’exemple de définition JSON d’une activité Databricks Notebook :

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propriétés de l’activité Databricks Notebook

Le tableau suivant décrit les propriétés JSON utilisées dans la définition JSON :

|Propriété|Description|Obligatoire|
|---|---|---|
|Nom|Nom de l'activité dans le pipeline.|OUI|
|description|Texte décrivant l’activité.|Non |
|Type|Pour l’activité Databricks Notebook, le type d’activité est DatabricksNotebook.|OUI|
|linkedServiceName|Nom du service lié Databricks sur lequel s’exécute le bloc-notes Databricks. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md).|OUI|
|notebookPath|Chemin absolu du notebook à exécuter dans l’espace de travail Databricks. Ce chemin doit commencer par une barre oblique.|OUI|
|baseParameters|Tableau de paires clé-valeur. Des paramètres de base peuvent être utilisés pour chaque exécution d’activité. Si le notebook accepte un paramètre qui n’est pas spécifié, la valeur par défaut du notebook est utilisée. Pour obtenir d’autres paramètres, consultez [Databricks Notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Non |
