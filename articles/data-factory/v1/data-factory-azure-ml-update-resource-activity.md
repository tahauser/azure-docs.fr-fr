---
title: Mise à jour des modèles Machine Learning à l’aide d’Azure Data Factory | Microsoft Docs
description: Décrit comment créer des pipelines prédictifs à l’aide d’Azure Data Factory et d’Azure Machine Learning.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3702f4b7a58e9ca65a8ee309699a7e31b207159b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Mettre à jour les modèles Azure Machine Learning à l’aide de l’activité des ressources de mise à jour

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Activité Hive](data-factory-hive-activity.md) 
> * [Activité pig](data-factory-pig-activity.md)
> * [Activité MapReduce](data-factory-map-reduce.md)
> * [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Activité Spark](data-factory-spark.md)
> * [Activité d’exécution par lot Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Activité des ressources de mise à jour de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Activité de procédure stockée](data-factory-stored-proc-activity.md)
> * [Activité U-SQL Data Lake Analytics](data-factory-usql-activity.md)
> * [Activité personnalisée .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). Si vous utilisez la version 2 du service Data Factory, qui est en préversion, consultez [update machine learning models in Data Factory version 2](../update-machine-learning-models.md) (Mettre à jour des modèles d’apprentissage machine dans Data Factory version 2).

Cet article vient s’ajouter à l’article principal sur l’intégration Azure Data Factory - Azure Machine Learning : [Création de pipelines prédictifs à l'aide d'Azure Data Factory et Azure Machine Learning](data-factory-azure-ml-batch-execution-activity.md). Si vous ne l’avez pas encore fait, consultez l’article principal avant de lire cet article. 

## <a name="overview"></a>Vue d'ensemble
Au fil du temps, les modèles prédictifs dans les expériences de notation Azure ML doivent être reformés à l’aide de nouveaux jeux de données d’entrée. Une fois que vous avez fini la reformation, vous souhaitez mettre à jour le service web de notation avec le modèle ML reformé. Les étapes classiques pour activer la reformation et la mise à jour des modèles Azure ML via les services web sont les suivantes :

