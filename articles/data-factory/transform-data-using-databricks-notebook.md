---
title: "Exécuter une instance Databricks Notebook avec l’activité Databricks Notebook dans Azure Data Factory"
description: "Découvrez comment vous pouvez utiliser l’activité Databricks Notebook d’une fabrique de données Azure pour exécuter une instance Databricks Notebook sur un cluster de travaux Databricks."
services: data-factory
documentationcenter: 
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: d1dcec26529c747a209dd10fcefbbadaa40365a3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Exécuter une instance Databricks Notebook avec l’activité Databricks Notebook dans Azure Data Factory

Dans ce didacticiel, vous allez utiliser le portail Azure pour créer un pipeline Azure Data Factory qui exécute une instance Databricks Notebook sur le cluster de travaux Databricks. Il transmet également les paramètres Azure Data Factory à l’instance Databricks Notebook pendant l’exécution.

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

  - Créer une fabrique de données.

  - Créer un pipeline qui utilise l’activité Databricks Notebook.

  - Déclencher une exécution du pipeline.

  - Surveiller l’exécution du pipeline.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis


  - **Espace de travail Azure Databricks**. [Créez un espace de travail Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) ou utilisez-en un existant. Créez une instance Python Notebook dans votre espace de travail Azure Databricks. Exécutez ensuite l’instance Notebook et transmettez-lui les paramètres via Azure Data Factory.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1.  Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.

