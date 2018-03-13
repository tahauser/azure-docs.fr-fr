---
title: "Déploiement du service web de gestion des modèles Azure Machine Learning | Microsoft Docs"
description: "Ce document décrit les étapes à suivre pour déployer un modèle d’apprentissage automatique en utilisant la gestion des modèles Azure Machine Learning."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: 9fbdb190e7c745000b358451c1a6e3058cb861fd
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Déploiement d’un modèle d’apprentissage automatique comme service web

La gestion des modèles Azure Machine Learning fournit des interfaces permettant de déployer des modèles sous forme de services web en conteneur basés sur Docker. Vous pouvez déployer les modèles que vous créez en utilisant des frameworks tels que Spark, Microsoft Cognitive Toolkit (CNTK), Keras, Tensorflow et Python. 

Ce document décrit les étapes à suivre pour déployer vos modèles sous forme de services web à l’aide de l’interface de ligne de commande (CLI) de la gestion des modèles Azure Machine Learning.

## <a name="deploying-web-services"></a>Déploiement de services web
À partir de l’interface CLI, vous pouvez déployer des services web à exécuter sur l’ordinateur local ou sur un cluster.

Nous vous recommandons de commencer par un déploiement local. Dans un premier temps, vérifiez que votre modèle et votre code fonctionnent, puis déployez le service web sur un cluster pour une utilisation en production. Pour plus d’informations sur la configuration de votre environnement en vue d’un déploiement de cluster, consultez [Configuration de la gestion des modèles](deployment-setup-configuration.md). 

Les étapes de déploiement sont les suivantes :
1. Utilisez le modèle d’apprentissage automatique que vous avez enregistré et formé.
2. Créez un schéma pour les données d’entrée et de sortie de votre service web.
3. Créez une image de conteneur basée sur Docker.
4. Créez et déployez le service web

### <a name="1-save-your-model"></a>1. Enregistrez votre modèle
Partez de votre fichier de modèle enregistré et formé, par exemple, mymodel.pkl. L’extension de fichier varie en fonction de la plateforme que vous utilisez pour former et enregistrer le modèle. 

Par exemple, vous pouvez utiliser la bibliothèque Pickle de Python pour enregistrer un modèle formé dans un fichier. Voici un [exemple](http://scikit-learn.org/stable/modules/model_persistence.html) utilisant le jeu de données Iris:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Créez un fichier schema.json
Cette étape est facultative. 

Créez un schéma pour valider automatiquement l’entrée et la sortie de votre service web. Les interfaces CLI se servent aussi du schéma pour générer un document Swagger pour votre service web.

Pour créer le schéma, importez les bibliothèques suivantes :

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Ensuite, définissez les variables d’entrée, par exemple un cadre de données Spark, un cadre de données Pandas ou un tableau NumPy. L’exemple suivant utilise un tableau Numpy :

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```
L’exemple suivant utilise un cadre de données Spark :

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

L’exemple suivant utilise un cadre de données PANDAS :

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Créez un fichier score.py
Vous fournissez un fichier score.py, qui charge votre modèle et retourne les résultats de prédiction en utilisant le modèle.

Le fichier doit inclure deux fonctions : init et run.

Ajoutez le code suivant dans la partie supérieure du fichier score.py pour activer la fonctionnalité de collecte de données et ainsi permettre la collecte de l’entrée du modèle et des données de prédiction :

```python
from azureml.datacollector import ModelDataCollector
```

Pour plus d’informations sur l’utilisation de cette fonctionnalité, consultez la section [Collecte de données de modèle](how-to-use-model-data-collection.md).

#### <a name="init-function"></a>Fonction init
Utilisez la fonction init pour charger le modèle enregistré.

Exemple d’une fonction init simple chargeant le modèle :

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Fonction run
La fonction run utilise le modèle et les données d’entrée pour retourner une prédiction.

Exemple d’une fonction run simple traitant l’entrée et retournant le résultat de la prédiction :

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. Inscrivez un modèle
La commande suivante permet d’inscrire un modèle créé à l’étape 1 ci-dessus,

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Créer un manifeste
La commande suivante permet de créer un manifeste pour le modèle,

```
az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]
```
Vous pouvez ajouter au manifeste un modèle inscrit précédemment en utilisant l’argument `--model-id` ou `-i` dans la commande présentée ci-dessus. Plusieurs modèles peuvent être spécifiés avec des arguments -i supplémentaires.

### <a name="6-create-an-image"></a>6. Créer une image 
Vous pouvez créer une image avec la possibilité de créer préalablement son manifeste. 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

Vous pouvez aussi créer le manifeste et l’image avec une seule commande. 

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Pour plus d’informations sur les paramètres de la commande, tapez -h à la fin de la commande, par exemple az ml image create -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Créez et déployez le service web
Déployez le service à l’aide de la commande  suivante :

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Vous pouvez aussi utiliser une seule commande pour effectuer les quatre étapes précédentes. Pour plus d’informations, utilisez -h avec la commande service create.

### <a name="8-test-the-service"></a>8. Testez le service
Pour savoir comment appeler le service, utilisez la commande suivante :

```
az ml service usage realtime -i <service id>
```

Appelez le service en utilisant la fonction run à l’invite de commandes :

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

L’exemple suivant appelle un exemple de service web Iris :

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez testé votre service web pour une exécution locale, vous pouvez le déployer sur un cluster pour une utilisation à grande échelle. Pour plus d’informations sur la configuration d’un cluster en vue du déploiement d’un service web, consultez [Configuration de la gestion des modèles](deployment-setup-configuration.md). 
