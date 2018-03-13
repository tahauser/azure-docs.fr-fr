---
title: "Didacticiel de génération d’un modèle pour les services Azure Machine Learning (préversion) | Microsoft Docs"
description: "Ce didacticiel complet montre comment utiliser les services Azure Machine Learning (préversion) de bout en bout. Cela fait partie de deux et traite l’expérimentation."
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 9a1613e2137e178d00a24f9f5b3c802f8a894b15
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-classify-iris-part-2---build-a-model"></a>Didacticiel de classification d’Iris, partie 2 : générer un modèle
Les services Azure Machine Learning (version préliminaire) forment une solution d’analytique avancée et de science des données intégrée de bout en bout qui permet aux scientifiques des données professionnels de préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Ce didacticiel est **le deuxième d’une série de trois**. Dans cette partie du didacticiel, vous utilisez les services Azure Machine Learning (version préliminaire) pour :

> [!div class="checklist"]
> * Utiliser Azure Machine Learning Workbench.
> * Ouvrir des scripts et passer le code en revue.
> * Exécuter des scripts dans un environnement local.
> * Passer en revue l’historique des exécutions.
> * Exécuter des scripts dans un environnement Docker local.
> * Exécuter des scripts dans une fenêtre Azure CLI locale.
> * Exécuter des scripts dans un environnement Docker distant.
> * Exécuter des scripts dans un environnement Azure HDInsight cloud.

Ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporel. Les captures d’écran sont spécifiques à Windows, mais l’expérience Mac OS est presque identique.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>configuration requise
Terminez la première partie de cette série de didacticiels. Suivez le [didacticiel Préparer les données](tutorial-classifying-iris-part-1.md) pour créer des ressources Azure Machine Learning et installer l’application Azure Machine Learning Workbench avant de commencer les étapes de ce didacticiel.

