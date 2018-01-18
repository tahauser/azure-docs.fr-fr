---
title: "L’apprentissage profond pour des scénarios réels de maintenance prédictive - Azure | Microsoft Docs"
description: "Découvrez comment reproduire le didacticiel d’apprentissage profond pour la maintenance prédictive avec Azure Machine Learning Workbench."
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>L’apprentissage profond pour des scénarios réels de maintenance prédictive

L’apprentissage profond (« Deep Learning ») est l’une des tendances les plus populaires du Machine Learning. Il est utilisé dans de nombreux champs et pour de nombreuses applications, notamment les voitures autonomes, la reconnaissance vocale et la reconnaissance d’images, la robotique et la finance. Il s’agit d’un ensemble d’algorithmes qui s’inspirent de la forme du cerveau (réseaux neuronaux biologiques) et du Machine Learning. Les chercheurs en sciences cognitives désignent généralement l’apprentissage profond sous le terme de « réseaux neuronaux artificiels » (RNA).

La maintenance prédictive est également souvent utilisée. Dans ce domaine, différentes techniques sont mises en place pour déterminer l’état de l’équipement et prévoir le moment où une maintenance sera nécessaire. Parmi ses utilisations courantes, on peut citer la prédiction des défaillances, les diagnostics de défaillances (analyse des causes racines), la détection des défaillances, la classification des types de défaillances et la recommandation d’actions d’atténuation ou de maintenance après une défaillance.

Dans les scénarios de maintenance prédictive, des données sont collectées au fil du temps pour effectuer le monitoring de l’état de l’équipement. L’objectif est de trouver des modèles susceptibles d’aider à prévoir et, à terme, à empêcher les défaillances. Les réseaux [LSTM (« Long Short Term Memory »)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) représentent une méthode d’apprentissage profond particulièrement intéressante pour la maintenance prédictive. Ils sont performants quand il s’agit d’apprendre à partir de séquences. Des données de série chronologique peuvent être utilisées pour revenir plus loin en arrière afin de détecter des modèles de défaillance.

Dans ce didacticiel, nous allons créer un réseau LSTM pour le jeu de données et le scénario décrits sur la page [Maintenance prédictive](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3). Nous utiliserons le réseau pour prédire la vie utile restante de moteurs d’avions. Le modèle utilise des valeurs de capteurs d’avions simulés pour prévoir les défaillances futures d’un moteur d’avion. Grâce à ces prédictions, la maintenance peut être planifiée à l’avance de façon à éviter toute défaillance.

Ce didacticiel utilise la bibliothèque d’apprentissage profond [Keras](https://keras.io/) et le kit de ressources Microsoft Cognitive Toolkit [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras) comme back end.

Le référentiel GitHub public contenant les exemples de ce didacticiel se trouve à l’adresse [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance).

## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

Ce didacticiel prend l’exemple d’événements de fonctionnement-défaillance de moteurs d’avions simulés pour illustrer le processus de modélisation de la maintenance prédictive. 

L’hypothèse implicite des données de modélisation décrites ici est que la ressource suit un modèle de dégradation progressive. Ce modèle se reflète dans les mesures de capteur du bien. En examinant ces valeurs au fil du temps, l’algorithme de Machine Learning peut apprendre la relation entre les valeurs de capteur, leurs évolutions et les défaillances qui se sont produites. Cette relation est utilisée pour prédire les futures défaillances. 

Nous vous recommandons d’examiner le format de données et de suivre les trois étapes du modèle avant de remplacer les exemples de données par vos propres données.

## <a name="prerequisites"></a>Conditions préalables

- Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).
- Azure Machine Learning Workbench avec un espace de travail.
- Pour l’opérationnalisation du modèle : Azure Machine Learning Operationalization avec un environnement de déploiement local configuré et un [compte Gestion des modèles Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview).

## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :

1. Ouvrez Machine Learning Workbench.
2. Sur la page **Projets**, sélectionnez **+**, puis **Nouveau projet**.
3. Dans le volet **Créer un projet**, entrez les informations relatives au nouveau projet.
4. Dans la zone de recherche **Rechercher dans les modèles de projet**, entrez **Maintenance prédictive**, puis sélectionnez le modèle.
5. Sélectionnez **Créer**.

## <a name="prepare-the-notebook-server-computation-target"></a>Préparer la cible de calcul du serveur de notebooks

Sur votre ordinateur local, dans le menu **Fichier** de Machine Learning Workbench, sélectionnez soit **Ouvrir une invite de commandes** soit **Ouvrir PowerShell**. Dans la fenêtre d’invite de commandes de l’option choisie, exécutez les commandes suivantes :

`az ml experiment prepare --target docker --run-configuration docker`

Nous recommandons d’exécuter le serveur Notebook sur une [Machine virtuelle Science des données (DSVM) pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) standard DS4_V2. Une fois la DSVM configurée, exécutez les commandes suivantes pour préparer les images Docker :

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Une fois les images Docker préparées, ouvrez le serveur Jupyter Notebook. Pour ouvrir le serveur Jupyter Notebook, accédez à l’onglet **Notebooks** de Machine Learning Workbench ou démarrez un serveur sur navigateur : `az ml notebook start`.

Les Notebooks sont stockés dans le répertoire Code de l’environnement Jupyter. Exécutez ces Notebooks dans l’ordre, en commençant par Code\1_data_ingestion_and_preparation.ipynb.

Sélectionnez le noyau correspondant à [nom_projet]_Template [nom_connexion], puis sélectionnez **Définir le noyau**.

