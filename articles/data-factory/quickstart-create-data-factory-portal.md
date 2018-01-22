---
title: "Créer une fabrique de données Azure à l’aide de l’interface utilisateur d’Azure Data Factory | Microsoft Docs"
description: "Ce didacticiel vous montre comment créer une fabrique de données avec un pipeline qui copie les données d’un dossier vers un autre dossier dans Stockage Blob Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: ebce4e0d137d2e56cc914efad357593af7abb255
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-data-factory-using-the-azure-data-factory-ui"></a>Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](quickstart-create-data-factory-portal.md)

Ce guide de démarrage rapide explique comment utiliser l’interface utilisateur d’Azure Data Factory pour créer et surveiller une fabrique de données. Le pipeline que vous créez dans cette fabrique de données **copie** les données d’un dossier vers un autre dossier dans un stockage Blob Azure. Pour suivre un didacticiel sur la **transformation** des données à l’aide d’Azure Data Factory, consultez [Didacticiel : transformation des données à l’aide de Spark](tutorial-transform-data-spark-portal.md). 


> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](data-factory-introduction.md) avant de commencer ce guide de démarrage rapide. 
>
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 de Data Factory, qui est généralement disponible, consultez [Data Factory version 1 - didacticiel](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vidéo 
Regardez cette vidéo pour comprendre l’interface de fabrique de Data Factory : 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Accédez au [portail Azure](https://portal.azure.com). 
2. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page Nouvelle fabrique de données](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
     ![Nom indisponible - erreur](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
    Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V2 (préversion)** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge par Data Factory sont affichés dans la liste déroulante. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
    ![Page d'accueil Data Factory](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. Cliquez sur la vignette **Créer et surveiller** pour lancer l’application d’interface utilisateur (IU) d’Azure Data Factory dans un onglet séparé. 
11. Dans la page de prise en main, basculez vers l’onglet **Modifier** dans le volet gauche comme illustré dans l’image suivante : 

    ![Page de prise en main](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-azure-storage-linked-service"></a>Créer le service lié Stockage Azure
Dans cette étape, vous créez un service lié qui relie votre compte de stockage Azure à la fabrique de données. Le service lié comporte les informations de connexion utilisées par le service Data Factory lors de l’exécution pour s’y connecter.

2. Cliquez sur **Connexions**, puis cliquez sur le bouton **Nouveau** dans la barre d’outils. 

    ![Nouvelle connexion](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
3. Dans la page **Nouveau service lié**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Continuer**. 

    ![Sélectionnez Stockage Azure](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
4. Dans la page **Nouveau service lié**, procédez comme suit : 

    1. Entrez **AzureStorageLinkedService** pour le **Nom**.
    2. Sélectionnez le nom de votre compte de stockage Azure pour le **nom du compte de stockage**.
    3. Cliquez sur **Tester la connexion** pour confirmer que le service Data Factory peut se connecter au compte de stockage. 
    4. Cliquez sur **Enregistrer** pour enregistrer le service lié. 

        ![Paramètres du service lié Stockage Azure](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
5. Vérifiez que **AzureStorageLinkedService** apparaît dans la liste des services liés. 

    ![Service lié Stockage Azure dans la liste](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>Créez les jeux de données
Dans cette étape, vous créez deux jeux de données : **InputDataset** et **OutputDataset**. Ces jeux de données sont de type **AzureBlob**. Ils font référence au **service lié Stockage Azure** que vous avez créé à l’étape précédente. 

Le jeu de données d’entrée représente les données sources dans le dossier d’entrée. Dans la définition du jeu de données d’entrée, vous spécifiez le conteneur d’objets Blob (**adftutorial**), le dossier (**input**) et le fichier (**emp.txt**) qui contient la source de données. 

Le jeu de données de sortie représente les données qui sont copiées vers la destination. Dans la définition du jeu de données de sortie, vous spécifiez le conteneur d’objets Blob (**adftutorial**), le dossier (**output**) et le fichier dans lequel les données sont copiées. Un ID est associé à chaque exécution d’un pipeline, qui est accessible à l’aide de la variable système **RunId**. Le nom du fichier de sortie est évalué dynamiquement en fonction de l’ID d’exécution du pipeline.   

Dans les paramètres du service lié, vous avez spécifié le compte de stockage Azure qui contient les données sources. Dans les paramètres de jeu de données source, vous spécifiez où se trouvent exactement les données sources (conteneur d’objets Blob, dossier et fichier). Dans les paramètres de jeu de données récepteur, vous spécifiez où les données sont copiées (conteneur d’objets Blob, dossier et fichier). 
 
1. Cliquez sur le bouton **+ (plus)**, puis sélectionnez **Jeu de données**.

    ![Menu Nouveau jeu de données](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. Dans la page **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer**. 

    ![Sélectionner le stockage Blob Azure](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. Dans la fenêtre **Propriétés** du jeu de données, entrez **InputDataset** pour **Nom**. 

    ![Paramètres généraux du jeu de données](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. Basculez vers l’onglet **Connexions** et procédez comme suit : 

    1. Sélectionnez **AzureStorageLinkedService** pour le service lié. 
    2. Cliquez sur le bouton **Parcourir** pour le **Chemin d’accès du fichier**. 
        ![Rechercher le fichier d’entrée](./media/quickstart-create-data-factory-portal/file-path-browse-button.png)
    3. Dans la fenêtre **Choisir un fichier ou dossier**, accédez au dossier d’**entrée** dans le conteneur **adftutorial**, sélectionnez le fichier **emp.txt**, puis cliquez sur **Terminer**.

        ![Rechercher le fichier d’entrée](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    4. (facultatif) Cliquez sur **Aperçu des données** pour afficher un aperçu des données dans le fichier emp.txt.     
5. Répétez les étapes pour créer le jeu de données de sortie.  

    1. Cliquez sur le bouton **+ (plus)** dans le volet gauche, puis sélectionnez **Jeu de données**.
    2. Dans la page **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer**.
    3. Spécifiez **OutputDataset** pour le nom.
    4. Entrez **adftutorial/output** pour le dossier. L’activité de copie crée le dossier de sortie s’il n’existe pas. 
    5. Entrez `@CONCAT(pipeline().RunId, '.txt')` pour le nom de fichier. Chaque fois que vous exécutez un pipeline, un ID unique est associé à l’exécution du pipeline. L’expression concatène l’ID d’exécution du pipeline avec **.txt** pour évaluer le nom du fichier de sortie. Pour obtenir la liste des variables système et expressions prises en charge, consultez [Variables système](control-flow-system-variables.md) et [Langage d’expression](control-flow-expression-language-functions.md).

        ![Paramètres du jeu de données de sortie](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>Créer un pipeline 
Dans cette étape, vous créez et validez un pipeline avec une activité **Copie** qui utilise les jeux de données d’entrée et de sortie. L’activité de copie copie les données du fichier spécifié dans les paramètres du jeu de données d’entrée dans le fichier spécifié dans les paramètres du jeu de données de sortie. Si le jeu de données d’entrée ne spécifie qu’un dossier (et pas le nom de fichier), l’activité de copie copie tous les fichiers dans le dossier source vers la destination. 

1. Cliquez sur le bouton **+ (plus)**, puis sélectionnez **Pipeline**. 

    ![Menu Nouveau pipeline](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. Spécifiez **CopyPipeline** pour **Nom** dans la fenêtre **Propriétés**. 

    ![Paramètres généraux du pipeline](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. Dans la boîte à outils **Activités**, développez **Flux de données** et glissez-déposez l’activité **Copie** de la boîte à outils **Activités** vers la surface du concepteur de pipeline. Vous pouvez également rechercher des activités dans la boîte à outils **Activités**. Spécifiez **CopyFromBlobToBlob** pour le **nom**.

    ![Paramètres généraux de l’activité de copie](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. Basculez vers l’onglet **Source** dans les paramètres de l’activité de copie et sélectionnez **InputDataset** pour le **jeu de données source**.

    ![Paramètres de la source de l’activité de copie](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. Basculez vers l’onglet **Récepteur** dans les paramètres de l’activité de copie et sélectionnez **OutputDataset** pour le **jeu de données récepteur**.

    ![Paramètres du récepteur de l’activité de copie](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. Cliquez sur **Valider** pour valider les paramètres du pipeline. Vérifiez que le pipeline a été validé avec succès. Pour fermer la sortie de validation, cliquez sur le bouton **flèche droite** (>>). 

    ![Valider le pipeline](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>Série de tests du pipeline
Dans cette étape, vous testez l’exécution du pipeline avant de le déployer vers Data Factory. 

1. Dans la barre d’outils du pipeline, cliquez sur **Série de tests**. 
    
    ![Séries de tests du pipeline](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. Vérifiez que vous voyez l’état de l’exécution du pipeline dans l’onglet **Sortie** des paramètres du pipeline. 

    ![Sortie de la série de tests](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. Vérifiez qu’un fichier de sortie apparaît bien dans le dossier de **sortie** du conteneur **adftutorial**. Si le dossier de sortie n’existe pas, le service Data Factory le crée automatiquement. 
    
    ![Vérifier la sortie](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>Déclencher le pipeline manuellement
Dans cette étape, vous déployez des entités (services liés, jeux de données, pipelines) vers Azure Data Factory. Vous déclenchez ensuite manuellement une exécution du pipeline. Vous pouvez également publier des entités dans votre propre référentiel VSTS GIT, qui est abordé dans un [autre didacticiel](tutorial-copy-data-portal.md?#configure-code-repository).

1. Avant de déclencher un pipeline, vous devez publier des entités dans Data Factory. Pour publier, cliquez sur **Publier** dans le volet gauche. 

    ![Bouton Publier](./media/quickstart-create-data-factory-portal/publish-button.png)
2. Pour déclencher le pipeline manuellement, cliquez sur **Déclencher** dans la barre d’outils, puis sélectionnez **Déclencher maintenant**. 
    
    ![Déclencher maintenant](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline

1. Basculez vers l’onglet **Surveiller** sur la gauche. Utilisez le bouton **Actualiser** pour actualiser la liste.

    ![Surveiller le pipeline](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Cliquez sur le lien **Afficher les exécutions d’activités** sous **Actions**. Vous voyez l’état de l’exécution de l’activité de copie s’exécutent dans cette page. 

    ![Exécutions d’activités du pipeline](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. Pour afficher les détails de l’opération de copie, cliquez sur le lien **Détails** (image en forme de lunettes) dans la colonne **Actions**. Pour plus d’informations sur les propriétés, consultez [Vue d’ensemble de l’activité de copie](copy-activity-overview.md). 

    ![Détails de l’opération de copie](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Vérifiez qu’un nouveau fichier apparaît bien dans le dossier de **sortie**. 
5. Vous pouvez revenir à la vue **Exécutions du pipeline** à partir de la vue **Exécutions d’activités** en cliquant sur le lien **Pipelines**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Déclencher le pipeline selon une planification
Cette étape est facultative dans ce didacticiel. Vous pouvez créer un **déclencheur du planificateur** afin de planifier une exécution périodique du pipeline (toutes les heures, tous les jours, etc.). Dans cette étape, vous créez un déclencheur d’exécution toutes les minutes jusqu’à la date/heure que vous spécifiez comme date de fin. 

1. Basculez vers l’onglet **Modifier**. 

    ![Basculer vers l’onglet Modifier](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. Cliquez sur **Déclencheur** dans le menu, puis cliquez sur **Nouveau/Modifier**. 

    ![Menu de nouveau déclencheur](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. Dans la page **Ajouter un déclencheur**, cliquez sur **Choose trigger...**  (Choisir un déclencheur...), puis cliquez sur **Nouveau**. 

    ![Ajouter un déclencheur - nouveau déclencheur](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. Dans la page **Nouveau déclencheur**, pour le champ **Fin**, sélectionnez **On Date** (À la date), spécifiez l’heure de fin quelques minutes après l’heure actuelle, puis cliquez sur **Appliquer**. Un coût est associé à chaque exécution du pipeline, vous devez donc spécifier l’heure de fin quelques minutes seulement après l’heure de début. Vérifiez qu’elle est le même jour. Toutefois, vérifiez que la durée est suffisante entre l’heure de publication et l’heure de fin pour permettre l’exécution du pipeline. Le déclencheur ne s’applique que lorsque vous avez publié la solution dans Data Factory, et non lorsque vous enregistrez le déclencheur dans l’interface utilisateur. 

    ![Paramètres du déclencheur](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. Cochez l’option **Activé** dans la page **Nouveau déclencheur**, puis cliquez sur **Suivant** 

    ![Paramètres du déclencheur - Bouton Suivant](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. Dans la page **Nouveau déclencheur**, consultez le message d’avertissement, puis cliquez sur **Terminer**.

    ![Paramètres du déclencheur - Bouton Terminer](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Cliquez sur **Publier** pour publier les modifications dans Data Factory. 

    ![Bouton Publier](./media/quickstart-create-data-factory-portal/publish-2.png)
8. Basculez vers l’onglet **Surveiller** sur la gauche. Cliquez sur **Actualiser** pour actualiser la liste. Vous voyez que le pipeline s’exécute toutes les minutes entre l’heure de publication et l’heure de fin. Notez les valeurs dans la colonne **Déclenché par**. Le déclencheur d’exécution manuel était celui de l’étape (**Déclencher maintenant**) précédente. 

    ![Surveiller les exécutions déclenchées](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Cliquez sur la flèche vers le bas en regard de **Exécutions du pipeline** pour basculer vers la vie **Trigger Runs** (Exécutions du déclencheur). 

    ![Surveiller les exécutions du déclencheur](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Vérifiez qu’un **fichier de sortie** est créé pour chaque exécution du pipeline jusqu’à la date/heure de fin spécifiée dans le dossier de **sortie**. 

## <a name="next-steps"></a>étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Consultez les [didacticiels](tutorial-copy-data-portal.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios. 