2.  Sélectionnez **Nouveau** dans le menu de gauche, sélectionnez **Données + Analytique**, puis **Data Factory**.

    ![Créer une fabrique de données](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  Dans le volet **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** sous **Nom**.

    Le nom de la fabrique de données Azure doit être un nom *global unique*. Si vous voyez l’erreur suivante, modifiez le nom de la fabrique de données. Par exemple, utilisez **\<votrenom\>ADFTutorialDataFactory**. Consultez l’article [Data Factory - Règles d’affectation des noms](https://docs.microsoft.com/en-us/azure/data-factory/naming-rules) pour en savoir plus sur les règles d’affectation des noms d’artefacts Data Factory.

    ![Entrer un nom pour la nouvelle fabrique de données](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  Pour **Abonnement**, sélectionnez l’abonnement Azure dans lequel vous voulez créer la fabrique de données.

5.  Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
    
    - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.
    
    - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.

    Certaines étapes de ce guide de démarrage rapide supposent que vous utilisez le nom **ADFTutorialResourceGroup** pour le groupe de ressources. Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1.  Pour **Version**, sélectionnez **V2 (préversion)**.

2.  Pour **Emplacement**, sélectionnez l’emplacement de la fabrique de données.

    À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (tels que le Stockage Azure et Azure SQL Database) et les services de calcul (comme HDInsight) utilisés par Data Factory peuvent se trouver dans d’autres régions.

3.  Sélectionnez **Épingler au tableau de bord**.

4.  Sélectionnez **Créer**.

5.  Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** :

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  Une fois la création terminée, la page **Data Factory** s’affiche. Sélectionnez la vignette **Créer et surveiller** pour démarrer l’application d’interface utilisateur (IU) de Data Factory dans un onglet séparé.

    ![Lancer l’application d’interface utilisateur de la fabrique de données](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Créez des services liés

Dans cette section, vous allez créer un service Databricks lié. Ce service lié contient les informations de connexion au cluster Databricks :

### <a name="create-an-azure-databricks-linked-service"></a>Créer un service Azure Databricks lié

1.  Dans la page **Prise en main**, basculez vers l’onglet **Modifier** dans le volet gauche.

    ![Modifier le nouveau service lié](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  Cliquez sur **Connexions** au bas de la fenêtre, puis cliquez sur **+ Nouveau**.
    
    ![Créer une nouvelle connexion](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  Dans la fenêtre **Nouveau service lié**, sélectionnez **Magasin de données** \> **Azure Databricks**, puis cliquez sur **Continuer**.
    
    ![Spécifier un service Azure Databricks lié](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  Dans la fenêtre **Nouveau service lié**, procédez comme suit :
    
    1.  Dans le champ **Nom**, entrez ***AzureDatabricks\_LinkedService***
    
    2.  Dans le champ **Cluster**, sélectionnez **Nouveau cluster**
    
    3.  Dans le champ **Domaine/Région**, sélectionnez la région où se trouve votre espace de travail Azure Databricks.
    
    4.  Dans le champ **Type de nœud de cluster**, sélectionnez **Standard\_D3\_v2** pour ce didacticiel.
    
    5.  Dans le champ **Jeton d’accès**, indiquez le jeton généré à partir de l’espace de travail Azure Databricks. Vous trouverez la procédure [ici](https://docs.databricks.com/api/latest/authentication.html#generate-token).
    
    6.  Pour **Version du cluster,** sélectionnez **4.0 version bêta** (version la plus récente)
    
    7.  Dans le champ **Nombre de nœuds de travail**, entrez la valeur **2**.
    
    8.  Sélectionnez **Terminer**.

        ![Terminer la création du service lié](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>Créer un pipeline

1.  Cliquez sur le bouton **+** (plus), puis sélectionnez **Pipeline** dans le menu.

    ![Boutons pour créer un nouveau pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  Créez un **paramètre** à utiliser dans le **pipeline**. Vous pourrez ensuite le transmettre à l’activité Databricks Notebook. Dans le pipeline vide, cliquez sur l’onglet **Paramètres**, puis sur **Nouveau** et nommez-le '**name**'.

    ![Créer un paramètre](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Créer le paramètre de nom](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  Dans la boîte à outils **Activités**, étendez **Databricks**. Faites glisser l’activité **Notebook** depuis la boîte à outils **Activités** vers la surface du concepteur de pipeline.

    ![Faire glisser l’instance Notebook vers l’aire de conception](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  Dans les propriété de l’activité **Databricks** **Notebook**, au bas de la fenêtre, procédez comme suit :

    a. Basculez vers l’onglet **Paramètres** .

    b. Sélectionnez **myAzureDatabricks\_LinkedService** (créé lors de la procédure précédente).

    c. Sélectionnez un chemin d’accès à Databricks **Notebook**. Créez une instance Notebook et spécifiez le chemin d’accès ici. Vous obtenez le chemin d’accès de l’instance Notebook en procédant comme suit.

       1. Lancer votre espace de travail Azure Databricks

       2. Créez un **nouveau dossier** dans l’espace de travail et nommez-le **adftutorial**.

          ![Créer un nouveau dossier](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [Créez une nouvelle instance Notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), appelons-la **mynotebook** sous le dossier **adftutorial****,** cliquez sur **Créer.**

          ![Créer une nouvelle instance Notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Définissez les propriétés de la nouvelle instance Notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. Dans l’instance Notebook récemment créée, mynotebook, ajoutez le code suivant :

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Créer des widgets pour les paramètres](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. Le **chemin d’accès à l’instance Notebook** dans ce cas est le suivant : **/adftutorial/mynotebook**

5.  Revenez à la **l’outil de création de l’interface utilisateur de la fabrique de données**. Accédez à l’onglet **Paramètres** sous l’**Activité Notebook1**. 
    
    a.  **Ajoutez un paramètre** à l’activité Notebook. Utilisez le même paramètre que celui ajouté précédemment au **pipeline**.

       ![Ajouter un paramètre](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    b.  Nommez le paramètre**input** et indiquez la valeur sous la forme de l’expression **@pipeline().parameters.name**.

6.  Pour valider le pipeline, cliquez sur le bouton **Valider** dans la barre d’outils. Pour fermer la fenêtre de validation, cliquez sur le bouton **\>\>** (flèche droite).

    ![Valider le pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  Sélectionnez **Publier tout**. L’interface utilisateur de Data Factory publie des entités (services liés et pipelines) sur le service Azure Data Factory.

    ![Publier les nouvelles entités de fabrique de données](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Déclencher une exécution du pipeline

Sélectionnez **Déclencher** dans la barre d’outils, puis **Déclencher maintenant**.

![Sélectionner la commande Déclencher maintenant](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

La boîte de dialogue **Exécution du pipeline** invite à saisir le paramètre **name**. Utilisez ici **/path/filename** comme paramètre. Cliquez sur **Terminer.**

![Indiquer une valeur pour les paramètres de nom](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1.  Basculez vers l’onglet **Surveiller**. Vérifiez qu’un pipeline est exécuté. Il faut compter environ 5 à 8 minutes pour créer un cluster de travaux Databricks, où s’exécute l’instance Notebook.

    ![Surveiller le pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  Cliquez régulièrement sur **Actualiser** pour vérifier l’état de l’exécution des pipelines.

3.  Pour voir les exécutions d’activités associées à l’exécution du pipeline, cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**.

    ![Afficher les exécutions d’activités](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Vous pouvez basculer vers la vue des exécutions de pipelines en cliquant sur le lien **Pipelines** en haut.

## <a name="verify-the-output"></a>Vérifier la sortie

Vous pouvez vous connecter à l’**espace de travail Azure Databricks**, accéder à **Clusters** et voir le statut du **Travail** : *en attente d’exécution, en cours d’exécution ou terminé*.

![Afficher le cluster de travaux et le travail](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Vous pouvez cliquer sur le **nom du travail** et naviguer pour afficher plus de détails. Une fois l’exécution réussie, vous pouvez valider les paramètres transmis et la sortie de l’instance Notebook Python.

![Afficher les détails et la sortie de l’exécution](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>Étapes suivantes

Le pipeline dans cet exemple déclenche une activité Databricks Notebook et lui transmet un paramètre. Vous avez appris à effectuer les actions suivantes :

  - Créer une fabrique de données.

  - Créer un pipeline qui utilise l’activité Databricks Notebook.

  - Déclencher une exécution du pipeline.

  - Surveiller l’exécution du pipeline.
