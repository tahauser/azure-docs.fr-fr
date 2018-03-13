---
title: "L’apprentissage profond pour des scénarios réels de maintenance prédictive - Azure | Microsoft Docs"
description: "Découvrez comment reproduire le didacticiel d’apprentissage profond pour la maintenance prédictive avec Azure Machine Learning Workbench."
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 022e629469745fceb4fb9355cb6259a144dda222
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>L’apprentissage profond pour des scénarios réels de maintenance prédictive

L’apprentissage profond, l’une des tendances les plus populaires de l’apprentissage machine, possède des applications dans plusieurs domaines, notamment :
- la robotique et les voitures sans pilote ;
- la reconnaissance vocale et la reconnaissance d’images ;
- les prévisions financières.

Également connues sous le nom de « réseaux de neurones profonds », ces méthodes sont inspirées des neurones individuels du cerveau (réseaux de neurones biologiques).

L’impact des temps d’arrêt non planifiés de l’équipement peut nuire aux entreprises. Il est essentiel de maintenir les équipements opérationnels afin d’optimiser les performances et l’utilisation, et de réduire les temps d’arrêt non planifiés et coûteux. Cette identification précoce des problèmes peut aider à allouer des ressources de maintenance limitées de façon économique et à améliorer la qualité et les processus de la chaîne d’approvisionnement. 

Une stratégie de maintenance prédictive utilise des méthodes d’apprentissage machine pour déterminer l’état de l’équipement, afin d’effectuer une maintenance de manière préemptive pour éviter les performances indésirables des machines. Dans le cadre de la maintenance prédictive, les données sont collectées dans le temps pour surveiller l’état de la machine avant d’être analysées pour rechercher des modèles de prédiction des défaillances. Les réseaux avec [mémoire à court et long termes](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) sont intéressants pour ce paramètre, car ils sont conçus pour apprendre à partir de séquences de données.

### <a name="cortana-intelligence-gallery-github-repository"></a>Référentiel GitHub de la galerie Cortana Intelligence

Le didacticiel Galerie Cortana Intelligence pour la maintenance prédictive est un référentiel GitHub public ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) dans lequel vous pouvez signaler des problèmes et apporter des contributions.


## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

