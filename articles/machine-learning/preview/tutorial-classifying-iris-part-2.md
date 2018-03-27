---
title: Didacticiel de génération d’un modèle pour les services Azure Machine Learning (préversion) | Microsoft Docs
description: Ce didacticiel complet montre comment utiliser les services Azure Machine Learning (préversion) de bout en bout. Cela fait partie de deux et traite l’expérimentation.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: e4fc13e88d56677687e0f97d156f9b7761eae1d8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>Didacticiel 2 : Classifier Iris - Générer un modèle
Les services Azure Machine Learning (préversion) forment une solution d’analytique avancée et de science des données intégrée qui permet aux scientifiques des données professionnels de préparer des données, développer des expériences et déployer des modèles à l’échelle du cloud.

Ce didacticiel est **le deuxième d’une série de trois**. Dans cette partie du didacticiel, vous utilisez les services Azure Machine Learning pour :

> [!div class="checklist"]
> * Ouvrir des scripts et passer le code en revue
> * Exécuter des scripts dans un environnement local
> * Examiner les historiques des exécutions
> * Exécuter des scripts dans une fenêtre Azure CLI locale
> * Exécuter des scripts dans un environnement Docker local
> * Exécuter des scripts dans un environnement Docker distant
> * Exécuter des scripts dans un environnement Azure HDInsight cloud

Ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporel. 

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vous avez besoin des éléments suivants :
- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 
- Un compte d’expérimentation et Azure Machine Learning Workbench installé, comme décrit dans ce [démarrage rapide](quickstart-installation.md)
- Le projet et les données Iris préparées du [premier didacticiel](tutorial-classifying-iris-part-1.md)
- Un moteur Docker installé et exécuté en local. L’édition Communauté de Docker suffit. Découvrez comment installer Docker ici : https://docs.docker.com/engine/installation/.

## <a name="review-irissklearnpy-and-the-configuration-files"></a>Examiner les fichiers de configuration et iris_sklearn.py

1. Lancez l’application Machine Learning Workbench.

1. Ouvrez le projet **myIris** que vous avez créé dans la [première partie de la série de didacticiels](tutorial-classifying-iris-part-1.md).

2. Dans le projet ouvert, sélectionnez le bouton **Fichiers** (l’icône de dossier) dans le volet tout à gauche pour ouvrir la liste des fichiers dans votre dossier de projet.

   ![Ouvrir le projet Azure Machine Learning Workbench](media/tutorial-classifying-iris/2-project-open.png)

