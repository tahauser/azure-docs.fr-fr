---
title: "Préparer les données pour le didacticiel de classification d’Iris dans les services de Azure Machine Learning (préversion) | Microsoft Docs"
description: "Ce didacticiel complet montre comment utiliser les services Azure Machine Learning (préversion) de bout en bout. Cette première partie décrit la préparation des données."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 09/28/2017
ms.openlocfilehash: 4e558518a5a1fb7b4cd0a58fe2453fd4c083b46a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="classify-iris-part-1-prepare-the-data"></a>Classification d’Iris, partie 1 : préparer les données
Les services Azure Machine Learning (préversion) forment une solution d’analytique avancée et de science des données intégrée de bout en bout qui permet aux scientifiques des données professionnels de préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Ce didacticiel est la première partie d’une série qui en compte trois. Dans ce didacticiel, nous allons étudier les principes de base des services d’Azure Machine Learning (préversion). Vous allez apprendre à effectuer les actions suivantes :
> [!div class="checklist"]
> * Créer un projet dans Azure Machine Learning Workbench
> * Créer un package de préparation de données
> * Générer du code Python/PySpark pour appeler un package de préparation de données

Ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporel. Les captures d’écran sont spécifiques à Windows, mais l’expérience Mac OS est presque identique.

## <a name="prerequisites"></a>configuration requise
- Créez un compte Azure Machine Learning - Expérimentation.
- Installez Azure Machine Learning Workbench.

Vous pouvez installer l’application Azure Machine Learning Workbench en suivant les instructions indiquées dans le [Guide de démarrage rapide sur l’installation et la création](quickstart-installation.md). Cette installation inclut également l’outil en ligne de commande multiplateforme Azure, également appelé Azure CLI.

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Créer un nouveau projet dans Azure Machine Learning Workbench
1. Ouvrez l’application Azure Machine Learning Workbench et connectez-vous si nécessaire. Dans le volet **PROJETS**, cliquez sur le signe « plus » (**+**) pour créer un **Nouveau projet**.

   ![Nouvel espace de travail](media/tutorial-classifying-iris/new_ws.png)

2. Sous **Créer un projet**, indiquez les détails du projet : 

   ![Nouveau projet](media/tutorial-classifying-iris/new_project.png)

   - Dans la zone **Nom du projet**, nommez le projet. Par exemple, utilisez la valeur **myIris**.
   - Sélectionnez le **Répertoire du projet** dans lequel le projet est créé. Par exemple, utilisez la valeur `C:\Temp\`. 
   - Renseignez éventuellement le champ **Description du projet**. 
   - Le champ **Dépôt Git**, également facultatif, peut être vide. Vous pouvez fournir un dépôt Git vide existant (dépôt sans branche maître) sur Visual Studio Team Services. Si vous utilisez un dépôt Git existant, vous pourrez activer les scénarios d’itinérance et de partage ultérieurement. Pour plus d’informations, consultez l’article [Utilisation d’un référentiel Git](using-git-ml-project.md). 
   - Sélectionnez un **Espace de travail**. Par exemple, ce didacticiel utilise **IrisGarden**. 
   - Sélectionnez le modèle **Classification d’Iris** dans la liste des modèles de projet. 

3. Cliquez sur le bouton **Créer**. Un projet est créé et ouvert pour vous.

## <a name="create-a-data-preparation-package"></a>Créer un package de préparation de données
1. Ouvrez le fichier **iris.csv** à partir de **l’affichage des fichiers**. Le fichier se compose d’une table de 5 colonnes et de 150 lignes. Elle comprend quatre colonnes numériques et une colonne cible de chaînes. Il n’y a pas d’en-têtes de colonnes.

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

9. Effectuons à présent quelques tâches de base de préparation de données. Renommez les colonnes. Sélectionnez chaque en-tête de colonne pour activer la modification du texte d’en-tête. 

   Entrez la **Longueur des sépales**, la **Largeur des sépales**, la **Longueur des pétales**, la **Largeur des pétales**, et les **Espèces** pour les cinq colonnes, respectivement.

   ![Renommer les colonnes](media/tutorial-classifying-iris/rename_column.png)

10. Pour compter les valeurs distinctes, sélectionnez la colonne **Espèces**, puis cliquez dessus avec le bouton droit pour la sélectionner. Sélectionnez **Nombre de valeurs** dans le menu déroulant. 

   ![Sélectionner les nombres de valeur](media/tutorial-classifying-iris/value_count.png)

   Cette action ouvre le volet **Inspecteurs** et affiche un histogramme à quatre barres. La colonne cible comporte trois valeurs distinctes : **Iris_virginica**, **Iris_versicolor**, **Iris-setosa**et une valeur **(null)**.

11. Pour filtrer les valeurs null, sélectionnez la barre du graphique qui représente la valeur null. Il y a également une ligne avec une valeur **(null)**. Pour supprimer cette ligne, sélectionnez le signe moins (**-**).

   ![Nombre de valeurs de l’histogramme](media/tutorial-classifying-iris/filter_out.png)

12. Notez que les étapes individuelles sont détaillées dans le volet **ÉTAPES**. Lorsque vous avez renommé les colonnes et filtré les lignes correspondant à une valeur null, chaque action a été enregistrée comme une étape de préparation des données. Vous pouvez modifier les étapes individuelles pour ajuster les paramètres, réorganiser les étapes et supprimer des étapes.

   ![Étapes](media/tutorial-classifying-iris/steps.png)

13. Fermez l’éditeur de préparation des données. Sélectionnez **Fermer** (x) sur l’onglet **iris-1** qui comporte l’icône de graphique. Votre travail est automatiquement sauvegardé dans le fichier **iris-1.dprep** sous le titre **Préparations des données**.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Générer du code Python/PySpark pour appeler un package de préparation de données

1. Cliquez avec le bouton droit sur le fichier **iris-1.dprep** pour afficher le menu contextuel, puis sélectionnez **Générer un fichier de code d’accès aux données**. 

   ![Générer le code](media/tutorial-classifying-iris/generate_code.png)

2. Un nouveau fichier nommé **iris-1.py** s’ouvre avec les lignes de code suivantes :

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

   Cet extrait de code appelle la logique que vous avez créée sous la forme d’un package de préparation de données. En fonction du contexte d’exécution du code, `df` représente les différentes sortes de trames de données. Une trame de donnée [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) est utilisée lorsqu’elle est exécutée par un runtime Python. Une trame de données [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) est utilisée quand elle est exécutée dans un contexte Spark. 

   Pour plus d’informations sur la préparation des données dans Azure Machine Learning Workbench, consultez le guide [Bien démarrer avec la préparation des données](data-prep-getting-started.md).

## <a name="next-steps"></a>étapes suivantes
Dans ce premier didacticiel de notre série qui en compte trois, vous avez utilisé Azure Machine Learning Workbench pour :
> [!div class="checklist"]
> * Créer un projet 
> * Créer un package de préparation de données
> * Générer du code Python/PySpark pour appeler un package de préparation de données

Vous pouvez à présent passer à la partie suivante de la série, qui vous expliquera comment générer un modèle Azure Machine Learning :
> [!div class="nextstepaction"]
> [Générer un modèle](tutorial-classifying-iris-part-2.md)
