---
title: "Préparer les données pour le didacticiel de classification d’Iris dans les services de Azure Machine Learning (préversion) | Microsoft Docs"
description: "Ce didacticiel complet montre comment utiliser les services Azure Machine Learning (préversion) de bout en bout. Cette première partie décrit la préparation des données."
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
ms.openlocfilehash: 0bef557ee1394e3c786fd2c54e821b5dea28fabf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>Didacticiel : Première partie de la classification d’Iris (préparation des données)

Les services Azure Machine Learning (version préliminaire) forment une solution d’analytique avancée et de science des données intégrée de bout en bout qui permet aux scientifiques des données professionnels de préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Ce didacticiel est la première partie d’une série qui en compte trois. Dans ce didacticiel, nous allons étudier les principes de base des services d’Azure Machine Learning (préversion). Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet dans Azure Machine Learning Workbench
> * Créer un package de préparation de données
> * Générer du code Python/PySpark pour appeler un package de préparation de données

Ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporel. Les captures d’écran sont spécifiques à Windows, mais l’expérience macOS est presque identique.

## <a name="prerequisites"></a>configuration requise

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :
- un compte Azure Machine Learning Expérimentation ;
- Azure Machine Learning Workbench installé.

Si tel n’est pas le cas, suivez les étapes décrites dans l’article [Démarrage rapide : Installer et démarrer](quickstart-installation.md) pour configurer ce compte et installer l’application Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Créer un projet dans Workbench

Si vous avez suivi les étapes décrites dans l’article [Démarrage rapide : Installer et démarrer](quickstart-installation.md), vous devez disposer déjà de ce projet et pouvez passer à la section suivante.

1. Ouvrez l’application Azure Machine Learning Workbench et connectez-vous si nécessaire. 
   
   + Sur Windows, vous pouvez la lancer via le raccourci du Bureau **Machine Learning Workbench**. 
   + Sur macOS, sélectionnez **Azure Machine Learning Workbench** dans Launchpad.

1. Dans le volet **PROJETS**, sélectionnez le signe plus (+) et choisissez **Nouveau projet**.  

   ![Nouvel espace de travail](media/tutorial-classifying-iris/new_ws.png)

