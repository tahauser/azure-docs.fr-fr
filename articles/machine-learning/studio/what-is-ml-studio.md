---
title: Azure Machine Learning Studio - De quoi s'agit-il ? | Microsoft Docs
description: Vue d'ensemble d'Azure ML Studio, un outil de glisser-déplacer pour créer rapidement des modèles à partir d'une bibliothèque prête à l'emploi d'algorithmes et de modules.
keywords: azure machine learning,azure ml, ml studio
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/20/2017
ms.openlocfilehash: 17a95ab00ed7bbda85a8fba5456c758f3ee9f116
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning Studio - De quoi s'agit-il ?
Microsoft Azure Machine Learning Studio est un outil collaboratif fonctionnant par glisser-déplacer qui vous permet de générer, tester et déployer des solutions d'analyse prédictive à partir de vos données. Machine Learning Studio publie des modèles en tant que services web pouvant facilement être consommés par des applications personnalisées ou des outils décisionnels tels qu'Excel.

Machine Learning Studio : là où convergent votre connaissance des données, l'analyse prédictive, les ressources cloud et vos données.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>Espace de travail interactif de Machine Learning Studio
Pour développer un modèle d'analyse prédictive, vous utilisez généralement des données d'une ou plusieurs sources que vous transformez et analysez par diverses manipulations et fonctions statistiques. Vous créez ensuite un ensemble de résultats. Le développement d'un modèle de ce type est un processus itératif. Quand vous modifiez les diverses fonctions et leurs paramètres, vos résultats convergent jusqu'à ce que l'efficacité du modèle formé vous donne satisfaction.

**Azure Machine Learning Studio** offre un espace de travail visuel et interactif qui vous permet de générer, tester et répéter facilement un modèle d'analyse prédictive. Vous faites glisser des ***jeux de données*** et des ***modules*** d’analyse sur un canevas interactif, en les connectant ensemble pour former une ***expérience***, que vous exécutez dans Machine Learning Studio. Pour affiner votre modèle, vous modifiez l’expérience, enregistrez une copie si vous le souhaitez et l’exécutez de nouveau. Quand vous êtes prêt, vous pouvez convertir votre ***expérience de formation*** en une ***expérience prédictive***, puis la publier en tant que ***service web*** afin que votre modèle soit accessible à d’autres.

Aucune programmation n'est nécessaire : il suffit de visualiser la connexion des jeux de données et des modules pour construire votre modèle d'analyse prédictive.

> [!TIP]
> Pour télécharger et imprimer un diagramme offrant une vue d’ensemble des fonctionnalités de Machine Learning Studio, consultez [Diagramme de vue d’ensemble des fonctionnalités d’Azure Machine Learning Studio](studio-overview-diagram.md).
> 
> 

