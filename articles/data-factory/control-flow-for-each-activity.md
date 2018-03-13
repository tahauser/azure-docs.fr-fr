---
title: "Activité ForEach dans Azure Data Factory | Microsoft Docs"
description: "L’activité ForEach définit un flux de contrôle répétitif dans votre pipeline. Elle permet d’effectuer une itération sur une collection et d’exécuter des activités spécifiées."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: b9a151ac04bc539e337b0007a264e196dc0ae6a3
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="foreach-activity-in-azure-data-factory"></a>Activité ForEach dans Azure Data Factory
L’activité ForEach définit un flux de contrôle répétitif dans votre pipeline. Elle permet d’effectuer une itération sur une collection, et exécute des activités spécifiées dans une boucle. L’implémentation en boucle de cette activité est similaire à la structure d’exécution en boucle de Foreach dans les langages de programmation.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), consultez [Documentation de Data Factory V1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Syntaxe
Les propriétés sont décrites plus loin dans cet article. La propriété items est la collection, et chaque élément dans celle-ci est référencé à l’aide de `@item()`, comme illustré dans la syntaxe suivante :  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
Nom | Nom de l’activité ForEach. | Chaîne | OUI
Type | Doit être défini sur **ForEach** | Chaîne | OUI
isSequential | Spécifie si la boucle doit être exécutée de façon séquentielle ou parallèle.  Le nombre maximal d’itérations de boucle exécutables simultanément en parallèle est de 20. Par exemple, si vous avez une activité ForEach effectuant une itération sur une activité de copie portant sur 10 jeux de données de source et de récepteur différents avec la valeur **isSequential** définie sur False, toutes les copies sont exécutées en même temps. La valeur par défaut est False. <br/><br/> Si la valeur de « isSequential » est définie sur False, assurez-vous qu’il existe une configuration correcte pour exécuter plusieurs exécutables. Autrement, cette propriété doit être utilisée avec précaution pour éviter des conflits d’écriture. Pour plus d’informations, voir la section [Exécution parallèle](#parallel-execution). | Booléen | Non. La valeur par défaut est False.
Éléments | Expression qui retourne un tableau JSON auquel appliquer l’itération. | Expression (qui retourne un tableau JSON) | OUI
Activités | Activités à exécuter. | Liste des activités | OUI

## <a name="parallel-execution"></a>Exécution en parallèle
Si la valeur de **isSequential** est définie sur False, l’activité effectue l’itération en parallèle avec un maximum de 20 itérations simultanées. Ce paramètre doit être utilisé avec précaution. Si les itérations simultanées écrivent dans le même dossier, mais dans des fichiers différents, cette approche convient. Si les itérations simultanées écrivent en même temps dans le même fichier, cette approche entraînera très probablement une erreur. 

## <a name="iteration-expression-language"></a>Langage d’expression de l’itération
Dans l’activité ForEach, fournissez un tableau sur lequel effectuer l’itération pour la propriété **items**. Utilisez `@item()` pour itérer sur une seule énumération dans l’activité ForEach. Par exemple, si **items** est un tableau [1, 2, 3], `@item()` retourne 1 dans la première itération, 2 dans la deuxième, et 3 dans la troisième.

## <a name="iterating-over-a-single-activity"></a>Itération sur une activité unique
**Scénario :** copier à partir du même fichier source dans un objet blob Azure vers plusieurs fichiers de destination dans l’objet blob Azure.

### <a name="pipeline-definition"></a>Définition de pipeline

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Définition du jeu de données d'objet blob

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Valeurs de paramètre d’exécution

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Itérer sur plusieurs activités
Il est possible d’itérer sur plusieurs activités (par exemple : des activités web et de copie) dans une activité ForEach. Dans ce scénario, nous vous recommandons d’abstraire plusieurs activités dans un pipeline distinct. Ensuite, vous pouvez utiliser l’[activité ExecutePipeline](control-flow-execute-pipeline-activity.md) dans le pipeline avec l’activité ForEach pour appeler le pipeline séparé avec plusieurs activités. 


### <a name="syntax"></a>Syntaxe

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```
### <a name="example"></a>exemples
**Scénario :**  itérer sur un pipeline interne au sein d’une activité ForEach avec l’activité d’exécution du pipeline. Le pipeline interne copie avec des définitions de schéma paramétrées.

#### <a name="master-pipeline-definition"></a>Définition du pipeline principal

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Définition de pipeline interne

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Définition du jeu de données de la source

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Définition du jeu de données du récepteur

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Paramètres du pipeline principal
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}

```
## <a name="aggregating-metric-output"></a>Agrégation des résultats de la métrique
L’expression pour la collecte des résultats de toutes les itérations d’une instruction ForEach est `@activity('NameofInnerActivity')`. Par exemple, si une activité ForEach est itérée sur une activité « MyCopyActivity », la syntaxe est la suivante : `@activity('MyCopyActivity')`. Le résultat est un tableau dans lequel chaque élément fournit des détails sur une itération spécifique.

> [!NOTE]
> Si vous souhaitez obtenir des détails sur une itération spécifique, la syntaxe est la suivante : `@activity('NameofInnerActivity')[0]` pour la dernière itération. Utilisez le nombre entre crochets pour accéder à l’itération spécifique du tableau. Pour accéder à une propriété spécifique d’une itération spécifique, vous devez utiliser `@activity('NameofInnerActivity')[0].output` ou `@activity('NameofInnerActivity')[0].pipelineName`.

**Détails des résultats de toutes les itérations au tableau :**
```json
[    
    {      
        "pipelineName": "db1f7d2b-dbbd-4ea8-964e-0d9b2d3fe676",      
        "jobId": "a43766cb-ba13-4c68-923a-8349af9a76a3",      
        "activityRunId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",      
        "linkedServiceName": "ADFService",      
        "status": "Succeeded",      
        "statusCode": null,      
        "output": 
            {        
                "progress": 100,        
                "loguri": null,        
                "dataRead": "6.00 Bytes",        
                "dataWritten": "6.00 Bytes",        
                "regionOrGateway": "West US",        
                "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",        
                "copyDuration": "00:00:05",        
                "dataVolume": "6.00 Bytes",        
                "throughput": "1.16 Bytes/s",       
                 "totalDuration": "00:00:10"      
            },      
        "resumptionToken": 
            {       
                "ExecutionId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",        
                "ResumptionToken": 
                    {          
                        "in progress": "217526fa-0218-42f1-b85c-e0b4f7b170ce/wu/cloud/"       
                    },        
                "ExtendedProperties": 
                    {          
                        "dataRead": "6.00 Bytes",          
                        "dataWritten": "6.00 Bytes",          
                        "regionOrGateway": "West US",          
                        "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",          
                        "copyDuration": "00:00:05",          
                        "dataVolume": "6.00 Bytes",          
                        "throughput": "1.16 Bytes/s",          
                        "totalDuration": "00:00:10"        
                    }      
            },      
        "error": null,      
        "executionStartTime": "2017-08-01T04:17:27.5747275Z",      
        "executionEndTime": "2017-08-01T04:17:46.4224091Z",     
        "duration": "00:00:18.8476816"    
    },
    {      
        "pipelineName": "db1f7d2b-dbbd-4ea8-964e-0d9b2d3fe676",      
        "jobId": "54232-ba13-4c68-923a-8349af9a76a3",      
        "activityRunId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",      
        "linkedServiceName": "ADFService",      
        "status": "Succeeded",      
        "statusCode": null,      
        "output": 
            {        
                "progress": 100,        
                "loguri": null,        
                "dataRead": "6.00 Bytes",        
                "dataWritten": "6.00 Bytes",        
                "regionOrGateway": "West US",        
                "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",        
                "copyDuration": "00:00:05",        
                "dataVolume": "6.00 Bytes",        
                "throughput": "1.16 Bytes/s",       
                 "totalDuration": "00:00:10"      
            },      
        "resumptionToken": 
            {       
                "ExecutionId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",        
                "ResumptionToken": 
                    {          
                        "in progress": "217526fa-0218-42f1-b85c-e0b4f7b170ce/wu/cloud/"       
                    },        
                "ExtendedProperties": 
                    {          
                        "dataRead": "6.00 Bytes",          
                        "dataWritten": "6.00 Bytes",          
                        "regionOrGateway": "West US",          
                        "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",          
                        "copyDuration": "00:00:05",          
                        "dataVolume": "6.00 Bytes",          
                        "throughput": "1.16 Bytes/s",          
                        "totalDuration": "00:00:10"        
                    }      
            },      
        "error": null,      
        "executionStartTime": "2017-08-01T04:18:27.5747275Z",      
        "executionEndTime": "2017-08-01T04:18:46.4224091Z",     
        "duration": "00:00:18.8476816"    
    }
]

```
## <a name="next-steps"></a>étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)