3. Sélectionnez le fichier de script Python **iris_sklearn.py**. 

   ![Choisir un script](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   Le code s’ouvre dans un nouvel onglet de l’éditeur de texte dans Workbench. Il s’agit du script que vous utilisez tout au long de cette partie du didacticiel. 

   >[!NOTE]
   >Le code que vous voyez peut différer légèrement du code précédent, car cet exemple de projet est fréquemment mis à jour.
   
   ![Ouvrir un fichier](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. Examinez le code de script Python pour vous familiariser avec le style de codage. 

   Le script **iris_sklearn.py** effectue les tâches suivantes :

   * Il charge le package de préparation des données par défaut nommé **iris.dprep** pour créer un [cadre de données pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

   * Il ajoute des caractéristiques aléatoires pour rendre le problème plus difficile à résoudre. Le caractère aléatoire est nécessaire, car Iris est un petit jeu de données qui est facilement classé avec près de 100 % de précision.

   * Il utilise la bibliothèque de machine learning [scikit-learn](http://scikit-learn.org/stable/index.html) pour générer un modèle de régression logistique.  Cette bibliothèque est fournie avec Azure Machine Learning Workbench par défaut.

   * Il sérialise le modèle avec la bibliothèque [pickle](https://docs.python.org/3/library/pickle.html) dans un fichier dans le dossier `outputs`. 
   
   * Il charge le modèle sérialisé, puis le désérialise en mémoire.

   * Il utilise le modèle désérialisé pour faire une prédiction sur un nouvel enregistrement. 

   * Il trace deux graphiques, une matrice de confusion et une courbe ROC multiclasses, à l’aide de la bibliothèque [matplotlib](https://matplotlib.org/), puis les enregistre dans le dossier `outputs`. Si elle n’existait pas déjà, vous pouvez installer cette bibliothèque dans votre environnement.

   * Il trace automatiquement le taux de régularisation et la précision du modèle dans l’historique des exécutions. L’objet `run_logger` est utilisé pour enregistrer le taux de régularisation et la précision du modèle dans les journaux. 


## <a name="run-irissklearnpy-in-your-local-environment"></a>Exécuter iris_sklearn.py dans votre environnement local

1. Démarrez l’interface de ligne de commande Azure Machine Learning :
   1. Lancez Azure Machine Learning Workbench.

   1. Dans le menu Workbench, sélectionnez **Fichier** > **Ouvrir l’invite de commande**. 
   
   La fenêtre de l’interface de ligne de commande d’Azure Machine Learning commence dans le dossier du projet `C:\Temp\myIris\>` sur Windows. Ce projet est le même que celui que vous avez créé dans la première partie du didacticiel.

   >[!IMPORTANT]
   >Vous devez utiliser cette fenêtre d’interface de ligne de commande pour accomplir les étapes suivantes.

1. Dans la fenêtre d’interface de ligne de commande, installez la bibliothèque de traçage Python, **matplotlib**, si vous n’avez pas encore de bibliothèque.

   Le script **iris_sklearn.py** possède des dépendances sur les deux packages Python : **scikit-learn** et **matplotlib**.  Pour plus de simplicité, le package **scikit-learn** est installé par Azure Machine Learning Workbench. Mais, vous devez installer **matplotlib** si vous ne l’avez pas encore installé.

   Si vous passez aux étapes suivantes sans installer **matplotlib**, le code dans ce didacticiel peut néanmoins s’exécuter correctement. Toutefois, le code ne pourra pas produire la sortie de la matrice de confusion et les tracés de courbe ROC multiclasses indiqués dans les visualisations de l’historique.

   ```azurecli
   pip install matplotlib
   ```

   Cette installation prend environ une minute.

1. Revenez à l’application Workbench. 

1. Recherchez l’onglet appelé **iris_sklearn.py**. 

   ![Rechercher l’onglet avec le script](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. Dans la barre d’outils de cet onglet, sélectionnez **local** comme environnement d’exécution, puis `iris_sklearn.py` comme script à exécuter. Ils peuvent être déjà sélectionnés.

   ![Choix de l’option « local » et du script](media/tutorial-classifying-iris/2-local-script.png)

1. Passez à droite de la barre d’outils et entrez `0.01` dans le champ **Arguments**. 

   Cette valeur correspond au taux de régularisation du modèle de régression logistique.

   ![Choix de l’option « local » et du script](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. Sélectionnez le bouton **Exécuter**. Un travail est planifié immédiatement. Le travail est répertorié dans le volet **Travaux** sur le côté droit de la fenêtre Workbench. 

   ![Choix de l’option « local » et du script](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   Après quelques instants, l’état du travail passe de **Soumission** à **Exécution**, puis à **Terminé**.

1. Sélectionnez **Terminé** dans le texte d’état du travail dans le volet **Travaux**. 

   ![Exécuter sklearn](media/tutorial-classifying-iris/2-completed.png)

   Une fenêtre indépendante s’ouvre, puis affiche le texte de sortie standard (stdout) du script en cours d’exécution. Pour fermer le texte stdout, sélectionnez le bouton **Fermer** (**x**) dans le coin supérieur droit de la fenêtre indépendante.

   ![Sortie standard](media/tutorial-classifying-iris/2-standard-output.png)

9. Dans le même état du travail dans le volet **Travaux**, sélectionnez le texte en bleu **iris_sklearn.py [n]** (_n_ est le numéro d’exécution) juste au-dessus de l’état **Terminé** et de l’heure de début. La fenêtre **Propriétés de l’exécution** s’ouvre et affiche les informations suivantes pour cette exécution particulière :
   - Informations **Propriétés de l’exécution**
   - **Sorties**
   - **Métriques**
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

    Dans le champ **Arguments**, entrez une série de valeurs comprises entre `0.001` et `10`. Sélectionnez **Exécuter** pour exécuter le code plusieurs fois encore. La valeur d’argument que vous changez chaque fois est transmise au modèle de régression logistique dans le code, ce qui entraîne des résultats différents systématiquement.

## <a name="review-the-run-history-in-detail"></a>Passer en revue l’historique des exécutions
Dans Azure Machine Learning Workbench, chaque exécution du script est capturée dans un enregistrement de l’historique des exécutions. Si vous ouvrez la vue **Exécutions**, vous pouvez afficher l’historique des exécutions d’un script particulier.

1. Pour ouvrir la liste des **exécutions**, sélectionnez le bouton **Exécutions** (icône en forme d’horloge) dans la barre d’outils de gauche. Sélectionnez ensuite **iris_sklearn.py** pour afficher le **Tableau de bord des exécutions** de `iris_sklearn.py`.

   ![Vue de l’exécution](media/tutorial-classifying-iris/run_view.png)

1. L’onglet **Tableau de bord des exécutions** s’ouvre. 

   Passez en revue les statistiques capturées sur plusieurs exécutions. Les graphiques sont affichés en haut de l’onglet. Chaque exécution porte un numéro consécutif, et les détails de l’exécution sont répertoriés dans le tableau en bas de l’écran.

   ![Tableau de bord des exécutions](media/tutorial-classifying-iris/run_dashboard.png)

1. Filtrez la table, puis sélectionnez l’un des graphiques pour afficher l’état, la durée, la précision et le taux de régularisation de chaque exécution. 

1. Sélectionnez les cases à cocher en regard de deux ou plusieurs exécutions dans la table **Exécutions**. Sélectionnez le bouton **Comparer** pour ouvrir un volet de comparaison détaillée. Passez en revue la comparaison côte-à-côte. 

1. Pour revenir au **Tableau de bord des exécutions**, sélectionnez le bouton précédent **Liste des exécutions** dans le coin supérieur gauche du volet **Comparaison**.

   ![Retour à la liste des exécutions](media/tutorial-classifying-iris/2-compare-back.png)

1. Sélectionnez une exécution pour en afficher les détails. Notez que les statistiques de l’exécution sélectionnée sont répertoriées dans la section **Propriétés de l’exécution**. Les fichiers écrits dans le dossier de sortie sont répertoriés dans la section **Sorties** et vous pouvez télécharger les fichiers à partir de celle-ci.

   ![Détails de l’exécution](media/tutorial-classifying-iris/run_details.png)

   Les deux tracés, la matrice de confusion et la courbe ROC multiclasses, apparaissent dans la section **Visualisations**. Tous les fichiers journaux se trouvent également dans la section **Journaux**.


## <a name="run-scripts-in-local-docker-environments"></a>Exécuter des scripts dans des environnements Docker locaux

Vous pouvez essayer d’exécuter des scripts dans un conteneur Docker local (facultatif). Vous pouvez configurer des environnements d’exécution supplémentaires, tels que Docker, et exécuter votre script dans ces environnements. 

>[!NOTE]
>Pour essayer de répartir les scripts à exécuter dans un conteneur Docker d’une machine virtuelle Azure distante ou un cluster Azure HDInsight Spark, vous pouvez suivre les [instructions pour créer une machine virtuelle de science des données Azure basée sur Ubuntu ou un cluster HDInsight](how-to-create-dsvm-hdi.md).

1. Si vous ne l’avez pas encore fait, installez et démarrez Docker localement sur votre ordinateur Windows ou MacOS. Pour plus d’informations, consultez les instructions d’installation de Docker à l’emplacement https://docs.docker.com/install/. L’édition Communauté suffit.

1. Dans le volet de gauche, sélectionnez l’icône **Dossier** pour ouvrir la liste **Fichiers** associée à votre projet. Développez le dossier `aml_config`. 

2. Plusieurs environnements sont préconfigurés : **docker-python**, **docker-spark** et **local**. 

   Chaque environnement possède deux fichiers, tels que `docker.compute` (pour **docker-python** et **docker-spark**) et `docker-python.runconfig`. Ouvrez chaque fichier pour constater que certaines options sont configurables dans l’éditeur de texte.  

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
   
   Le moteur Docker effectue les tâches suivantes :

    - Il télécharge l’image de base à partir d’Azure.
    - Il installe les packages Python spécifiés dans le fichier `conda_dependencies.yml`.
    - Il démarre un conteneur Docker.
    - Il copie ou référence, selon la configuration d’exécution, la copie locale du dossier du projet.      
    - Il exécute le script `iris_sklearn.py`.

   Le résultat final doit être exactement le même que celui obtenu lorsque vous ciblez **local**.

5. À présent, essayons Spark. L’image de base Docker contient une instance de Spark préalablement installée et configurée que vous pouvez utiliser pour exécuter un script PySpark. Utiliser cette image de base constitue un moyen simple de développer et tester votre programme Spark sans avoir à consacrer de temps à l’installation et à la configuration de Spark. 

   Ouvrez le fichier `iris_spark.py` . Ce script charge le fichier de données `iris.csv` et utilise l’algorithme de régression logistique à partir de la bibliothèque Spark Machine Learning pour classer le jeu de données Iris. À présent, définissez l’environnement d’exécution sur **docker-spark** et le script sur **iris_spark.py**, puis effectuez une nouvelle exécution. Ce processus prend un peu plus de temps, car une session Spark doit être créée et démarrée à l’intérieur du conteneur Docker. Vous pouvez également voir que la sortie stdout est différente de celle de `iris_spark.py`.

6. Effectuez plusieurs exécutions supplémentaires en expérimentant différents arguments. 

7. Ouvrez le fichier `iris_spark.py` pour voir le modèle de régression logistique créé à l’aide de la bibliothèque de Spark Machine Learning. 

8. Interagissez avec le volet **Travaux**, la vue d’une liste de l’historique des exécutions et la vue des détails des exécutions entre les différents environnements d’exécution.

## <a name="run-scripts-in-the-cli-window"></a>Exécuter des scripts dans la fenêtre de l’interface de ligne de commande

1. Démarrez l’interface de ligne de commande Azure Machine Learning :
   1. Lancez Azure Machine Learning Workbench.

   1. Dans le menu Workbench, sélectionnez **Fichier** > **Ouvrir l’invite de commande**. 
   
   L’interface de ligne de commande commence dans le dossier du projet `C:\Temp\myIris\>` sur Windows. Il s’agit du projet que vous avez créé dans la première partie du didacticiel.

   >[!IMPORTANT]
   >Vous devez utiliser cette fenêtre d’interface de ligne de commande pour accomplir les étapes suivantes.

1. Dans la fenêtre de l’interface de ligne de commande, connectez-vous à Azure. [En savoir plus sur az login](https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   Vous êtes peut-être déjà connecté. Dans ce cas, vous pouvez ignorer cette étape.

   1. À l’invite de commandes, tapez :
      ```azurecli
      az login
      ```

      Cette commande renvoie un code à utiliser dans votre navigateur à l’adresse https://aka.ms/devicelogin.

   1. Accédez à https://aka.ms/devicelogin dans votre navigateur.

   1. Lorsque vous y êtes invité, entrez dans votre navigateur le code que vous avez reçu dans l’interface de ligne de commande.

   L’application Workbench et l’interface de ligne de commande utilisent des caches d’informations d’identification indépendants pendant l’authentification auprès des ressources Azure. Après vous être connecté, vous n’aurez plus besoin de vous authentifier à nouveau jusqu’à ce que le jeton mis en cache expire. 

1. Si votre organisation a plusieurs abonnements Azure (environnement d’entreprise), vous devez définir l’abonnement à utiliser. Trouvez votre abonnement, définissez-le à l’aide de l’ID d’abonnement, puis testez-le.

   1. Répertoriez chaque abonnement Azure auquel vous avez accès à l’aide de cette commande :
   
      ```azurecli
      az account list -o table
      ```

      La commande **az account list** retourne la liste des abonnements disponibles pour votre connexion. 
      S’il en existe plusieurs, identifiez la valeur d’ID de l’abonnement que vous souhaitez utiliser.

   1. Définissez l’abonnement Azure que vous souhaitez utiliser en tant que compte par défaut :
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      où \<your-subscription-id\> est la valeur d’ID de l’abonnement que vous souhaitez utiliser. N’incluez pas les chevrons.

   1. Confirmez le nouveau paramètre d’abonnement en demandant les détails de l’abonnement actuel. 

      ```azurecli
      az account show
      ```    

1. Dans la fenêtre d’interface de ligne de commande, installez la bibliothèque de traçage Python, **matplotlib**, si vous n’avez pas encore de bibliothèque.

   ```azurecli
   pip install matplotlib
   ```

1. Dans la fenêtre de l’interface de ligne de commande, envoyez le script **iris_sklearn.py** en tant qu’expérience.

   L’exécution du script iris_sklearn.py a lieu dans le contexte de calcul local.

   + Sous Windows :
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + Sur MacOS :
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   Votre sortie doit ressembler à ce qui suit :
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
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
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. Passez en revue la sortie. Vous obtenez la même sortie et les mêmes résultats que lorsque vous avez utilisé Workbench pour exécuter le script. 

1. Dans la fenêtre de l’interface de ligne de commande, exécutez le script Python **iris_sklearn.py**, en utilisant une fois de plus un environnement d’exécution Docker (si vous avez Docker installé sur votre ordinateur).

   + Si votre conteneur est sous Windows : 
     |Exécution<br/>Environnement|Commande sous Windows|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + Si votre conteneur est sous MacOS : 
     |Exécution<br/>Environnement|Commande sous MacOS|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. Revenez à Workbench, et :
   1. Sélectionnez l’icône de dossier dans le volet gauche pour répertorier les fichiers projet.
   
   1. Ouvrez le script Python nommé **run.py**. Ce script est utile pour obtenir une boucle sur différents taux de régularisation. 

   ![Retour à la liste des exécutions](media/tutorial-classifying-iris/2-runpy.png)

1. Exécutez plusieurs fois l’expérience avec ces taux. 

   Ce script démarre un travail `iris_sklearn.py` avec un taux de régularisation de `10.0` (un nombre extraordinairement élevé). Le script réduit alors le taux de moitié lors de l’exécution suivante, et ainsi de suite tant que le taux n’est pas inférieur à `0.005`. 

   Le script contient le code suivant :

   ![Retour à la liste des exécutions](media/tutorial-classifying-iris/2-runpy-code.png)

1. Exécutez le script **run.py** à partir de la ligne de commande, comme suit :

   ```cmd
   python run.py
   ```

   Cette commande envoie iris_sklearn.py plusieurs fois avec des taux de régularisation différents

   Lorsque `run.py` se termine, un graphique apparaît dans la vue de liste de l’historique des exécutions dans Workbench, contenant plusieurs mesures.

## <a name="run-scripts-in-a-remote-docker-container"></a>Exécuter des scripts dans un conteneur Docker à distance
Pour exécuter votre script dans un conteneur Docker sur une machine Linux distante, vous devez disposer d’un accès SSH (nom d’utilisateur et mot de passe) à cette machine. Le moteur Docker doit également être installé et en cours d’exécution sur cette machine. Pour obtenir une machine Linux de ce type, le plus simple consiste à créer une machine virtuelle DSVM (Data Science Virtual Machine) basée sur Ubuntu dans Azure. Découvrez [comment créer une machine virtuelle DSVM Ubuntu à utiliser dans Azure Machine Learning Workbench](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal).

>[!NOTE] 
>La machine virtuelle DSVM basée sur CentOS n’est *pas* prise en charge.

1. Une fois la machine virtuelle créée, vous pouvez l’attacher en tant qu’environnement d’exécution en générant une paire de fichiers `.runconfig` et `.compute`. Utilisez la commande suivante pour générer les fichiers. 

 Nous allons nommer la nouvelle cible de calcul `myvm`.
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >L’adresse IP peut également être un FQDN (nom de domaine complet) adressable publiquement, tel que `vm-name.southcentralus.cloudapp.azure.com`. Il est conseillé d’ajouter un FQDN (nom de domaine complet) à votre DSVM et de l’utiliser au lieu d’une adresse IP. Cette pratique est utile, car vous pourriez désactiver la machine virtuelle à un moment donné pour réaliser des économies. De plus, la prochaine fois que vous démarrez la machine virtuelle, l’adresse IP peut avoir changé.

   >[!NOTE]
   >En plus du nom d’utilisateur et du mot de passe, vous pouvez aussi spécifier une clé privée et la phrase secrète correspondante (s’il y en a une) avec l’option `--private-key-file` et l’option (non obligatoire) `--private-key-passphrase`.

   Ensuite, préparez la cible de calcul **myvm** en exécutant cette commande.
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   La commande précédente construit l’image Docker sur la machine virtuelle afin de la préparer pour l’exécution des scripts.
   
   >[!NOTE]
   >Vous pouvez également changer la valeur de `PrepareEnvironment` dans `myvm.runconfig` en remplaçant la valeur par défaut `false` par `true`. Cette opération change automatiquement le conteneur Docker à la première exécution.

2. Modifiez le fichier `myvm.runconfig` généré sous `aml_config` et changez le Framework en remplaçant la valeur par défaut `PySpark` par `Python` :

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >PySpark fonctionne aussi, mais l’utilisation de Python est plus efficace si vous n’avez pas vraiment besoin d’une session Spark pour exécuter votre script Python.

3. Exécutez la même commande que celle que vous avez exécutée auparavant dans la fenêtre d’interface de ligne de commande, mais cette fois, en ciblant _myvm_ pour exécuter iris_sklearn.py dans un conteneur Docker distant :
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   La commande s’exécute comme si vous utilisiez un environnement `docker-python`, à la différence que l’exécution se produit sur la machine virtuelle Linux distante. La fenêtre CLI affiche les mêmes informations de sortie.

4. Essayons Spark dans le conteneur. Ouvrez l’Explorateur de fichiers. Effectuez une copie du fichier `myvm.runconfig` et nommez-la `myvm-spark.runconfig`. Modifiez le nouveau fichier afin de remplacer la valeur `Python` du paramètre `Framework` par `PySpark` :
   ```yaml
   Framework: PySpark
   ```
   N’apportez aucune modification au fichier `myvm.compute`. La même image Docker sur la même machine virtuelle est utilisée pour l’exécution Spark. Dans le nouveau fichier `myvm-spark.runconfig`, le champ `Target` pointe vers le même fichier `myvm.compute` par le biais de son nom `myvm`.

5. Entrez la commande suivante pour exécuter le script **iris_spark.py** dans l’instance Spark dans le conteneur Docker distant :
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>Exécuter des scripts dans des clusters HDInsight
Vous pouvez également exécuter ce script dans un cluster HDInsight Spark. Découvrez [comment créer un cluster HDInsight Spark à utiliser dans Azure Machine Learning Workbench](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal).

>[!NOTE] 
>Le cluster HDInsight doit utiliser Stockage Blob Azure comme stockage principal. L’utilisation du stockage Azure Data Lake n’est pas encore prise en charge.

1. Si vous avez accès à un cluster Spark pour Azure HDInsight, générez une commande de configuration d’exécution HDInsight, comme indiqué ici. Indiquez comme paramètres le nom du cluster HDInsight, ainsi que vos nom d’utilisateur et mot de passe HDInsight. 

   Utilisez la commande suivante pour créer une cible de calcul qui pointe vers un cluster HDInsight :

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   Pour préparer le cluster HDInsight, exécutez cette commande :

   ```
   az ml experiment prepare -c myhdi
   ```

   Le FQDN (nom de domaine complet) du nœud de cluster est généralement `<your_cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` est le nom d’utilisateur SSH de cluster défini pendant la configuration de HDInsight. Par défaut, la valeur est `sshuser`. La valeur n’est pas `admin`, qui correspond à l’autre utilisateur créé pendant la configuration pour activer l’accès au site web d’administration du cluster. 

2. Exécutez le script **iris_spark.py** dans le cluster HDInsight avec cette commande :

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >Lorsque vous effectuez une exécution sur un cluster HDInsight distant, vous pouvez également afficher les détails de l’exécution du travail YARN (Yet Another Resource Negotiator) à l’adresse `https://<your_cluster_name>.azurehdinsight.net/yarnui` à l’aide du compte d’utilisateur `admin`.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans cette deuxième partie de la série de trois didacticiels, vous avez appris comment :
> [!div class="checklist"]
> * Ouvrir des scripts et vérifier le code dans Workbench
> * Exécuter des scripts dans un environnement local
> * Passer en revue l’historique des exécutions
> * Exécuter des scripts dans un environnement Docker local

Maintenant, vous pouvez essayer la troisième partie de cette série de didacticiels dans laquelle vous pouvez déployer le modèle de régression logistique que vous avez créé en tant que service web en temps réel.

> [!div class="nextstepaction"]
> [Didacticiel 3 : Classifier Iris : déployer un modèle](tutorial-classifying-iris-part-3.md)