![Diagramme Azure ML Studio : créer des expériences, lire les données de nombreuses sources, écrire des données évaluées, écrire des modèles.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Prise en main de Machine Learning Studio
Quand vous ouvrez [Machine Learning Studio](https://studio.azureml.net) pour la première fois, la page **Accueil** apparaît. À partir de là, vous pouvez afficher la documentation, des vidéos, des webinaires et rechercher d’autres ressources précieuses.

Cliquez sur le menu supérieur gauche ![Menu](./media/what-is-ml-studio/menu.png) pour voir apparaître différentes options.

### <a name="cortana-intelligence"></a>Cortana Intelligence
Cliquez sur **Cortana Intelligence**. Vous êtes dirigé vers la page d’accueil de [Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite). Cortana Intelligence Suite est une suite de traitement du Big Data et d’analyse avancée entièrement gérée conçue pour convertir vos données en action intelligente. Consultez la page d’accueil de la suite pour découvrir une documentation complète, y compris les témoignages de clients.

### <a name="azure-machine-learning"></a>Azure Machine Learning
À ce stade, deux options s’offrent à vous : **Accueil**, la page qui s’est affichée au démarrage, et **Studio**.

Cliquez sur **Studio**. Vous êtes dirigé vers **Azure Machine Learning Studio**. Vous êtes invité à vous connecter à l’aide de votre compte Microsoft, ou de votre compte professionnel ou scolaire. Une fois que vous êtes connecté, les onglets suivants apparaissent sur la gauche :

* **PROJETS** - Collections d’expériences, de DataSets, de notebooks et d’autres ressources représentant un projet spécifique
* **EXPÉRIENCES** : expériences que vous avez créées et exécutées ou enregistrées comme brouillons
* **SERVICES WEB** : services que vous avez déployés à partir de vos expériences web
* **NOTEBOOKS** : notebooks Jupyter que vous avez créés
* **JEUX DE DONNÉES** : jeux de données que vous avez téléchargés dans Studio
* **MODÈLES FORMÉS** : modèles que vous avez formés dans les expériences, puis enregistrés dans Studio
* **PARAMÈTRES** : ensemble des paramètres que vous pouvez utiliser pour configurer votre compte et vos ressources.

### <a name="gallery"></a>Galerie
Cliquez sur **Galerie** pour accéder à la **[galerie Azure AI](http://gallery.cortanaintelligence.com/)**. La galerie est l’endroit où la communauté des développeurs et des chercheurs en science des données peut partager des solutions créées à l’aide des composants de Cortana Intelligence Suite.

Pour plus d’informations sur la galerie, voir [Partager et découvrir des solutions dans la galerie Azure AI](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Composants d'une expérience
Une expérience se compose de jeux de données qui fournissent des données aux modules d'analyse que vous connectez ensemble pour construire un modèle d'analyse prédictive. En particulier, une expérience valide a les caractéristiques suivantes :

* L'expérience comporte au moins un jeu de données et un module
* Il est possible de connecter des jeux de données uniquement à des modules
* Les modules peuvent être connectés à des jeux de données ou à d'autres modules
* Tous les ports d'entrée des modules doivent comporter une connexion au flux de données
* Tous les paramètres obligatoires de chaque module doivent être configurés

Vous pouvez créer une expérience à partir de zéro, ou utiliser un exemple d’expérience existante comme modèle. Pour plus d’informations, consultez [Copier des exemples d’expérience pour créer des expériences d’apprentissage automatique](sample-experiments.md).

Pour obtenir un exemple de création d'une expérience simple, consultez la rubrique [Création d'une expérience simple dans Azure Machine Learning Studio](create-experiment.md).

Pour une description plus complète de la création d'une solution d'analyse prédictive, consultez la rubrique [Développement d'une solution prédictive avec Azure Machine Learning](walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Groupes de données
Un jeu de données représente des données téléchargées dans Machine Learning Studio de façon à les utiliser dans la procédure de modélisation. Machine Learning Studio fournit divers exemples de jeux de données utilisables pour vos expériences ; vous pouvez télécharger vers le serveur d'autres jeux de données si vous en avez besoin. Voici quelques exemples de jeux de données fournis :

* **Données sur la quantité de litres au 100 pour différents véhicules automobiles** : valeurs de quantité de litres au 100 pour des automobiles identifiées par leur nombre de cylindres, leur puissance, etc.
* **Données sur le cancer du sein** : données de diagnostics sur le cancer du sein.
* **Données sur les feux de forêts** : tailles des incendies de forêts au nord-est du Portugal.

Lorsque vous créez une expérience, vous pouvez utiliser la liste des jeux de données à gauche du canevas.

Pour obtenir une liste des exemples de jeux de données inclus dans Machine Learning Studio, consultez [Utilisation des exemples de jeux de données dans Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Modules
Un module est un algorithme que vous appliquez à vos données. Machine Learning Studio comporte divers modules, allant de fonctions de saisie des données à des procédures de formation, de notation et de validation. Voici quelques exemples de modules fournis :

* [Conversion au format ARFF][convert-to-arff] : convertit un jeu de données sérialisé .NET au format ARFF (Attribute-Relation File Format).
* [Statistiques de calcul élémentaires][elementary-statistics] : calcule des statistiques élémentaires (par exemple, moyenne, écart type, etc.).
* [Régression linéaire][linear-regression] : crée un modèle de régression linéaire à gradient décroissant en ligne.
* [Noter le modèle][score-model] : note un modèle de classification ou de régression formé.

Lorsque vous créez une expérience, vous pouvez utiliser la liste des modules à gauche du canevas.  

Un module peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du module. Quand vous sélectionnez un module dans le canevas, ses paramètres sont affichés dans le volet **Propriétés** à droite du canevas. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle.

Pour obtenir de l’aide sur la navigation dans la vaste bibliothèque d’algorithmes disponibles dans Machine Learning, consultez [Comment choisir les algorithmes dans Microsoft Azure Machine Learning](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Déploiement d'un service web d'analyse prédictive
Une fois votre modèle d'analyse prédictive prêt, vous pouvez le déployer comme un service web directement à partir de Machine Learning Studio. Pour plus de détails sur ce processus, consultez [Déploiement d’un service web Azure Machine Learning](publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
