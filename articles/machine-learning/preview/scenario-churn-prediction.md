---
title: "Prédiction de l’attrition des clients à l’aide d’Azure Machine Learning | Microsoft Docs"
description: "Décrit comment effectuer l’analytique de l’attrition à l’aide d’Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 7db93786b71fb7876ae02fd8fd006a1e8e0c2271
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Prédiction de l’attrition des clients à l’aide d’Azure Machine Learning

En moyenne, la rétention des clients existants coûte cinq fois moins cher que la recherche de nouveaux clients. Cela explique pourquoi les responsables marketing cherchent souvent à évaluer la probabilité d’attrition de leurs clients et à mettre en place des actions appropriées pour minimiser le taux d’attrition.

L’objectif de cette solution est d’illustrer l’analytique prédictive de l’attrition à l’aide d’Azure Machine Learning Workbench. Cette solution fournit un modèle simple d’emploi qui permet de développer des pipelines de données prédictives de l’attrition pour les détaillants. Le modèle peut être appliqué à divers jeux de données et à des définitions différentes de l’attrition. L’exemple pratique a les objectifs suivants :

1. Expliquer l’utilisation des outils de préparation des données d’Azure Machine Learning Workbench pour nettoyer et ingérer les données de la relation client pour les besoins de l’analytique de l’attrition.

2. Effectuer la transformation des caractéristiques pour traiter des données hétérogènes bruyantes.

3. Intégrer des bibliothèques tierces (telles que `scikit-learn` et `azureml`) pour créer des classifieurs bayésiens et arborescents utilisés dans la prédiction de l’attrition.

4. Effectuer le déploiement.

## <a name="link-of-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie
Voici le lien vers le dépôt GitHub public où se trouve tout le code nécessaire :

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage
Les entreprises ont besoin d’une stratégie efficace pour gérer le problème de l’attrition des clients. L’attrition des clients concerne les clients qui arrêtent d’utiliser un service, ceux qui se tournent vers un service concurrent, ceux qui passent à un niveau de service inférieur et ceux qui réduisent leur engagement avec le service.

Dans ce cas d’usage, nous analysons des données provenant de l’entreprise de télécommunications française Orange pour identifier les clients susceptibles de quitter Orange à court terme. Cette analyse a pour but d’aider l’entreprise à améliorer le service et à créer des campagnes marketing personnalisées pour fidéliser ses clients.

Les entreprises de télécommunications font face à une forte concurrence. Beaucoup d’opérateurs perdent des clients postpayés à cause de l’attrition. Leur capacité à identifier avec précision la probabilité d’attrition des clients peut donc représenter un énorme avantage concurrentiel.

Voici quelques facteurs qui contribuent à l’attrition des clients dans le secteur des télécommunications :

* Expériences d’interruptions fréquentes du service
* Expériences de service client non satisfaisantes dans les agences ou en ligne
* Offres d’autres opérateurs concurrents (meilleur forfait famille, forfait de données, etc.).

Dans cette solution, nous allons utiliser un exemple concret de création d’un modèle prédictif de l’attrition des clients pour les entreprises de télécommunications.

## <a name="prerequisites"></a>Prérequis

* Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles)

* Une copie [d’Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installée selon les instructions du [guide d’installation et de démarrage rapide](./quickstart-installation.md) pour installer le programme et créer un espace de travail

* Pour l’opérationnalisation, nous vous recommandons d’avoir un moteur Docker installé et exécuté localement. Sinon, vous pouvez utiliser l’option cluster, mais tenez compte du fait que l’exécution d’un cluster Azure Container Service (ACS) peut être coûteuse.

* Cette solution suppose que vous exécutez Azure Machine Learning Workbench sur Windows 10 avec le moteur Docker installé localement. Si vous utilisez macOS, la procédure à suivre est en grande partie la même.

## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Prédiction de l’attrition des clients » et sélectionnez le modèle
5.  Cliquez sur **Créer**

## <a name="data-description"></a>Description des données

Le jeu de données utilisé dans la solution provient du SIDKDD 2009. Il s’agit du fichier `CATelcoCustomerChurnTrainingSample.csv` situé dans le dossier [`data`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data). Le jeu de données contient des données bruyantes hétérogènes (variables catégorielles/numériques) provenant de l’entreprise de télécommunications française Orange et rendues anonymes.

Les variables capturent diverses informations sur les clients, notamment des données démographiques, des statistiques sur les appels (par exemple, la durée moyenne et le taux d’échec des appels), des informations sur les contrats et des statistiques sur les réclamations. La variable de l’attrition (churn) est binaire (0 : pas d’attrition, 1 : attrition).

## <a name="scenario-structure"></a>Structure du scénario

La structure des dossiers est la suivante :

__data__ : contient le jeu de données utilisé dans la solution.  

__docs__ : contient tous les labos pratiques.

Pour mener à bien la solution, vous devez effectuer les exercices pratiques dans l’ordre suivant :
1. Préparation des données : le principal fichier associé à cette tâche dans le dossier data est `CATelcoCustomerChurnTrainingSample.csv`.
2. Modélisation et évaluation : le principal fichier associé à cette tâche dans le dossier racine est `CATelcoCustomerChurnModeling.py`.
3. Modélisation et évaluation sans .dprep : le principal fichier associé à cette tâche dans le dossier racine est `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Opérationnalisation : les fichiers principaux associés au déploiement sont le modèle (`model.pkl`) et `churn_schema_gen.py`

| Ordre| Nom de fichier | Fichiers associés |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | « data/CATelcoCustomerChurnTrainingSample.csv » |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | « CATelcoCustomerChurnModeling.py » |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | « CATelcoCustomerChurnModelingWithoutDprep.py » |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | « model.pkl »<br>« churn_schema_gen.py » |

Effectuez les labs dans l’ordre indiqué ci-dessus.

## <a name="conclusion"></a>Conclusion
Ce scénario concret vous a montré comment effectuer la prédiction de l’attrition à l’aide d’Azure Machine Learning Workbench. Nous avons d’abord nettoyé les données pour traiter les données hétérogènes bruyantes, puis effectué l’ingénierie des caractéristiques à l’aide des outils de préparation des données. Ensuite, nous avons utilisé des outils d’apprentissage automatique open source pour créer et évaluer un modèle de classification. Enfin, nous avons utilisé un conteneur docker local pour déployer le modèle et le rendre opérationnel en production.
