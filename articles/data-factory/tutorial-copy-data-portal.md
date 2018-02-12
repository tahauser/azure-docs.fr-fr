---
title: "Utiliser le portail Azure pour créer un pipeline Data Factory | Microsoft Docs"
description: "Ce didacticiel fournit des instructions détaillées sur l’utilisation d’un portail Azure pour créer une fabrique de données avec un pipeline. Le pipeline utilise l’activité de copie pour copier des données d’un stockage Blob Azure vers une base de données SQL Azure. "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 8b5211e9c932221c6b6134e7e0627f4d7f964123
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copier des données à partir d’un objet blob Azure vers Azure SQL Database à l’aide d’Azure Data Factory
Dans ce didacticiel, vous créez une fabrique de données à l’aide de l’interface utilisateur (IU) d’Azure Data Factory. Le pipeline de cette fabrique de données copie les données du Stockage Blob Azure vers Azure SQL Database. Le modèle de configuration de ce didacticiel s’applique à la copie depuis un magasin de données de fichiers vers un magasin de données relationnelles. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).
>
> - Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de copie.
> * Série de tests du pipeline
> * Déclencher le pipeline manuellement
> * Déclencher le pipeline selon une planification
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>configuration requise
* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Stockage Azure**. Vous utilisez le stockage blob comme magasins de données **source**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un.
* **Base de données SQL Azure**. Vous utilisez la base de données en tant que magasin de données **récepteur**. Si vous n’avez pas de base de données Azure SQL Database, consultez l’article [Création d’une base de données Azure SQL](../sql-database/sql-database-get-started-portal.md) pour savoir comme en créer une.

### <a name="create-a-blob-and-a-sql-table"></a>Créer un objet blob et une table SQL

À présent, préparez votre objet blob Azure et Azure SQL Database pour ce didacticiel, en effectuant les étapes suivantes :

#### <a name="create-a-source-blob"></a>Créer un objet blob source

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **emp.txt** sur votre disque.

    ```
    John,Doe
    Jane,Doe
    ```

