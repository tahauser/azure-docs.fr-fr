---
title: "Maintenance prédictive pour scénarios réels | Microsoft Docs"
description: "Maintenance prédictive pour scénarios à l’aide de PySpark"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 81e227194ff64d7b7af842a208349ccc63528ab8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Maintenance prédictive pour scénarios réels

L’impact des temps d’arrêt non planifiés de l’équipement peut nuire aux entreprises. Il est essentiel de maintenir les équipements opérationnels afin d’optimiser les performances et l’utilisation, tout en réduisant les temps d’arrêt non planifiés et coûteux. Cette identification précoce des problèmes peut aider à allouer des ressources de maintenance limitées de façon économique et à améliorer la qualité et les processus de la chaîne d’approvisionnement. 

Ce scénario utilise un [jeu de données simulé relativement important](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) pour guider l’utilisateur à travers les étapes d’un projet de science des données au service de la maintenance prédictive. Il aborde l’ingestion des données, l’ingénierie des fonctionnalités, la création d’un modèle, mais aussi l’opérationnalisation et le déploiement de ce modèle. Le code de l’ensemble du processus est écrit dans le notebook Jupyter à l’aide de PySpark, dans Azure Machine Learning Workbench. Le modèle définitif est déployé à l’aide de la Gestion des modèles Azure Machine Learning pour effectuer des prédictions en temps réel sur les défaillances de l’équipement.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Référentiel GitHub de la galerie Cortana Intelligence

Le didacticiel Galerie Cortana Intelligence pour la maintenance prédictive est un référentiel GitHub public ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) dans lequel vous pouvez signaler des problèmes et apporter des contributions.


## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

L’un des problèmes majeurs auxquels sont confrontées les entreprises des secteurs gourmands en ressources n’est autre que les coûts importants associés aux retards dus à des problèmes mécaniques. La plupart des entreprises aimeraient être en mesure de prédire à quel moment ces problèmes sont susceptibles de survenir pour éviter de façon proactive qu’ils ne se produisent. Cela permet d’alléger les coûts en réduisant les temps d’arrêt et, dans certains cas, d’améliorer la sécurité. 

Ce scénario s’appuie sur le [manuel de maintenance prédictive](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) afin d’illustrer la création d’un modèle prédictif pour un jeu de données simulé. Les données fournies à titre d’exemple proviennent d’éléments habituellement observés dans de nombreux cas d’utilisation de maintenance prédictive.

Le problème métier pour ces données simulées consiste à prédire les problèmes causés par les échecs de composants. La question qui se pose est donc la suivante : « *Quelle est la probabilité qu’une machine tombe en panne en raison d’un échec de composant* ? » Ce problème est représenté sous la forme d’un problème de classification multiclasse (plusieurs composants par machine). Un algorithme d’apprentissage automatique est utilisé pour créer le modèle prédictif. L’apprentissage du modèle est effectué à partir de données d’historique collectées sur des machines. Dans ce scénario, l’utilisateur suit différentes étapes d’implémentation du modèle dans l’environnement Machine Learning Workbench.

## <a name="prerequisites"></a>configuration requise

* Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).
* Une copie installée [d’Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Pour installer le programme et créer un espace de travail, consultez le [guide de démarrage rapide relatif à l’installation](./quickstart-installation.md).
* L’opérationnalisation d’Azure Machine Learning requiert un environnement de déploiement local et un [compte de gestion des modèles Azure Machine Learning](model-management-overview.md).

Cet exemple s’exécute sur n’importe quel contexte de calcul Machine Learning Workbench. Il est toutefois recommandé d’exécuter l’exemple avec au moins 16 Go de mémoire. Ce scénario a été conçu et testé sur une machine Windows 10 exécutant une [machine virtuelle de science des données (DVSM) pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) standard DS4_V2 distante.

L’opérationnalisation du modèle a été effectuée à l’aide de la version 0.1.0a22 de l’interface de ligne de commande Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Machine Learning Workbench.
2.  Sur la page **Projets**, sélectionnez **+**, puis **Nouveau projet**.
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet.
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Maintenance prédictive », puis sélectionnez le modèle **Maintenance prédictive**.
5.  Sélectionnez **Créer**.

## <a name="prepare-the-notebook-server-computation-target"></a>Préparer la cible de calcul du serveur de notebooks

Pour une exécution sur votre ordinateur local, dans le menu **Fichier** de Machine Learning Workbench, sélectionnez soit **Ouvrir une invite de commandes** soit **Ouvrir PowerShell CLI**. L’interface CLI vous permet d’accéder à vos services Azure à l’aide des commandes `az`. Tout d’abord, connectez-vous à votre compte Azure avec la commande suivante :

```
az login
``` 

Cette commande fournit une clé d’authentification que vous devez utiliser avec l’URL https:\\aka.ms\devicelogin. L’interface CLI patiente le temps du retour de l’opération de connexion de l’appareil, puis fournit des informations de connexion. Ensuite, si une variable locale [docker](https://www.docker.com/get-docker) est installée, préparez l’environnement Compute local avec la commande suivante :

```
az ml experiment prepare --target docker --run-configuration docker
```

Il est préférable d’exécuter une [machine virtuelle DSVM pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) afin de répondre aux exigences en termes de mémoire et d’espace disque. Une fois la machine virtuelle DSVM configurée, préparez l’environnement Docker à distance avec les deux commandes suivantes :

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Une fois connecté au conteneur Docker à distance, préparez l’environnement Compute Docker de la machine virtuelle DSVM à l’aide de la commande suivante : 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Dès que l’environnement Compute Docker est prêt, ouvrez le serveur de notebooks Jupyter dans l’onglet **Notebooks** d’Azure Machine Learning Workbench ou exécutez un serveur basé sur un navigateur avec la commande suivante : 

```
az ml notebook start
```

Les notebooks fournis à titre d’exemple sont stockés dans le répertoire Code. Ces notebooks sont configurés pour une exécution par ordre séquentiel, en commençant par le premier d’entre eux (Code\1_data_ingestion.ipynb). Lorsque vous ouvrez chaque notebook, vous êtes invité à sélectionner le noyau de calcul. Choisissez le noyau [Nom_Projet]_Modèle [Nom_Connexion] à exécuter sur la machine virtuelle DSVM précédemment configurée.

## <a name="data-description"></a>Description des données

Les [données simulées](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) sont constituées de cinq fichiers de valeurs séparées par une virgule (.csv). Utilisez les liens suivants pour obtenir des descriptions détaillées sur les jeux de données.

* [Machines](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv) : caractéristiques qui différencient chaque machine, telles que l’âge et le modèle.
* [Erreur](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv) : le journal des erreurs contient des erreurs sans rupture levées alors que l’ordinateur est toujours opérationnel. Ces erreurs ne sont pas considérées comme des échecs, même si elles peuvent prédire un événement d’échec futur. Les valeurs d’horodatage des erreurs sont arrondies à l’heure la plus proche étant donné que les données de télémétrie sont collectées toutes les heures.
* [Maintenance](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv) : le journal de maintenance contient à la fois les enregistrements de la maintenance planifiée et de la maintenance non planifiée. La maintenance planifiée correspond à l’inspection régulière des composants. Une maintenance non planifiée peut être ordonnée à la suite d’une panne mécanique ou d’une dégradation des performances. Les valeurs d’horodatage de la maintenance sont arrondies à l’heure la plus proche étant donné que les données de télémétrie sont collectées toutes les heures.
* [Télémétrie](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv) : les données de télémétrie sont des données chronologiques provenant de plusieurs capteurs au sein de chaque ordinateur. Elles sont enregistrées en effectuant la moyenne des valeurs des capteurs pour chaque intervalle d’une heure.
* [Échecs](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv) : les échecs correspondent aux remplacements de composants dans le journal de maintenance. Chaque enregistrement contient l’ID de machine, le type de composant et les date et heure de remplacement. Ces enregistrements sont utilisés pour créer les étiquettes Machine Learning que le modèle tente de prédire.

Pour télécharger les jeux de données brutes à partir du dépôt GitHub et créer les jeux de données PySpark pour cette analyse, consultez le scénario [d’ingestion des données](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) du notebook Jupyter dans le dossier Code.

## <a name="scenario-structure"></a>Structure du scénario
Le contenu pour le scénario est disponible dans le [dépôt GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Le fichier [Lisez-moi](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) présente le workflow qui englobe la préparation des données, la création d’un modèle, puis le déploiement d’une solution en production. Chaque étape de ce workflow est encapsulée dans un notebook Jupyter, dans le dossier [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) du référentiel.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) : ce notebook télécharge les cinq fichiers .csv d’entrée et procède à un nettoyage préliminaire des données et à leur visualisation. Ce notebook convertit chaque jeu de données au format PySpark, puis stocke les résultats dans un conteneur d’objets blob Azure en vue de leur utilisation dans le notebook d’ingénierie des fonctionnalités.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb) : les fonctionnalités du modèle sont construites à l’aide du jeu de données brutes provenant du Stockage Blob Azure, mais aussi à l’aide d’une approche chronologique standard pour les données de télémétrie, les erreurs et les données de maintenance. Le remplacement des composants liés à une défaillance entre dans la conception des étiquettes du modèle, l’objectif étant de décrire lequel des composants est en échec. Les données des fonctionnalités ainsi étiquetées sont enregistrées dans un objet blob Azure pour le notebook de génération du modèle.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb) : le notebook de génération du modèle utilise le jeu de données de fonctionnalités étiqueté et fractionne les données en jeux de données de formation et de développement avec l’horodatage correspondant. Le notebook est configuré sous la forme d’une expérience avec les modèles pyspark.ml.classification. Les données d’apprentissage sont vectorisées. L’utilisateur peut procéder à des essais avec un **DecisionTreeClassifier** ou un **RandomForestClassifier** pour manipuler les hyperparamètres de manière à trouver le modèle le plus performant. Les performances sont déterminées par l’évaluation de statistiques de mesure sur le jeu de données de développement. Ces statistiques sont reconsignées dans l’écran du temps d’exécution Machine Learning Workbench à des fins de suivi. À chaque exécution, le notebook enregistre le modèle résultant sur le disque local exécutant le noyau Jupyter Notebook. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb) : ce notebook utilise le dernier modèle enregistré sur le disque en local (noyau du notebook Jupyter) pour générer les composants servant au déploiement du modèle dans un service web Azure. Les ressources entièrement opérationnelles sont compressées dans le fichier o16n.zip stocké dans un autre conteneur d’objets blob Azure. Le fichier compressé contient les éléments suivants :

