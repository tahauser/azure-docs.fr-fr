---
title: "Guide d’utilisation détaillé de l’opérationnalisation de la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit des détails sur l’exécution des sources de données conçues au préalable et des packages de préparation de données."
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparation-operationalization"></a>Opérationnalisation de la préparation des données 

## <a name="operationalization-scenario"></a>Scénario d’opérationnalisation

Voici les différents scénarios d’opérationnalisation de la préparation des données qu’un utilisateur peut rencontrer.

### <a name="develop-your-model-based-on-training-data"></a>Développer votre modèle en fonction des données d’apprentissage

Supposez que vous cherchez à créer et à déployer un service d’API de calcul de score en temps réel. La première étape consiste à développer un modèle pour calculer les scores en fonction d’un jeu de données d’apprentissage. La Préparation des données importe un échantillon de vos données d’apprentissage en tant que nouvelle source de données. Une fois les données préparées, le package DataPrep contient les étapes de préparation. À l’aide d’un compte d’expérimentation AzureML, l’utilisateur peut effectuer une itération sur un modèle d’apprentissage automatique afin de générer des étiquettes pour chaque entrée dans les données d’apprentissage.

Quand les caractéristiques des données doivent être mises à jour, l’utilisateur retourne au package DataPrep afin de préparer les données d’une nouvelle façon et d’enregistrer ces étapes. Ce processus d’itération de la génération de nouvelles caractéristiques et de modification de leur modèle d’apprentissage automatique se poursuit jusqu’à ce que le modèle calcule de manière précise les scores de leurs données de test. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>Déployer votre modèle sur le service de gestion de modèle Azure

Vous disposez maintenant de données client auxquelles vous souhaiteriez affecter un score en temps réel. À l’aide du service de gestion de modèle Azure, vous pouvez déployer ce modèle à partir de l’interface CLI Python d’AzureML en tant que service. Le service déployé expose un point de terminaison REST pour la réception des données client en temps réel et le retour des étiquettes de sortie correspondantes à partir de votre modèle formé.

Pour simplifier l’utilisation, le point de terminaison de modèle accepte les données au format JSON. L’entrée doit être une chaîne JSON représentant un tableau de données à deux dimensions, qui sera soumis à la transformation ReadJSONLiteral et converti en une source de données DataPrep. Le package de préparation des données créé pendant la phase d’expérimentation peut ensuite être exécuté sur les données JSON diffusés en continu. Le DataFrame résultant peut ensuite être passé au modèle formé afin de calculer les scores.

## <a name="read-json-literal-transformation"></a>Transformation ReadJsonLiteral

### <a name="description"></a>DESCRIPTION

À des fins d’opérationnalisation, la préparation des données contient une transformation **ReadJsonLiteral** pour exécuter une activité et générer un DataFrame Pandas ou Spark. Cette transformation prend exclusivement comme entrée un package de préparation des données existant et une source de données JSON. Elle est exposée par le biais de l’interface CLI Python de DataPrep.

### <a name="instructions"></a>Instructions

#### <a name="pythoncli"></a>Interface CLI Python

Ouvrez l’interface de ligne de commande à partir d’Azure Machine Learning Workbench (Fichier > Ouvrir l’invite de commandes).

Dans cet exemple, le package de préparation des données TrainingPreparationSteps sert à préparer les données et à ajouter la caractéristique de volume à chaque entrée.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` a les paramètres facultatifs suivants
 - `dataflow_idx` : spécifier l’index du flux de données souhaité à exécuter dans le package
 - `secrets` : dictionnaire de magasin des secrets (clé : secretId, valeur : secret stocké)
 - `spark` : fournir une session spark pour l’exécution de spark

#### <a name="input"></a>Entrée

Une entrée de tableau à deux dimensions (« tableau de tableaux »). Dans Python, l’entrée doit être une liste de listes. JSON n’ayant pas de type de date natif, tous les objets datetime.datetime Python seront convertis en chaînes de date au format ISO. Pour les points de terminaison REST, l’entrée doit être une chaîne littérale JSON représentant un tableau JSON à deux dimensions.

#### <a name="output"></a>Sortie

Un DataFrame Pandas ou Spark. Le type de DataFrame est déterminé par le framework sélectionné dans la configuration d’exécution (`.runconfig`). Le DataFrame résultant peut être passé comme entrée au modèle formé afin de calculer les scores.
