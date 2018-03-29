---
title: Préparer les données pour le didacticiel de classification d’Iris dans les services de Azure Machine Learning (préversion) | Microsoft Docs
description: Ce didacticiel complet montre comment utiliser les services Azure Machine Learning (préversion) de bout en bout. Cette première partie décrit la préparation des données.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/7/2018
ms.openlocfilehash: 7eec99cc0f63d4d82a7eda6dffc6c45471d9915d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-1-classify-iris---preparing-the-data"></a>Didacticiel 1 : Classifier Iris - Préparation des données

Le service Azure Machine Learning (préversion) forme une solution d’analytique avancée et de science des données intégrée de bout en bout qui permet aux scientifiques des données professionnels de préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Ce tutoriel est **la première partie d’une série de trois**. Dans ce didacticiel, vous allez étudier les principes de base des services Azure Machine Learning (préversion) et apprendre à :

> [!div class="checklist"]
> * Créer un projet dans Azure Machine Learning Workbench
> * Créer un package de préparation de données
> * Générer du code Python/PySpark pour appeler un package de préparation de données

Ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporel. 

## <a name="prerequisites"></a>Prérequis


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Pour suivre ce didacticiel, vous avez besoin de :
- un compte Azure Machine Learning Expérimentation ;
- Azure Machine Learning Workbench installé.

Si vous n’avez pas encore ces conditions préalables, suivez les étapes décrites dans l’article [Démarrage rapide : Installer et démarrer](quickstart-installation.md) pour configurer vos comptes et installer l’application Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Créer un projet dans Workbench

Si vous avez suivi les étapes décrites dans l’article [Démarrage rapide : Installer et démarrer](quickstart-installation.md), vous devez disposer déjà de ce projet et pouvez passer à la section suivante.

1. Ouvrez l’application Azure Machine Learning Workbench et connectez-vous si nécessaire. 
   
   + Sur Windows, vous pouvez la lancer via le raccourci du Bureau **Machine Learning Workbench**. 
   + Sur macOS, sélectionnez **Azure Machine Learning Workbench** dans Launchpad.

1. Dans le volet **PROJETS**, sélectionnez le signe plus (+) et choisissez **Nouveau projet**.  

   ![Nouvel espace de travail](media/tutorial-classifying-iris/new_ws.png)

