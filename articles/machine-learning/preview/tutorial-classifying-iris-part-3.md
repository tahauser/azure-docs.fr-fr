---
title: Didacticiel de déploiement d’un modèle pour les services Azure Machine Learning
description: Ce didacticiel complet montre comment utiliser les services Azure Machine Learning de bout en bout. Il s’agit de la troisième partie qui décrit le modèle de déploiement.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/13/2018
ms.openlocfilehash: 87d1e605bfd7603e4e07f6b427033fe2c1d2b83e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-3-classify-iris-deploy-a-model"></a>Didacticiel 3 : Classifier Iris : déployer un modèle
Azure Machine Learning (préversion) est une solution d’analytique avancée et de science des données intégrée de bout en bout destinée aux experts en science des données. Les scientifiques des données peuvent l’utiliser pour préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Ce didacticiel est **le troisième d’une série de trois**. Dans cette partie du didacticiel, vous utilisez Machine Learning (préversion) pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Rechercher le fichier de modèle.
> * Générer un fichier de script de notation et un fichier de schéma.
> * Préparer l’environnement.
> * Créer un service web en temps réel.
> * Exécuter le service web en temps réel.
> * Examiner les données d’objet blob de sortie. 

Ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporel. 

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vous avez besoin des éléments suivants :
- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 
- Un compte d’expérimentation et Azure Machine Learning Workbench installé, comme décrit dans ce [démarrage rapide](quickstart-installation.md)
- Le modèle de classification de la [partie 2 du didacticiel](tutorial-classifying-iris-part-2.md)
- Un moteur Docker installé et exécuté en local

