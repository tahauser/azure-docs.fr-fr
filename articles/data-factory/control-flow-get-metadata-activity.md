---
title: "Activité d’obtention des métadonnées dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l'activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données SQL Azure ou un entrepôt Azure SQL Data Warehouse à partir d'un pipeline Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 20f3d4bb876a46b67385dd4435296e149641149e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Activité d’obtention des métadonnées dans Azure Data Factory
L’activité d’obtention des métadonnées peut être utilisée pour récupérer les métadonnées de n’importe quelle donnée dans Azure Data Factory. Cette activité est prise en charge uniquement pour les fabriques de données version 2. Elle peut être utilisée dans les scénarios suivants :

- Valider les informations de métadonnées de n’importe quelle donnée
- Déclencher un pipeline lorsque des données sont prêtes/disponibles

La fonctionnalité suivante est disponible dans le flux de contrôle :
- La sortie de l’activité d’obtention des métadonnées peut être utilisée dans des expressions conditionnelles pour effectuer la validation.
- Un pipeline peut être déclenché lorsque la condition est remplie via une bouche Do-Until

L’activité d’obtention des métadonnées sélectionne un jeu de données en tant qu’entrée requise, puis génère les informations de métadonnées disponibles en tant que sortie. Actuellement, seul le jeu de données d’objets blob Azure est pris en charge. Les champs de métadonnées pris en charge sont size, structure et lastModified time.  

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), consultez [Documentation de Data Factory V1](v1/data-factory-introduction.md).


## <a name="syntax"></a>Syntaxe

### <a name="get-metadata-activity-definition"></a>Définition de l’activité d’obtention des métadonnées :
Dans l’exemple suivant, l’activité d’obtention des métadonnées renvoie les métadonnées concernant les données représentées par MyDataset. 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>Définition du jeu de données :

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Sortie
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Propriétés type
Actuellement, l’activité d’obtention des métadonnées peut extraire les types d’informations de métadonnées suivants à partir d’un jeu de données de stockage Azure.

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
fieldList | Répertorie les types d’informations de métadonnées requis.  | <ul><li>size</li><li>structure</li><li>lastModified</li></ul> |    Non <br/>Si cette valeur est vide, l’activité retourne toutes les 3 informations de métadonnées prises en charge. 
dataset | Jeu de données de référence à partir duquel l’activité de métadonnées doit être récupérée par l’activité d’obtention des métadonnées. <br/><br/>Le type de jeu de données actuellement pris en charge est Azure Blob. Les propriétés secondaires sont : <ul><li><b>referenceName</b>: référence à un jeu de données Azure Blob existant</li><li><b>type</b> : comme le jeu de données est référencé, le type est « DatasetReference »</li></ul> |    <ul><li>Chaîne</li><li>DatasetReference</li></ul> | OUI

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)