2. Créez un conteneur nommé **adftutorial** dans votre stockage Blob Azure. Créez un dossier nommé **input** dans ce conteneur. Ensuite, chargez le fichier **emp.txt** dans le dossier **input**. Utilisez le portail Azure ou des outils tels que l’[Explorateur Stockage Azure](http://storageexplorer.com/) pour effectuer ces tâches.

#### <a name="create-a-sink-sql-table"></a>Créer une table SQL de récepteur

1. Utilisez le script SQL suivant pour créer la table **dbo.emp** dans Azure SQL Database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est **ACTIVÉ** pour votre serveur SQL Azure pour que le service Data Factory puisse écrire des données sur votre serveur SQL Azure. Pour vérifier et activer ce paramètre, procédez comme suit :

    1. Cliquez sur le hub **Plus de services** situé à gauche, puis sur **Serveurs SQL**.
    2. Sélectionnez votre serveur, puis cliquez sur **Pare-feu** sous **PARAMÈTRES**.
    3. Dans la page **Paramètres de pare-feu**, cliquez sur **ACTIVER** pour **Autoriser l’accès aux services Azure**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données
Dans cette étape, vous créez une fabrique de données et lancez l’interface utilisateur d’Azure Data Factory pour créer un pipeline dans la fabrique de données. 

1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page Nouvelle fabrique de données](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
     ![Page Nouvelle fabrique de données](./media/tutorial-copy-data-portal/name-not-available-error.png)
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
        Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V2 (préversion)** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.      
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
   ![Page d'accueil Data Factory](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. Cliquez sur la vignette **Créer et surveiller** pour lancer l’interface utilisateur d’Azure Data Factory dans un onglet séparé.

## <a name="create-a-pipeline"></a>Créer un pipeline
Dans cette étape, vous créez un pipeline avec une activité de copie dans la fabrique de données. L’activité de copie copie des données d’un stockage Blob Azure vers Azure SQL Database. Dans le [didacticiel de démarrage rapide](quickstart-create-data-factory-portal.md), vous avez créé un pipeline en procédant comme suit :

1. Créer le service lié. 
2. Créer des jeux de données d’entrée et de sortie.
3. Puis créer un pipeline.

Dans ce didacticiel, vous commencez par créer le pipeline, puis vous créez des services liés et des jeux de données lorsque en avez besoin pour configurer le pipeline. 

1. Dans la page de prise en main, cliquez sur **Créer un pipeline**. 

   ![Vignette Créer un pipeline](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. Dans la fenêtre **Propriétés** du pipeline, définissez le **nom** du pipeline sur **CopyPipeline**.

    ![Nom du pipeline](./media/tutorial-copy-data-portal/pipeline-name.png)
4. Dans la boîte à outils **Activités**, développez la catégorie **Flux de données** et glissez-déposez l’activité **Copie** de la boîte à outils vers la surface du concepteur de pipeline. 

    ![Glisser-déposer de l’activité de copie](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. Dans l’onglet **Général** de la fenêtre **Propriétés**, spécifiez **CopyFromBlobToSql** comme nom de l’activité.

    ![Nom de l’activité](./media/tutorial-copy-data-portal/activity-name.png)
6. Basculez vers l’onglet **Source**. Cliquez sur **+ Nouveau** pour créer un jeu de données source. 

    ![Menu de nouveau jeu de données source](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer**. Les données sources se trouvent dans un stockage Blob Azure, vous sélectionnez donc le stockage Blob Azure pour le jeu de données source. 

    ![Sélectionner le stockage Blob Azure](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. Vous voyez un nouvel **onglet** ouvert dans l’application avec le titre **AzureBlob1**.

    ![Onglet Azure Blob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. Dans l’onglet **Général** de la fenêtre **Propriétés** en bas, spécifiez **SourceBlobDataset** pour le **nom**.

    ![Nom du jeu de données](./media/tutorial-copy-data-portal/dataset-name.png)
10. Basculez vers l’onglet **Connexions** dans la fenêtre Propriétés. Cliquez sur **+ Nouveau** en regard de la zone de texte **Service lié**. Un service lié rattache un magasin de données ou une ressource de calcul à une fabrique de données. Dans ce cas, vous créez un service lié de stockage Azure pour lier votre compte de Stockage Azure à la fabrique de données. Le service lié comporte les informations de connexion utilisées par le service Data Factory pour établir la connexion au stockage Blob lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) qui contient les données sources. 

    ![Bouton de nouveau service lié](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    1. spécifiez **AzureStorageLinkedService** pour le champ **Nom**. 
    2. Sélectionnez votre compte de stockage Azure pour le champ **Nom du compte de stockage**.
    3. Cliquez sur **Tester la connexion** pour tester la connexion au compte de stockage Azure.  
    4. Cliquez sur **Enregistrer** pour enregistrer le service lié.

        ![Nouveau service lié Stockage Azure](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. Cliquez sur **Parcourir** pour le champ **Chemin d’accès du fichier**.  

    ![Bouton Parcourir pour le fichier](./media/tutorial-copy-data-portal/file-browse-button.png)
14. Accédez au dossier **adftutorial/input**, sélectionnez le fichier **emp.txt**, puis cliquez sur **Terminer**. Vous pouvez également double-cliquer sur emp.txt. 

    ![Sélectionner le fichier d’entrée](./media/tutorial-copy-data-portal/select-input-file.png)
15. Vérifiez que le **Format de fichier** est défini sur **Format texte** et que le **délimiteur de colonne** est défini sur **Virgule (`,`)**. Si le fichier source utilise des délimiteurs de lignes et de colonnes différents, vous pouvez cliquer sur **Detect Text Format** (Détecter le format texte) pour le champ **Format de fichier**. L’outil de copie des données détecte automatiquement le format de fichier et les délimiteurs pour vous. Vous pouvez toujours remplacer ces valeurs. Vous pouvez afficher un aperçu des données de cette page en cliquant sur **Aperçu des données**.

    ![Détecter le format texte](./media/tutorial-copy-data-portal/detect-text-format.png)
17. Basculez vers l’onglet **Schéma** dans la fenêtre Propriétés, puis cliquez sur **Importer un schéma**. Notez que l’application a détecté deux colonnes dans le fichier source. Vous importez le schéma ici afin de pouvoir mapper des colonnes du magasin de données source au magasin de données récepteur. Si vous n’avez pas besoin de mapper les colonnes, vous pouvez ignorer cette étape. Pour ce didacticiel, importez le schéma.

    ![Détecter le schéma source](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. Basculez maintenant vers l’**onglet avec le pipeline** ou cliquez sur le pipeline dans l’**arborescence** sur la gauche.  

    ![Onglet Pipeline](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. Vérifiez que **SourceBlobDataset** est sélectionné pour le champ Jeu de données source dans la fenêtre Propriétés. Vous pouvez afficher un aperçu des données de cette page en cliquant sur **Aperçu des données**. 
    
    ![Jeu de données source](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. Basculez vers l’onglet **Récepteur**, puis cliquez sur **Nouveau** pour créer un jeu de données récepteur. 

    ![Menu de nouveau jeu de données récepteur](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Azure SQL Database**, puis cliquez sur **Terminer**. Dans ce didacticiel, vous copiez des données dans une base de données SQL Azure. 

    ![Sélectionner Azure SQL Database](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. Dans l’onglet **Général** de la fenêtre Propriétés, définissez le nom sur **OutputSqlDataset**. 
    
    ![Nom du jeu de données de sortie](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. Basculez vers l’onglet **Connexions**, puis cliquez sur **Nouveau** pour **Service lié**. Un jeu de données doit être associé à un service lié. Le service lié comporte la chaîne de connexion utilisée par le service Data Factory pour établir la connexion à la base de données SQL Azure lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) dans lequel les données sont copiées. 
    
    ![Bouton de nouveau service lié](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    1. Entrez **AzureSqlDatabaseLinkedService** pour le champ **Nom**. 
    2. Sélectionnez votre serveur SQL Azure pour le champ **Nom du serveur**.
    4. Sélectionnez votre base de données SQL Azure pour le champ **Nom de la base de données**. 
    5. Entrez le nom de l’utilisateur pour le champ **Nom d’utilisateur**. 
    6. Entrez le mot de passe de l’utilisateur pour le champ **Mot de passe**. 
    7. Cliquez sur **Tester la connexion** pour tester la connexion.
    8. Cliquez sur **Enregistrer** pour enregistrer le service lié. 
    
        ![Nouveau service lié Azure SQL Database](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. Sélectionnez **[dbo].[emp]** pour **Table**. 

    ![Sélectionner la table emp](./media/tutorial-copy-data-portal/select-emp-table.png)
27. Basculez vers l’onglet **Schéma**, puis cliquez sur Importer le schéma. 

    ![Importer le schéma de destination](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. Sélectionnez la colonne **ID**, puis cliquez sur **Supprimer**. La colonne d’ID est une colonne d’identité dans la base de données SQL, l’activité de copie ne doit donc pas insérer des données dans cette colonne.

    ![Supprimer la colonne d’ID](./media/tutorial-copy-data-portal/delete-id-column.png)
30. Basculez vers l’onglet avec le **pipeline**, et vérifiez que **OutputSqlDataset** est sélectionné pour **Jeu de données récepteur**.

    ![Onglet Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. Basculez vers l’onglet **Mappage** dans la fenêtre Propriétés en bas, puis cliquez sur **Importer des schémas**. Notez que les première et deuxième colonnes dans le fichier source sont mappées aux champs **FirstName** et **LastName** dans la base de données SQL.

    ![Mapper des schémas](./media/tutorial-copy-data-portal/map-schemas.png)
33. Validez le pipeline en cliquant sur le bouton **Valider**. Cliquez sur la **flèche droite** pour fermer la fenêtre de validation.

    ![Sortie de validation de pipeline](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. Cliquez sur le bouton **Code** dans le coin droit. Vous voyez le code JSON associé au pipeline. 

    ![Bouton Code](./media/tutorial-copy-data-portal/code-button.png)
35. Le code JSON ressemble à l’extrait de code suivant :  

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>Série de tests du pipeline
Vous pouvez tester l’exécution d’un pipeline avant de publier des artefacts (services liés, jeux de données et pipeline) dans Data Factory ou votre propre référentiel VSTS GIT. 

1. Pour tester l’exécution du pipeline, cliquez sur **Série de tests** dans la barre d’outils. Vous voyez l’état de l’exécution du pipeline dans l’onglet **Sortie** de la fenêtre en bas. 

    ![Bouton Série de tests](./media/tutorial-copy-data-portal/test-run-output.png)
2. Vérifiez que les données du fichier source sont insérées dans la base de données SQL de destination. 

    ![Vérifier la sortie SQL](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. Cliquez sur **Publier tout** dans le volet gauche. Cette action publie les entités (services liés, jeux de données et pipelines) que vous avez créées dans Azure Data Factory.

    ![Bouton Publier](./media/tutorial-copy-data-portal/publish-button.png)
4. Patientez jusqu’à voir le message **Publication réussie**. Pour afficher les messages de notification, cliquez sur l’onglet **Afficher les notifications** dans la barre de gauche. Fermez la fenêtre de notifications en cliquant sur le **X**.

    ![Afficher les notifications](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Configurer le référentiel de code
Vous pouvez publier le code associé à vos artefacts de fabrique de données dans un référentiel de code Visual Studio Team System (VSTS). Dans cette étape, vous créez le référentiel de code. 

Si vous ne voulez pas utiliser le référentiel de code VSTS, vous pouvez ignorer cette étape et continuer la publication dans Data Factory comme dans l’étape précédente. 

1. Cliquez sur **Data Factory** dans le coin gauche (ou) sur la flèche en regard de celui-ci, puis cliquez sur **Configure Code Repository** (Configurer le référentiel de code). 

    ![Bouton Code](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. Dans la page **Paramètres du référentiel**, procédez comme suit : 
    1. Sélectionnez **Visual Studio Team Services Git** pour le champ **Type de référentiel**.
    2. Sélectionnez votre compte VSTS pour le champ **Compte Visual Studio Team Services**.
    3. Sélectionnez un projet dans votre compte VSTS pour le champ **Nom du projet**.
    4. Entrez **Tutorial2** pour le **nom du référentiel Git** à associer à votre fabrique de données. 
    5. Vérifiez que l’option **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) est sélectionnée. 
    6. Cliquez sur **Enregistrer** pour enregistrer les paramètres. 

        ![Paramètres du référentiel](./media/tutorial-copy-data-portal/repository-settings.png)
3. Vérifiez que **VSTS GIT** est sélectionné pour le référentiel.

    ![VSTS GIT sélectionné](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. Dans un autre onglet du navigateur web, accédez au référentiel **Tutorial2** ; vous voyez deux branches : **master** et **adf_publish**.

    ![Branches master et adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Vérifiez que les **fichiers JSON** des entités Data Factory se trouvent dans la branche **master**.

    ![Fichiers dans la branche master](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Vérifiez que les **fichiers JSON** ne se trouvent pas déjà dans la branche **adf_publish**. 

    ![Fichiers dans la branche adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Ajoutez une **description** du **pipeline**, puis cliquez sur le bouton **Enregistrer** dans la barre d’outils. 

    ![Ajouter une description du pipeline](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Vous devez maintenant voir une **branche** avec votre nom d’utilisateur dans le référentiel **Tutorial2**. La modification que vous avez apportée se trouve dans votre propre branche, et non dans la branche master. Vous ne pouvez publier des entités qu’à partir de la branche master.

    ![Votre branche](./media/tutorial-copy-data-portal/your-branch.png)
9. Passez au bouton **synchronisation** (ne cliquez pas pour le moment), sélectionnez l’option **Valider les modifications**, puis cliquez sur le bouton **synchronisation** pour synchroniser vos modifications avec la branche **master**. 

    ![Valider et synchroniser vos modifications](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. Dans la fenêtre de synchronisation de vos modifications, effectuez les actions suivantes : 

    1. Vérifiez que **CopyPipeline** apparaît dans la liste des pipelines mis à jour.
    2. Vérifiez que **Publish changes after sync** (Publier les modifications après la synchronisation) est sélectionné. Si vous décochez cette option, vous synchronisez uniquement vos modifications dans votre branche avec la branche master, mais vous ne les publiez dans le service Data Factory. Vous pouvez les publier ultérieurement à l’aide du bouton **Publier**. Si vous cochez cette option, les modifications sont tout d’abord synchronisées avec la branche master, puis publiées dans le service Data Factory.
    3. Cliquez sur **Synchroniser**. 

    ![Fenêtre de synchronisation de vos modifications](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. Vous pouvez maintenant voir les fichiers dans la branche **adf_publish** du référentiel **Tutorial2**. Vous trouverez également le modèle Azure Resource Manager de votre solution Data Factory dans cette branche.  

    ![Fichiers dans la branche adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Déclencher le pipeline manuellement
Dans cette étape, vous déclenchez manuellement le pipeline que vous avez publié dans l’étape précédente. 

1. Cliquez sur **Déclencher** dans la barre d’outils, puis cliquez sur **Déclencher maintenant**. Sur la page **Exécution du pipeline**, cliquez sur **Terminer**.  

    ![Menu Déclencher maintenant](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Basculez vers l’onglet **Surveiller** sur la gauche. Vous voyez un pipeline qui est déclenché par un déclencheur manuel. Vous pouvez utiliser les liens dans la colonne Actions pour afficher les détails de l’activité et réexécuter le pipeline.

    ![Surveillance de l’exécution du pipeline](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Pour voir les exécutions d’activités associées à l’exécution du pipeline, cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Dans cet exemple, il n’y a qu’une seule activité, vous ne voyez donc qu’une seule entrée dans la liste. Pour plus de détails sur l’opération de copie, cliquez sur le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Vous pouvez cliquer sur **Pipelines** en haut pour revenir à la vue **Exécutions du pipeline**. Pour actualiser la vue, cliquez sur **Actualiser**.

    ![Afficher les exécutions d’activités](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Vérifiez que deux lignes supplémentaires sont ajoutées à la table **emp** dans la base de données SQL Azure. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Déclencher le pipeline selon une planification
Dans cette planification, vous créez un déclencheur de planificateur pour le pipeline. Le déclencheur exécute le pipeline selon la planification spécifiée (toutes les heures, quotidienne, etc.). Dans cet exemple, vous définissez le déclencheur pour s’exécuter toutes les minutes jusqu’à ce que la date/heure de fin spécifiée. 

1. Basculez vers l’onglet **Modifier** sur la gauche. 

    ![Onglet Modifier](./media/tutorial-copy-data-portal/edit-tab.png)
2. Cliquez sur **Déclencheur**, puis sélectionnez **Nouveau/Modifier**. Si le pipeline n’est pas actif, basculez vers celui-ci. 

    ![Menu de nouveau déclencheur/modifier le déclencheur](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. Dans la fenêtre **Ajouter un déclencheur**, cliquez sur **Choose trigger...**  (Choisir un déclencheur...), puis cliquez sur **+ Nouveau**. 

    ![Ajouter un déclencheur - nouveau déclencheur](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Dans la fenêtre **Nouveau déclencheur**, procédez comme suit : 

    1. Définissez le **nom** sur **RunEveryMinute**.
    2. Sélectionnez **On Date** (À la date) pour **Fin**. 
    3. Cliquez sur la liste déroulante pour **End On** (Fin à).
    4. Sélectionnez le **jour actuel**. Par défaut, le jour de fin est défini sur le jour suivant. 
    5. Mettez à jour la partie des **minutes** quelques minutes après la date/heure actuelle. Le déclencheur n’est activé qu’après avoir publié les modifications. Par conséquent, si vous le définissez à quelques minutes d’intervalle et que vous ne publiez pas, vous ne voyez pas d’exécution du déclencheur.  
    6. Cliquez sur **Appliquer**. 

        ![Définir des propriétés de déclencheur](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. Cochez l’option **Activé**. Vous pouvez la désactiver et l’activer ultérieurement à l’aide de cette case à cocher.
    8. Cliquez sur **Suivant**.

        ![Déclencheur activé - suivant](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Un coût est associé à chaque exécution du pipeline. Par conséquent, définissez la date de fin de manière appropriée. 
6. Dans la page **Trigger Run Parameters** (Paramètres d’exécution du déclencheur), lisez l’avertissement, puis cliquez sur **Terminer**. Le pipeline de cet exemple n’accepte pas de paramètres. 

    ![Paramètres du pipeline](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. Cliquez sur **Synchroniser** pour synchroniser les modifications dans votre branche avec la branche principale. Vérifiez que **Publier les modifications après la synchronisation** est sélectionné par défaut. Par conséquent, lorsque vous sélectionnez **Synchroniser**, il publie également les entités mises à jour pour le service Azure Data Factory à partir de la branche principale. Le déclencheur n’est activé réellement que lorsque la publication réussit.

    ![Déclencheur de publication](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
9. Basculez vers l’onglet **Surveiller** sur la gauche pour voir les exécutions du pipeline déclenchées. 

    ![Exécutions du pipeline déclenchées](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. Pour basculer de la vue des exécutions du pipeline vers la vue des exécutions du déclencheur, cliquez sur Exécutions du pipeline, puis sélectionnez Exécutions du déclencheur.
    
    ![Menu Exécutions du déclencheur](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. Vous voyez les exécutions du déclencheur dans une liste. 

    ![Liste d’exécutions du déclencheur](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. Vérifiez que deux lignes par minute (pour chaque exécution du pipeline) sont insérées dans la table **emp** jusqu’à l’heure de fin spécifiée. 

## <a name="next-steps"></a>étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de copie.
> * Série de tests du pipeline
> * Déclencher le pipeline manuellement
> * Déclencher le pipeline selon une planification
> * Surveiller les exécutions de pipeline et d’activité.


Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud : 

> [!div class="nextstepaction"]
>[Copier des données locales vers le cloud](tutorial-hybrid-copy-portal.md)