1. Renseignez les champs du formulaire et sélectionnez le bouton **Créer** permettant de créer un projet dans Workbench.

   Champ|Valeur suggérée pour le didacticiel|Description
   ---|---|---
   Nom du projet | myIris |Entrez un nom unique qui identifie votre compte. Vous pouvez utiliser votre propre nom, ou celui d’un service ou projet qui identifie le mieux l’expérimentation. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés. 
   Répertoire du projet | c:\Temp\ | Spécifiez le répertoire dans lequel le projet est créé.
   Description du projet | _Laisser vide_ | Champ facultatif utile pour décrire les projets.
   URL du référentiel GIT de Visualstudio.com |_Laisser vide_ | Champ facultatif. Vous pouvez associer un projet à un répertoire Git sur Visual Studio Team Services pour le contrôle de code source et la collaboration. [Voici les étapes de configuration à suivre](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Espace de travail sélectionné | IrisGarden (s’il existe) | Choisissez un espace de travail que vous avez créé pour votre compte Expérimentation dans le portail Azure. <br/>Si vous avez suivi le démarrage rapide, vous devez disposer d’un espace de travail nommé IrisGarden. Si ce n’est pas le cas, sélectionnez celui que vous avez créé en même temps que votre compte Expérimentation ou tout autre compte que vous souhaitez utiliser.
   Modèle de projet | Classifying Iris | Les modèles contiennent des scripts et des données que vous pouvez utiliser pour découvrir le produit. Ce modèle contient les scripts et les données nécessaires pour ce démarrage rapide et les autres didacticiels de ce site de documentation. 

   ![Nouveau projet](media/tutorial-classifying-iris/new_project.png)
 
 Un projet est créé et le tableau de bord du projet s’ouvre avec ce projet. À ce stade, vous pouvez explorer la page d’accueil du projet, les sources de données, les blocs-notes et les fichiers de code source. 

   ![Ouvrir le projet](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>Créer un package de préparation de données

Ensuite, vous pouvez explorer et démarrer la préparation des données dans Azure Machine Learning Workbench. Chaque transformation que vous effectuez dans Workbench est stockée au format JSON dans un package de préparation de données locales (fichier *.dprep). Ce package de préparation des données est le conteneur principal de votre travail de préparation des données dans Workbench.

Ce package de préparation des données peut être transmis ultérieurement à un runtime, par exemple local-C#/CoreCLR, Scala/Spark ou Scala/HDI. 

1. Sélectionnez l’icône Dossier pour ouvrir l’affichage des fichiers, puis sélectionnez le fichier **iris.csv** pour l’ouvrir.

   Ce fichier contient un tableau de 5 colonnes et 50 lignes. Quatre colonnes sont des colonnes de fonctionnalités numériques. La cinquième colonne est une colonne de cible de chaînes. Aucune colonne n’a de nom d’en-tête.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Évitez d’inclure des fichiers de données dans votre dossier de projet, en particulier s’ils sont volumineux. Étant donné que le fichier de données **iris.csv** est petit, il a été inclus dans ce modèle à des fins de démonstration. Pour plus d’informations, consultez l’article [Guide pratique pour lire et écrire des fichiers de données volumineux](how-to-read-write-files.md).

2. Dans la **Vue de données**, cliquez sur le signe « plus » (**+**) pour ajouter une nouvelle source de données. La page **Ajouter une source de données** s’ouvre. 

   ![Afficher des données dans Azure Machine Learning Workbench](media/tutorial-classifying-iris/data_view.png)

3. Sélectionnez **Fichiers texte (\*.csv, \*.json, \*.txt...)**, puis cliquez sur **Suivant**.
   ![Source de données dans Azure Machine Learning Workbench](media/tutorial-classifying-iris/data-source.png)

4. Accédez au fichier **iris.csv**, puis cliquez sur **Terminé**. Les valeurs par défaut pour les paramètres seront utilisées, comme le type de données et du séparateur.

   >[!IMPORTANT]
   >Veillez à sélectionner le fichier **iris.csv** dans le répertoire de projet actif pour cet exercice. Sinon, les étapes suivantes risquent d’échouer.
 
   ![Sélectionner iris](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. Un nouveau fichier nommé **iris-1.dsource** est créé. Le fichier est nommé de façon unique à l’aide de « -1 », car l’exemple de projet est déjà fourni avec un fichier **iris.dsource** non numéroté.  

   Le fichier s’ouvre et les données s’affichent. Une série d’en-têtes de colonne, de **Colonne1** à **Colonne5**, est automatiquement ajoutée à ce jeu de données. Si vous faites défiler les données vers le bas, vous noterez que la dernière ligne du jeu de données est vide. La ligne est vide, car le fichier CSV comporte un saut de ligne supplémentaire.

   ![Affichage de données Iris](media/tutorial-classifying-iris/iris_data_view.png)

1. Cliquez sur le bouton **Métriques**. Les histogrammes sont générés et affichés.

   Vous pouvez revenir à la vue de données en sélectionnant le bouton **Données**.
   
   ![Affichage de données Iris](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. Observez les histogrammes. Un ensemble complet de statistiques a été calculé pour chaque colonne. 

   ![Affichage de données Iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Commencez à créer un package de préparation des données en sélectionnant le bouton **Préparation**. La boîte de dialogue **Préparer** s’ouvre. 

   L’exemple de projet contient un fichier de préparation des données **iris.dprep** sélectionné par défaut. 

   ![Affichage de données Iris](media/tutorial-classifying-iris/prepare.png)

1. Créez un nouveau package de préparation de données en sélectionnant **+ Nouveau package de préparation de données** à partir du menu de la liste déroulante.

   ![Affichage de données Iris](media/tutorial-classifying-iris/prepare_new.png)

1. Entrez une nouvelle valeur pour le nom du package, utilisez **iris-1**, puis sélectionnez **OK**.

   Un nouveau package de préparation de données nommé **iris-1.dprep** est créé et ouvert dans l’éditeur de préparation des données.

   ![Affichage de données Iris](media/tutorial-classifying-iris/prepare_iris-1.png)

   Effectuons à présent quelques tâches de base de préparation de données. 

1. Sélectionnez chaque en-tête de colonne pour activer la modification du texte d’en-tête. Ensuite, renommez chaque colonne comme suit : 

   Dans l’ordre, entrez **Longueur des sépales**, **Largeur des sépales**, **Longueur des pétales**, **Largeur des pétales** et **Espèces** pour les cinq colonnes.

   ![Renommer les colonnes](media/tutorial-classifying-iris/rename_column.png)

1. Comptez les valeurs distinctes :
   1. Sélectionnez la colonne **Espèces**
   1. Cliquez avec le bouton droit pour la sélectionner. 
   1. Sélectionnez **Nombre de valeurs** dans le menu déroulant. 

   Le volet **Inspecteurs** s’ouvre au-dessous des données. Un histogramme comprenant quatre barres s’affiche. La colonne cible comporte quatre valeurs distinctes : **Iris_virginica**, **Iris_versicolor**, **Iris-setosa**et une valeur **(null)**.

   ![Sélectionner les nombres de valeur](media/tutorial-classifying-iris/value_count.png)

   ![Nombre de valeurs de l’histogramme](media/tutorial-classifying-iris/filter_out.png)

1. Pour exclure les valeurs Null, sélectionnez la barre « (Null) », puis le signe moins (**-**). 

   Ensute, la ligne (Null) devient grise pour indiquer qu’elle a été filtrée. 

   ![Filtrer les valeurs Null](media/tutorial-classifying-iris/filter_out2.png)

1. Prenez en compte les étapes de préparation de données individuelles qui sont décrites en détail dans le volet **ÉTAPES**. Lorsque vous avez renommé les colonnes et filtré les lignes correspondant à une valeur null, chaque action a été enregistrée comme une étape de préparation des données. Vous pouvez modifier les étapes individuelles pour ajuster leurs paramètres, réorganiser les étapes et supprimer des étapes.

   ![Étapes](media/tutorial-classifying-iris/steps.png)

1. Fermez l’éditeur de préparation des données. Sélectionnez l’icône **x** sur l’onglet **iris-1** avec l’icône de graphique pour fermer l’onglet. Votre travail est automatiquement sauvegardé dans le fichier **iris-1.dprep** sous le titre **Préparations des données**.

   ![fermez](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Générer du code Python/PySpark pour appeler un package de préparation de données

 La sortie d’un package de préparation de données peut être explorée directement dans Python ou dans un Bloc-notes Jupyter. Les packages peuvent être exécutés sur plusieurs runtimes, notamment Python local, Spark (y compris dans Docker) et HDInsight. 

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

   En fonction du contexte d’exécution du code, `df` peut représenter différents types de trame de données :
   + Lors de l’exécution sur un runtime Python, une [trame de données pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) est utilisée.
   + Lors de l’exécution dans un contexte Spark, une [trame de données Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) est utilisée. 
   
   Pour en savoir plus sur la préparation des données dans Azure Machine Learning Workbench, consultez le guide [Bien démarrer avec la préparation des données](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé Azure Machine Learning Workbench pour effectuer les tâches suivantes :
> [!div class="checklist"]
> * Création d'un projet
> * Créer un package de préparation de données
> * Générer du code Python/PySpark pour appeler un package de préparation de données

Vous pouvez à présent passer à la partie suivante de la série de didacticiels, qui vous expliquera comment générer un modèle Azure Machine Learning :
> [!div class="nextstepaction"]
> [Didacticiel 2 : Génération de modèles](tutorial-classifying-iris-part-2.md)
