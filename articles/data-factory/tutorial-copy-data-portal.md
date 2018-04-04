---
title: Utiliser le portail Azure pour créer un pipeline de fabrique de données | Microsoft Docs
description: Ce didacticiel fournit des instructions détaillées sur l’utilisation d’un portail Azure pour créer une fabrique de données avec un pipeline. Le pipeline utilise l’activité de copie pour copier des données d’un stockage Blob Azure vers une base de données SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 34c78a114c1d106c400a94941aa113153383e206
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Copier des données à partir d’un stockage Blob Azure vers une base de données SQL en utilisant Azure Data Factory
Dans ce didacticiel, vous créez une fabrique de données à l’aide de l’interface utilisateur (IU) d’Azure Data Factory. Le pipeline de cette fabrique de données copie les données d’un stockage Blob Azure vers une base de données SQL. Le modèle de configuration de ce didacticiel s’applique à la copie depuis un magasin de données de fichiers vers un magasin de données relationnelles. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Si vous débutez avec Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).
>
> - Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 de Data Factory, qui est généralement disponible, consultez [Mise en route de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Dans ce didacticiel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de copie.
> * Effectuer une série de tests sur le pipeline.
> * Déclencher le pipeline manuellement.
> * Déclencher le pipeline en fonction d’une planification.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Azure Storage**. Vous utilisez le stockage Blob comme magasin de données *source*. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un.
* **Base de données SQL Azure**. Vous utilisez la base de données comme magasin de données *récepteur*. Si vous ne disposez pas d’une base de données SQL, consultez [Créer une base de données SQL](../sql-database/sql-database-get-started-portal.md) pour découvrir comment en créer une.

### <a name="create-a-blob-and-a-sql-table"></a>Créer un objet blob et une table SQL

À présent, préparez votre stockage Blob et votre base de données SQL pour ce didacticiel, en procédant comme suit.

#### <a name="create-a-source-blob"></a>Créer un objet blob source

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **emp.txt** sur votre disque :

    ```
    John,Doe
    Jane,Doe
    ```

2. Créez un conteneur nommé **adftutorial** dans votre stockage Blob. Créez un dossier nommé **input** dans ce conteneur. Ensuite, chargez le fichier **emp.txt** dans le dossier **input**. Utilisez le portail Azure ou des outils tels que l’[Explorateur Stockage Azure](http://storageexplorer.com/) pour effectuer ces tâches.

#### <a name="create-a-sink-sql-table"></a>Créer une table SQL de récepteur

1. Utilisez le script SQL suivant pour créer la table **dbo.emp** dans votre base de données SQL :

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

2. Autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est **ACTIVÉ** pour votre serveur SQL afin que Data Factory puisse écrire des données sur votre serveur SQL. Pour vérifier et activer ce paramètre, procédez comme suit :

    a. Sur la gauche, sélectionnez **Autres services** > **Serveurs SQL**.

    b. Sélectionnez votre serveur, puis sous **PARAMÈTRES**, sélectionnez **Pare-feu**.

    c. Dans la page **Paramètres de pare-feu**, sélectionnez **ACTIVÉ** pour **Autoriser l’accès aux services Azure**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données
À cette étape, vous allez créer une fabrique de données et démarrer l’interface utilisateur de Data Factory afin de créer un pipeline dans la fabrique de données. 

1. Ouvrez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Dans le menu sur la gauche, sélectionnez **Nouveau** > **Données + Analytique** > **Data Factory**. 
  
   ![Création d’une fabrique de données](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**. 
      
     ![Nouvelle fabrique de données](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si le message d’erreur suivant s’affiche pour le champ du nom, modifiez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les règles Data Factory.
  
   ![Message d’erreur](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
5. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md). 
6. Sous **Version**, sélectionnez **V2 (préversion)**.
7. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (comme Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.
8. Sélectionnez **Épingler au tableau de bord**. 
9. Sélectionnez **Créer**. 
10. Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** : 

    ![Vignette Déploiement d’une fabrique de données](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. Une fois la création terminée, la page **Fabrique de données** s’affiche comme sur l’image.
   
    ![Page d'accueil Data Factory](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé.

## <a name="create-a-pipeline"></a>Créer un pipeline
À cette étape, vous créez un pipeline avec une activité de copie dans la fabrique de données. L’activité de copie permet de copier les données d’un stockage Blob vers une base de données SQL. Dans le [didacticiel de démarrage rapide](quickstart-create-data-factory-portal.md), vous avez créé un pipeline en procédant comme suit :

1. Créer le service lié. 
2. Créer des jeux de données d’entrée et de sortie.
3. Créer un pipeline.

Dans ce didacticiel, vous commencez par créer le pipeline. Puis vous créez des jeux de données et des services liés lorsque vous en avez besoin pour configurer le pipeline. 

1. Dans la page **Prise en main**, cliquez sur **Créer un pipeline**. 

   ![Création d’un pipeline](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. Dans la fenêtre **Propriétés** du pipeline, sous **Nom**, spécifiez **CopyPipeline** comme nom du pipeline.

    ![Nom du pipeline](./media/tutorial-copy-data-portal/pipeline-name.png)
3. Dans la boîte à outils **Activités**, développez la catégorie **Flux de données** et glissez-déplacez l’activité **Copie** de la boîte à outils vers la surface du concepteur de pipeline. 

    ![Activité de copie](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
4. Dans l’onglet **Général** de la fenêtre **Propriétés**, spécifiez **CopyFromBlobToSql** comme nom de l’activité.

    ![Nom de l’activité](./media/tutorial-copy-data-portal/activity-name.png)
5. Accédez à l’onglet **Source**. Sélectionnez **+ Nouveau** pour créer un jeu de données source. 

    ![Onglet Source](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
6. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis sélectionnez **Terminer**. Sachant que les données sources se trouvent dans un stockage Blob, vous devez sélectionner le **Stockage Blob Azure** pour le jeu de données source. 

    ![Sélection du stockage](./media/tutorial-copy-data-portal/select-azure-storage.png)
7. Un nouvel onglet s’ouvre dans l’application : il se nomme **AzureBlob1**.

    ![Onglet AzureBlob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
8. Dans l’onglet **Général** au bas de la fenêtre **Propriétés**, sous **Nom**, entrez **SourceBlobDataset**.

    ![Nom du jeu de données](./media/tutorial-copy-data-portal/dataset-name.png)
9. Accédez à l’onglet **Connexion** dans la fenêtre **Propriétés**. En regard de la zone de texte **Service lié**, sélectionnez **+ Nouveau**. 

    Un service lié rattache un magasin de données ou une ressource de calcul à une fabrique de données. Dans ce cas, vous créez un service lié de stockage pour lier votre compte de stockage au magasin de données. Le service lié comporte les informations de connexion utilisées par Data Factory pour établir la connexion au stockage Blob lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) qui contient les données sources. 

    ![Bouton de nouveau service lié](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
10. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    a. Dans le champ **Nom**, entrez **AzureStorageLinkedService**. 

    b. Sous **Nom du compte de stockage**, sélectionnez votre compte de stockage.

    c. Sélectionnez **Tester la connexion** pour tester la connexion au compte de stockage.

    d. Cliquez sur **Enregistrer** pour enregistrer le service lié.

    ![Nouveau service lié](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
11. En regard de **Chemin d’accès du fichier**, sélectionnez **Parcourir**.

    ![Bouton Parcourir pour le chemin d’accès du fichier](./media/tutorial-copy-data-portal/file-browse-button.png)
12. Accédez au dossier **adftutorial/input**, sélectionnez le fichier **emp.txt**, puis sélectionnez **Terminer**. Vous pouvez également double-cliquer sur **emp.txt**. 

    ![Sélectionner le fichier d’entrée](./media/tutorial-copy-data-portal/select-input-file.png)
13. Vérifiez que **Format de fichier** est défini sur **Format texte** et que **Séparateur de colonne** est défini sur **Virgule (`,`)**. Si le fichier source utilise des séparateurs de lignes et de colonnes différents, vous pouvez sélectionner **Détecter le format texte** pour **Format de fichier**. L’outil de copie des données détecte automatiquement le format de fichier et les délimiteurs pour vous. Vous pouvez toujours remplacer ces valeurs. Pour afficher un aperçu des données dans cette page, sélectionnez **Aperçu des données**.

    ![Détecter le format texte](./media/tutorial-copy-data-portal/detect-text-format.png)
14. Accédez à l’onglet **Schéma** dans la fenêtre **Propriétés**, puis sélectionnez **Importer un schéma**. Notez que l’application a détecté deux colonnes dans le fichier source. Vous importez le schéma ici afin de pouvoir mapper les colonnes du magasin de données source au magasin de données récepteur. Si vous n’avez pas besoin de mapper les colonnes, vous pouvez ignorer cette étape. Pour ce didacticiel, importez le schéma.

    ![Détecter le schéma source](./media/tutorial-copy-data-portal/detect-source-schema.png)  
15. À présent, accédez à l’onglet avec le pipeline ou sélectionnez le pipeline sur la gauche.

    ![Onglet Pipeline](./media/tutorial-copy-data-portal/pipeline-tab.png)
16. Dans le champ **Jeu de données source** de la fenêtre **Propriétés**, vérifiez que l’option **SourceBlobDataset** est sélectionnée. Pour afficher un aperçu des données dans cette page, sélectionnez **Aperçu des données**. 
    
    ![Jeu de données source](./media/tutorial-copy-data-portal/source-dataset-selected.png)
17. Accédez à l’onglet **Récepteur**, puis sélectionnez **+ Nouveau** pour créer un jeu de données récepteur. 

    ![Jeu de données récepteur](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
18. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Azure SQL Database**, puis sélectionnez **Terminer**. Dans ce didacticiel, vous copiez des données vers une base de données SQL. 

    ![Sélection de la base de données SQL](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
19. Dans l’onglet **Général** de la fenêtre **Propriétés**, sous **Nom**, entrez **OutputSqlDataset**. 
    
    ![Nom du jeu de données de sortie](./media/tutorial-copy-data-portal/output-dataset-name.png)
20. Accédez à l’onglet **Connexion**, et en regard de **Service lié**, sélectionnez **+ Nouveau**. Un jeu de données doit être associé à un service lié. Le service lié comporte la chaîne de connexion utilisée par Data Factory pour établir la connexion à la base de données SQL lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) dans lequel les données sont copiées. 
    
    ![Service lié](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
21. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    a. Sous **Nom**, entrez **AzureSqlDatabaseLinkedService**.

    b. Sous **Nom du serveur**, sélectionnez votre instance SQL Server.

    c. Sous **Nom de la base de données**, sélectionnez votre base de données SQL.

    d. Sous **Nom d’utilisateur**, entrez le nom de l’utilisateur.

    e. Sous **Mot de passe**, entrez le mot de passe de l’utilisateur.

    f. Sélectionnez **Tester la connexion** pour tester la connexion.

    g. Cliquez sur **Enregistrer** pour enregistrer le service lié. 
    
    ![Enregistrer le nouveau service lié](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

22. Sélectionnez **[dbo].[emp]** dans le champ **Table**. 

    ![Table](./media/tutorial-copy-data-portal/select-emp-table.png)
23. Accédez à l’onglet **Schéma**, puis sélectionnez **Importer un schéma**. 

    ![Sélectionner Importer un schéma](./media/tutorial-copy-data-portal/import-destination-schema.png)
24. Sélectionnez la colonne **ID**, puis sélectionnez **Supprimer**. La colonne **ID** est une colonne d’identité dans la base de données SQL. L’activité de copie n’a donc pas besoin d’insérer de données dans cette colonne.

    ![Supprimer la colonne d’ID](./media/tutorial-copy-data-portal/delete-id-column.png)
25. Accédez à l’onglet avec le pipeline, puis dans **Jeu de données récepteur**, vérifiez que l’option **OutputSqlDataset** est sélectionnée.

    ![Onglet Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
26. Accédez à l’onglet **Mappage** au bas de la fenêtre **Propriétés**, puis sélectionnez **Importer des schémas**. Les première et deuxième colonnes du fichier source sont mappées aux champs **FirstName** et **LastName** dans la base de données SQL.

    ![Mapper des schémas](./media/tutorial-copy-data-portal/map-schemas.png)
27. Sélectionnez **Valider** pour valider les paramètres du pipeline. En haut à droite, sélectionnez la flèche droite pour fermer la fenêtre de validation.

    ![Sortie de validation de pipeline](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
28. En haut à droite, sélectionnez **Code**. Vous voyez le code JSON associé au pipeline. 

    ![Bouton Code](./media/tutorial-copy-data-portal/code-button.png)
29. Le code JSON ressemble à l’extrait de code suivant : 

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
Vous pouvez effectuer une série de tests sur un pipeline avant de publier des artefacts (services liés, jeux de données et pipeline) dans Data Factory ou votre propre référentiel Visual Studio Team Services Git. 

1. Pour effectuer une série de tests sur le pipeline, sélectionnez **Série de tests** dans la barre d’outils. L’état d’exécution du pipeline apparaît dans l’onglet **Sortie** au bas de la fenêtre. 

    ![Tester le pipeline](./media/tutorial-copy-data-portal/test-run-output.png)
2. Vérifiez que les données du fichier source sont insérées dans la base de données SQL de destination. 

    ![Vérifier la sortie SQL](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. Dans le volet gauche, sélectionnez **Publier tout**. Cette action publie les entités (services liés, jeux de données et pipelines) que vous avez créées dans Data Factory.

    ![Publish](./media/tutorial-copy-data-portal/publish-button.png)
4. Patientez jusqu’à voir le message **Publication réussie**. Pour afficher les messages de notification, sélectionnez l’onglet **Afficher les notifications** dans la barre latérale gauche. Pour fermer la fenêtre de notification, sélectionnez **Fermer**.

    ![Afficher les notifications](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Configurer le référentiel de code
Vous pouvez publier le code associé à vos artefacts de fabrique de données dans un référentiel de code Visual Studio Team Services. Dans cette étape, vous créez le référentiel de code.  Pour en savoir plus sur la création visuelle avec l’intégration de VSTS, consultez [Author with VSTS Git integration](author-visually.md#author-with-vsts-git-integration) (Créer avec l’intégration de VSTS Git).

Si vous ne souhaitez pas travailler avec le référentiel de code Visual Studio Team Services, vous pouvez ignorer cette étape. Vous pouvez continuer vos publications dans Data Factory comme vous l’avez fait à l’étape précédente. 

1. En haut à gauche, sélectionnez **Data Factory**, ou utilisez la flèche bas située en regard pour le faire, puis sélectionnez **Configurer le référentiel de code**. 

    ![Configurer le référentiel de code](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. Dans la page **Paramètres du référentiel**, procédez comme suit :

    a. Sous **Type de référentiel**, sélectionnez **Visual Studio Team Services Git**.

    b. Sous **Compte Visual Studio Team Services**, sélectionnez votre compte Visual Studio Team Services.

    c. Sous **Nom du projet**, sélectionnez un projet de votre compte Visual Studio Team Services.

    d. Sous **nom du référentiel Git**, entrez **Tutorial2** afin d’associer le référentiel Git à votre fabrique de données.

    e. Vérifiez que la case à cocher **Importer des ressources Data Factory existantes dans le référentiel** est activée.

    f. Sélectionnez **Enregistrer** pour enregistrer les paramètres. 

    ![Paramètres du référentiel](./media/tutorial-copy-data-portal/repository-settings.png)
3. Vérifiez que **VSTS GIT** est sélectionné pour le référentiel.

    ![Sélectionner VSTS GIT](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. Dans un onglet distinct du navigateur web, accédez au référentiel **Tutorial2**. Deux branches s’affichent : **adf_publish** et **master**.

    ![Branches master et adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Vérifiez que les fichiers JSON des entités Data Factory se trouvent dans la branche **master**.

    ![Fichiers dans la branche master](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Vérifiez que les fichiers JSON ne se trouvent pas déjà dans la branche **adf_publish**. 

    ![Fichiers dans la branche adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Dans **Description**, ajoutez la description du pipeline, puis sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Description du pipeline](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Vous devez maintenant voir une branche avec votre nom d’utilisateur dans le référentiel **Tutorial2**. La modification que vous avez apportée se trouve dans votre propre branche, et non dans la branche master. Vous ne pouvez publier d’entités qu’à partir de la branche master.

    ![Votre branche](./media/tutorial-copy-data-portal/your-branch.png)
9. Déplacez la souris sur le bouton **Synchroniser** (ne le sélectionnez pas pour le moment), activez la case à cocher **Valider les modifications**, puis sélectionnez le bouton **Synchroniser** pour synchroniser vos modifications avec la branche master. 

    ![Valider et synchroniser les modifications](./media/tutorial-copy-data-portal/commit-and-sync.png)
10. Dans la fenêtre de **synchronisation de vos modifications**, effectuez les actions suivantes : 

    a. Vérifiez que **CopyPipeline** apparaît dans la liste **Pipelines** mise à jour.

    b. Vérifiez que **Publish changes after sync** (Publier les modifications après la synchronisation) est sélectionné. Si vous désactivez cette case à cocher, vous synchronisez uniquement vos modifications dans votre branche avec la branche master. Elles ne sont pas publiées dans Data Factory. Vous pouvez les publier ultérieurement à l’aide du bouton **Publier**. Si vous activez cette case à cocher, les modifications sont tout d’abord synchronisées avec la branche master, puis publiées dans Data Factory.

    c. Sélectionnez **Synchroniser**. 

    ![Synchronisation de vos modifications](./media/tutorial-copy-data-portal/sync-your-changes.png)
11. Vous pouvez maintenant voir les fichiers dans la branche **adf_publish** du référentiel **Tutorial2**. Vous trouverez également le modèle Azure Resource Manager de votre solution Data Factory dans cette branche. 

    ![Liste de fichiers dans la branche adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Déclencher le pipeline manuellement
Dans cette étape, vous déclenchez manuellement le pipeline que vous avez publié dans l’étape précédente. 

1. Sélectionnez **Déclencher** dans la barre d’outils, puis **Déclencher maintenant**. Dans la page **Exécution du pipeline**, sélectionnez **Terminer**.  

    ![Déclencher le pipeline](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Accédez à l’onglet **Surveiller** sur la gauche. Vous voyez un pipeline qui est déclenché par un déclencheur manuel. Vous pouvez utiliser les liens dans la colonne **Actions** pour afficher les détails de l’activité et réexécuter le pipeline.

    ![Surveiller des exécutions de pipelines](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Dans cet exemple, il n’y a qu’une seule activité, vous ne voyez donc qu’une seule entrée dans la liste. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Sélectionnez **Pipelines** au sommet de la page pour revenir à l’affichage des **exécutions du pipeline**. Sélectionnez **Actualiser** pour actualiser l’affichage.

    ![Surveiller des exécutions d’activités](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Vérifiez que deux lignes supplémentaires sont ajoutées à la table **emp** dans la base de données SQL. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Déclencher le pipeline selon une planification
Dans cette planification, vous créez un déclencheur de planificateur pour le pipeline. Le déclencheur exécute le pipeline selon la planification spécifiée, par exemple toutes les heures ou tous les jours. Dans cet exemple, vous définissez le déclencheur pour s’exécuter toutes les minutes jusqu’à ce que la date/heure de fin spécifiée. 

1. Accédez à l’onglet **Modifier** sur la gauche. 

    ![Onglet Modifier](./media/tutorial-copy-data-portal/edit-tab.png)
2. Sélectionnez **Déclencheur**, puis sélectionnez **Nouveau/Modifier**. Si le pipeline n’est pas actif, accédez à ce dernier. 

    ![Option de déclencheur](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. Dans la fenêtre **Ajouter des déclencheurs**, sélectionnez **Choisir un déclencheur**, puis **+ Nouveau**. 

    ![Bouton Nouveau](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Dans la fenêtre **Nouveau déclencheur**, procédez comme suit : 

    a. Sous **Nom**, entrez **RunEveryMinute**.

    b. Sous **Fin**, sélectionnez **On Date**.

    c. Sous **Fin à**, sélectionnez la liste déroulante.

    d. Sélectionnez le **jour actuel**. Par défaut, le jour de fin est défini sur le jour suivant.

    e. Mettez à jour la partie des **minutes** quelques minutes après la date/heure actuelle. Le déclencheur n’est activé qu’après avoir publié les modifications. Si vous le définissez à quelques minutes d’intervalle mais ne publiez pas, vous ne voyez pas d’exécution du déclencheur.

    f. Sélectionnez **Appliquer**. 

    ![Propriétés de déclencheur](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. Sélectionnez l’option **Activé**. Vous pouvez la désactiver et l’activer ultérieurement à l’aide de cette case à cocher.

    h. Sélectionnez **Suivant**.

    ![Bouton Activé](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Chaque exécution de pipeline coûte de l’argent. Il est donc important de définir correctement la date de fin. 
5. Dans la page **Paramètres d’exécution du déclencheur**, lisez l’avertissement, puis sélectionnez **Terminer**. Le pipeline de cet exemple n’accepte pas de paramètres. 

    ![Paramètres d’exécution du déclencheur](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
6. Sélectionnez **Synchroniser** pour synchroniser les modifications dans votre branche avec la branche master. L’option **Publier les modifications après la synchronisation** est sélectionnée par défaut. Lorsque vous sélectionnez **Synchroniser**, les entités mises à jour sont également publiées dans Data Factory à partir de la branche master. Le déclencheur n’est activé que lorsque la publication réussit.

    ![Synchronisation de vos modifications](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
7. Accédez à l’onglet **Surveiller** sur la gauche pour voir les exécutions du pipeline déclenchées. 

    ![Exécutions du pipeline déclenchées](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. Pour basculer de la vue des **exécutions du pipeline** vers la vue des **exécutions du déclencheur**, sélectionnez **Exécutions du pipeline**, puis sélectionnez **Exécutions du déclencheur**.
    
    ![Exécutions de déclencheur](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. Vous voyez les exécutions du déclencheur dans une liste. 

    ![Liste d’exécutions du déclencheur](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. Vérifiez que deux lignes par minute (pour chaque exécution du pipeline) sont insérées dans la table **emp** jusqu’à l’heure de fin spécifiée. 

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un pipeline avec une activité de copie.
> * Effectuer une série de tests sur le pipeline.
> * Déclencher le pipeline manuellement.
> * Déclencher le pipeline en fonction d’une planification.
> * Surveiller les exécutions de pipeline et d’activité.


Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud : 

> [!div class="nextstepaction"]
>[Copier des données locales vers le cloud](tutorial-hybrid-copy-portal.md)
