---
title: "Activité de recherche dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de recherche pour rechercher une valeur à partir d’une source externe. Cette sortie peut être référencée par des activités complémentaires."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: d498705ef7f714b4f15b8d2722053bf3081b5045
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Activité de recherche dans Azure Data Factory
L’activité de recherche peut être utilisée pour lire ou rechercher un enregistrement, un nom de la table ou une valeur à partir de n’importe quelle source externe. Cette sortie peut être référencée par des activités complémentaires. 

Les sources de données suivantes sont actuellement prises en charge pour la recherche :
- Fichier JSON dans un objet blob Azure
- Fichier JSON sur site
- Azure SQL Database (données JSON converties à partir d’une requête)
- Stockage de table Azure (données JSON converties à partir d’une requête)

L’activité de recherche est utile lorsque vous souhaitez récupérer de manière dynamique une liste de fichiers/enregistrements/tables à partir d’un fichier de configuration ou d’une source de données. La sortie de l’activité peut être utilisée par d’autres activités pour effectuer un traitement spécifique sur ces éléments uniquement.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, qui est actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), consultez [Documentation de Data Factory V1](v1/data-factory-introduction.md).


## <a name="example"></a>Exemple
Dans cet exemple, l’activité de copie copie les données d’une table SQL d’une base de données Azure SQL vers Stockage Blob Azure. Le nom de la table SQL est stocké dans un fichier JSON dans le stockage d’objets blob. L’activité de recherche recherche le nom de la table lors de l’exécution. Cette approche permet de modifier JSON de manière dynamique sans avoir à redéployer les pipelines/jeux de données. 

### <a name="pipeline"></a>Pipeline
Ce pipeline contient deux activités : **recherche** et **copie**. 

- L’activité de recherche est configurée pour utiliser LookupDataset, qui fait référence à un emplacement dans Stockage Blob Azure. L’activité de recherche lit le nom de la table SQL à partir d’un fichier JSON à cet emplacement. 
- L’activité de copie utilise la sortie de l’activité de recherche (nom de la table SQL). Le paramètre tableName dans le jeu de données source (SourceDataset) est configuré pour utiliser la sortie de l’activité de recherche. L’activité de copie copie les données de la table SQL vers un emplacement de Stockage Blob Azure spécifié par le paramètre SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Jeu de données de recherche
Le jeu de données de recherche fait référence au fichier sourcetable.json dans le dossier de recherche du stockage Azure spécifié par AzureStorageLinkedService. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>Jeu de données source pour l'activité de copie
Le jeu de données source utilise la sortie de l’activité de recherche, qui correspond au nom de la table SQL. L’activité de copie copie les données de cette table SQL vers un emplacement Stockage Blob Azure spécifié par le jeu de données du récepteur. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Jeu de données du récepteur pour l'activité de copie
L’activité de copie copie les données de la table SQL dans le fichier filebylookup.csv du dossier csv dans le stockage Azure spécifié par le paramètre AzureStorageLinkedService. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Service lié Stockage Azure
Ce compte de stockage contient le fichier JSON avec les noms des tables SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Service lié pour base de données SQL Azure
Cette base de données SQL Azure contient les données à copier dans le stockage d’objets blob. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Ensemble d’objets

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```
#### <a name="array-of-objects"></a>Tableau d’objets

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```



## <a name="type-properties"></a>Propriétés type
Nom | Description | Type | Requis
---- | ----------- | ---- | --------
dataset | L’attribut dataset doit fournir la référence de jeu de données pour la recherche. Actuellement, les types de jeu de données pris en charge sont :<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | paire clé/valeur | Oui
source | Propriétés source spécifiques au jeu de données, identiques à la source de l’activité de copie | Paire clé/valeur | Non
firstRowOnly | Retourne la première ligne ou toutes les lignes. | booléenne | Non

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité Web](control-flow-web-activity.md)
