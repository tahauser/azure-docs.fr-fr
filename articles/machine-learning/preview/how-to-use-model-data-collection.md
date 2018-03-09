---
title: "Utiliser la fonctionnalité de collecte des données de modèle dans Azure Machine Learning Workbench | Microsoft Docs"
description: "Cet article décrit comment utiliser la fonctionnalité de collecte des données de modèle dans Azure Machine Learning Workbench."
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 2251f8d241f3ec47d46c04160caf997bcd025124
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="collect-model-data-by-using-data-collection"></a>Collecter les données de modèle à l’aide d’une collecte de données

La fonctionnalité de collecte de données de modèle dans Azure Machine Learning vous permet d’archiver des entrées et des prédictions de modèle à partir d’un service web.

## <a name="install-the-data-collection-package"></a>Installer le package de collecte de données
Vous pouvez installer la bibliothèque de collecte de données en mode natif sous Linux et Windows.

### <a name="windows"></a>Windows
Sous Windows, installez le module du collecteur de données à l’aide de la commande suivante :

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Sur Linux, installez tout d’abord la bibliothèque libxml++. Exécutez la commande suivante sous sudo :

    sudo apt-get install libxml++2.6-2v5

Exécutez ensuite la commande suivante :

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Définition des variables d'environnement

La collecte de données de modèle dépend de deux variables d’environnement. AML_MODEL_DC_STORAGE_ENABLED doit avoir la valeur **true** (tout en minuscules) et AML_MODEL_DC_STORAGE doit avoir comme valeur la chaîne de connexion pour le compte de stockage Azure où vous souhaitez stocker les données.

Ces variables d’environnement sont déjà définies pour vous quand le service web est en cours d’exécution sur un cluster dans Azure. En cas d’exécution locale, vous devez les définir vous-même. Si vous utilisez Docker, utilisez le paramètre -e de la commande docker run pour passer les variables d’environnement.

## <a name="collect-data"></a>Collecter les données

Pour pouvoir utiliser la collecte de données de modèle, vous devez apporter les modifications suivantes à votre fichier de notation :

1. Ajoutez le code suivant au début du fichier :
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Ajoutez les lignes de code suivantes à la fonction `init()` :
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Ajoutez les lignes de code suivantes à la fonction `run(input_df)` :
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Assurez-vous que les variables `input_df` et `pred` (valeur de prédiction provenant de `model.predict()`) sont initialisées avant d’appeler la fonction `collect()` sur ces variables.

4. Utilisez la commande `az ml service create realtime` avec le commutateur `--collect-model-data true` pour créer un service web en temps réel. Cette étape permet de s’assurer que les données de modèle sont collectées quand le service est en cours d’exécution.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Pour tester la collecte de données, exécutez la commande `az ml service run realtime` :

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Afficher les données collectées
Pour afficher les données collectées dans le stockage d’objets blob :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**.
3. Dans la zone de recherche, saisissez **Comptes de stockage**, puis appuyez sur la touche Entrée.
4. Dans le panneau de recherche **Comptes de stockage**, sélectionnez la ressource **Compte de stockage**. Pour déterminer votre compte de stockage, utilisez les étapes suivantes :

    a. Accédez à Azure Machine Learning Workbench, sélectionnez le projet sur lequel vous travaillez, puis ouvrez une invite de commande à partir du menu **Fichier**.
    
    b. Saisissez `az ml env show -v` et vérifiez la valeur de *storage_account*. Il s’agit du nom de votre compte de stockage.

5. Sélectionnez **Conteneurs** dans le menu du panneau de ressources, puis le conteneur appelé **modeldata**. Pour que les données commencent à s’afficher dans le compte de stockage, vous devrez peut-être patienter jusqu'à 10 minutes après l’envoi de la première requête de service web. Les données sont transmises dans les objets blob via le chemin de conteneur suivant :

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Les données peuvent être utilisées à partir d’objets blob Azure de différentes façons, via des logiciels Microsoft et des outils open source. Voici quelques exemples :
- Azure Machine Learning Workbench : ouvrir le fichier .csv dans Azure Machine Learning Workbench en l’ajoutant comme source de données.
- Excel : ouvrir les fichiers .csv quotidiens sous la forme d’une feuille de calcul.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/) : créer des graphiques avec les données extraites des données .csv d’objets blob.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview) : créer un cadre de données avec une grande partie des données .csv.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) : charger les données .csv dans une table Hive et effectuer des requêtes SQL directement sur l’objet blob.