Ce didacticiel prend l’exemple d’événements de fonctionnement-défaillance de moteurs d’avions simulés pour illustrer le processus de modélisation de la maintenance prédictive. Le scénario est décrit dans [Maintenance prédictive](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

L’hypothèse principale pour ce paramètre est la dégradation progressive du moteur tout au long de sa vie. La dégradation peut être détectée dans les mesures des capteurs du moteur. La maintenance prédictive tente de modéliser la relation entre les modifications dans ces valeurs de capteur et les défaillances passées. Le modèle peut alors prédire le moment où un moteur peut tomber en panne en fonction de l’état actuel des mesures des capteurs.

Ce scénario crée un réseau avec mémoire à court et long termes pour prédire la durée de vie restante des moteurs d’avion à l’aide de valeurs des capteurs passées. Le scénario utilise la bibliothèque [Keras](https://keras.io/) avec l’infrastructure d’apprentissage profond [Tensorflow](https://www.tensorflow.org/) comme moteur de calcul. Le scénario effectue l’apprentissage du LSTM avec un ensemble de moteurs et teste le réseau sur un ensemble de moteurs invisible.

## <a name="prerequisites"></a>configuration requise
- Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).
- Azure Machine Learning Workbench avec un espace de travail.
- Pour l’opérationnalisation du modèle : Azure Machine Learning Operationalization avec un environnement de déploiement local configuré et un [compte Gestion des modèles Azure Machine Learning](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :

1. Ouvrez Machine Learning Workbench.
2. Sur la page **Projets**, sélectionnez **+**, puis **Nouveau projet**.
3. Dans le volet **Créer un projet**, entrez les informations relatives au nouveau projet.
4. Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Maintenance prédictive », puis sélectionnez le modèle **Apprentissage profond pour le scénario de maintenance prédictive**.
5. Sélectionnez **Créer**.

## <a name="prepare-the-notebook-server-computation-target"></a>Préparer la cible de calcul du serveur de notebooks

Pour une exécution sur votre ordinateur local, dans le menu **Fichier** de Machine Learning Workbench, sélectionnez soit **Ouvrir une invite de commandes** soit **Ouvrir PowerShell CLI**. L’interface CLI vous permet d’accéder à vos services Azure à l’aide des commandes `az`. Tout d’abord, connectez-vous à votre compte Azure avec la commande suivante :

```
az login
``` 

Cette commande fournit une clé d’authentification que vous devez utiliser avec l’URL https:\\aka.ms\devicelogin. L’interface CLI patiente le temps du retour de l’opération de connexion de l’appareil, puis fournit des informations de connexion. Ensuite, si une variable locale [docker](https://www.docker.com/get-docker) est installée, préparez l’environnement Compute local avec la commande suivante :

```
az ml experiment prepare --target docker --run-configuration docker
```

Il est préférable d’exécuter une [machine virtuelle Science des données (DSVM) pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) afin de répondre aux exigences en termes de mémoire et d’espace disque. Une fois la machine virtuelle DSVM configurée, préparez l’environnement Docker à distance avec les deux commandes suivantes :

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Une fois connecté au conteneur Docker à distance, préparez l’environnement Compute Docker de la machine virtuelle DSVM à l’aide de la commande suivante : 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Dès que l’environnement Compute Docker est prêt, ouvrez le serveur de notebooks Jupyter dans l’onglet **Notebooks** de Machine Learning Workbench ou exécutez un serveur basé sur un navigateur avec la commande suivante : 

```
az ml notebook start
```

Les notebooks fournis à titre d’exemple sont stockés dans le répertoire Code. Ces notebooks sont configurés pour une exécution par ordre séquentiel, en commençant par le premier d’entre eux (Code\1_data_ingestion.ipynb). Lorsque vous ouvrez chaque notebook, vous êtes invité à sélectionner le noyau de calcul. Choisissez le noyau [Nom_Projet]_Modèle [Nom_Connexion] à exécuter sur la machine virtuelle DSVM précédemment configurée.

## <a name="data-description"></a>Description des données

Le modèle utilise trois jeux de données en entrée, dans les fichiers PM_train.txt, PM_test.txt et PM_truth.txt. 

- **Données d’apprentissage** : données de fonctionnement-défaillance du moteur d’avion. Les données d’apprentissage (PM_train.txt) se composent de plusieurs séries chronologiques multivariées, l’unité de temps étant le *cycle*. Elles comportent 21 relevés de capteur par cycle. 

    - Toutes les séries chronologiques sont générées à partir d’un moteur différent du même type. Tous les moteurs démarrent avec différents niveaux d’usure initiale et des variations de fabrication uniques. Cette information n’est pas connue de l’utilisateur. 

    - Dans ces données simulées, on suppose que le moteur fonctionne normalement au début de chaque série chronologique. Il commence à se dégrader à un moment donné pendant la série de cycles d’exploitation. La dégradation progresse et prend de l’ampleur. 

    - Lorsqu’elle atteint un seuil prédéfini, le moteur est considéré comme trop dangereux pour continuer à fonctionner. Le dernier cycle de chaque série chronologique est le point de défaillance de ce moteur.

- **Données de test** : données de fonctionnement du moteur d’avion, sans les événements de défaillance. Les données de test (PM_test.txt) suivent le même schéma que les données d’apprentissage. La seule différence est qu’elles n’indiquent pas quand la défaillance se produit (la dernière période ne représente *pas* le point de défaillance). On ne connaît pas le nombre de cycles durant lesquels ce moteur pourra fonctionner avant une défaillance.

- **Les données de réalité** : informations réelles sur les cycles restants de chacun des moteurs des données de test. Les données de la réalité du terrain indiquent le nombre de cycles de travail restants pour les moteurs des données de test.

## <a name="scenario-structure"></a>Structure du scénario

Le flux de travail du scénario est divisé en trois étapes, chacune étant exécutée dans un bloc-notes Jupyter. Chaque bloc-notes génère des artefacts de données qui sont rendus persistants localement pour une utilisation dans les blocs-notes.

### <a name="task-1-data-ingestion-and-preparation"></a>Tâche 1 : Ingestion et préparation des données

Le Notebook Jupyter Ingestion des données, dans Code/1_data_ingestion_and_preparation.ipnyb, charge les trois jeux de données d’entrée au format de la trame de données Pandas. Ensuite, il prépare les données pour la modélisation et effectue une première visualisation des données. Les jeux de données sont stockés en local dans le contexte de calcul pour une utilisation dans le Notebook Jupyter Création d’un modèle.

### <a name="task-2-model-building-and-evaluation"></a>Tâche 2 : Création et évaluation d’un modèle

Le Notebook Jupyter Création d’un modèle dans Code/2_model_building_and_evaluation.ipnyb lit les jeux de données d’apprentissage et de test à partir du disque et génère un réseau avec mémoire à court et long termes pour le jeu de données d’apprentissage. Les performances du modèle sont mesurées sur le jeu de données de test. Le modèle résultant est sérialisé et stocké dans le contexte de calcul local en vue de son utilisation dans la tâche d’opérationnalisation.

### <a name="task-3-operationalization"></a>Tâche 3 : Opérationnalisation

Le Notebook Jupyter Opérationnalisation, dans Code/3_operationalization.ipnyb, utilise le modèle stocké pour générer les fonctions et le schéma nécessaires pour appeler le modèle sur un service web hébergé par Azure. Il teste les fonctions, puis compresse les ressources dans le fichier LSTM_o16n.zip. Le fichier est alors chargé dans votre conteneur de stockage Azure en vue du déploiement.

Le fichier de déploiement LSTM_o16n.zip contient les artefacts suivants :

- **webservices_conda.yaml** : définit les packages Python requis pour exécuter le modèle avec mémoire à court et long termes sur la cible de déploiement.  
- **service_schema.JSON** : définit le schéma de données attendu par le modèle LSTM.   
- **lstmscore.py** : définit les fonctions que la cible de déploiement exécute pour évaluer de nouvelles données.    
- **modellstm.json** : définit l’architecture LSTM. Les fonctions lstmscore.py lisent l’architecture et le poids pour initialiser le modèle.
- **modellstm.h5** : définit le poids du modèle.
- **test_service.py** : un script de test qui appelle le point de terminaison de déploiement avec les enregistrements de données de test. 
- **PM_test_files.pkl** : le script test_service.py lit les données passées des moteurs à partir du fichier PM_test_files.pkl et envoie au service web suffisamment de cycles pour que la mémoire à court et long termes renvoie une probabilité de défaillance du moteur.

Le Notebook teste les fonctions avec la définition de modèle avant d’empaqueter les ressources d’opérationnalisation pour le déploiement. Des instructions pour configurer et tester le service web sont fournies à la fin du bloc-notes Code/3_operationalization.ipnyb.

## <a name="conclusion"></a>Conclusion

Ce didacticiel décrit un scénario simple faisant appel à des valeurs de capteur pour élaborer des prédictions. Des scénarios de maintenance prédictive plus avancés, tels que [Predictive Maintenance Modeling Guide R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) (Guide de modélisation de maintenance prédictive - Bloc-notes R), peuvent utiliser de nombreuses sources de données telles que des enregistrements de maintenance passés, des journaux d’erreurs et des fonctionnalités de machine. Ces sources de données supplémentaires nécessitent parfois d’autres types de traitements pour être utilisées avec l’apprentissage profond.


## <a name="references"></a>Références

Les références suivantes fournissent des exemples d’autres cas d’usage de la maintenance prédictive pour différentes plateformes :

* [Modèle de solution de maintenance prédictive](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide de modélisation de maintenance prédictive](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guide de modélisation de maintenance prédictive avec les services SQL R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guide de modélisation de maintenance prédictive - Bloc-notes Python](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Maintenance prédictive avec PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Scénarios réels de maintenance prédictive](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>Étapes suivantes

Machine Learning Workbench inclut d’autres exemples de scénarios qui illustrent des fonctionnalités supplémentaires du produit. 
