---
title: "Analyse des sentiments à l’aide de l’apprentissage profond dans Azure Machine Learning | Microsoft Docs"
description: "Décrit comment effectuer une analyse des sentiments à l’aide de l’apprentissage profond dans Azure ML Workbench."
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
ms.date: 01/20/2018
ms.author: miprasad
ms.openlocfilehash: 3b0a5bfc911f3edf91367cbf4fde907cbf98e114
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Analyse des sentiments à l’aide de l’apprentissage profond dans Azure Machine Learning

L’analyse des sentiments est une tâche bien connue dans le domaine du traitement du langage naturel. Elle a pour but de déterminer le sentiment d’un texte à partir d’un ensemble de données texte. L’objectif de cette solution est d’utiliser l’apprentissage profond pour prédire les sentiments en se basant sur des critiques de films.

La solution se trouve dans https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie

Suivez ce lien vers le dépôt GitHub public :

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

Avec l’explosion des données disponibles et la prolifération des appareils mobiles, les clients ont de multiples occasions d’exprimer leurs impressions et positions sur n’importe quel sujet et à toute heure. Cette opinion ou ce « sentiment » est souvent véhiculé par le biais des canaux sociaux sous différentes formes, comme les avis, les discussions, les partages, les mentions J’aime, les tweets, etc. Le sentiment peut être une donnée capitale pour les entreprises qui cherchent à améliorer leurs produits et services, prendre des décisions plus éclairées et mieux promouvoir leurs marques.

Pour tirer parti de l’analyse des sentiments, les entreprises doivent être capables d’explorer de vastes référentiels de données sociales non structurées et d’en dégager des insights actionnables. Dans cet exemple, nous allons créer des modèles d’apprentissage profond pour effectuer l’analyse des sentiments de critiques de films à l’aide d’AML Workbench

## <a name="prerequisites"></a>Prérequis

* Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).

* Une copie [d’Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installée selon les instructions du [guide d’installation et de démarrage rapide](./quickstart-installation.md) pour installer le programme et créer un espace de travail.

* Pour l’opérationnalisation, nous vous recommandons d’avoir un moteur Docker installé et exécuté localement. Sinon, vous pouvez utiliser l’option cluster. Sachez toutefois que l’exécution d’un service ACS (Azure Container Service) peut être coûteuse.

* Cette solution suppose que vous exécutez Azure Machine Learning Workbench sur Windows 10 avec le moteur Docker installé localement. Sur macOS, la procédure à suivre est en grande partie la même.

## <a name="data-description"></a>Description des données

Cet exemple utilise un petit jeu de données créé manuellement qui se trouve dans le [dossier data](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

La première colonne contient les critiques de films et la deuxième colonne affiche le sentiment associé (0 pour négatif et 1 pour positif). Ce jeu de données est simplement destiné à la démonstration. Pour obtenir des scores de sentiment fiables, vous devez généralement utiliser un jeu de données plus volumineux. Par exemple, la [classification des sentiments des critiques de films sur IMDB](https://keras.io/datasets/#datasets ) fournie par Keras porte sur un jeu de données regroupant 25 000 critiques de films d’IMDB, toutes étiquetées avec un sentiment (positif ou négatif). L’objectif de ce lab est de vous montrer comment effectuer une analyse des sentiments à l’aide de l’apprentissage profond dans AML Workbench.

## <a name="scenario-structure"></a>Structure du scénario

La structure des dossiers est la suivante :

1. Tout le code associé à l’analyse des sentiments avec AML Workbench se trouve dans le dossier racine.
2. data : contient le jeu de données utilisé dans la solution.
3. docs : contient tous les labos pratiques.

Pour mener à bien la solution, vous devez effectuer les exercices pratiques dans l’ordre suivant :

| Ordre| Nom de fichier | Fichiers associés |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | « data/sampleReviews.txt » |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | « SentimentExtraction.py » |
| 4 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | « Operationalization » |

## <a name="conclusion"></a>Conclusion

En conclusion, la solution présentée vous montre comment utiliser l’apprentissage profond pour effectuer une analyse des sentiments avec Azure Machine Learning Workbench. De plus, nous opérationnalisons également à l’aide de modèles HDF5.
