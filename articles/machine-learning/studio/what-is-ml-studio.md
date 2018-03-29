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
ms.date: 03/28/2018
ms.openlocfilehash: e7fb545b985968b4d9e5a516c812cbf594352e08
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
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

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
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



## <a name="key-machine-learning-terms-and-concepts"></a>Concepts clé et terminologie de l’apprentissage automatique
Les termes de l’apprentissage automatique peuvent prêter à confusion. Vous trouverez ici la définition des termes clés pour vous aider. Utilisez les commentaires suivants pour nous indiquer ceux dont vous souhaiteriez lire la définition.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Exploration des données, analyse descriptive et analyse prédictive

**exploration des données** désigne le processus de collecte des informations sur un jeu volumineux de données, souvent non structurées, afin d’y trouver des caractéristiques utiles pour une analyse ciblée.

**Data mining** fait référence à l'exploration automatisée des données.

**analyse descriptive** est le processus d'analyse d'un jeu de données afin de synthétiser ce qui s'est passé. La majeure partie des analyses commerciales, comme les rapports de ventes, les mesures du web et l’analyse de réseaux sociaux, est descriptive.

**analyse prédictive** est le processus de création de modèles à partir de données historiques ou actuelles afin de prévoir les futurs résultats.

### <a name="supervised-and-unsupervised-learning"></a>Apprentissage supervisé et non supervisé
 **apprentissage supervisé** sont formés avec des données étiquetées, c'est-à-dire des données composées d'exemples de réponses souhaitées. Par exemple, un modèle qui identifie l’utilisation frauduleuse d’une carte de crédit exploite un jeu de données qui contient des points de données indiquant des utilisations frauduleuses et valides connues. La plupart des apprentissages automatiques sont supervisés.

 L’**apprentissage non supervisé** est utilisé sur les données sans étiquette. L’objectif est alors de trouver des relations entre les données. Par exemple, vous voulez trouver des groupes de données démographiques de clients avec des habitudes d’achat similaires.

### <a name="model-training-and-evaluation"></a>Formation et évaluation du modèle
Un modèle d’apprentissage automatique est une abstraction de la question à laquelle vous essayez de répondre ou le résultat que vous souhaitez prédire. Les modèles sont formés et évalués à partir de données existantes.

#### <a name="training-data"></a>Données de formation
Lorsque vous formez un modèle, vous utilisez un jeu de données connu, puis vous adaptez le modèle en fonction des caractéristiques des données pour obtenir la réponse la plus précise. Dans Azure Machine Learning, un modèle est créé à partir d’un module d’algorithme qui traite les données d’apprentissage et à partir de modules fonctionnels, tels qu’un module d’évaluation.

Dans le cadre d’un apprentissage supervisé, si vous formez un modèle de détection des fraudes, vous utilisez un ensemble de transactions étiquetées comme frauduleuses ou valides. Vous fractionnez votre jeu de données de manière aléatoire et vous en utilisez une partie pour former le modèle et l’autre pour tester ou évaluer le modèle.

#### <a name="evaluation-data"></a>Données d’évaluation
Une fois que votre modèle est formé, évaluez-le en utilisant les autres données de test. Vous utilisez des données dont vous connaissez déjà les résultats afin de pouvoir déterminer si votre modèle prédit correctement.

## <a name="other-common-machine-learning-terms"></a>Autres termes courants relatifs à l’apprentissage automatique
* **Algorithme**: ensemble de règles utilisées pour résoudre les problèmes de traitement des données, de calcul mathématique ou de déduction automatisée.
* **Détection d’anomalies**: modèle qui signale les valeurs ou les événements hors normes et qui vous permet d’identifier les problèmes. Par exemple, la détection de fraudes à la carte de crédit consiste à rechercher les achats inhabituels.
* **Données catégorielles**: données organisées en catégories et pouvant être divisées en groupes. Par exemple, un jeu de données catégorielles relatif à des véhicules peut spécifier l’année, la marque, le modèle et le prix.
* **Classification**: modèle d'organisation des points de données en catégories basées sur un jeu de données dont les groupes de catégorie sont déjà connus.
* **Conception de caractéristiques**: processus d'extraction ou de sélection des caractéristiques liées à un jeu de données afin d'améliorer ce dernier et les résultats. Par exemple, les données relatives aux prix des billets d’avion peuvent être améliorées par jour de la semaine et par période de vacance. Consultez la page [Ingénierie et sélection de caractéristiques dans Azure Machine Learning](../team-data-science-process/create-features.md).
* **Module**: élément fonctionnel d’un modèle Machine Learning Studio, comme le module Entrer des données, qui permet d’entrer et de modifier de petits jeux de données. Un algorithme est également un type de module dans Machine Learning Studio.
* **Modèle**: dans le cadre d’un apprentissage supervisé, un modèle est le résultat d’une expérience d’apprentissage automatique, comprenant des données de formation, un module d’algorithme et des modules fonctionnels, comme un module d’évaluation.
* **Données numériques**: données qui ont une signification sous forme de mesures (données continues) ou de compteurs (données discrètes). Également appelées *données quantitatives*.
* **Partition**: méthode permettant de diviser les données en échantillons. Consultez la page [Partition et échantillon](https://msdn.microsoft.com/library/azure/dn905960.aspx) pour plus d'informations.
* **Prédiction**: prévision d'une valeur ou de plusieurs valeurs, à partir d'un modèle d'apprentissage automatique. On rencontre parfois le terme « note prédite ». Toutefois, les notes prédites ne sont pas le résultat final d’un modèle. L’évaluation du modèle suit la note.
* **Régression**: modèle permettant de prédire une valeur en fonction de variables indépendantes, comme la prédiction du prix d’une voiture en fonction de son année et de sa marque.
* **Note**: valeur prédite générée à partir d'un modèle formé de régression ou de classification, à l'aide du module [Noter le modèle](https://msdn.microsoft.com/library/azure/dn905995.aspx) dans Machine Learning Studio. Les modèles de classification retournent également une note pour la probabilité de la valeur prédite. Une fois que vous avez généré les notes à partir d'un modèle, vous pouvez évaluer la précision du modèle à l'aide du module [Évaluer le modèle](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **Échantillon**: partie d'un jeu de données destinée à être représentative de l'ensemble. Les échantillons peuvent être sélectionnés de manière aléatoire ou sur la base de fonctionnalités spécifiques du jeu de données.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez apprendre les principes fondamentaux de l’analyse prédictive et de l’apprentissage automatique à l’aide d’un [didacticiel](create-experiment.md) et avec la [génération à partir d’exemples](sample-experiments.md).  


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
