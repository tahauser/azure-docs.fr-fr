---
title: "Déployer un modèle pour les services Azure Machine Learning (version préliminaire) | Microsoft Docs"
description: "Ce didacticiel complet montre comment utiliser les services Azure Machine Learning (version préliminaire) de bout en bout. Il s’agit de la troisième partie qui décrit le modèle de déploiement."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 11/29/2017
ms.openlocfilehash: b48e5bc2552c92b45e0417e5a8a34705a473073e
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="classify-iris-part-3-deploy-a-model"></a>Classification d’Iris, partie 3 : déployer un modèle
Les services Azure Machine Learning (version préliminaire) constituent une solution d’analytique avancée et de science des données intégrée de bout en bout destinée aux scientifiques des données professionnels. Les scientifiques des données peuvent l’utiliser pour préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Ce didacticiel est le troisième d’une série de trois. Dans cette partie du didacticiel, vous utilisez les services Azure Machine Learning (version préliminaire) pour :

> [!div class="checklist"]
> * Rechercher le fichier de modèle.
> * Générer un fichier de script de notation et un fichier de schéma.
> * Préparer l’environnement.
> * Créer un service web en temps réel.
> * Exécuter le service web en temps réel.
> * Examiner les données d’objet blob de sortie. 

 Ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/iris_flower_data_set) intemporel. Les captures d’écran sont spécifiques à Windows, mais l’expérience Mac OS est presque identique.

## <a name="prerequisites"></a>Composants requis
Effectuer les deux premières parties de cette série de didacticiels :

   * Suivez le [didacticiel Préparer les données](tutorial-classifying-iris-part-1.md) pour créer des ressources Machine Learning et installer l’application Azure Machine Learning Workbench.

   * Suivez le [didacticiel Générer un modèle](tutorial-classifying-iris-part-2.md) pour créer un modèle de régression logistique dans Azure Machine Learning.

Un moteur Docker doit être installé et exécuté en local. Vous pouvez également déployer un cluster Azure Container Service dans Azure.

