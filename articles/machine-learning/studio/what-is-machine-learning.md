---
title: "Qu’est-ce que l’apprentissage automatique (Machine Learning) sur Azure ? | Microsoft Docs"
description: "Explique les concepts de base de l’apprentissage automatique dans le cloud, décrit à quoi il sert, et en définit les principaux termes."
keywords: "qu’est-ce que l’apprentissage automatique,termes de l’apprentissage automatique,prédictive,qu’est-ce que l’analyse prédictive,termes de l’apprentissage automatique"
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: eaee083e-eaa1-4408-838b-93e51423d159
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/23/2018
ms.author: cgronlun;tedway;olgali
ms.openlocfilehash: 2968a71141eb6e036bc8bff1bc620b978f3ffdd9
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="introduction-to-machine-learning-in-the-azure-cloud"></a>Présentation de Machine Learning dans le cloud Azure

## <a name="what-is-machine-learning"></a>L’apprentissage automatique - De quoi s’agit-il ?
Machine Learning est une technique de science des données qui permet aux ordinateurs d’utiliser des données existantes afin de prévoir les tendances, les résultats et les comportements futurs. Grâce à Machine Learning, les ordinateurs apprennent sans être explicitement programmés.

Les prévisions ou prédictions générées à partir de Machine Learning peuvent rendre les applications et les appareils plus intelligents. Lorsque vous faites vos achats en ligne, l’apprentissage automatique permet de recommander d’autres produits que vous êtes susceptible d’aimer, en fonction de ce que vous avez acheté. Lorsque vous utilisez votre carte de crédit, l’apprentissage automatique compare la transaction à une base de données de transactions et aide la banque à détecter des fraudes. Lorsque votre robot aspirateur nettoie une pièce, l’apprentissage automatique l’aide à déterminer si le travail est terminé.

Pour une vue d’ensemble rapide, visionnez la série de vidéos [Science des données pour les débutants](data-science-for-beginners-the-5-questions-data-science-answers.md). Sans utiliser de jargon ou de mathématiques, la série Science des données pour les débutants présente l’apprentissage automatique et décrit un modèle prédictif simple.

## <a name="what-is-machine-learning-in-the-microsoft-azure-cloud"></a>Présentation de l’apprentissage automatique dans le cloud Microsoft Azure
Azure Machine Learning est un service d’analyse prédictive sur le cloud qui permet de créer et de déployer rapidement des modèles prédictifs sous forme de solutions d’analyse.

Vous pouvez exploiter une bibliothèque d’algorithmes prêts à l’emploi, les utiliser pour créer des modèles sur un ordinateur connecté à Internet et déployer votre solution prédictive rapidement. Commencez avec des solutions et des exemples prêts à l’emploi dans la [galerie Azure AI](https://gallery.cortanaintelligence.com/).

![L’apprentissage automatique - De quoi s’agit-il ? Flux de travail de base pour configurer l'analyse prédictive dans Azure Machine Learning.](./media/what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Azure Machine Learning fournit non seulement des outils pour modéliser des analyses prédictives, mais également un service entièrement pris en charge, que vous pouvez utiliser pour déployer vos modèles prédictifs sous la forme de services web prêts à l’emploi.

## <a name="what-is-predictive-analytics"></a>Qu’est-ce qu’une analyse prédictive ?
L’analyse prédictive utilise des formules mathématiques, appelées algorithmes, qui analysent les données historiques ou actuelles afin d’identifier des schémas ou des tendances permettant de prévoir des événements futurs.

## <a name="tools-to-build-complete-machine-learning-solutions-in-the-cloud"></a>Outils de génération de solutions complètes d’apprentissage automatique sur le cloud
Azure Machine Learning propose tout ce dont vous avez besoin pour créer des solutions d’analyse prédictive complètes dans le cloud : une bibliothèque d’algorithmes volumineuse, un environnement pour générer des modèles et un outil pour déployer facilement votre modèle sous la forme d’un service web. Créez, testez, rendez opérationnels et gérez rapidement des modèles prédictifs.

### <a name="machine-learning-studio-create-predictive-models"></a>Machine Learning Studio : création de modèles prédictifs
Dans [Machine Learning Studio](what-is-ml-studio.md), vous pouvez créer rapidement des modèles prédictifs, en déplaçant les modules et en les connectant les uns aux autres. Vous pouvez essayer différentes combinaisons et les [tester gratuitement](https://studio.azureml.net/?selectAccess=true&o=2).

* Dans la [galerie Azure AI](gallery-how-to-use-contribute-publish.md), vous pouvez tester les solutions d’analyse créées par d’autres personnes ou créer la vôtre. Publiez vos questions ou commentaires sur vos expériences à l’intention de la communauté, ou partagez des liens vers des expériences via les réseaux sociaux tels que LinkedIn et Twitter.

  ![Essayez des expériences prédictives ou proposez la vôtre dans la galerie Azure AI](./media/what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)
* Utilisez une volumineuse bibliothèque d' [algorithmes et de modules Machine Learning](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) dans Machine Learning Studio afin de lancer vos modèles prédictifs. Faites votre choix parmi les exemples d’expérience, les packages R et Python, ainsi que les meilleurs algorithmes Microsoft tels que Xbox et Bing. Développez les modules Studio avec vos propres scripts personnalisés [R](extend-your-experiment-with-r.md) et [Python](execute-python-scripts.md).

  ![Qu’est-ce que l’analyse prédictive : exemple d’une expérience d’analyse prédictive dans Azure Machine Learning Studio](./media/what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

### <a name="operationalize-predictive-analytics-solutions-by-publishing-your-own"></a>Rendez des solutions d’analyse prédictive opérationnelles en publiant vos propres solutions
Les didacticiels suivants vous montrent comment faire fonctionner vos modèles d’analyse prédictive :

 * [Déployez des services web](publish-a-machine-learning-web-service.md)
 * [Reformez des modèles via les API](retrain-models-programmatically.md)
 * [Gérez les points de terminaison des services web](create-endpoint.md)
 * [Mettez à l’échelle un service web](scaling-webservice.md)
 * [Utilisez des services web](consume-web-services.md)

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
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/
