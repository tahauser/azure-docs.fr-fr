---
title: "Réglage distribué d’hyperparamètres à l’aide d’Azure Machine Learning Workbench | Microsoft Docs"
description: "Ce scénario montre comment effectuer un réglage distribué d’hyperparamètres à l’aide d’Azure Machine Learning Workbench"
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 467111978d43d35788276cf7a464496393e4599b
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Réglage distribué d’hyperparamètres à l’aide d’Azure Machine Learning Workbench

Ce scénario montre comment utiliser Azure Machine Learning Workbench pour effectuer une montée en puissance parallèle du réglage d’hyperparamètres d’algorithmes Machine Learning qui implémentent l’API scikit-learn. Nous expliquerons comment configurer et utiliser un conteneur Docker distant et un cluster Spark comme serveur principal d’exécution pour le réglage d’hyperparamètres.

## <a name="link-of-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie
Le lien vers le dépôt GitHub public est le suivant : 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

De nombreux algorithmes Machine Learning ont un ou plusieurs « boutons », appelés hyperparamètres. Ces boutons permettent le réglage d’algorithmes pour optimiser leurs performances sur de futures données, mesurées en fonction des indicateurs de performance spécifiés par l’utilisateur (par exemple, la précision, AUC, RMSE). Le scientifique des données doit fournir des valeurs d’hyperparamètres durant la création d’un modèle à partir des données d’apprentissage et avant de voir les futures données de test. Comment pouvons-nous, à partir des données d’apprentissage connues, définir les valeurs d’hyperparamètres de manière à ce que le modèle offre de bonnes performances sur les données de test inconnues ? 
    
Une technique courante de réglage d’hyperparamètres consiste à combiner une *recherche par grille* avec une *validation croisée*. La validation croisée est une technique qui évalue les performances de prédiction sur un jeu de test d’un modèle formé sur un jeu d’apprentissage. Avec cette technique, nous divisons d’abord le jeu de données en K plis, puis effectuons l’apprentissage de l’algorithme K fois, par tourniquet (round robin). Nous le faisons sur tous les plis sauf un, appelé pli récupéré. Nous calculons la valeur moyenne des indicateurs de performance de K modèles sur K plis récupérés. Cette valeur moyenne, appelée *estimation des performances de validation croisée*, dépend des valeurs d’hyperparamètres utilisées au cours de la création de K modèles. Durant le réglage d’hyperparamètres, nous recherchons dans l’espace de valeurs d’hyperparamètres candidates celles qui optimisent l’estimation des performances de validation croisée. La recherche par grille est une technique de recherche courante. Dans la recherche par grille, l’espace de valeurs candidates de plusieurs hyperparamètres est un produit croisé d’ensembles de valeurs candidates d’hyperparamètres individuels. 

La recherche par grille à l’aide de la validation croisée peut être longue. Si un algorithme comprend cinq hyperparamètres ayant chacun cinq valeurs candidates, nous utilisons K = 5 plis. Nous effectuons ensuite une recherche par grille en apprenant 5<sup>6</sup>= 15625 modèles. La recherche par grille à l’aide de la validation croisée est heureusement une procédure excessivement parallèle, ce qui permet un apprentissage en parallèle de tous ces modèles.

## <a name="prerequisites"></a>Prérequis

* Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).
* Une copie d’[Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installée conformément au [guide de démarrage rapide d’installation et de création](./quickstart-installation.md) pour installer Workbench et créer des comptes.
* Ce scénario part du principe que vous exécutez Azure ML Workbench sur Windows 10 ou MacOS avec le moteur Docker installé localement. 
* Pour exécuter le scénario avec un conteneur Docker distant, configurez la machine virtuelle de science des données (DSVM) Ubuntu en suivant ces [instructions](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Nous recommandons d’utiliser une machine virtuelle avec au moins 8 cœurs et 28 Go de mémoire. Les instances D4 de machines virtuelles ont cette capacité. 
* Pour exécuter ce scénario avec un cluster Spark, provisionnez le cluster HDInsight Spark en suivant ces [instructions](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Nous vous recommandons d’utiliser un cluster avec la configuration suivante dans les nœuds d’en-tête et worker :
    - quatre nœuds worker
    - huit cœurs
    - 28 Go de mémoire  
      
  Les instances D4 de machines virtuelles ont cette capacité. 

     **Résolution des problèmes** : votre abonnement Azure peut avoir un quota défini quant au nombre de cœurs pouvant être utilisés. Le portail Azure ne permet pas de créer un cluster avec un nombre total de cœurs dépassant ce quota. Pour connaître ce quota, dans le portail Azure, accédez à la section des abonnements, cliquez sur l’abonnement utilisé pour déployer un cluster, puis cliquez sur **Utilisation+quotas**. Les quotas sont généralement définis par région Azure. Vous pouvez choisir de déployer le cluster Spark dans une région dans laquelle vous disposez de cœurs libres suffisants. 

* Créez un compte de stockage Azure utilisé pour stocker le jeu de données. Suivez ces [instructions](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) pour créer un compte de stockage.

## <a name="data-description"></a>Description des données

Nous utilisons le [jeu de données TalkingData](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Ce jeu de données contient des événements provenant d’applications de téléphones cellulaires. L’objectif est de prédire le sexe et la tranche d’âge de l’utilisateur du téléphone cellulaire en fonction du type de téléphone et des événements récemment générés par l’utilisateur.  

## <a name="scenario-structure"></a>Structure du scénario
Les différents dossiers de ce scénario sont disponibles dans le dépôt GitHub. Les fichiers de configuration et de code se trouvent dans le dossier**Code**, tous les documents se trouvent dans le dossier **Docs** et toutes les images dans le dossier **Images**. Le dossier racine contient un fichier LISEZ-MOI qui inclut un bref résumé de ce scénario.

### <a name="getting-started"></a>Prise en main
Cliquez sur l’icône Azure Machine Learning Workbench pour exécuter Azure Machine Learning Workbench et créer un projet à partir du modèle « Réglage distribué d’hyperparamètres ». Pour des instructions détaillées sur la création d’un projet, consultez le [guide de démarrage rapide d’installation et de création](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Configuration d’environnements d’exécution
Nous expliquerons comment exécuter le code dans un conteneur Docker distant et dans un cluster Spark. Nous commencerons par la description des paramètres communs aux deux environnements. 

Nous utilisons les packages [scikit-learn](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost) et [azure-storage](https://pypi.python.org/pypi/azure-storage) qui ne sont pas fournis dans le conteneur Docker par défaut d’Azure Machine Learning Workbench. Le package de stockage Azure nécessite l’installation des packages [cryptography](https://pypi.python.org/pypi/cryptography) et [azure](https://pypi.python.org/pypi/azure). Pour installer ces packages dans le conteneur Docker et dans les nœuds du cluster Spark, nous modifions le fichier conda_dependencies.yml :

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

Le fichier conda\_dependencies.yml est stocké dans le répertoire aml_config du didacticiel. 

Dans les étapes suivantes, nous connectons l’environnement d’exécution au compte Azure. Cliquez sur le menu Fichier dans le coin supérieur gauche d’AML Workbench. Puis choisissez « Ouvrir l’invite de commandes ». Exécutez ensuite dans l’interface CLI

    az login

Vous obtenez le message

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Accédez à cette page web, entrez le code et connectez-vous à votre compte Azure. Après cette étape, exécutez dans l’interface CLI

    az account list -o table

et recherchez l’ID d’abonnement Azure de votre compte AML Workbench Workspace. Enfin, exécutez dans l’interface CLI

    az account set -s <subscription ID>

pour établir la connexion à votre abonnement Azure.

Dans les deux sections suivantes, nous expliquerons comment effectuer la configuration d’un conteneur Docker distant et d’un cluster Spark.

#### <a name="configuration-of-remote-docker-container"></a>Configuration d’un conteneur Docker distant

 Pour configurer un conteneur Docker distant, exécutez dans l’interface CLI

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

avec l’adresse IP, le nom d’utilisateur et le mot de passe de la machine virtuelle de science des données. L’adresse IP de la machine virtuelle de science des données est disponible dans la section Vue d’ensemble de la page de votre machine virtuelle de science des données sur le portail Azure :

![IP de machine virtuelle](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Configuration d’un cluster Spark

Pour configurer l’environnement Spark, exécutez dans l’interface CLI

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

avec le nom, le nom d’utilisateur SSH et le mot de passe du cluster. La valeur par défaut du nom d’utilisateur SSH est `sshuser`, sauf si vous l’avez modifiée durant la configuration du cluster. Le nom du cluster est disponible dans la section Propriétés de la page de votre cluster sur le portail Azure :

![Nom du cluster](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Nous utilisons le package spark-sklearn pour utiliser Spark comme environnement d’exécution pour le réglage distribué d’hyperparamètres. Nous avons modifié le fichier spark_dependencies.yml pour installer ce package quand l’environnement d’exécution Spark est utilisé :

    configuration: 
      #"spark.driver.cores": "8"
      #"spark.driver.memory": "5200m"
      #"spark.executor.instances": "128"
      #"spark.executor.memory": "5200m"  
      #"spark.executor.cores": "2"
  
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7.91"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

Le fichier spark\_dependencies.yml est stocké dans le répertoire aml_config du didacticiel. 

### <a name="data-ingestion"></a>Ingestion de données
Le code dans ce scénario suppose que les données sont stockées dans le stockage blob Azure. Nous expliquerons d’abord comment télécharger des données sur votre ordinateur à partir du site de Kaggle, puis les charger dans le stockage blob. Nous expliquerons ensuite comment lire les données à partir du stockage blob. 

Pour télécharger des données à partir du site de Kaggle, accédez à la [page du jeu de données](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) et cliquez sur le bouton Télécharger. Vous êtes invité à vous connecter à Kaggle. Une fois connecté, vous êtes redirigé vers la page du jeu de données. Téléchargez ensuite les sept premiers fichiers de la colonne de gauche en les sélectionnant et en cliquant sur le bouton Télécharger. La taille totale des fichiers téléchargés est de 289 Mo. Pour charger ces fichiers dans le stockage blob, créez un conteneur de stockage blob « dataset » dans votre compte de stockage. Pour ce faire, accédez à la page Azure de votre compte de stockage et cliquez sur Objets blob, puis sur +Conteneur. Nommez-le « dataset » et cliquez sur OK. Les captures d’écran suivantes illustrent ces étapes :

![Ouvrir l’objet blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![Ouvrir le conteneur](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Sélectionnez ensuite le conteneur du jeu de données dans la liste et cliquez sur le bouton Charger. Le portail Azure vous permet de charger plusieurs fichiers simultanément. Dans la section « Charger l’objet blob », cliquez sur le bouton de dossier, sélectionnez tous les fichiers du jeu de données et cliquez sur Ouvrir, puis sur Charger. La capture d’écran suivante illustre ces étapes :

![Charger l’objet blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Le chargement des fichiers prend plusieurs minutes, en fonction de votre connexion Internet. 

Dans notre code, nous utilisons le kit [SDK Stockage Azure](https://azure-storage.readthedocs.io/en/latest/) pour télécharger le jeu de données à partir du stockage blob dans l’environnement d’exécution actuel. Le téléchargement est effectué dans la fonction load\_data() à partir du fichier load_data.py. Pour utiliser ce code, vous devez remplacer <ACCOUNT_NAME> et <ACCOUNT_KEY> par le nom et la clé primaire de votre compte de stockage qui héberge le jeu de données. Le nom du compte apparaît dans le coin supérieur gauche de la page Azure de votre compte de stockage. Pour obtenir la clé du compte, sélectionnez Clés d’accès dans la page Azure du compte de stockage (voir la première capture d’écran dans la section Ingestion de données), puis copiez la longue chaîne dans la première ligne de la colonne des clés :
 
![Clé d’accès](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Le code suivant dans la fonction load_data() télécharge un fichier unique :

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Notez que vous n’avez pas besoin d’exécuter manuellement le fichier load_data.py. Il est appelé à partir d’autres fichiers ultérieurement.

### <a name="feature-engineering"></a>Ingénierie des caractéristiques
Le code pour toutes les fonctionnalités de calcul se trouve dans le fichier feature\_engineering.py. Vous n’avez pas besoin d’exécuter manuellement le fichier feature_engineering.py. Il sera appelé ultérieurement à partir d’autres fichiers.

Nous créons plusieurs ensembles de fonctionnalités :
* Encodage à chaud de la marque et du modèle du téléphone cellulaire (fonction one\_hot\_brand_model)
* Fraction d’événements générés par l’utilisateur chaque jour de la semaine (fonction weekday\_hour_features)
* Fraction d’événements générés par l’utilisateur chaque heure (fonction weekday\_hour_features)
* Fraction d’événements générés par l’utilisateur dans chaque combinaison de jour de la semaine et heure (fonction weekday\_hour_features)
* Fraction d’événements générés par l’utilisateur dans chaque application (fonction one\_hot\_app_labels)
* Fraction d’événements générés par l’utilisateur dans chaque étiquette d’application (fonction one\_hot\_app_labels)
* Fraction d’événements générés par l’utilisateur chaque catégorie d’application (fonction text\_category_features)
* Fonctionnalités d’indicateur pour les catégories d’applications qui ont été utilisées pour les événements générés (fonction one\_hot_category)

Ces fonctionnalités ont été inspirées par le noyau de Kaggle : [Un modèle linéaire pour les applications et les étiquettes](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels) (en anglais).

Le calcul de ces fonctionnalités nécessite beaucoup de mémoire. Nous avons d’abord essayé de calculer des fonctionnalités dans l’environnement local avec 16 Go de RAM. Nous avons été en mesure de calculer les quatre premiers ensembles de fonctionnalités, mais reçu un message d’erreur de « mémoire insuffisante » durant le calcul du cinquième ensemble de fonctionnalités. Le calcul des quatre premiers ensembles de fonctionnalités se trouve dans le fichier singleVMsmall.py et il peut être exécuté dans l’environnement local en exécutant 

     az ml experiment submit -c local .\singleVMsmall.py   

dans la fenêtre de l’interface CLI.

Étant donné que l’environnement local est trop petit pour le calcul de tous les ensembles de fonctionnalités, nous basculons vers une machine virtuelle de science des données avec une plus grande capacité de mémoire. L’exécution sur une machine virtuelle de science des données s’effectue dans le conteneur Docker managé par AML Workbench. Cette machine virtuelle de science des données nous permet de calculer toutes les fonctionnalités, d’effectuer l’apprentissage des modèles et de régler les hyperparamètres (voir la section suivante). Le fichier singleVM.py a un code de calcul de fonctionnalités et de modélisation complet. Dans la section suivante, nous expliquerons comment exécuter singleVM.py sur une machine virtuelle de science des données distante. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Réglage d’hyperparamètres avec une machine virtuelle de science des données
Nous utilisons l’implémentation [xgboost](https://anaconda.org/conda-forge/xgboost) [1] de boosting d’arbres de décision. Nous utilisons aussi le package [scikit-learn](http://scikit-learn.org/) pour régler les hyperparamètres de xgboost. Bien que xgboost ne fasse pas partie du package scikit-learn, il implémente l’API scikit-learn et peut donc être utilisé conjointement avec les fonctions de réglage d’hyperparamètres de scikit-learn. 

Xgboost possède huit hyperparamètres, décrits [ici](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md) :
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* subsample
* objective  
 
Nous commençons par utiliser une machine virtuelle de science des données et régler les hyperparamètres à partir d’une petite grille de valeurs candidates :

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Cette grille contient quatre combinaisons de valeurs d’hyperparamètres. Nous utilisons une validation croisée à 5 plis, ce qui donne lieu à 4 x 5 = 20 exécutions de xgboost. Pour mesurer les performances des modèles, nous utilisons l’indicateur de performances de perte logarithmique négative. Le code suivant recherche dans la grille les valeurs d’hyperparamètres qui optimisent la perte logarithmique négative de validation croisée. Le code utilise également ces valeurs pour effectuer l’apprentissage du modèle final sur le jeu d’apprentissage complet :

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Après avoir créé le modèle, nous enregistrons les résultats du réglage des hyperparamètres. Nous utilisons l’API de journalisation d’AML Workbench pour enregistrer les meilleures valeurs d’hyperparamètres et l’estimation de validation croisée correspondante de la perte logarithmique négative :

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Nous créons également un fichier sweeping_results.txt avec les pertes logarithmiques négatives de validation croisée de toutes les combinaisons de valeurs d’hyperparamètres de la grille.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Ce fichier est stocké dans un répertoire ./outputs spécial. Nous expliquerons plus tard comment le télécharger.  

 Avant d’exécuter pour la première fois singleVM.py sur une machine virtuelle de science des données, nous y créons un conteneur Docker en exécutant 

    az ml experiment prepare -c dsvm

dans les fenêtres de l’interface CLI. La création du conteneur Docker prend plusieurs minutes. Nous exécutons ensuite singleVM.py sur la machine virtuelle de science des données :

    az ml experiment submit -c dsvm .\singleVM.py

Cette commande s’effectue en 1 heure et 38 minutes quand la machine virtuelle de science des données a 8 cœurs et 28 Go de mémoire. Les valeurs enregistrées peuvent être affichées dans la fenêtre d’historique des exécutions d’AML Workbench :

![Historique des exécutions](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Par défaut, la fenêtre d’historique des exécutions affiche les valeurs et graphiques des 1-2 premières valeurs enregistrées. Pour afficher la liste complète des valeurs d’hyperparamètres choisies, cliquez sur l’icône des paramètres signalée par un encadré rouge dans la capture d’écran précédente. Sélectionnez ensuite les hyperparamètres à afficher dans le tableau. De même, pour sélectionner les graphiques à afficher dans la partie supérieure de la fenêtre d’historique des exécutions, cliquez sur l’icône des paramètres signalée par un encadré bleu et sélectionnez les graphiques dans la liste. 

Les valeurs d’hyperparamètres choisies peuvent également être consultées dans la fenêtre des propriétés d’exécution : 

![Propriétés d’exécution](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

Dans le coin supérieur droit de la fenêtre des propriétés d’exécution se trouve une section pour les fichiers de sortie avec la liste de tous les fichiers ayant été créés dans le dossier « .\output ». Vous pouvez télécharger le fichier sweeping\_results.txt à partir de cette section en le sélectionnant et en cliquant sur le bouton Télécharger. Le fichier sweeping_results.txt devrait présenter la sortie suivante :

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Réglage d’hyperparamètres avec un cluster Spark
Nous utilisons un cluster Spark pour monter en puissance parallèle le réglage d’hyperparamètres et utiliser une grille plus importante. Notre nouvelle grille est

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Cette grille contient 16 combinaisons de valeurs d’hyperparamètres. Étant donné que nous utilisons une validation croisée à 5 plis, nous exécutons xgboost 16 x 5 = 80 fois.

Le package scikit-learn n’offre pas de prise en charge native du réglage d’hyperparamètres avec un cluster Spark. Heureusement, le package [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) de Databricks comble cette lacune. Ce package fournit la fonction GridSearchCV qui a pratiquement la même API que la fonction GridSearchCV dans scikit-learn. Pour utiliser spark-sklearn et procéder au réglage d’hyperparamètres avec Spark, nous devons créer un contexte Spark :

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Nous remplaçons ensuite 

    from sklearn.model_selection import GridSearchCV

par 

    from spark_sklearn import GridSearchCV

Nous remplaçons également l’appel à GridSearchCV de scikit-learn par celui de spark-sklearn :

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Le code final pour le réglage d’hyperparamètres avec Spark se trouve dans le fichier distributed\_sweep.py. La différence entre singleVM.py et distributed_sweep.py réside au niveau de la définition de grille et de quatre lignes de code supplémentaires. Notez également que, en raison des services AML Workbench, le code de journalisation ne change pas quand l’environnement d’exécution passe d’une machine virtuelle de science des données à un cluster Spark.

Avant d’exécuter pour la première fois distributed_sweep.py dans un cluster Spark, nous devons d’abord y installer des packages Python. Pour ce faire, exécutez 

    az ml experiment prepare -c spark

dans les fenêtres de l’interface CLI. L’installation prend plusieurs minutes. Nous exécutons ensuite distributed_sweep.py dans le cluster Spark :

    az ml experiment submit -c spark .\distributed_sweep.py

Cette commande s’effectue en 1 heure et 6 minutes quand le cluster Spark a 6 nœuds Worker avec 28 Go de mémoire. Les résultats du réglage d’hyperparamètres est accessible dans Azure Machine Learning Workbench de la même façon que l’exécution DSVM distante. (à savoir les journaux, les meilleures valeurs d’hyperparamètres et le fichier sweeping_results.txt)

### <a name="architecture-diagram"></a>Diagramme de l’architecture

Le diagramme suivant illustre le flux de travail global : ![architecture](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Conclusion 

Dans ce scénario, vous avez appris à utiliser Azure Machine Learning Workbench pour effectuer un réglage d’hyperparamètres sur des machines virtuelles distante et des clusters Spark. Nous avons vu qu’Azure Machine Learning Workbench fournit des outils pour configurer facilement des environnements d’exécution. Il permet également de basculer facilement entre eux. 

## <a name="references"></a>Références

[1] T. Chen et C. Guestrin. [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/abs/1603.02754). KDD 2016.