1. Renseignez les champs du formulaire et sélectionnez le bouton **Créer** permettant de créer un projet dans Workbench.

   Champ|Valeur suggérée pour le didacticiel|DESCRIPTION
   ---|---|---
   Nom du projet | myIris |Entrez un nom unique qui identifie votre compte. Vous pouvez utiliser votre propre nom, ou celui d’un service ou projet qui identifie le mieux l’expérimentation. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés. 
   Répertoire du projet | c:\Temp\ | Spécifiez le répertoire dans lequel le projet est créé.
   Description du projet | _Laisser vide_ | Champ facultatif utile pour décrire les projets.
   Visualstudio.com |_Laisser vide_ | Champ facultatif. Un projet peut éventuellement être associé à un répertoire Git sur Visual Studio Team Services pour le contrôle de code source et la collaboration. [Voici les étapes de configuration à suivre.](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Espace de travail | IrisGarden (s’il existe) | Choisissez un espace de travail que vous avez créé pour votre compte Expérimentation dans le portail Azure. <br/>Si vous avez suivi le démarrage rapide, vous devez disposer d’un espace de travail nommé IrisGarden. Si ce n’est pas le cas, sélectionnez celui que vous avez créé en même temps que votre compte Expérimentation ou tout autre compte que vous souhaitez utiliser.
   Modèle de projet | Classifying Iris | Les modèles contiennent des scripts et des données que vous pouvez utiliser pour découvrir le produit. Ce modèle contient les scripts et les données nécessaires pour ce démarrage rapide et les autres didacticiels de ce site de documentation. 

   ![Nouveau projet](media/tutorial-classifying-iris/new_project.png)
 
 Un nouveau projet est créé, et le tableau de bord du projet s’ouvre avec ce projet. À ce stade, vous pouvez explorer la page d’accueil du projet, les sources de données, les blocs-notes et les fichiers de code source. 

## <a name="create-a-data-preparation-package"></a>Créer un package de préparation de données

Dans cette partie du didacticiel, vous explorez les données et démarrez le processus de préparation des données. Lorsque vous préparez vos données dans Azure Machine Learning Workbench, une représentation JSON des transformations que vous effectuez dans Workbench est stockée dans un package de préparation des données locales (fichier *.dprep). Ce package de préparation des données est le conteneur principal de votre travail de préparation des données dans Workbench.

Ce package de préparation des données peut être transmis pour exécution à un runtime, par exemple local-C#/CoreCLR, Scala/Spark ou Scala/HDI. où le code est généré pour le runtime approprié pour l’exécution. 

1. Sélectionnez l’icône Dossier pour ouvrir l’affichage des fichiers, puis sélectionnez le fichier **iris.csv** pour l’ouvrir.  

   Le fichier se compose d’une table de 5 colonnes et de 150 lignes. Elle comprend quatre colonnes numériques et une colonne cible de chaînes. Il n’y a pas d’en-têtes de colonnes.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Évitez d’inclure des fichiers de données dans votre dossier de projet, en particulier s’ils sont volumineux. Nous incluons le fichier **iris.csv** dans ce modèle à des fins de démonstration, car il ne pèse pratiquement rien. Pour plus d’informations, consultez l’article [Guide pratique pour lire et écrire des fichiers de données volumineux](how-to-read-write-files.md).

2. Dans la **Vue de données**, cliquez sur le signe « plus » (**+**) pour ajouter une nouvelle source de données. La page **Ajouter une source de données** s’ouvre. 

   ![Vue de données](media/tutorial-classifying-iris/data_view.png)

3. Sélectionnez **Fichiers texte (*.csv, .json, .txt...)**, puis cliquez sur **Suivant**.
   ![Source de données](media/tutorial-classifying-iris/data-source.png)
   

4. Accédez au fichier **iris.csv**, puis cliquez sur **Suivant**.  
 
   ![Sélectionner iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Veillez à sélectionner le fichier **iris.csv** dans le répertoire de projet actif pour cet exercice. Sinon, les étapes suivantes risquent d’échouer.
   
5. Laissez les valeurs par défaut et cliquez sur **Terminer**.

6. Un nouveau fichier nommé **iris-1.dsource** est créé. Le fichier est nommé de façon unique à l’aide de « -1 », car l’exemple de projet est déjà fourni avec un fichier **iris.dsource** non numéroté.  

   Le fichier s’ouvre et les données s’affichent. Une série d’en-têtes de colonne, de **Colonne1** à **Colonne5**, est automatiquement ajoutée à ce jeu de données. Si vous faites défiler les données vers le bas, vous noterez que la dernière ligne du jeu de données est vide. La ligne est vide, car le fichier CSV comporte un saut de ligne supplémentaire.

   ![Affichage de données Iris](media/tutorial-classifying-iris/iris_data_view.png)

7. Cliquez sur le bouton **Métriques**. Observez les histogrammes. Un ensemble complet de statistiques a été calculé pour chaque colonne. Vous pouvez également sélectionner le bouton **Données** pour afficher à nouveau les données. 

   ![Affichage de données Iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Cliquez sur le bouton **Préparer**. La boîte de dialogue **Préparer** s’ouvre. 

   L’exemple de projet est fourni avec un fichier **iris.dprep**. Par défaut, vous êtes invité à créer un nouveau flux de données dans le package de préparation de données **iris.dprep** existant. 

   Sélectionnez **+ Nouveau package de préparation de données** dans le menu déroulant, entrez une nouvelle valeur pour le nom du package, utilisez **iris-1**, puis cliquez sur **OK**.

   ![Affichage de données Iris](media/tutorial-classifying-iris/new_dprep.png)

   Un nouveau package de préparation de données nommé **iris-1.dprep** est créé et ouvert dans l’éditeur de préparation des données.

9. Effectuons à présent quelques tâches de base de préparation de données. Sélectionnez chaque en-tête de colonne pour activer la modification du texte d’en-tête et renommez chaque colonne comme suit : 

   Dans l’ordre, entrez **Longueur des sépales**, **Largeur des sépales**, **Longueur des pétales**, **Largeur des pétales** et **Espèces** pour les cinq colonnes.

   ![Renommer les colonnes](media/tutorial-classifying-iris/rename_column.png)

10. Pour compter les valeurs distinctes, sélectionnez la colonne **Espèces**, puis cliquez dessus avec le bouton droit pour la sélectionner. Sélectionnez **Nombre de valeurs** dans le menu déroulant. 

   Cette action ouvre le volet **Inspecteurs** volet au-dessous des données. Un histogramme comprenant quatre barres s’affiche. La colonne cible comporte trois valeurs distinctes : **Iris_virginica**, **Iris_versicolor**, **Iris-setosa**et une valeur **(null)**.

   ![Sélectionner les nombres de valeur](media/tutorial-classifying-iris/value_count.png)

11. Pour exclure les valeurs Null, sélectionnez l’étiquette « Null » ainsi que le signe moins (**-**). La ligne Null devient alors grise pour indiquer qu’elle a été filtrée. 

   ![Nombre de valeurs de l’histogramme](media/tutorial-classifying-iris/filter_out.png)

12. Notez que les étapes individuelles sont détaillées dans le volet **ÉTAPES**. Lorsque vous avez renommé les colonnes et filtré les lignes correspondant à une valeur null, chaque action a été enregistrée comme une étape de préparation des données. Vous pouvez modifier les étapes individuelles pour ajuster les paramètres, réorganiser les étapes et supprimer des étapes.

   ![Étapes](media/tutorial-classifying-iris/steps.png)

13. Fermez l’éditeur de préparation des données. Sélectionnez **Fermer** (x) sur l’onglet **iris-1** qui comporte l’icône de graphique. Votre travail est automatiquement sauvegardé dans le fichier **iris-1.dprep** sous le titre **Préparations des données**.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Générer du code Python/PySpark pour appeler un package de préparation de données

<!-- The output/results of a Package can be explored in Python or via a Jupyter Notebook. A Package can be executed across multiple runtimes including local Python, Spark (including in Docker), and HDInsight. A Package contains one or more Dataflows that are the steps and transforms applied to the data. A Package may use another Package as a Data Source (referred to as a Reference Data Flow). -->

1. Recherchez le fichier **iris-1.dprep** dans l’onglet Préparation des données.

1. Cliquez avec le bouton droit sur le fichier **iris-1.dprep**, puis sélectionnez **Générer un fichier de code d’accès aux données** dans le menu contextuel. 

   ![Générer le code](media/tutorial-classifying-iris/generate_code.png)

   Un nouveau fichier nommé **iris-1.py** s’ouvre et affiche les lignes de code suivantes pour appeler la logique que vous avez créée en tant que package de préparation des données :

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   En fonction du contexte d’exécution du code, `df` représente les différentes sortes de trames de données. Une trame de donnée [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) est utilisée lorsqu’elle est exécutée par un runtime Python. Une trame de données [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) est utilisée quand elle est exécutée dans un contexte Spark. 
   
   Pour découvrir comment préparer des données dans Azure Machine Learning Workbench, consultez le guide [Bien démarrer avec la préparation des données](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez utilisé Azure Machine Learning Workbench pour effectuer les tâches suivantes :
> [!div class="checklist"]
> * Création d'un projet
> * Créer un package de préparation de données
> * Générer du code Python/PySpark pour appeler un package de préparation de données

Vous pouvez à présent passer à la partie suivante de la série de didacticiels, qui vous expliquera comment générer un modèle Azure Machine Learning :
> [!div class="nextstepaction"]
> [Didacticiel 2 : Génération de modèles](tutorial-classifying-iris-part-2.md)