## <a name="data-description"></a>Description des données

Le modèle utilise trois jeux de données en entrée, dans les fichiers PM_train.txt, PM_test.txt et PM_truth.txt.

-  **Données d’apprentissage** : données de fonctionnement-défaillance du moteur d’avion. Les données d’apprentissage (PM_train.txt) se composent de plusieurs séries chronologiques multivariées, l’unité de temps étant le *cycle*. Elles comportent 21 relevés de capteur par cycle. 

    On peut supposer que les séries chronologiques sont toutes générées à partir de différents moteurs du même type et que les moteurs partent avec différents niveaux d’usure initiale et des variations de fabrication. Cette information n’est pas connue de l’utilisateur. 

    Dans ces données simulées, on suppose que le moteur fonctionne normalement au début de chaque série chronologique. Il commence à se dégrader à un moment donné pendant la série de cycles d’exploitation. La dégradation progresse et prend de l’ampleur. 

    Lorsqu’elle atteint un seuil prédéfini, le moteur est considéré comme trop dangereux pour continuer à fonctionner. Le dernier cycle de chaque série chronologique peut être considéré comme le point de défaillance du moteur correspondant.

-   **Données de test** : données de fonctionnement du moteur d’avion, sans les événements de défaillance. Les données de test (PM_test.txt) suivent le même schéma que les données d’apprentissage. La seule différence est qu’elles n’indiquent pas quand la défaillance se produit (la dernière période ne représente *pas* le point de défaillance). On ne connaît pas le nombre de cycles durant lesquels ce moteur pourra fonctionner avant une défaillance.

- **Les données de réalité** : informations réelles sur les cycles restants de chacun des moteurs des données de test. Les données de la réalité du terrain indiquent le nombre de cycles de travail restants pour les moteurs des données de test.

## <a name="scenario-structure"></a>Structure du scénario

Le contenu du scénario est accessible dans le [référentiel GitHub] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance). 

Dans le référentiel, un fichier [Lisez-moi] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) décrit les processus, de la préparation des données à la génération et à l’opérationnalisation du modèle. Les trois Notebooks Jupyter sont disponibles dans le dossier [Code] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) du référentiel. 

Décrivons à présent le flux de travail du scénario étape par étape.

### <a name="task-1-data-ingestion-and-preparation"></a>Tâche 1 : Ingestion et préparation des données

Le Notebook Jupyter Ingestion des données, dans Code/1_data_ingestion_and_preparation.ipnyb, charge les trois jeux de données d’entrée au format de la trame de données Pandas. Ensuite, il prépare les données pour la modélisation et effectue une première visualisation des données. Les données sont ensuite converties au format PySpark et stockées dans un conteneur de Stockage Blob Azure au sein de votre abonnement Azure en vue de leur utilisation dans la tâche de modélisation suivante.

### <a name="task-2-model-building-and-evaluation"></a>Tâche 2 : Création et évaluation d’un modèle

Le Notebook Jupyter Création d’un modèle, dans Code/2_model_building_and_evaluation.ipnyb, lit les jeux de données d’entraînement et de test PySpark dans le Stockage Blob. Ensuite, un réseau LSTM est généré avec les jeux de données d’apprentissage. Les performances du modèle sont mesurées sur le jeu de test. Le modèle résultant est sérialisé et stocké dans le contexte de calcul local en vue de son utilisation dans la tâche d’opérationnalisation.

### <a name="task-3-operationalization"></a>Tâche 3 : Opérationnalisation

Le Notebook Jupyter Opérationnalisation, dans Code/3_operationalization.ipnyb, utilise le modèle stocké pour générer les fonctions et le schéma requis pour appeler le modèle sur un service web hébergé par Azure. Il teste les fonctions, puis compresse les ressources d’opérationnalisation dans un fichier .zip.

Celui-ci contient les fichiers suivants :

- **modellstm.json** : fichier de définition de schéma pour le déploiement. 
- **lstmscore.py** : fonctions **init()** et **run()** requises par le service web Azure.
- **lstm.model** : répertoire de définition de modèle.

Le Notebook teste les fonctions avec la définition de modèle avant d’empaqueter les ressources d’opérationnalisation pour le déploiement. Des instructions de déploiement sont fournies à la fin du notebook.


## <a name="conclusion"></a>Conclusion

Ce didacticiel utilise un scénario simple dans lequel une seule source de données (les valeurs de capteur) est utilisée pour faire des prédictions. Dans des scénarios plus avancés de maintenance prédictive, comme le [Notebook R Guide de modélisation de la maintenance prédictive](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), il arriver que de nombreuses sources de données soient utilisées. Il peut s’agir d’enregistrements d’historiques de maintenance, de journaux des erreurs et de caractéristiques des ordinateurs et des opérateurs. Ces sources de données supplémentaires nécessitent parfois d’autres types de traitements pour être utilisées dans des réseaux d’apprentissage profond. Il est également important de paramétrer les modèles pour obtenir les paramètres adaptés, comme la taille de la fenêtre. 

Vous pouvez modifier les passages pertinents de ce scénario et essayer différents scénarios de problèmes, par exemple ceux qui sont décrits dans le [Guide de modélisation de la maintenance prédictive](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1), qui impliquent d’autres sources de données.


## <a name="references"></a>Références

- [Modèle de solution de maintenance prédictive](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [Guide de modélisation de maintenance prédictive](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [Guide de modélisation de maintenance prédictive - Bloc-notes Python](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [Maintenance prédictive avec PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