1. Créez une expérience dans [Azure ML Studio](https://studio.azureml.net).
2. Lorsque vous êtes satisfait du modèle, utilisez Azure ML Studio pour publier des services web à la fois pour l’**expérience de formation** et l’expérience de notation/**prédictive**.

Le tableau suivant décrit les services web utilisés dans cet exemple.  Pour plus d’informations, consultez [Reformation des modèles Machine Learning par programme](../../machine-learning/machine-learning-retrain-models-programmatically.md) .

- **Service Web de formation** - Reçoit les données d’apprentissage et produit les modèles formés. La sortie de la reformation est un fichier .ilearner dans un stockage d’objets blob Azure. Le **point de terminaison par défaut** est automatiquement créé pour vous lorsque vous publiez l’expérience de formation en tant que service web. Vous pouvez créer d’autres points de terminaison, mais l’exemple utilise uniquement le point de terminaison par défaut.
- **Service Web de notation** - Reçoit des exemples de données sans étiquette et effectue des prédictions. La sortie de la prédiction peut prendre plusieurs formes, comme un fichier .csv ou des lignes dans une base de données SQL Azure, selon la configuration de l’expérience. Le point de terminaison par défaut est automatiquement créé pour vous lorsque vous publiez l’expérience prédictive comme un service web. 

Le schéma suivant illustre la relation entre points de terminaison de formation et de notation dans Azure ML.

![SERVICES WEB](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Vous pouvez appeler le **training web service** à l’aide du **activité d’exécution par lot Azure ML**. L’appel d’un service web de formation est similaire à l’appel d’un service web Azure ML (service web de notation) pour les données de notation. Les sections précédentes expliquent de manière détaillée comment appeler un service web Azure ML à partir d’un pipeline Azure Data Factory. 

Vous pouvez appeler le **scoring web service** à l’aide du **activité des ressources de mise à jour Azure ML** pour mettre à jour le service web avec le modèle qui vient d’être formé. Les exemples suivants fournissent les définitions de service associé : 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Service web de notation est un service web classique
Si le service web de notation est un **service classique web**, créez le deuxième **point de terminaison (qui n’est pas le point de terminaison par défaut) pouvant être mis à jour** à l’aide du portail Azure. Pour connaître les étapes, consultez l’article [Créer des points de terminaison](../../machine-learning/machine-learning-create-endpoint.md). Après avoir créé le point de terminaison non par défaut pouvant être mis à jour, procédez comme suit :

* Cliquez sur **EXÉCUTION PAR LOT** pour obtenir la valeur d’URI pour la propriété JSON **mlEndpoint**.
* Cliquez sur le lien **RESSOURCE DE MISE À JOUR** pour obtenir la valeur d’URI pour la propriété JSON **updateResourceEndpoint**. La clé API est sur la page du point de terminaison même (dans le coin inférieur droit).

![point de terminaison pouvant être mis à jour](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

L’exemple suivant présente un exemple de définition JSON pour le service lié AzureML. Le service lié utilise apiKey pour l’authentification.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Le service web de notification est un service web Azure Resource Manager 
Si le service web est un nouveau type de service web qui expose un point de terminaison Azure Resource Manager, vous n’avez pas besoin ajouter le second point de terminaison, **qui n’est pas celui par défaut** . Le **updateResourceEndpoint** du service lié est au format : 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Vous pouvez obtenir des valeurs pour les espaces réservés dans l’URL lors de l’interrogation du service web sur le [portail des services web Azure Machine Learning](https://services.azureml.net/). Le nouveau type de point de terminaison de ressource de mise à jour requiert un jeton AAD (Azure Active Directory). Spécifiez **servicePrincipalId** et **servicePrincipalKey**dans le service lié AzureML. Consultez [how to create service principal and assign permissions to manage Azure resource](../../azure-resource-manager/resource-group-create-service-principal-portal.md) (comment créer le principal de service et affecter des autorisations de gestion de ressources Azure). Voici un exemple de définition de service lié AzureML : 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Le scénario suivant fournit plus de détails. Il présente un exemple de reformation et de mise à jour de modèles Azure ML à partir d’un pipeline Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scénario : reformation et mise à jour d’un modèle Azure ML
Cette section fournit un exemple de pipeline qui utilise **l’activité d’exécution par lot Azure ML** pour reformer un modèle. Le pipeline utilise également **l’activité des ressources de mise à jour Azure ML** pour mettre à jour le modèle dans le service web de notation. La section fournit également des extraits de code JSON pour tous les services liés, jeux de données et éléments de pipeline dans l’exemple.

Voici la vue schématique de l’exemple de pipeline. Comme vous pouvez le voir, l’activité d’exécution par lot Azure ML prend l’entrée de formation et génère une sortie de formation (fichier iLearner). L’activité des ressources de mise à jour Azure ML prend cette sortie de formation et met à jour le modèle dans le point de terminaison de service web de notation. L’activité des ressources de mise à jour ne génère aucune sortie. placeholderBlob est simplement un jeu de données de sortie factice requis par le service Azure Data Factory pour exécuter le pipeline.

![schéma du pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Service lié Azure Blob Storage :
Azure Storage contient les données suivantes :

* Données de formation. Les données d’entrée pour le service web de formation Azure ML.  
* Fichier iLearner. La sortie du service web de formation Azure ML. Ce fichier est également l’entrée de l’activité des ressources de mise à jour.  

Voici la définition d’exemple JSON du service lié :

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Jeu de données d’entrée de formation
Le jeu de données suivant représente les données de formation d’entrée pour le service web de formation Azure ML. L’activité d’exécution par lots Azure ML prend ce jeu de données comme entrée.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Jeu de données de sortie de formation :
Le jeu de données suivant représente le fichier iLearner de sortie issu du service web de formation Azure ML. L’activité d’exécution par lots Azure ML génère ce jeu de données. Ce jeu de données est également l’entrée de l’activité des ressources de mise à jour Azure ML.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Service lié pour le point de terminaison de formation Azure ML
L’extrait de code JSON suivant définit un service lié Azure Machine Learning qui pointe vers le point de terminaison par défaut du service web de formation.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

Dans **Azure ML Studio**, procédez comme suit pour obtenir les valeurs pour **mlEndpoint** et **apiKey** :

1. Cliquez sur **SERVICES WEB** dans le menu de gauche.
2. Cliquez sur le **service web de formation** dans la liste des services web.
3. Cliquez sur Copier regard de la zone de texte **Clé API** . Collez la clé copiée dans l’éditeur JSON Data Factory.
4. Dans le **studio Azure ML**, cliquez sur le lien **EXÉCUTION PAR LOT**.
5. Copiez l’**URI de demande** à partir de la section **Demande**, et collez-le dans l’éditeur JSON Data Factory.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Service lié pour le point de terminaison de notation pouvant être mis à jour Azure ML :
L’extrait de code JSON suivant définit un service lié Azure Machine Learning qui pointe vers le point de terminaison autre que par défaut pouvant être mis à jour du service web de notation.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Jeu de données de sortie de l’espace réservé
L’activité des ressources de mise à jour Azure ML ne génère aucune sortie. Toutefois, Azure Data Factory requiert un jeu de données de sortie pour que la planification d’un pipeline fonctionne. Par conséquent, nous utilisons dans cet exemple un jeu de données factice/paramètre fictif.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Le pipeline a deux activités : **AzureMLBatchExecution** et **AzureMLUpdateResource**. L’activité d’exécution par lot Azure ML prend les données d’apprentissage comme entrée et génère un fichier .iLearner comme sortie. L’activité appelle le service web de formation (expérience de formation exposée comme un service web) avec les données de formation d’entrée et reçoit le fichier iLearner du service web. placeholderBlob est simplement un jeu de données de sortie factice requis par le service Azure Data Factory pour exécuter le pipeline.

![schéma du pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