Vous pouvez essayer d’exécuter des scripts dans un conteneur Docker local (facultatif). Pour ce faire, un moteur Docker (l’édition Community est suffisante) doit être installé et démarré en local sur votre machine Windows ou Mac OS. Pour plus d’informations sur l’installation de Docker, consultez les [Instructions d’installation de Docker](https://docs.docker.com/engine/installation/).

Pour essayer de répartir les scripts à exécuter dans un conteneur Docker d’une machine virtuelle Azure distante ou un cluster Azure HDInsight Spark, vous pouvez suivre les [instructions pour créer une machine virtuelle de science des données Azure basée sur Ubuntu ou un cluster HDInsight](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Examiner les fichiers de configuration et iris_sklearn.py
1. Ouvrez l’application Azure Machine Learning Workbench, puis ouvrez le projet **myIris** que vous avez créé dans la partie précédente de la série de didacticiels.

2. Lorsque le projet est ouvert, sélectionnez le bouton **Fichiers** (l’icône de dossier) dans le volet tout à gauche pour ouvrir la liste des fichiers dans votre dossier de projet.

3. Sélectionnez le fichier **iris_sklearn.py**. Le code Python s’ouvre sous un nouvel onglet de l’éditeur de texte dans Workbench.

   ![Ouvrir un fichier](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Le code que vous voyez peut différer légèrement du code précédent, car cet exemple de projet est fréquemment mis à jour.

4. Passez en revue le code de script Python pour vous familiariser avec le style de codage. Le script effectue les tâches suivantes :

   - Il charge le package de préparation des données **iris.dprep** pour créer un [cadre de données pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Utilisez le package de préparation des données `iris.dprep` qui est fourni avec l’exemple de projet, qui doit être le même que le fichier `iris-1.dprep` que vous avez créé dans la partie 1 de ce didacticiel.

   - Il ajoute des caractéristiques aléatoires pour rendre le problème plus difficile à résoudre. Le caractère aléatoire est nécessaire, car Iris est un petit jeu de données qui est facilement classé avec près de 100 % de précision.

   - Il utilise la bibliothèque de machine learning [scikit-learn](http://scikit-learn.org/stable/index.html) pour générer un modèle de régression logistique. 

   - Il sérialise le modèle en insérant la bibliothèque [pickle](https://docs.python.org/2/library/pickle.html) dans un fichier dans le dossier `outputs`. Le script le charge et le désérialise ensuite en mémoire.

   - Il utilise le modèle désérialisé pour faire une prédiction sur un nouvel enregistrement. 

   - Il trace deux graphiques, une matrice de confusion et une courbe ROC multiclasses, à l’aide de la bibliothèque [matplotlib](https://matplotlib.org/), puis les enregistre dans le dossier `outputs`.

   - L’objet `run_logger` est utilisé pour enregistrer le taux de régularisation et pour modéliser la précision dans les journaux. Les journaux sont tracés automatiquement dans l’historique des exécutions.


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>Exécuter le script iris_sklearn.py dans un environnement local

Nous allons préparer l’exécution du script **iris_sklearn.py** pour la première fois. Ce script nécessite les packages **scikit-learn** et **matplotlib**. Le package **scikit-learn** est déjà installé par Azure Machine Learning Workbench. Vous devrez toujours installer **matplotlib**. 

1. Dans Azure Machine Learning Workbench, sélectionnez le menu **Fichier** puis **Ouvrir l’invite de commandes** pour ouvrir l’invite de commandes. La fenêtre de l’interface de ligne de commande est appelée *fenêtre CLI Azure Machine Learning Workbench*, ou *fenêtre CLI* dans sa forme abrégée.

2. Dans la fenêtre CLI, entrez la commande suivante pour installer le package Python **matplotlib**. L’opération doit prendre moins d’une minute.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Si vous ignorez la commande `pip install` ci-dessus, le code dans `iris_sklearn.py` s’exécute correctement. Si vous ignorez `iris_sklearn.py` uniquement, le code ne produit pas la sortie de la matrice de confusion et les tracés de courbe ROC multiclasses comme indiqué dans les visualisations de l’historique.

3. Revenez à la fenêtre d’application Workbench. 

4. Dans la barre d’outils en haut de l’onglet **iris_sklearn.py**, sélectionnez pour ouvrir le menu déroulant en regard de l’icône **Enregistrer**, puis sélectionnez **Configuration d’exécution**. Sélectionnez **local** comme environnement d’exécution, puis entrez `iris_sklearn.py` comme script à exécuter.

5. Déplacez ensuite vers la droite de la barre d’outils et entrez `0.01` dans le champ **Arguments**. 

   ![Exécuter le contrôle](media/tutorial-classifying-iris/run_control.png)

6. Sélectionnez le bouton **Exécuter**. Un travail est planifié immédiatement. Le travail est répertorié dans le volet **Travaux** sur le côté droit de la fenêtre Workbench. 

7. Après quelques instants, l’état du travail passe de **Soumission** à **Exécution**, puis à **Terminé**.

   ![Exécuter sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Sélectionnez **Terminé** dans le texte d’état du travail dans le volet **Travaux**. Une fenêtre indépendante s’ouvre, puis affiche le texte de sortie standard (stdout) du script en cours d’exécution. Pour fermer le texte stdout, sélectionnez le bouton **Fermer** (**x**) dans le coin supérieur droit de la fenêtre indépendante.

9. Dans le même état du travail dans le volet **Travaux**, sélectionnez le texte en bleu **iris_sklearn.py [n]** (_n_ est le numéro d’exécution) juste au-dessus de l’état **Terminé** et de l’heure de début. La fenêtre **Propriétés de l’exécution** s’ouvre et affiche les informations suivantes pour cette exécution particulière :
   - Informations **Propriétés de l’exécution**
   - Fichiers **Sorties**
   - **Visualisations**, le cas échéant
   - **Journaux** 

   Lorsque l’exécution est terminée, la fenêtre indépendante affiche les résultats suivants :

   >[!NOTE]
   >Étant donné que nous avons introduit un degré de randomisation dans la formation définie précédemment, vos résultats peuvent varier des résultats présentés ici.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```

10. Fermez l’onglet **Propriétés de l’exécution**, puis revenez à l’onglet **iris_sklearn.py**. 

11. Effectuez d’autres exécutions. 

    Dans le champ **Arguments**, entrez une série de valeurs numériques différentes comprises entre `0.001` et `10`. Sélectionnez **Exécuter** pour exécuter le code plusieurs fois encore. La valeur d’argument que vous changez chaque fois est transmise à l’algorithme de régression logistique dans le code, ceci entraînant des résultats différents systématiquement.

## <a name="review-the-run-history-in-detail"></a>Passer en revue l’historique des exécutions
Dans Azure Machine Learning Workbench, chaque exécution du script est capturée dans un enregistrement de l’historique des exécutions. Si vous ouvrez la vue **Exécutions**, vous pouvez afficher l’historique des exécutions d’un script particulier.

1. Pour ouvrir la liste des **exécutions**, sélectionnez le bouton **Exécutions** (icône en forme d’horloge) dans la barre d’outils de gauche. Sélectionnez ensuite **iris_sklearn.py** pour afficher le **Tableau de bord des exécutions** de `iris_sklearn.py`.

   ![Vue de l’exécution](media/tutorial-classifying-iris/run_view.png)

2. L’onglet **Tableau de bord des exécutions** s’ouvre. Passez en revue les statistiques capturées sur plusieurs exécutions. Les graphiques sont affichés en haut de l’onglet. Chaque exécution porte un numéro consécutif, et les détails de l’exécution sont répertoriés dans le tableau en bas de l’écran.

   ![Tableau de bord des exécutions](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtrez la table, puis sélectionnez l’un des graphiques pour afficher l’état, la durée, la précision et le taux de régularisation de chaque exécution. 

4. Sélectionnez deux ou trois exécutions dans le tableau **Exécutions**, puis sélectionnez le bouton **Comparer** pour ouvrir un volet de comparaison détaillé. Passez en revue la comparaison côte-à-côte. Sélectionnez le bouton précédent **Liste des exécutions** dans le coin supérieur gauche du volet **Comparaison** pour revenir au **Tableau de bord des exécutions**.

5. Sélectionnez une exécution pour en afficher les détails. Notez que les statistiques de l’exécution sélectionnée sont répertoriées dans la section **Propriétés de l’exécution**. Les fichiers écrits dans le dossier de sortie sont répertoriés dans la section **Sorties** et vous pouvez télécharger les fichiers à partir de celle-ci.

   ![Détails de l’exécution](media/tutorial-classifying-iris/run_details.png)

   Les deux tracés, la matrice de confusion et la courbe ROC multiclasses, apparaissent dans la section **Visualisations**. Tous les fichiers journaux se trouvent également dans la section **Journaux**.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Exécuter des scripts dans l’environnement Docker local

Grâce à Machine Learning, vous pouvez configurer facilement des environnements d’exécution supplémentaires, tels que Docker, et exécuter votre script dans ces environnements. 

>[!IMPORTANT]
>Pour que vous puissiez effectuer cette étape, un moteur Docker doit être installé localement et démarré. Pour plus d’informations, consultez les instructions d’installation de Docker.

1. Dans le volet de gauche, sélectionnez l’icône **Dossier** pour ouvrir la liste **Fichiers** associée à votre projet. Développez le dossier `aml_config`. 

2. Plusieurs environnements sont préconfigurés, tels que **docker-python**, **docker-spark** et **local**. 

   Chaque environnement a deux fichiers, tels que `docker-python.compute` et `docker-python.runconfig`. Ouvrez chaque fichier pour constater que certaines options sont configurables dans l’éditeur de texte.  

   Pour faire de la place, sélectionnez **Fermer** (**x**) sur les onglets des éditeurs de texte ouverts.

3. Exécutez le script **iris_sklearn.py** à l’aide de l’environnement **docker-python** : 

   - Dans la barre d’outils de gauche, sélectionnez l’icône **Horloge** pour ouvrir le volet **Exécutions**. Sélectionnez **Toutes les exécutions**. 
   - En haut de l’onglet **Toutes les exécutions**, sélectionnez **docker-python** comme environnement ciblé à la place de l’environnement par défaut **local**. 
   - Déplacez ensuite vers la droite et sélectionnez **iris_sklearn.py** comme script à exécuter. 
   - Laissez le champ **Arguments** vide, car le script spécifie une valeur par défaut. 
   - Sélectionnez le bouton **Exécuter**.

4. Vous constatez qu’un nouveau travail démarre. Il apparaît dans le volet **Travaux** sur le côté droit de la fenêtre Workbench.

   Lors de la première exécution dans un environnement Docker, quelques minutes supplémentaires sont nécessaires. 

   En arrière-plan, Azure Machine Learning Workbench génère un nouveau fichier de docker. 
   Le nouveau fichier fait référence à l’image de base Docker spécifiée dans le fichier `docker.compute` et les packages Python de dépendance spécifiés dans le fichier `conda_dependencies.yml`. 
   
   Le moteur Docker effectue les tâches suivantes :

    - Il télécharge l’image de base à partir d’Azure.
    - Il installe les packages Python spécifiés dans le fichier `conda_dependencies.yml`.
    - Il démarre un conteneur Docker.
    - Il copie ou référence, selon la configuration d’exécution, la copie locale du dossier du projet.      
    - Il exécute le script `iris_sklearn.py`.

   Le résultat final doit être exactement le même que celui obtenu lorsque vous ciblez **local**.

5. À présent, essayons Spark. L’image de base Docker contient une instance de Spark préalablement installée et configurée. Grâce à cette instance, vous pouvez y exécuter un script PySpark. Il s’agit d’un moyen simple de développer et tester votre programme Spark sans avoir à consacrer de temps à l’installation et à la configuration de Spark. 

   Ouvrez le fichier `iris_spark.py` . Ce script charge le fichier de données `iris.csv` et utilise l’algorithme de régression logistique à partir de la bibliothèque Spark Machine Learning pour classer le jeu de données Iris. À présent, définissez l’environnement d’exécution sur **docker-spark** et le script sur **iris_spark.py**, puis effectuez une nouvelle exécution. Ce processus prend un peu plus de temps, car une session Spark doit être créée et démarrée à l’intérieur du conteneur Docker. Vous pouvez également voir que la sortie stdout est différente de celle de `iris_spark.py`.

6. Effectuez plusieurs exécutions supplémentaires en expérimentant différents arguments. 

7. Ouvrez le fichier `iris_spark.py` pour voir le modèle de régression logistique créé à l’aide de la bibliothèque de Spark Machine Learning. 

8. Interagissez avec le volet **Travaux**, la vue d’une liste de l’historique des exécutions et la vue des détails des exécutions entre les différents environnements d’exécution.

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>Exécuter des scripts dans la fenêtre CLI Azure Machine Learning

1. Dans Azure Machine Learning Workbench, ouvrez la fenêtre de ligne de commande, sélectionnez le menu **Fichier**, puis **Ouvrir l’invite de commandes**. Votre invite de commandes démarre dans le dossier du projet avec l’invite `C:\Temp\myIris\>`.

   >[!IMPORTANT]
   >Vous devez utiliser la fenêtre de ligne de commande (ouverte à partir de Workbench) pour effectuer les étapes suivantes.

2. Utilisez l’invite de commandes pour vous connecter à Azure. 

   L’application Workbench et l’interface CLI utilisent des caches d’informations d’identification indépendants pendant l’authentification auprès des ressources Azure. Vous ne devez effectuer cette opération qu’une seule fois, jusqu’à ce que le jeton mis en cache expire. La commande **az account list** retourne la liste des abonnements disponibles pour votre connexion. S’il existe plusieurs options, utilisez la valeur d’ID de l’abonnement souhaité. Définissez cet abonnement en tant que compte par défaut à utiliser avec la commande **az account set -s**, puis indiquez la valeur ID d’abonnement. Vérifiez ensuite le paramétrage à l’aide de la commande **show** de compte.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Une fois l’authentification terminée et le contexte de l’abonnement Azure actuel défini, entrez les commandes suivantes dans la fenêtre CLI pour installer **matplotlib**, puis envoyez le script Python comme expérience à exécuter.

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Passez en revue la sortie. Vous obtenez la même sortie et les mêmes résultats que lorsque vous avez utilisé Workbench pour exécuter le script. 

5. Réexécutez le même script à l’aide de l’environnement d’exécution Docker si Docker est installé sur votre machine.

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. Dans Workbench, sélectionnez l’icône **Dossier** dans le volet de gauche pour répertorier les fichiers de projet, puis ouvrez le script Python nommé **run.py**. 

   Ce script est utile pour obtenir une boucle sur différents taux de régularisation. Exécutez plusieurs fois l’expérience avec ces taux. Ce script démarre un travail `iris_sklearn.py` avec un taux de régularisation de `10.0` (un nombre extraordinairement élevé). Le script réduit alors le taux de moitié lors de l’exécution suivante, et ainsi de suite tant que le taux n’est pas inférieur à `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Pour ouvrir le script **run.py** à partir de la ligne de commande, exécutez les commandes suivantes :

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Lorsque `run.py` se termine, un graphique apparaît dans la vue de liste de l’historique des exécutions dans Workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Exécuter des scripts dans un conteneur Docker sur une machine distante
Pour exécuter votre script dans un conteneur Docker sur une machine Linux distante, vous devez disposer d’un accès SSH (nom d’utilisateur et mot de passe) à cette machine. Le moteur Docker doit également être installé et en cours d’exécution sur cette machine distante. Pour obtenir une machine Linux de ce type, le plus simple consiste à créer une machine virtuelle DSVM (Data Science Virtual Machine) basée sur Ubuntu dans Azure. Découvrez [comment créer une machine virtuelle DSVM Ubuntu à utiliser dans Azure Machine Learning Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>La machine virtuelle DSVM basée sur CentOS n’est *pas* prise en charge.

1. Une fois la machine virtuelle créée, vous pouvez l’attacher en tant qu’environnement d’exécution si vous générez une paire de fichiers `.runconfig` et `.compute`. Utilisez la commande suivante pour générer les fichiers. Nous allons nommer le nouvel environnement `myvm`.
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach remotedocker --name myvm --address <IP address> --username <username> --password <password>
   ```
   
   >[!NOTE]
   >L’adresse IP peut également être un FQDN (nom de domaine complet) adressable publiquement, tel que `vm-name.southcentralus.cloudapp.azure.com`. Il est conseillé d’ajouter le FQDN (nom de domaine complet) à votre DSVM et de l’utiliser ici au lieu d’une adresse IP. Cette pratique est utile, car vous pourriez désactiver la machine virtuelle à un moment donné pour réaliser des économies. De plus, la prochaine fois que vous démarrez la machine virtuelle, l’adresse IP peut avoir changé.

   Exécutez ensuite la commande suivante pour construire l’image Docker sur la machine virtuelle afin de la préparer pour l’exécution des scripts :
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >Vous pouvez également changer la valeur de `PrepareEnvironment` dans `myvm.runconfig` en remplaçant la valeur par défaut `false` par `true`. Cette opération change automatiquement le conteneur Docker à la première exécution.

2. Modifiez le fichier `myvm.runconfig` généré sous `aml_config` et changez le Framework en remplaçant la valeur par défaut `PySpark` par `Python` :

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Cela fonctionne également si vous laissez le paramètre Framework défini sur PySpark. Toutefois, ce paramétrage est quelque peu inefficace si vous n’avez pas vraiment besoin d’une session Spark pour exécuter votre script Python.

3. Exécutez la même commande que celle que vous avez exécutée auparavant dans la fenêtre CLI, mais cette fois, en ciblant _myvm_ :
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   La commande s’exécute comme si vous utilisiez un environnement `docker-python`, à la différence que l’exécution se produit sur la machine virtuelle Linux distante. La fenêtre CLI affiche les mêmes informations de sortie.

4. Essayons Spark dans le conteneur. Ouvrez l’Explorateur de fichiers. Vous pouvez également effectuer l’opération à partir de la fenêtre CLI si vous êtes familiarisé avec les commandes de manipulation de fichiers de base. Effectuez une copie du fichier `myvm.runconfig` et nommez-la `myvm-spark.runconfig`. Modifiez le nouveau fichier afin de remplacer la valeur `Python` du paramètre `Framework` par `PySpark` :
   ```yaml
   "Framework": "PySpark"
   ```
   N’apportez aucune modification au fichier `myvm.compute`. La même image Docker sur la même machine virtuelle est utilisée pour l’exécution Spark. Dans le nouveau fichier `myvm-spark.runconfig`, le champ `target` pointe vers le même fichier `myvm.compute` par le biais de son nom `myvm`.

5. Entrez la commande suivante pour l’exécuter dans l’instance Spark dans le conteneur Docker distant :
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Exécuter le script dans un cluster HDInsight
Vous pouvez également exécuter ce script dans un cluster HDInsight Spark. Découvrez [comment créer un cluster HDInsight Spark à utiliser dans Azure Machine Learning Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>Le cluster HDInsight doit utiliser Stockage Blob Azure comme stockage principal. L’utilisation du stockage Azure Data Lake n’est pas encore prise en charge.

1. Si vous avez accès à un cluster Spark pour Azure HDInsight, générez une commande de configuration d’exécution HDInsight, comme indiqué ici. Indiquez comme paramètres le nom du cluster HDInsight, ainsi que vos nom d’utilisateur et mot de passe HDInsight. Utilisez la commande suivante :

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <username> --password <password>

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   Le FQDN (nom de domaine complet) du nœud de cluster est généralement `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` est le nom d’utilisateur SSH pour le cluster. La valeur par défaut est `sshuser` si vous ne la changez pas pendant la configuration d’HDInsight. La valeur n’est pas `admin`, qui correspond à l’autre utilisateur créé pendant la configuration pour activer l’accès au site web d’administration du cluster. 

2. Exécutez la commande suivante afin d’exécuter le script dans le cluster HDInsight :

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Lorsque vous effectuez une exécution sur un cluster HDInsight distant, vous pouvez également afficher les détails de l’exécution du travail YARN (Yet Another Resource Negotiator) à l’adresse `https://<cluster_name>.azurehdinsight.net/yarnui` à l’aide du compte d’utilisateur `admin`.


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans cette deuxième partie de la série de trois didacticiels, vous avez appris à utiliser les services Azure Machine Learning pour effectuer les opérations suivantes :
> [!div class="checklist"]
> * Utiliser Azure Machine Learning Workbench.
> * Ouvrir des scripts et passer le code en revue.
> * Exécuter des scripts dans un environnement local.
> * Passer en revue l’historique des exécutions.
> * Exécuter des scripts dans un environnement Docker local.
> * Exécuter des scripts dans une fenêtre Azure CLI locale.
> * Exécuter des scripts dans un environnement Docker distant.
> * Exécuter des scripts dans un environnement HDInsight cloud.

Vous êtes prêt à passer à la troisième partie de la série. Maintenant que vous avez créé le modèle de régression logistique, déployons-le en tant que service web en temps réel.

> [!div class="nextstepaction"]
> [Didacticiel 3 - Classification d’Iris : déployer des modèles](tutorial-classifying-iris-part-3.md)
