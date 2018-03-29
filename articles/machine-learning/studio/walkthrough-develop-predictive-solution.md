---
title: Solution prédictive des risques de crédit avec Machine Learning| Microsoft Docs
description: Guide pas à pas détaillé indiquant comment créer une solution d'analyse prédictive pour l'évaluation des risques de crédit dans Azure Machine Learning Studio.
keywords: risque de crédit, solution d’analyse prédictive, évaluation des risques
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/23/2017
ms.openlocfilehash: d8a9776d97f10c64f7c29fa32fb1187bd76af714
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Guide pas à pas : développer une solution d'analyse prédictive pour l'évaluation des risques de crédit dans Azure Machine Learning

Dans cette procédure détaillée, nous étudierons de manière approfondie le processus de développement d’une solution d’analyse prédictive dans Machine Learning Studio. Nous allons développer un modèle simple dans Machine Learning Studio, puis le déployer dans le service web Azure Machine Learning, où le modèle peut effectuer des prévisions à l’aide de nouvelles données. 

Cette procédure détaillée suppose que vous avez utilisé Machine Learning Studio au moins une fois auparavant et que vous comprenez les concepts de Machine Learning. Il ne suppose pas non plus que vous êtes un expert.

Si vous n’avez jamais utilisé **Azure Machine Learning Studio** auparavant, commencez par le didacticiel [Création de votre première expérience de science des données dans Azure Machine Learning Studio](create-experiment.md). Ce didacticiel vous accompagne lors de votre première utilisation de Machine Learning Studio. Vous y découvrirez les principes fondamentaux de glisser-déplacer des modules vers votre expérience, et comment les connecter, réaliser votre expérience et étudier les résultats. Un autre outil qui peut être utile pour la prise en main est un diagramme qui donne une vue d’ensemble des fonctionnalités de Machine Learning Studio. Vous pouvez télécharger le document et l’imprimer ici : [Diagramme de vue d’ensemble des fonctionnalités d’Azure Machine Learning Studio](studio-overview-diagram.md).
 
Si vous êtes novice dans le domaine de l’apprentissage automatique en général, voici une série de vidéos qui peuvent s’avérer utiles pour vous. Il s’agit de la [Science des données pour les débutants](data-science-for-beginners-the-5-questions-data-science-answers.md), une excellente introduction à l’apprentissage automatique, qui utilise le langage et les concepts courants.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>Le problème

Supposons que vous deviez prédire le risque lié à l'octroi d'un crédit à un individu sur la base des informations fournies lors d'une demande de crédit.  

L’évaluation du risque de crédit est un problème complexe, mais nous pouvons le simplifier un peu pour cette procédure pas à pas. Nous allons l’utiliser comme exemple de création d’une solution d’analyse prédictive à l’aide de Microsoft Azure Machine Learning. Pour ce faire, nous utilisons Machine Learning Studio et un service web Machine Learning.  

## <a name="the-solution"></a>La solution

Dans cette procédure pas à pas détaillée, nous démarrons avec des données de risque crédit disponibles publiquement, puis développons et formons un modèle prédictif basé sur ces données. Nous déployons ensuite le modèle comme un service web afin qu’il puisse être utilisé par d’autres pour l’évaluation du risque de crédit.

Pour créer cette solution d'évaluation des risques de crédit, nous suivons ces étapes :  

1. [Créer un espace de travail Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Télécharger des données existantes](walkthrough-2-upload-data.md)
3. [Création d'une expérience](walkthrough-3-create-new-experiment.md)
4. [Former et évaluer les modèles](walkthrough-4-train-and-evaluate-models.md)
5. [Déployer le service web](walkthrough-5-publish-web-service.md)
6. [Accéder au service web](walkthrough-6-access-web-service.md)

> [!TIP] 
> Vous pouvez obtenir une copie de travail de l’expérience que nous développons lors de cette procédure pas à pas dans la [galerie Azure AI](https://gallery.cortanaintelligence.com). Accédez à **[Procédure pas à pas - Prédiction du risque de crédit](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)** et cliquez sur **Ouvrir dans Studio** pour télécharger une copie de l’expérience dans votre espace de travail Machine Learning Studio.
> 
> Cette procédure pas à pas est basée sur une version simplifiée de l’exemple d’expérience [Classification binaire : prédiction du risque de crédit](http://go.microsoft.com/fwlink/?LinkID=525270) également disponible dans la [Galerie](http://gallery.cortanaintelligence.com/).
