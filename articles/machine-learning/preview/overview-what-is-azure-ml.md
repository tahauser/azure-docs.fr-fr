---
title: Qu'est-ce que Azure Machine Learning ? | Microsoft Docs
description: Vue d’ensemble des services Expérimentation et Gestion des modèles Azure Machine Learning, une solution de science des données de bout en bout intégrée et destinée aux chercheurs de données professionnels pour développer, tester et déployer des applications d’analytique avancée à l’échelle du cloud.
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: e5716e3fc519c48aaea3ec17939d11008a1b1fd4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-azure-machine-learning"></a>Qu'est-ce que Azure Machine Learning ?

Azure Machine Learning est une solution d’analytique avancée et de science des données de bout en bout intégrée. Cette solution permet aux scientifiques des données de préparer des données, de développer des expérimentations et de déployer des modèles à l’échelle du cloud.

Les principaux composants d’Azure Machine Learning sont :
- Azure Machine Learning Workbench
- Service Expérimentation Azure Machine Learning
- Service Gestion des modèles Azure Machine Learning
- Bibliothèques Microsoft Machine Learning pour Apache Spark (bibliothèque MMLSpark)
- Visual Studio Code Tools pour IA

Ensemble, ces applications et services permettent d’accélérer considérablement le développement et le déploiement d’un projet de science des données. 

