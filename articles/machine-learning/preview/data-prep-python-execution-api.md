---
title: "Guide d’utilisation détaillé de l’API d’exécution de la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit des détails sur l’exécution des sources de données conçues au préalable et des packages de préparation de données."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 36814d238aabd12e7cc6947809c135130002eb46
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Exécuter des sources de données et des packages de préparation des données à partir de Python

Pour utiliser des sources de données Azure Machine Learning ou un package de préparation des données Azure Machine Learning dans Python :

1. Accédez à l’onglet **Données** de votre projet.

2. Cliquez avec le bouton droit de la souris sur la source appropriée.

3. Choisissez l’option permettant de **générer le fichier de code d’accès aux données**.

Cette action crée un petit script Python qui exécute le package et renvoie un tableau de données.

## <a name="data-sources"></a>Sources de données

Le module `azureml.dataprep.datasource` contient une seule fonction pour exécuter une source de données et renvoyer un tableau de données : `load_datasource(path, secrets=None, spark=None)`.
- `path` est le chemin d’accès à la source de données (fichier .dsource).
- `secrets` est un dictionnaire facultatif qui mappe des clés à des secrets.
- `spark` est une valeur booléenne facultative qui spécifie s’il faut renvoyer un tableau de données Spark ou Pandas. Par défaut, Azure Machine Learning Workbench détermine le type de tableau de données à renvoyer lors de l’exécution selon le contexte.

## <a name="data-preparations-packages"></a>Packages de préparation des données

Le module `azureml.dataprep.package` contient trois fonctions qui exécutent un flux de données à partir d’un package de préparation des données.

### <a name="execution-functions"></a>Fonctions d’exécution

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` envoie le flux de données spécifié pour exécution, mais ne renvoie pas de tableau de données.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` exécute le flux de données spécifié et renvoie les résultats sous la forme d’un tableau de données.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` exécute le flux de données spécifié en fonction d’une source de données en mémoire et renvoie les résultats sous la forme d’un tableau de données. L’argument `user_config` est un objet dictionnaire qui mappe le chemin d’accès absolu d’une source de données (fichier .dsource) à une source de données en mémoire représentée sous la forme d’une liste de listes.

### <a name="common-arguments"></a>Arguments communs

- `package_path` est le chemin d’accès au package de préparation des données (fichier .dprep).
- `dataflow_idx` est l’index à partir de zéro du flux de données dans le package à exécuter. Si le flux de données spécifié fait référence à d’autres flux de données ou sources de données, ils sont également exécutés.
- `secrets` est un dictionnaire facultatif qui mappe des clés à des secrets.
- `spark` est une valeur booléenne facultative qui spécifie s’il faut renvoyer un tableau de données Spark ou Pandas. Par défaut, Workbench détermine le type de tableau de données à renvoyer lors de l’exécution selon le contexte.
