---
title: Créer une fabrique de données Azure à l’aide de l’interface utilisateur d’Azure Data Factory | Microsoft Docs
description: Créez une fabrique de données avec un pipeline copiant les données d’un emplacement dans un stockage Blob Azure vers un autre emplacement.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 02/01/2018
ms.author: jingwang
ms.openlocfilehash: 79b19121b25b03181eeda1bedd800f45a2adf57e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](quickstart-create-data-factory-portal.md)

Ce guide de démarrage rapide explique comment utiliser l’interface utilisateur d’Azure Data Factory pour créer et surveiller une fabrique de données. Le pipeline que vous créez dans cette fabrique de données *copie* les données d’un dossier vers un autre dossier dans un stockage Blob Azure. Pour suivre un didacticiel sur la *transformation* des données à l’aide d’Azure Data Factory, consultez [Didacticiel : transformation des données à l’aide de Spark](tutorial-transform-data-spark-portal.md). 


> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](data-factory-introduction.md) avant de commencer ce guide de démarrage rapide. 
>
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service, qui est en disponibilité générale (GA), consultez [Didacticiel de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vidéo 
Regardez cette vidéo pour comprendre l’interface de fabrique de Data Factory : 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Accédez au [portail Azure](https://portal.azure.com). 
3. Sélectionnez **Nouveau** dans le menu de gauche, sélectionnez **Données + Analytique**, puis **Data Factory**. 
   
   ![Sélection Data Factory dans le volet « Nouveau »](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
   ![Page « Nouvelle fabrique de données »](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, **&lt;votrenom&gt;ADFTutorialDataFactory**), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour en savoir plus sur les règles d’affectation des noms d’artefacts Data Factory.
  
   ![Erreur quand le nom n’est pas disponible](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Pour **Abonnement**, sélectionnez l’abonnement Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
   - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste. 
   - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
   Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Pour **Version**, sélectionnez **V2 (préversion)**.
5. Pour **Emplacement**, sélectionnez l’emplacement de la fabrique de données.

   La liste affiche uniquement les emplacements que la fabrique de données prend en charge. Les magasins de données (tels que le Stockage Azure et Azure SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par Data Factory peuvent se trouver dans d’autres emplacements.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Sélectionnez **Créer**.
8. Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** : 

   ![Vignette « Déploiement de Data Factory »](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche. Sélectionnez la vignette **Créer et surveiller** pour démarrer l’application d’interface utilisateur (IU) d’Azure Data Factory dans un onglet séparé.
   
   ![Page d’accueil de la fabrique de données, avec la vignette « Créer et surveiller »](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. Dans la page **Prise en main**, basculez vers l’onglet **Modifier** dans le volet gauche. 

    ![Page « Prise en main »](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Créer un service lié
Dans cette procédure, vous créez un service lié qui relie votre compte de stockage Azure à la fabrique de données. Le service lié comporte les informations de connexion utilisées par le service Data Factory lors de l’exécution pour s’y connecter.

1. Sélectionnez **Connexions**, puis cliquez sur le bouton **Nouveau** dans la barre d’outils. 

   ![Boutons pour la création d’une nouvelle connexion](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
2. Dans la page **Nouveau service lié**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Continuer**. 

   ![Sélectionner la vignette « Stockage Blob Azure »](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
3. Effectuez ensuite les tâches suivantes : 

   a. Pour le **Nom**, entrez **AzureStorageLinkedService**.

   b. Pour le **Nom du compte de stockage**, sélectionnez le nom de votre compte de stockage Azure.

   c. Cliquez sur **Tester la connexion** pour confirmer que le service Data Factory peut se connecter au compte de stockage. 

   d. Cliquez sur **Enregistrer** pour enregistrer le service lié. 

   ![Paramètres du service lié Stockage Azure](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
4. Vérifiez que **AzureStorageLinkedService** apparaît dans la liste des services liés. 

   ![Service lié Stockage Azure dans la liste](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>Créez les jeux de données
Dans cette procédure, vous créez deux jeux de données : **InputDataset** et **OutputDataset**. Ces jeux de données sont de type **AzureBlob**. Ils font référence au service lié Stockage Azure que vous avez créé dans la section précédente. 

Le jeu de données d’entrée représente les données sources dans le dossier d’entrée. Dans la définition du jeu de données d’entrée, vous spécifiez le conteneur d’objets Blob (**adftutorial**), le dossier (**input**) et le fichier (**emp.txt**) qui contient la source de données. 

Le jeu de données de sortie représente les données qui sont copiées vers la destination. Dans la définition du jeu de données de sortie, vous spécifiez le conteneur d’objets Blob (**adftutorial**), le dossier (**output**) et le fichier dans lequel les données sont copiées. Chaque exécution d’un pipeline possède un ID unique associé. Vous pouvez accéder à cet ID à l’aide de la variable système **RunId**. Le nom du fichier de sortie est évalué dynamiquement en fonction de l’ID d’exécution du pipeline.   

Dans les paramètres du service lié, vous avez spécifié le compte de stockage Azure qui contient les données sources. Dans les paramètres de jeu de données source, vous spécifiez où se trouvent exactement les données sources (conteneur d’objets Blob, dossier et fichier). Dans les paramètres de jeu de données récepteur, vous spécifiez où les données sont copiées (conteneur d’objets Blob, dossier et fichier). 
 
1. Cliquez sur le bouton **+** (plus), puis sélectionnez **Jeu de données**.

   ![Menu pour la création d’un jeu de données](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. Dans la page **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer**. 

   ![Sélectionner le « Stockage Blob Azure »](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. Dans la fenêtre **Propriétés** du jeu de données, entrez **InputDataset** pour **Nom**. 

   ![Paramètres généraux du jeu de données](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. Basculez vers l’onglet **Connexions**, et complétez les étapes suivantes : 

   a. Pour le **Service lié**, sélectionnez **AzureStorageLinkedService**.

   b. Pour le **Chemin d’accès**, sélectionnez le bouton **Parcourir**.

      ![Onglet « Connexion » et bouton « Parcourir »](./media/quickstart-create-data-factory-portal/file-path-browse-button.png) c. Dans la fenêtre **Choisir un fichier ou dossier**, accédez au dossier d’**entrée** dans le conteneur **adftutorial**, sélectionnez le fichier **emp.txt**, puis cliquez sur **Terminer**.

      ![Rechercher le fichier d’entrée](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    
   d. (facultatif) Sélectionnez **Aperçu des données** pour afficher un aperçu des données dans le fichier emp.txt.     
5. Répétez les étapes pour créer le jeu de données de sortie :  

   a. Cliquez sur le bouton **+** (plus), puis sélectionnez **Jeu de données**.

   b. Dans la page **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer**.

   c. Spécifiez **OutputDataset** pour le nom.

   d. Entrez **adftutorial/output** pour le dossier. Si le dossier **Sortie** n’existe pas, il est créé lors de l’exécution de l’activité de copie.

   e. Entrez `@CONCAT(pipeline().RunId, '.txt')` pour le nom de fichier. 
   
      Chaque fois que vous exécutez un pipeline, un ID unique est associé à l’exécution du pipeline. L’expression concatène l’ID d’exécution du pipeline avec **.txt** pour évaluer le nom du fichier de sortie. Pour obtenir la liste des variables système et expressions prises en charge, consultez [Variables système](control-flow-system-variables.md) et [Langage d’expression](control-flow-expression-language-functions.md).

   ![Paramètres du jeu de données de sortie](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>Créer un pipeline 
Dans cette procédure, vous créez et validez un pipeline avec une activité de copie qui utilise les jeux de données d’entrée et de sortie. L’activité de copie copie les données du fichier que vous avez spécifié dans les paramètres du jeu de données d’entrée dans le fichier que vous avez spécifié dans les paramètres du jeu de données de sortie. Si le jeu de données d’entrée ne spécifie qu’un dossier (et pas le nom de fichier), l’activité de copie copie tous les fichiers dans le dossier source vers la destination. 

1. Cliquez sur le bouton **+** (plus), puis sélectionnez **Pipeline**. 

   ![Menu pour créer un nouveau pipeline](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. Dans la fenêtre **Propriétés**, spécifiez **CopyPipeline** pour le **Nom**. 

   ![Paramètres généraux du pipeline](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. Dans la boîte à outils **Activités**, étendez le **Flux de données**. Faites glisser l’activité **Copier** depuis la boîte à outils **Activités** vers la surface du concepteur de pipeline. Vous pouvez également rechercher des activités dans la boîte à outils **Activités**. Spécifiez **CopyFromBlobToBlob** pour le **Nom**.

   ![Paramètres généraux de l’activité de copie](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. Basculez vers l’onglet **Source** dans les paramètres de l’activité de copie et sélectionnez **InputDataset** pour le **Jeu de données source**.

   ![Paramètres de la source de l’activité de copie](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. Basculez vers l’onglet **Récepteur** dans les paramètres de l’activité de copie et sélectionnez **OutputDataset** pour le **Jeu de données récepteur**.

   ![Paramètres du récepteur de l’activité de copie](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. Sélectionnez **Valider** pour valider les paramètres du pipeline. Vérifiez que le pipeline a été validé avec succès. Pour fermer la sortie de validation, sélectionnez le bouton **>>** (flèche droite). 

   ![Valider le pipeline](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>Série de tests du pipeline
Dans cette étape, vous réalisez une série de tests sur le pipeline avant de le déployer vers Data Factory. 

1. Dans la barre d’outils du pipeline, sélectionnez **Série de tests**. 
    
   ![Séries de tests du pipeline](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. Vérifiez que vous voyez l’état de l’exécution du pipeline dans l’onglet **Sortie** des paramètres du pipeline. 

   ![Sortie de la série de tests](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. Vérifiez qu’un fichier de sortie apparaît bien dans le dossier de **sortie** du conteneur **adftutorial**. Si le dossier de sortie n’existe pas, le service Data Factory le crée automatiquement. 
    
   ![Vérifier la sortie](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>Déclencher le pipeline manuellement
Dans cette procédure, vous déployez des entités (services liés, jeux de données, pipelines) vers Azure Data Factory. Vous déclenchez ensuite manuellement une exécution du pipeline. Vous pouvez également publier des entités dans votre propre référentiel Git Visual Studio Team Services, qui est abordé dans un [autre didacticiel](tutorial-copy-data-portal.md?#configure-code-repository).

1. Avant de déclencher un pipeline, vous devez publier des entités dans Data Factory. Pour publier, cliquez sur **Publier tout** dans le volet gauche. 

   ![Bouton Publier](./media/quickstart-create-data-factory-portal/publish-button.png)
2. Pour déclencher le pipeline manuellement, sélectionnez **Déclencher** dans la barre d’outils, puis sélectionnez **Déclencher maintenant**. 
    
   ![Commande « Déclencher maintenant »](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline

1. Basculez vers l’onglet **Surveiller** sur la gauche. Utilisez le bouton **Actualiser** pour actualiser la liste.

   ![Onglet de Surveillance des exécutions de pipelines, avec le bouton « Actualiser »](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Sélectionnez le lien **Afficher les exécutions d’activités** sous **Actions**. Vous voyez l’état de l’exécution de l’activité de copie dans cette page. 

   ![Exécutions d’activités du pipeline](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. Pour afficher les détails de l’opération de copie, cliquez sur le lien **Détails** (image en forme de lunettes) dans la colonne **Actions**. Pour plus d’informations sur les propriétés, consultez [Vue d’ensemble de l’activité de copie](copy-activity-overview.md). 

   ![Détails de l’opération de copie](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Vérifiez qu’un nouveau fichier apparaît bien dans le dossier de **sortie**. 
5. Vous pouvez revenir à la vue **Exécutions du pipeline** à partir de la vue **Exécutions d’activités** en sélectionnant le lien **Pipelines**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Déclencher le pipeline selon une planification
Cette procédure est facultative dans ce didacticiel. Vous pouvez créer un *déclencheur par planificateur* afin de planifier une exécution périodique du pipeline (toutes les heures, tous les jours, etc.). Dans cette procédure, vous créez un déclencheur qui doit s’exécuter toutes les minutes jusqu’à la date et l’heure de fin que vous spécifiez. 

1. Basculez vers l’onglet **Modifier**. 

   ![Bouton Modifier](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. Sélectionnez **Déclencheur** dans le menu, puis sélectionnez **Nouveau/Modifier**. 

   ![Menu pour un nouveau déclencheur](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. Sur la page **Ajouter des déclencheurs**, sélectionnez **Choisir un déclencheur**, puis **Nouveau**. 

   ![Sélections pour ajouter un nouveau déclencheur](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. Dans la page **Nouveau déclencheur**, sous **Fin**, sélectionnez **On Date** (À la date), spécifiez une heure de fin quelques minutes après l’heure actuelle, puis cliquez sur **Appliquer**. 

   Un coût est associé à chaque exécution du pipeline, vous devez donc spécifier l’heure de fin quelques minutes seulement après l’heure de début. Vérifiez qu’elle est le même jour. Toutefois, vérifiez que la durée est suffisante entre l’heure de publication et l’heure de fin pour permettre l’exécution du pipeline. Le déclencheur ne s’applique que lorsque vous avez publié la solution dans Data Factory, et non lorsque vous enregistrez le déclencheur dans l’interface utilisateur. 

   ![Paramètres du déclencheur](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. Sur la page **Nouveau déclencheur**, cliquez sur la case à cocher **Activé**, puis sélectionnez **Suivant**. 

   ![Case à cocher « Activé » et bouton « Suivant »](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. Examinez le message d’avertissement, puis cliquez sur **Terminer**.

   ![Bouton d’avertissement et « Terminer »](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Cliquez sur **Publier tout** pour publier les modifications dans Data Factory. 

   ![Bouton Publier](./media/quickstart-create-data-factory-portal/publish-button.png)
8. Basculez vers l’onglet **Surveiller** sur la gauche. Sélectionnez **Actualiser** pour actualiser la liste. Vous voyez que le pipeline s’exécute toutes les minutes entre l’heure de publication et l’heure de fin. 

   Notez les valeurs dans la colonne **Déclenché par**. L’exécution manuelle du déclencheur était celle de l’étape précédente (**Déclencher maintenant**). 

   ![Liste des exécutions déclenchées](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Sélectionnez la flèche vers le bas à côté de **Exécutions du pipeline** pour basculer vers l’affichage **Exécutions du déclencheur**. 

   ![Basculer vers l’affichage « Exécutions du déclencheur »](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Vérifiez qu’un fichier de sortie est créé pour chaque exécution du pipeline jusqu’à la date/heure de fin spécifiée dans le dossier de **sortie**. 

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios, consultez les [didacticiels](tutorial-copy-data-portal.md). 