## <a name="download-the-model-pickle-file"></a>Télécharger le fichier pickle de modèle
Dans la partie précédente du didacticiel, le script **iris_sklearn.py** a été exécuté localement dans Machine Learning Workbench. Cette action a sérialisé le modèle de régression logistique à l’aide du package de sérialisation d’objets Python populaire [pickle](https://docs.python.org/3/library/pickle.html). 

1. Ouvrez l’application Machine Learning Workbench. Ouvrez ensuite le projet **myIris** que vous avez créé dans les parties précédentes de la série de didacticiels.

2. Lorsque le projet est ouvert, sélectionnez le bouton **Fichiers** (icône de dossier) dans le volet gauche pour ouvrir la liste des fichiers dans votre dossier de projet.

3. Sélectionnez le fichier **iris_sklearn.py**. Le code Python s’ouvre sous un nouvel onglet de l’éditeur de texte dans Workbench.

4. Examinez le fichier **iris_sklearn.py** pour voir où le fichier pickle a été généré. Sélectionnez Ctrl+F pour ouvrir la boîte de dialogue **Rechercher**, puis recherchez le mot **pickle** dans le code Python.

   Cet extrait de code montre comment le fichier de sortie pickle a été généré. Le fichier de sortie pickle est nommé **model.pkl** sur le disque. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Recherchez le fichier pickle du modèle dans les fichiers de sortie d’une exécution antérieure.
   
   Quand vous avez exécuté le script **iris_sklearn.py**, le fichier de modèle est nommé **model.pkl** dans le dossier **de sortie**. Ce dossier se trouve dans l’environnement d’exécution que vous choisissez pour exécuter le script et non dans votre dossier de projet local. 
   
   a. Pour rechercher le fichier, sélectionnez le bouton **Exécutions** (icône d’horloge) sur le volet gauche pour ouvrir la liste **Toutes les exécutions**. 

   b. L’onglet **Toutes les exécutions** s’ouvre. Dans le tableau des exécutions, sélectionnez-en une récente qui avait pour cible **local** et pour nom de script **iris_sklearn.py**. 

   c. Le volet **Propriétés de l’exécution** s’ouvre. Dans la section supérieure droite du volet, notez la section **Sorties**.

   d. Pour télécharger le fichier pickle, cochez la case en regard du fichier **model.pkl**, puis sélectionnez **Télécharger**. Enregistrez-le à la racine de votre dossier de projet. Le fichier sera nécessaire dans les étapes suivantes.

   ![Télécharger le fichier pickle](media/tutorial-classifying-iris/download_model.png)

   Découvrez plus en détail le dossier `outputs` dans l’article [Guide pratique pour lire et écrire des fichiers de données volumineux](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Obtenir les fichiers de script de notation et de schéma
Pour déployer le service web ainsi que le fichier de modèle, vous avez également besoin d’un script de score. Si vous le souhaitez, vous pouvez utiliser un schéma pour les données d’entrée du service web. Le script de notation charge le fichier **model.pkl** à partir du dossier actuel et l’utilise pour produire de nouvelles prédictions.

1. Ouvrez l’application Machine Learning Workbench. Ouvrez ensuite le projet **myIris** que vous avez créé dans la partie précédente de la série de didacticiels.

2. Lorsque le projet est ouvert, sélectionnez le bouton **Fichiers** (icône de dossier) dans le volet gauche pour ouvrir la liste des fichiers dans votre dossier de projet.

3. Sélectionnez le fichier **score_iris.py**. Le script Python s’ouvre. Ce fichier est utilisé en guise de fichier de notation.

   ![Fichier de notation](media/tutorial-classifying-iris/model_data_collection.png)

4. Pour obtenir le fichier de schéma, exécutez le script. Sélectionnez l’environnement **local** et le script **score_iris.py** dans la barre de commandes, puis sélectionnez **Exécuter**. 

   Ce script crée un fichier JSON dans la section **Sorties**, qui capture le schéma de données d’entrée requis par le modèle.

6. Notez le volet **Travaux** situé à droite du volet **Tableau de bord du projet**. Attendez que le dernier travail **score_iris.py** affiche l’état vert **Terminé**. Sélectionnez ensuite le lien hypertexte **score_iris.py** de la dernière exécution du travail afin d’afficher les détails de l’exécution. 

7. Dans le volet **Propriétés de l’exécution**, dans la section **Sorties**, sélectionnez le fichier **service_schema.json** créé. Cochez la case en regard du nom de fichier, puis sélectionnez **Télécharger**. Enregistrez le fichier dans le dossier racine de votre projet.

8. Revenez à l’onglet précédent où vous avez ouvert le script **score_iris.py**. Grâce à l’utilisation de la collecte de données, vous pouvez capturer des entrées de modèle et des prédictions à partir du service web. Les étapes suivantes sont particulièrement intéressantes pour la collecte de données.

9. Passez en revue le code en haut de la classe d’importations de fichier **ModelDataCollector**, car elle contient les fonctionnalités de collecte de données de modèle :

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Passez en revue les lignes de code suivantes dans la fonction **init()** qui instancie **ModelDataCollector** :

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
    ```

11. Passez en revue les lignes de code suivantes dans la fonction **run(input_df)** car elle collecte les données d’entrée et de prédiction :

    ```python
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

Vous pouvez à présent préparer votre environnement pour opérationnaliser le modèle.

## <a name="prepare-to-operationalize-locally"></a>Préparer l’opérationnalisation locale
Utilisez le déploiement _en mode local_ pour effectuer une exécution dans des conteneurs Docker sur votre ordinateur local.

Vous pouvez utiliser le _mode local_ pour le développement et de test. Le moteur Docker doit être en cours d’exécution localement pour que vous puissiez effectuer les étapes suivantes d’opérationnalisation du modèle. Vous pouvez utiliser l’indicateur `-h` à la fin de chaque commande pour afficher le message d’aide correspondant.

>[!NOTE]
>Si vous n’avez pas de moteur Docker en local, vous pouvez toujours poursuivre en créant un cluster dans Azure à des fins de déploiement. Vous pouvez conserver le cluster pour le réutiliser ou le supprimer après le didacticiel, afin de ne pas être facturé.

>[!NOTE]
>Les services web déployés localement ne s’affichent pas dans la liste des services du portail Azure. Ils s’exécuteront dans Docker sur l’ordinateur local.

1. Ouvrez l’interface de ligne de commande (CLI).
   Dans l’application Machine Learning Workbench, dans le menu **Fichier**, sélectionnez **Ouvrir l’invite de commandes**.

   L’invite de ligne de commande s’ouvre dans l’emplacement actuel du dossier de votre projet **c:\temp\myIris>**.

2. Créez l’environnement. Vous devez exécuter cette étape une fois par environnement. Par exemple, exécutez-la une fois pour l’environnement de développement et une fois pour l’environnement de production. Utilisez le _mode local_ pour ce premier environnement. Vous pouvez essayer le commutateur `-c` ou `--cluster` dans la commande suivante pour configurer un environnement en _mode cluster_ ultérieurement.

   La commande de configuration suivante nécessite que vous disposiez d’un accès Collaborateur à l’abonnement. Si vous ne disposez pas de cet accès, il vous faut au moins un accès Contributeur au groupe de ressources vers lequel vous effectuez le déploiement. Pour l’obtenir, vous devez spécifier le nom du groupe de ressources dans la commande de configuration à l’aide de l’indicateur `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Suivez les instructions à l’écran afin d’approvisionner un compte de stockage pour stocker les images Docker, un registre de conteneurs Azure qui répertorie les images Docker et un compte Azure Application Insights qui collecte les données de télémétrie. Si vous utilisez l’élément `-c`, la commande créera automatiquement un cluster de service de conteneur.
   
   Le nom du cluster est un moyen d’identifier l’environnement. L’emplacement doit être identique à celui du compte de Gestion des modèles que vous avez créé à partir du portail Azure.

   Pour vous assurer que l’environnement est configuré correctement, utilisez la commande suivante pour vérifier l’état :

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   Vérifiez que la valeur du paramètre « État d’approvisionnement » est définie sur « Réussi » (comme indiqué) avant de définir l’environnement à l’étape 5 :

   ![État d’approvisionnement](media/tutorial-classifying-iris/provisioning_state.png)
 
3. Si vous n’avez pas créé de compte de gestion des modèles dans les parties précédents de ce didacticiel, faites-le maintenant. Il s’agit d’une configuration unique.
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. Définissez le compte de Gestion des modèles.
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. Définissez l’environnement.

   Une fois la configuration terminée, utilisez la commande suivante pour définir les variables d’environnement nécessaires à l’opérationnalisation de l’environnement. Utilisez le même nom d’environnement que celui utilisé précédemment à l’étape 2. Utilisez le même nom de groupe de ressources généré dans la fenêtre de commande lorsque le processus d’installation s’est terminé.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. Pour vérifier que vous avez correctement configuré votre environnement mis en œuvre pour le déploiement du service web local, entrez la commande suivante :

   ```azurecli
   az ml env show
   ```

Vous êtes maintenant prêt à créer le service web en temps réel.

>[!NOTE]
>Vous pouvez réutiliser le compte et l’environnement de Gestion des modèles pour les déploiements de service web suivants. Vous n’avez pas besoin de les créer pour chaque service web. Un compte ou un environnement peuvent disposer de plusieurs services web associés.

## <a name="create-a-real-time-web-service-in-one-command"></a>Créer un service web en temps réel en une seule commande
1. Pour créer un service web en temps réel, utilisez la commande suivante :

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   Cette commande génère un ID de service web que vous pouvez utiliser ultérieurement.

   Les commutateurs suivants sont utilisés avec la commande **az ml service create realtime** :

   * `-f` : le nom du fichier de script de notation.

   * `--model-file` : le fichier de modèle. Dans ce cas, il s’agit du fichier pickle model.pkl.

   * `-s` : Le schéma de service. Il a été généré dans une étape précédente en exécutant le script **score_iris.py** localement.

   * `-n` : le nom de l’application, qui doit être en minuscules uniquement.

   * `-r` : le runtime du modèle. Dans ce cas, il s’agit d’un modèle Python. Les runtimes valides sont `python` et `spark-py`.

   * `--collect-model-data true` : ce commutateur active la collecte de données.

   * `-c`: chemin d’accès au fichier de dépendances Conda où les packages supplémentaires sont spécifiés.

   >[!IMPORTANT]
   >Le nom du service, qui est également le nouveau nom d’image Docker, doit être en minuscules uniquement. Sinon, vous recevez un message d’erreur. 

2. Quand vous exécutez la commande, le modèle et le fichier de notation sont chargés sur le compte de stockage que vous avez créé lors de la configuration de l’environnement. Le processus de déploiement crée une image Docker contenant le fichier de modèle, de schéma et de notation, puis la place dans le registre de conteneurs Azure : **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   La commande extrait l’image localement sur votre ordinateur, à partir de laquelle il démarre un conteneur Docker. Si votre environnement est configuré en mode cluster, le conteneur Docker est déployé sur le cluster Azure Cloud Services Kubernetes à la place.

   Dans le cadre du déploiement, un point de terminaison HTTP REST pour le service web est créé sur votre machine locale. Après quelques minutes, la commande doit se terminer avec un message de réussite. Votre service web est maintenant opérationnel !

3. Pour voir le conteneur Docker en cours d’exécution, utilisez la commande **docker ps** :

   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Créer un service web en temps réel à l’aide de plusieurs commandes
En guise d’alternative à la commande **az ml service create realtime** précédente, vous pouvez également effectuer les étapes séparément. 

Tout d’abord, inscrivez le modèle. Générez ensuite le manifeste, générez l’image Docker, puis créez le service web. Cette approche étape par étape vous permet d’obtenir plus de flexibilité à chaque étape. Vous pouvez également réutiliser les entités générées dans les étapes précédentes et ne regénérer les entités que lorsque cela est nécessaire.

1. Inscrire le modèle en fournissant le nom de fichier pickle

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Cette commande génère un ID de modèle.

2. Créez un manifeste.

   Pour créer un manifeste, utilisez la commande suivante et indiquez l’ID de modèle issu de l’étape précédente :

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   Cette commande génère un ID de manifeste.

3. Créez une image Docker.

   Pour créer une image Docker, utilisez la commande suivante et indiquez la valeur d’ID de manifeste issue de l’étape précédente. Vous pouvez également inclure les dépendances Conda à l’aide du commutateur `-c`.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
   ```
   Cette commande génère un ID d’image Docker.
   
4. Créez le service.

   Pour créer un service, utilisez la commande suivante et indiquez l’ID d’image issu de l’étape précédente :

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Cette commande génère un ID de service web.

Vous êtes maintenant prêt à exécuter le service web.

## <a name="run-the-real-time-web-service"></a>Exécuter le service web en temps réel

Pour tester le service web **irisapp** exécuté, utilisez un enregistrement JSON encodé contenant un tableau de quatre nombres aléatoires.

1. Le service web inclut des exemples de données. Lors de l’exécution en mode local, vous pouvez appeler la commande **az ml service usage realtime**. Cet appel récupère un exemple de commande d’exécution qui vous pouvez utiliser pour tester le service. L’appel récupère également l’URL de notation que vous pouvez utiliser pour intégrer le service à votre propre application personnalisée.

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. Pour tester le service, exécutez la commande service run retournée :
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```

   La sortie est **« Iris-setosa »**, qui est la classe prévue. (Votre résultat peut être différent.) 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Afficher les données collectées dans le stockage Blob Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez vos comptes de stockage. Pour ce faire, sélectionnez **Tous les services**.

3. Dans la zone de recherche, entrez **Comptes de stockage**, puis sélectionnez Entrée.

4. Dans la zone de recherche **Comptes de stockage**, sélectionnez la ressource **Compte de stockage** correspondant à votre environnement. 

   > [!TIP]
   > Pour déterminer le compte de stockage utilisé :
   > 1. Ouvrez Machine Learning Workbench.
   > 2. Sélectionnez le projet sur lequel vous travaillez.
   > 3. Ouvrez une invite de ligne de commande à partir du menu **Fichier**.
   > 4. À l’invite de ligne de commande, entrez `az ml env show -v` et vérifiez la valeur de *storage_account*. Il s’agit du nom de votre compte de stockage.

5. Lorsque le volet **Compte de stockage** s’ouvre, sélectionnez **Objets blob** dans la section **Services**. Recherchez le conteneur nommé **modeldata**. 
 
   Si vous ne voyez pas de données, vous devrez peut-être patienter jusqu’à 10 minutes après l’envoi de la première requête de service web avant que les données s’affichent dans le compte de stockage.

   Les données sont transmises dans les objets blob via le chemin de conteneur suivant :

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Vous pouvez utiliser ces données à partir du stockage Blob Azure. Une variété d’outils utilisent des logiciels Microsoft et des outils open source, tels que :

   * Machine Learning : ouvrez le fichier CSV en l’ajoutant en tant que source de données.

   * Excel : ouvrir les fichiers CSV quotidiens sous la forme d’une feuille de calcul.

   * [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/) : créer des graphiques avec les données extraites des données CSV d’objets blob.

   * [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) : chargez les données CSV dans une table Hive et effectuez des requêtes SQL directement sur les objets blob.

   * [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview) : créer un cadre de données avec une grande partie des données CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans cette troisième partie de la série de didacticiel à trois parties, vous avez appris à utiliser Machine Learning pour effectuer les tâches suivantes :
> [!div class="checklist"]
> * Rechercher le fichier de modèle.
> * Générer un fichier de script de notation et un fichier de schéma.
> * Préparer l’environnement.
> * Créer un service web en temps réel.
> * Exécuter le service web en temps réel.
> * Examiner les données d’objet blob de sortie. 

Vous avez correctement exécuté un script de formation dans différents environnements de calcul. Vous avez aussi créé, sérialisé et opérationnalisé un modèle via un service web Docker. 

Vous êtes maintenant prêt à effectuer une préparation avancée des données :
> [!div class="nextstepaction"]
> [Didacticiel 4 : Préparation avancée des données](tutorial-bikeshare-dataprep.md)