## <a name="download-the-model-pickle-file"></a>Télécharger le fichier pickle de modèle
Dans la partie précédente du didacticiel, le script **iris_sklearn.py** a été exécuté localement dans Machine Learning Workbench. Cette action a sérialisé le modèle de régression logistique à l’aide du package de sérialisation d’objets Python populaire [pickle](https://docs.python.org/2/library/pickle.html). 

1. Ouvrez l’application Machine Learning Workbench, puis ouvrez le projet **myIris** que vous avez créé dans la partie précédente de la série de didacticiels.

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
   
   - Pour rechercher le fichier, sélectionnez le bouton **Exécutions** (icône d’horloge) sur le volet gauche pour ouvrir la liste **Toutes les exécutions**.  
   - L’onglet **Toutes les exécutions** s’ouvre. Dans le tableau des exécutions, sélectionnez-en une récente qui avait pour cible **local** et pour nom de script **iris_sklearn.py**. 
   - Le volet **Propriétés de l’exécution** s’ouvre. Dans la section supérieure droite du volet, notez la section **Sorties**. 
   - Pour télécharger le fichier pickle, cochez la case en regard du fichier **model.pkl**, puis sélectionnez le bouton **Télécharger**. Enregistrez-le à la racine de votre dossier de projet. Le fichier sera nécessaire dans les étapes suivantes.

   ![Télécharger le fichier pickle](media/tutorial-classifying-iris/download_model.png)

   Découvrez plus en détail le dossier `outputs` dans l’article [Guide pratique pour lire et écrire de fichiers de données volumineux](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Obtenir les fichiers de script de notation et de schéma
Pour déployer le service web, ainsi que le fichier de modèle, vous avez également besoin d’un script de notation et éventuellement d’un schéma pour les données d’entrée du service web. Le script de notation charge le fichier **model.pkl** à partir du dossier actuel et l’utilise pour produire une classe Iris qui vient d’être prédite.  

1. Ouvrez l’application Azure Machine Learning Workbench, puis ouvrez le projet **myIris** que vous avez créé dans la partie précédente de la série de didacticiels.

2. Lorsque le projet est ouvert, sélectionnez le bouton **Fichiers** (icône de dossier) dans le volet gauche pour ouvrir la liste des fichiers dans votre dossier de projet.

3. Sélectionnez le fichier **score_iris.py**. Le script Python s’ouvre. Ce fichier est utilisé en guise de fichier de notation.

   ![Fichier de notation](media/tutorial-classifying-iris/model_data_collection.png)

4. Pour obtenir le fichier de schéma, exécutez le script. Sélectionnez l’environnement **local** et le script **score_iris.py** dans la barre de commandes, puis sélectionnez le bouton **Exécuter**. 

5. Ce script crée un fichier JSON dans la section **Sorties**, qui capture le schéma de données d’entrée requis par le modèle.

6. Notez le volet **Travaux** situé à droite du volet **Tableau de bord du projet**. Attendez que le dernier travail **score_iris.py** affiche l’état vert **Terminé**. Sélectionnez ensuite le lien hypertexte **score_iris.py [1]** de la dernière exécution du travail afin d’afficher les détails de l’exécution **score_iris.py**. 

7. Dans le volet **Propriétés de l’exécution**, dans la section **Sorties**, sélectionnez le fichier **service_schema.json** créé.  Cochez la case en regard du nom de fichier, puis sélectionnez **Télécharger**. Enregistrez le fichier dans le dossier racine de votre projet.

8. Revenez à l’onglet précédent où vous avez ouvert le script **score_iris.py**. Grâce à l’utilisation de la collecte de données, vous pouvez capturer des entrées de modèle et des prédictions à partir du service web. Les étapes suivantes sont particulièrement intéressantes pour la collecte de données.

9. Passez en revue le code en haut de la classe d’importations de fichier **ModelDataCollector**, car elle contient les fonctionnalités de collecte de données de modèle :

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
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

Vous pouvez à présent préparer votre environnement pour opérationnaliser le modèle.



## <a name="prepare-to-operationalize-locally"></a>Préparer l’opérationnalisation locale
Utilisez le déploiement _en mode local_ pour effectuer une exécution dans des conteneurs Docker sur votre ordinateur local.

Vous pouvez utiliser le _mode local_ pour le développement et de test. Le moteur Docker doit être exécuté localement pour que vous puissiez effectuer les étapes suivantes d’opérationnalisation du modèle. Vous pouvez utiliser l’indicateur `-h` à la fin d’une commande pour obtenir de l’aide sur celle-ci.

>[!NOTE]
>Si vous n’avez pas de moteur Docker en local, vous pouvez toujours poursuivre en créant un cluster dans Azure à des fins de déploiement. Veillez simplement à supprimer le cluster après le didacticiel afin de ne peut subir de frais permanents.

1. Ouvrez l’interface de ligne de commande (CLI).
   Dans l’application Azure Machine Learning Workbench, dans le menu **Fichier**, sélectionnez **Ouvrir une invite de commande**.

   L’invite de ligne de commande s’ouvre dans l’emplacement actuel du dossier de votre projet **c:\temp\myIris>**.

2. Vérifiez que le fournisseur de ressources Azure **Microsoft.ContainerRegistry** est inscrit dans votre abonnement. Vous devez inscrire ce fournisseur de ressources avant de pouvoir créer un environnement à l’étape 3. Vous pouvez vérifier s’il est déjà inscrit à l’aide de la commande suivante :
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Un résultat similaire à ceci s’affiche normalement : 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Si **Microsoft.ContainerRegistry** n’est pas inscrit, vous pouvez l’inscrire à l’aide de la commande suivante :
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   L’inscription peut prendre quelques minutes. Vous pouvez vérifier son état à l’aide de la commande **az provider list** précédente ou de la commande suivante :
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   La troisième ligne de la sortie affiche **"registrationState": "Registering"**. Attendez quelques instants et répétez la commande **show**, jusqu’à ce que la sortie affiche **"registrationState": "Registered"**.

3. Créez l’environnement. Vous devez exécuter cette étape une fois par environnement. Par exemple, exécutez-la une fois pour l’environnement de développement et une fois pour l’environnement de production. Utilisez le _mode local_ pour ce premier environnement. Vous pouvez essayer le commutateur `-c` ou `--cluster` dans la commande suivante pour configurer un environnement en _mode cluster_ ultérieurement.

   Notez que pour la commande de configuration suivante, vous devez disposer d’un accès collaborateur à l’abonnement. Si vous n’en avez pas, il vous faudra au moins un accès collaborateur au groupe de ressources dans lequel vous déployez. Pour l’obtenir, vous devez spécifier le nom du groupe de ressources dans la commande de configuration à l’aide de l’indicateur `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Suivez les instructions à l’écran afin d’approvisionner un compte de stockage pour stocker les images Docker, un registre de conteneurs Azure qui répertorie les images Docker et un compte AppInsight qui collecte les données de télémétrie. Si vous avez utilisé le commutateur `-c`, un cluster Azure Container Service est également créé.
   
   Le nom du cluster est un moyen d’identifier l’environnement. L’emplacement doit être identique à celui du compte de Gestion des modèles que vous avez créé à partir du portail Azure.

4. Créez un compte de Gestion des modèles. (Il s’agit d’une configuration unique.)  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. Définissez le compte de Gestion des modèles.  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. Définissez l’environnement.

   Une fois la configuration terminée, utilisez la commande suivante pour définir les variables d’environnement nécessaires à l’opérationnalisation de l’environnement. Utilisez le même nom d’environnement que celui utilisé précédemment à l’étape 4. Utilisez le même nom de groupe de ressources généré dans la fenêtre de commande lorsque le processus d’installation s’est terminé.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

7. Pour vérifier que vous avez correctement configuré votre environnement mis en œuvre pour le déploiement du service web local, entrez la commande suivante :

   ```azurecli
   az ml env show
   ```

Vous êtes maintenant prêt à créer le service web en temps réel.

>[!NOTE]
>Vous pouvez réutiliser le compte et l’environnement de Gestion des modèles pour les déploiements de service web suivants. Vous n’avez pas besoin de les créer pour chaque service web. Un compte ou un environnement peuvent disposer de plusieurs services web associés.

## <a name="create-a-real-time-web-service-in-one-command"></a>Créer un service web en temps réel en une seule commande
1. Pour créer un service web en temps réel, utilisez la commande suivante :

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   Cette commande génère un ID de service web que vous pouvez utiliser ultérieurement.

   Les commutateurs suivants sont utilisés avec la commande **az ml service create realtime** :
   * `-n` : le nom de l’application, qui doit être en minuscules uniquement.
   * `-f` : le nom du fichier de script de notation.
   * `--model-file` : le fichier de modèle. Dans ce cas, il s’agit du fichier pickle model.pkl.
   * `-r` : le type de modèle. Dans ce cas, il s’agit d’un modèle Python.
   * `--collect-model-data true` : active la collecte de données.

   >[!IMPORTANT]
   >Le nom du service, qui est également le nouveau nom d’image Docker, doit être en minuscules uniquement. Sinon, vous recevez un message d’erreur. 

2. Lorsque vous exécutez la commande, le modèle et le fichier de notation sont chargés sur le compte de stockage que vous avez créé lors de la configuration de l’environnement. Le processus de déploiement crée une image Docker contenant le fichier de modèle, de schéma et de notation, puis la place dans le registre de conteneurs Azure : **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   La commande extrait l’image localement sur votre ordinateur, à partir de laquelle il démarre un conteneur Docker. Si votre environnement est configuré en mode cluster, le conteneur Docker est déployé sur le cluster Azure Cloud Services Kubernetes à la place.

   Dans le cadre du déploiement, un point de terminaison HTTP REST pour le service web est créé sur votre machine locale. Après quelques minutes, la commande doit se terminer avec un message de réussite ; votre service web est alors opérationnel.

3. Pour voir le conteneur Docker en cours d’exécution, utilisez la commande **docker ps** :
   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Créer un service web en temps réel à l’aide de plusieurs commandes
En guise d’alternative à la commande **az ml service create realtime** précédente, vous pouvez également effectuer les étapes séparément. 

Tout d’abord, inscrivez le modèle. Générez ensuite le manifeste, générez l’image Docker, puis créez le service web. Cette approche étape par étape vous permet d’obtenir plus de flexibilité à chaque étape. Vous pouvez également réutiliser les entités générées à l’étape précédente et ne regénérer les entités que lorsque cela est nécessaire.

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

   Pour créer une image Docker, utilisez la commande suivante et indiquez la valeur d’ID de manifeste issue de l’étape précédente :

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
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

Pour tester le service web **irisapp** qui est en cours d’exécution, utilisez un enregistrement JSON encodé contenant un tableau de quatre nombres aléatoires :

1. Le service web inclut des exemples de données. Lors de l’exécution en mode local, vous pouvez appeler la commande **az ml service usage realtime**. Cet appel récupère un exemple de commande d’exécution qui vous pouvez utiliser pour tester le service. L’appel récupère également l’URL de notation que vous pouvez utiliser pour intégrer le service à votre propre application personnalisée :

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. Pour tester le service, exécutez la commande service run retournée :

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```
   La sortie est **"2"**, qui est la classe prévue. (Votre résultat peut être différent.) 

3. Pour exécuter le service à l’extérieur de l’interface de ligne de commande (CLI), vous devez obtenir les clés pour l’authentification :

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Afficher les données collectées dans le stockage Blob Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez vos comptes de stockage. Pour ce faire, sélectionnez **Plus de services**.

3. Dans la zone de recherche, entrez **Comptes de stockage**, puis sélectionnez **Entrée**.

4. Dans la zone de recherche **Comptes de stockage**, sélectionnez la ressource **Compte de stockage** correspondant à votre environnement. 

   > [!TIP]
   > Pour déterminer le compte de stockage utilisé :
   > 1. Ouvrez Azure Machine Learning Workbench.
   > 2. Sélectionnez le projet sur lequel vous travaillez.
   > 3. Ouvrez une invite de ligne de commande à partir du menu **Fichier**.
   > 4. À l’invite de ligne de commande, entrez `az ml env show -v` et vérifiez la valeur de *storage_account*. Il s’agit du nom de votre compte de stockage.

5. Lorsque le volet **Compte de stockage** s’ouvre, sélectionnez **Conteneurs** dans la liste à gauche. Recherchez le conteneur nommé **modeldata**. 
 
   Si vous ne voyez pas de données, vous devrez peut-être patienter jusqu’à 10 minutes après l’envoi de la première requête de service web avant que les données s’affichent dans le compte de stockage.

   Les données sont transmises dans les objets blob via le chemin de conteneur suivant :

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Vous pouvez utiliser ces données à partir du stockage Blob Azure. Une variété d’outils utilisent des logiciels Microsoft et des outils open source, tels que :

   - Azure Machine Learning : ouvrir le fichier CSV en l’ajoutant en tant que source de données. 
   - Excel : ouvrir les fichiers CSV quotidiens sous la forme d’une feuille de calcul.
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/) : créer des graphiques avec les données extraites des données CSV d’objets blob.
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) : charger les données CSV dans une table Hive et effectuer des requêtes SQL directement sur les objets blob.
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview) : créer un cadre de données avec une grande partie des données CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="next-steps"></a>Étapes suivantes
Dans cette troisième partie de la série de trois didacticiels, vous avez appris à utiliser les services Azure Machine Learning pour :
> [!div class="checklist"]
> * Rechercher le fichier de modèle.
> * Générer un fichier de script de notation et un fichier de schéma.
> * Préparer l’environnement.
> * Créer un service web en temps réel.
> * Exécuter le service web en temps réel.
> * Examiner les données d’objet blob de sortie. 

Vous avez correctement exécuté un script de formation dans différents environnements de calcul, créé un modèle, sérialisé le modèle et opérationnalisé ce dernier par le biais d’un service web basé sur Docker. 

Vous êtes maintenant prêt à effectuer une préparation avancée des données :
> [!div class="nextstepaction"]
> [Préparation avancée des données](tutorial-bikeshare-dataprep.md)