* **service_schema.json** : fichier de définition de schéma pour le déploiement. 
* **pdmscore.py** : fonctions **init()** et **run()** requises par le service web Azure.
* **pdmrfull.model** : répertoire de définition de modèle.
    
Le notebook teste les fonctions avec la définition de modèle avant d’empaqueter les ressources d’opérationnalisation pour le déploiement. Des instructions de déploiement sont fournies à la fin du notebook.

## <a name="conclusion"></a>Conclusion

Ce scénario offre une vue d’ensemble de la création d’une solution de maintenance prédictive de bout en bout à l’aide de PySpark dans l’environnement de notebooks Jupyter dans Machine Learning Workbench. Cet exemple de scénario détaille également le déploiement du modèle via l’environnement de Gestion des modèles Machine Learning pour réaliser des prédictions en temps réel sur les défaillances de l’équipement.

## <a name="references"></a>Références

Les références suivantes fournissent des exemples d’autres cas d’usage de la maintenance prédictive pour différentes plateformes :

* [Modèle de solution de maintenance prédictive](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide de modélisation de maintenance prédictive](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guide de modélisation de maintenance prédictive avec les services SQL R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guide de modélisation de maintenance prédictive - Bloc-notes Python](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Maintenance prédictive avec PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Apprentissage approfondi pour la maintenance prédictive](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Étapes suivantes

Machine Learning Workbench inclut d’autres exemples de scénarios qui illustrent des fonctionnalités supplémentaires du produit. 