![Concepts Azure Machine Learning](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>Compatible open source

Azure Machine Learning prend entièrement en charge les technologies open source. Vous pouvez utiliser des dizaines de milliers de packages Python open source, comme les frameworks d’apprentissage automatique ci-dessous :

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Vous pouvez effectuer vos expérimentations dans les environnements gérés tels que les conteneurs Docker et les clusters Spark. Vous pouvez également utiliser un matériel avancé comme les [machines virtuelles prenant en charge GPU dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) pour accélérer votre exécution.

Azure Machine Learning repose sur les technologies open source suivantes :

- [Bloc-notes Jupyter](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Il inclut également les propres technologies open source de Microsoft, comme la [bibliothèque Microsoft Machine Learning pour Apache Spark](https://github.com/Azure/mmlspark) et Cognitive Toolkit.

En outre, vous bénéficiez également de certaines des technologies Machine Learning éprouvées les plus avancées développées par Microsoft et conçues pour résoudre les problèmes à grande échelle. Elles sont testées dans de nombreux produits Microsoft, par exemple :

- Windows
- Bing
- Xbox
- Office
- SQL Server

Voici quelques-unes des technologies Microsoft Machine Learning incluses dans Azure Machine Learning :

- [PROSE](https://microsoft.github.io/prose/) (Program Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench est constitué d’une application de bureau ainsi que d’outils en ligne de commande, et est pris en charge à la fois sur Windows et macOS. Il vous permet de gérer des solutions d’apprentissage automatique dans l’intégralité du cycle de vie de science des données :

- Ingestion et préparation des données
- Développement des modèles et gestion des expérimentations
- Déploiement des modèles dans différents environnements cibles

Voici les principales fonctionnalités proposées par Azure Machine Learning Workbench :

- Outil de préparation des données qui peut acquérir une logique de transformation des données par exemple.
- Abstraction de la source de données accessible par du code UX et Python.
- Kit SDK Python pour appeler des packages de préparation des données construits visuellement.
- Service Bloc-notes Jupyter intégré et UX client.
- Surveillance et gestion des expérimentations via des affichages de l’historique des exécutions.
- Contrôle d’accès en fonction du rôle qui permet le partage et la collaboration via Azure Active Directory.
- Instantanés de projets automatiques pour chaque exécution et gestion de versions explicite activée par l’intégration de Git native. 
- Intégration aux IDE Python populaires.

Pour plus d’informations, reportez-vous aux articles suivants :
- [Guide de l’utilisateur de préparation des données](data-prep-user-guide.md)
- [Utilisation de Git avec Azure Machine Learning](using-git-ml-project.md)
- [Utilisation du Bloc-notes Jupyter dans Azure Machine Learning](how-to-use-jupyter-notebooks.md)
- Itinérance et partage
- [Guide de l’historique des exécutions](how-to-use-run-history-model-metrics.md)
- [Intégration de l’IDE](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Service Expérimentation Azure Machine Learning
Le service Expérimentation gère l’exécution des expérimentations Machine Learning. Il prend également en charge Workbench en assurant la gestion de projets, l’intégration de Git, le contrôle d’accès, l’itinérance et le partage. 

Une configuration facile vous permet d’exécuter vos expérimentations sur diverses options d’environnement Compute :

- Natif local
- Conteneur Docker local
- Conteneur Docker sur une machine virtuelle distante
- Cluster Spark avec montée en charge dans Azure

Le service Expérimentation construit des environnements virtuels pour garantir que votre script peut être exécuté de manière isolée avec des résultats reproductibles. Il enregistre les informations de l’historique des exécutions et vous présente cet historique visuellement. Vous pouvez facilement sélectionner le meilleur modèle parmi vos exécutions d’expérimentations. 

Pour plus d’informations, reportez-vous à [Configuration du service d’une expérimentation](experimentation-service-configuration.md).

## <a name="azure-machine-learning-model-management-service"></a>Service Gestion des modèles Azure Machine Learning

Le service Gestion des modèles permet aux chercheurs de données et aux équipes des opérations de développement de déployer des modèles prédictifs dans un large éventail d’environnements. Le suivi du lignage et des versions de modèle est assuré depuis les exécutions d’apprentissage jusqu’aux déploiements. Les modèles sont stockés, inscrits et gérés dans le cloud. 

À l’aide de commandes CLI simples, vous pouvez mettre en conteneur vos modèle, scripts de notation et dépendances dans des images Docker. Ces images sont inscrites dans votre propre registre Docker hébergé dans Azure (Azure Container Registry). Elles peuvent être déployées de manière fiable sur les cibles suivantes :

- Ordinateurs locaux
- Serveurs locaux
- Cloud
- Appareils IoT Edge

Kubernetes exécuté dans ACS (Azure Container Service) est utilisé pour le déploiement avec montée en charge dans le cloud. Les données de télémétrie d’exécution du modèle sont capturées dans AppInsights pour une analyse visuelle. 

Pour plus d’informations sur le service Gestion des modèles, reportez-vous à [Présentation de la gestion de modèles](model-management-overview.md)


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Bibliothèque Microsoft Machine Learning pour Apache Spark

La bibliothèque [MMLSpark](https://github.com/Azure/mmlspark) (Microsoft Machine Learning pour Apache Spark) est un package Spark open source qui fournit des outils d’apprentissage profond et de science des données pour Apache Spark. Elle intègre [Spark Machine Learning Pipelines](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) à [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) et la bibliothèque [OpenCV](http://opencv.org/) bibliothèque. Elle vous permet de créer rapidement de puissants modèles analytiques et prédictifs hautement scalables pour les volumineux jeux de données de textes et d’images. Les fonctions clés sont notamment les suivantes :

- Ingérer facilement les images depuis HDFS dans Spark DataFrame
- Prétraiter les données d’image à l’aide de transformations à partir d’OpenCV
- Caractériser des images à l’aide de réseaux DNN préformés avec Microsoft Cognitive Toolkit 
- Utiliser des LSTM bidirectionnels préformés de Keras pour l’extraction de l’entité médicale
- Former des modèles de classification d’images basés sur des réseaux DNN sur des machines virtuelles GPU série N sur Azure
- Caractériser des données de texte au format libre à l’aide d’API pratiques sur les primitives dans SparkML via un transformateur unique
- Former facilement des modèles de classification et de régression via une caractérisation implicite des données
- Calculer un ensemble complet de métriques d’évaluation, notamment les métriques de chaque instance

Pour plus d’informations, reportez-vous à [Utilisation de MMLSpark dans Azure Machine Learning](how-to-use-mmlspark.md).

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools pour IA
Visual Studio Code Tools pour IA est une extension dans Visual Studio Code permettant de générer, tester et déployer des solutions IA et d’apprentissage profond. Il propose de nombreux points d’intégration à Azure Machine Learning, dont :
- Un affichage de l’historique des exécutions qui montre les performances des exécutions d’apprentissage et des métriques enregistrées.
- Une vue Galerie permettant aux utilisateurs de parcourir et de démarrer de nouveaux projets avec Microsoft Cognitive Toolkit, TensorFlow et un grand nombre d’autres frameworks d’apprentissage profond. 
- Un mode explorateur pour sélectionner des cibles de calcul pour les scripts à exécuter.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Quelles sont les options d’apprentissage automatique de Microsoft ?
En dehors d’Azure Machine Learning, il existe une grande variété d’options dans Azure pour générer, déployer et gérer des modèles d’apprentissage automatique. [Apprenez-en plus ici.](overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Installer et créer Azure Machine Learning](quickstart-installation.md)
