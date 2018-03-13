---
title: "Informations de référence sur l’API de collecte de données de modèle Azure Machine Learning | Microsoft Docs"
description: "Informations de référence sur l’API de collecte de données de modèle Azure Machine Learning."
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
ms.openlocfilehash: 2de5e8bc8880fea5e7f57c88590e32a9fbb60ac6
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Informations de référence sur l’API de collecte de données de modèle Azure Machine Learning

La collecte de données de modèle vous permet d’archiver des entrées et des prédictions de modèle à partir d’un service web d’apprentissage automatique. Consultez le [guide pratique sur la collecte des données de modèle](how-to-use-model-data-collection.md) pour savoir comment installer `azureml.datacollector` sur votre machine Windows ou Linux.

Dans ce guide de référence de l’API, nous allons suivre une approche pas à pas pour apprendre à collecter des entrées et des prédictions de modèle à partir d’un service web d’apprentissage automatique.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Configurer la collecte de données de modèle dans un environnement Azure ML Workbench

 Recherchez le fichier conda\_dependencies.yml dans votre projet sous le dossier aml_config, puis ajoutez le module azureml.datacollector dans la section pip de votre fichier conda\_dependencies.yml, comme indiqué ci-dessous. Notez qu’il s’agit seulement d’une sous-section extraite d’un fichier conda\_dependencies.yml complet :

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>Vous pouvez utiliser le module collecteur de données dans Azure ML Workbench en mode docker. Le mode local ne fonctionne pas dans tous les environnements.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Configurer la collecte de données de modèle dans le fichier de score

Dans le fichier de score utilisé pour l’opérationnalisation, importez le module collecteur de données et la classe ModelDataCollector :

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Instanciation du collecteur de données de modèle
Instanciez une nouvelle instance d’un ModelDataCollector :

dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

La classe et les paramètres sont décrits ci-après :

### <a name="class"></a>Classe
| NOM | Description |
|--------------------|--------------------|
| ModelDataCollector | Classe de l’espace de noms azureml.datacollector. Une instance de cette classe est utilisée pour collecter des données de modèle. Un fichier de score peut contenir plusieurs ModelDataCollector. Chaque instance doit être utilisée pour collecter des données dans un seul emplacement discret dans le fichier de score pour garantir la cohérence du schéma des données collectées (autrement dit, les entrées et les prédictions)|


### <a name="parameters"></a>parameters

| NOM | type | Description |
|-------------|------------|-------------------------|
| model_name | chaîne | Nom du modèle pour lequel des données sont collectées |
| identificateur | chaîne | Emplacement dans le code qui identifie ces données ( « RawInput » ou « Prediction ») |
| feature_names | liste de valeurs string | Liste des noms de caractéristiques qui forment l’en-tête csv fourni |
| model_management_account_id | chaîne | Identificateur du compte de gestion des modèles dans lequel ce modèle est stocké. Ce paramètre est renseigné automatiquement quand les modèles sont rendus opérationnels via AML |
| webservice_name | chaîne | Nom du service web dans lequel ce modèle est actuellement déployé. Ce paramètre est renseigné automatiquement quand les modèles sont rendus opérationnels via AML |
| model_id | chaîne | Identificateur unique de ce modèle dans le contexte d’un compte de gestion des modèles. Ce paramètre est renseigné automatiquement quand les modèles sont rendus opérationnels via AML |
| model_version | chaîne | Numéro de version de ce modèle dans le contexte d’un compte de gestion des modèles. Ce paramètre est renseigné automatiquement quand les modèles sont rendus opérationnels via AML |



 

## <a name="collecting-the-model-data"></a>Collecte des données de modèle

Vous pouvez collecter les données de modèle à l’aide d’une instance du ModelDataCollector créé plus haut.

    dc.collect(input_data, user_correlation_id="")

La méthode et les paramètres sont décrits ci-après :

### <a name="method"></a>Méthode
| NOM | Description |
|--------------------|--------------------|
| collect | Utilisée pour collecter les données pour une entrée ou une prédiction de modèle|


### <a name="parameters"></a>parameters

| NOM | type | Description |
|-------------|------------|-------------------------|
| input_data | plusieurs types | Données à collecter (types pris en charge : list, numpy.array, pandas.DataFrame, pyspark.sql.DataFrame). Pour les types dataframe, si un en-tête est fourni avec des noms de caractéristiques, ces informations sont incluses dans la destination des données (sans avoir à passer explicitement ces noms de caractéristiques au constructeur ModelDataCollector) |
| user_correlation_id | chaîne | ID de corrélation facultatif, qui peut être fourni par l’utilisateur pour mettre en corrélation cette prédiction |

