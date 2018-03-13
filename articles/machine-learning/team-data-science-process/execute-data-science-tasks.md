---
title: "Exécuter des tâches de science des données - Azure Machine Learning | Microsoft Docs"
description: "Explique comment un scientifique des données peut réaliser un projet de science des données de manière traçable et collaborative, avec gestion de versions."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 7f3bf3bb5743bfb64489188d1016fb18d4967f79
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Exécuter des tâches de science des données : exploration, modélisation et déploiement

Les tâches courantes de science des données incluent l’exploration, la modélisation et le déploiement. Cet article explique comment recourir aux utilitaires **IDEAR (Interactive Data Exploration, Analysis, and Reporting)** et **AMAR (Automated Modeling and Reporting)** pour effectuer plusieurs tâches courantes de science des données, telles que l’exploration des données, l’analyse des données et la création de rapports interactives, ainsi que la création de modèles. Il décrit également les options de déploiement d’un modèle sur un environnement de production à l’aide d’un éventail de kits de ressources et de plateformes de données, tels que les suivants :

- [Azure Machine Learning](../preview/index.yml)
- [SQL Server avec les services ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Exploration 

Un scientifique des données peut effectuer les tâches d’exploration et de création de rapports de plusieurs façons : à l’aide de bibliothèques et de packages disponibles pour Python (matplotlib, par exemple) ou de R (ggplot ou lattice, par exemple). Il peut personnaliser ce code en fonction des besoins d’exploration de données pour des scénarios spécifiques. Les contraintes liées aux données structurées diffèrent de celles liées aux données non structurées, telles que le texte ou les images. 

En outre, des produits tels qu’Azure Machine Learning Workbench fournissent une [préparation avancée des données](../preview/tutorial-bikeshare-dataprep.md) pour le tri et l’exploration des données, notamment en ce qui concerne la création de caractéristiques. L’utilisateur doit choisir les outils, bibliothèques et packages qui répondent le mieux à ses besoins. 

Le livrable à la fin de cette phase est un rapport d’exploration de données. Ce rapport doit fournir une vue très complète des données à utiliser pour la modélisation et une évaluation indiquant si les données sont appropriées pour passer à l’étape de modélisation. Les utilitaires TDSP (Team Data Science Process) indiqués dans les sections suivantes pour l’exploration, la modélisation et la création de rapports semi-automatiques fournissent également des rapports de modélisation et d’exploration de données standard. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Exploration des données, analyse et création de rapports interactives à l’aide de l’utilitaire IDEAR

Cet utilitaire R Markdown ou Python Notebook constitue un outil souple et interactif qui permet d’évaluer et d’explorer les jeux de données. Les utilisateurs peuvent rapidement générer des rapports à partir d’un jeu de données avec un minimum de codage. Ils peuvent exporter les résultats de l’exploration dans l’outil interactif vers un rapport final, que vous pouvez remettre aux clients ou utiliser pour prendre des décisions concernant les variables à inclure dans l’étape suivante de modélisation.

Pour l’instant, l’outil fonctionne uniquement sur les trames de données en mémoire. Un fichier YAML est nécessaire pour spécifier les paramètres du jeu de données à explorer. Pour plus d’informations, consultez [IDEAR dans Utilitaires de science des données TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modélisation

Il existe de nombreux kits de ressources et packages pour l’apprentissage de modèles dans une variété de langages. Les scientifiques des données sont libres d’utiliser ceux avec lesquels ils se sentent le mieux, l’important étant qu’ils tiennent compte des contraintes de précision et de latence liées aux cas d’usage d’entreprise et aux scénarios de production.

La section suivante montre comment utiliser un utilitaire TDSP basé sur R pour la modélisation semi-automatique. Cet utilitaire AMAR peut être utilisé pour générer des modèles de ligne de base rapidement, ainsi que les paramètres qui doivent être ajustés pour fournir un modèle plus performant.
La section suivante Gestion des modèles montre comment obtenir un système permettant d’inscrire et de gérer plusieurs modèles.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Apprentissage des modèles : modélisation et création de rapports à l’aide de l’utilitaire AMAR

[L’utilitaire AMAR (Automated Modeling And Reporting)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) est un outil personnalisable et semi-automatique qui permet de créer des modèles à l’aide d’un balayage hyperparamétrique, et de comparer la précision de ces modèles. 

Cet utilitaire de création de modèles est un fichier R Markdown qui peut être exécuté pour produire une sortie HTML autonome comprenant une table des matières qui facilite la navigation entre ses différentes sections. Trois algorithmes sont exécutés quand le fichier Markdown est exécuté (compilé) : la régression régularisée qui utilise le package glmnet, une forêt aléatoire qui utilise le package randomForest, et des arbres de boosting qui utilisent le package xgboost. Chacun de ces algorithmes génère un modèle formé. La précision de ces modèles est ensuite comparée et les tracés d’importance relative des caractéristiques sont créés. Deux utilitaires sont actuellement disponibles : un pour la classification binaire et l’autre pour la régression. Leurs principales différences se situent au niveau de la manière dont sont spécifiés les paramètres de contrôle et les métriques de précision pour ces tâches d’apprentissage automatique. 

Un fichier YAML permet de spécifier :

- L’entrée de données (une source SQL ou un fichier de données R) 
- La proportion de données utilisée pour l’apprentissage et celle utilisée pour le test
- Les algorithmes à exécuter 
- Les paramètres de contrôle choisis pour l’optimisation des modèles :
    - validation croisée 
    - amorçage
    - plis de validation croisée
- Les jeux d’hyperparamètres de chaque algorithme. 

Le nombre d’algorithmes, le nombre de plis pour l’optimisation, les hyperparamètres et le nombre de jeux d’hyperparamètres à balayer peuvent également être modifiés dans le fichier Yaml pour exécuter les modèles plus rapidement. Par exemple, ils peuvent être exécutés avec un nombre inférieur de plis de validation croisée ou un nombre inférieur de jeux de paramètres. Si cela est garanti, ils peuvent également être exécutés avec un plus grand nombre de plis de validation croisée ou un plus grand nombre de jeux de paramètres.

Pour plus d’informations, consultez [Utilitaire de modélisation et de création de rapports automatisées dans Utilitaires de science des données TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Gestion des modèles
Une fois que plusieurs modèles ont été générés, vous devez généralement vous doter d’un système qui vous permet de les inscrire et de les gérer. En règle générale, vous avez besoin d’une combinaison de scripts ou d’API et d’une base de données backend ou d’un système de gestion de versions. Voici quelques options que vous pouvez envisager pour ces tâches de gestion :

1. [Azure Machine Learning - Service de gestion des modèles](../preview/index.yml)
2. [ModelDB de MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL Server comme système de gestion de modèle](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> Déploiement

Le déploiement de production permet à un modèle de jouer un rôle actif dans une entreprise. Les prédictions issues d’un modèle déployé peuvent être utilisées pour les décisions d’entreprise.

### <a name="production-platforms"></a>Plateformes de production
Il existe différentes approches et plateformes pour mettre les modèles en production. Voici quelques options :


- [Déploiement du modèle dans Azure Machine Learning](../preview/model-management-overview.md)
- [Déploiement d’un modèle dans SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>
>
>Remarque : Avant de procéder au déploiement, vous devez vérifier que le niveau de la latence du modèle est suffisamment faible pour permettre l’utilisation de ce dernier dans l’environnement de production.
>

Des exemples sont disponibles dans des procédures pas à pas qui illustrent toutes les étapes de **scénarios spécifiques**. L’article [Exemples de procédures pas à pas](walkthroughs.md) les liste et les décrit brièvement, en les accompagnant de liens. Ces procédures illustrent comment combiner des outils et services locaux ou cloud dans un flux de travail ou un pipeline pour créer une application intelligente.

REMARQUE : Pour un déploiement à l’aide de Microsoft Azure Machine Learning Studio, consultez [Déploiement d’un service web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

### <a name="ab-testing"></a>Test A/B
Quand plusieurs modèles sont en production, il peut être utile d’effectuer un [test A/B](https://en.wikipedia.org/wiki/A/B_testing) pour comparer les performances des modèles. 

 
## <a name="next-steps"></a>Étapes suivantes

[Suivre la progression des projets de science des données](track-progress.md) montre comment un scientifique des données peut suivre la progression d’un projet de science des données.
 


