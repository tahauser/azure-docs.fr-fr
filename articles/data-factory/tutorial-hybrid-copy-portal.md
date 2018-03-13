---
title: "Copier des données depuis SQL Server vers le stockage Blob à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier les données d’un magasin de données local dans le cloud en utilisant un runtime d’intégration auto-hébergé dans Azure Data Factory."
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: ced708febe848d4555429b78c0227a35b7f0c79f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Copier des données depuis une base de données SQL Server locale vers un stockage Blob Azure
Dans ce didacticiel, vous allez utiliser l’interface utilisateur d’Azure Data Factory pour créer un pipeline Data Factory qui copie les données d’une base de données SQL Server locale vers un stockage Blob Azure. Vous allez créer et utiliser un runtime d’intégration auto-hébergé, qui déplace les données entre les banques de données locales et cloud.

> [!NOTE]
> Cet article s’applique à la version 2 d’Azure Data Factory, actuellement en préversion. Si vous utilisez la version 1 de Data Factory, qui est généralement disponible, consultez la [Documentation de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Cet article ne fournit pas de présentation détaillée de Data Factory. Pour plus d’informations, consultez [Présentation d’Azure Data Factory](introduction.md). 

Dans ce didacticiel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créer des services liés SQL Server et au Stockage Azure. 
> * Créer des jeux de données SQL Server et Blob Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

## <a name="prerequisites"></a>Prérequis
### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="azure-roles"></a>Rôles Azure
Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit avoir le rôle de *contributeur* ou de *propriétaire*, ou doit appartenir à un *administrateur* de l’abonnement Azure. 

Pour afficher les autorisations dont vous disposez dans l’abonnement, accédez au portail Azure. Dans l’angle supérieur droit, sélectionnez votre nom d’utilisateur, puis **Autorisations**. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié. Pour des exemples d’instructions concernant l’ajout d’un utilisateur à un rôle, consultez l’article [Ajout de rôles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 et 2017
Dans le cadre de ce didacticiel, vous utilisez une base de données SQL Server locale comme magasin de données *source*. Le pipeline de la fabrique de données que vous allez créer dans ce didacticiel copie les données de cette base de données SQL Server locale (source) dans un stockage Blob (récepteur). Créez ensuite un tableau nommé **emp** dans votre base de données SQL Server, puis insérez-y quelques exemples d’entrées. 

1. Exécutez SQL Server Management Studio. S’il n’est pas déjà installé sur votre machine, accédez à [Télécharger SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Connectez-vous à votre instance SQL Server à l’aide de vos informations d’identification. 

3. Créez un exemple de base de données. Dans l’arborescence, cliquez avec le bouton droit sur **Bases de données**, puis sur **Nouvelle base de données**. 
4. Dans la fenêtre **Nouvelle base de données**, entrez un nom pour la base de données, puis cliquez sur **OK**. 

5. Exécutez le script de requête suivant sur la base de données pour créer la table **emp** et y insérer quelques données d’exemple :

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

6. Dans l’arborescence, cliquez avec le bouton droit sur la base de données créée, puis sur **Nouvelle requête**.

### <a name="azure-storage-account"></a>Compte Azure Storage
Dans ce didacticiel, vous utilisez un compte de stockage Azure à usage général (stockage Blob plus spécifiquement) comme banque de données réceptrice/de destination. Si vous ne possédez pas de compte Stockage Azure à usage général, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account). Le pipeline de la fabrique de données que vous allez créer dans ce didacticiel copie les données de cette base de données SQL Server locale (source) dans un stockage Blob (récepteur). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce didacticiel, vous utilisez le nom et la clé de votre compte de stockage. Pour obtenir le nom et la clé de votre compte de stockage, procédez comme suit : 

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre nom d’utilisateur et votre mot de passe Azure. 

2. Dans le volet gauche, sélectionnez **Plus de services**. Filtrez à l’aide du mot-clé **Stockage**, puis sélectionnez **Comptes de stockage**.

    ![Recherche de compte de stockage](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Dans la liste des comptes de stockage, appliquez un filtre pour votre compte de stockage (si nécessaire). Sélectionnez ensuite votre compte de stockage. 

4. Dans la fenêtre **Compte de stockage**, sélectionnez **Clés d’accès**.

    ![Clés d’accès](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Dans les zones **Nom du compte de stockage** et **key1**, copiez les valeurs, puis collez-les dans le bloc-notes ou un autre éditeur pour une utilisation ultérieure dans le didacticiel. 

#### <a name="create-the-adftutorial-container"></a>Créer le conteneur adftutorial 
Dans cette section, vous allez créer un conteneur d’objets blob nommé **adftutorial** dans votre stockage Blob. 

1. Dans la fenêtre **Compte de stockage**, accédez à **Vue d’ensemble**, puis sélectionnez **Objets blob**. 

    ![Sélection de l’option Objets blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Dans la fenêtre **Service Blob**, sélectionnez **Conteneur**. 

    ![Bouton Conteneur](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Dans la fenêtre **Nouveau conteneur**, sous **Nom**, entrez **adftutorial**. Sélectionnez ensuite **OK**. 

    ![Fenêtre Nouveau conteneur](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Cliquez sur **adftutorial** dans la liste des conteneurs.

    ![Sélection de conteneurs](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Gardez la fenêtre **conteneur** de **adftutorial** ouverte. Elle vous permet de vérifier la sortie à la fin du didacticiel. Data Factory crée automatiquement le dossier de sortie de ce conteneur, de sorte que vous n’avez pas besoin d’en créer.

    ![Fenêtre de conteneur](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Créer une fabrique de données
À cette étape, vous allez créer une fabrique de données et démarrer l’interface utilisateur de Data Factory pour créer un pipeline dans la fabrique de données. 

1. Ouvrez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Dans le menu sur la gauche, sélectionnez **Nouveau** > **Données + Analytique** > **Data Factory**.
   
   ![Création de la nouvelle fabrique de données](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
3. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**. 
      
     ![Page Nouvelle fabrique de données](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données doit être un *nom global unique*. Si le message d’erreur suivant s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les règles Data Factory.
  
   ![Nouveau nom de fabrique de données](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
4. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
5. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.
         
    Pour plus d'informations sur les groupes de ressources, consultez l’article qui traite de l’[utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).
6. Sous **Version**, sélectionnez **V2 (préversion)**.
7. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (tels que le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par Data Factory peuvent se trouver dans d’autres régions.
8. Sélectionnez **Épingler au tableau de bord**. 
9. Sélectionnez **Créer**.
10. Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** :

    ![Vignette Déploiement de Data Factory](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
11. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image :
   
    ![Page d'accueil Data Factory](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
12. Sélectionnez la vignette **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé. 


## <a name="create-a-pipeline"></a>Créer un pipeline

1. Sur la page **Prise en main**, cliquez sur **Créer un pipeline**. Un pipeline est automatiquement créé pour vous. Le pipeline apparaît dans l’arborescence et son éditeur s’ouvre. 

   ![Page Prise en main](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. Sous l’onglet **Général** au bas de la fenêtre **Propriétés**, entrez **SQLServerToBlobPipeline** dans le champ **Nom**.

   ![Nom du pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
3. Dans la boîte à outils **Activités**, développez **DataFlow**. Faites glisser et déposez l’activité **Copier** sur l’aire de conception du pipeline. Définissez le nom de l’activité sur **CopySqlServerToAzureBlobActivity**.

   ![Nom de l’activité](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
4. Dans la fenêtre **Propriétés**, accédez à l’onglet **Source**, puis sélectionnez **+ Nouveau**.

   ![Onglet Source](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
5. Dans la fenêtre **Nouveau jeu de données**, recherchez **SQL Server**. Sélectionnez **SQL Server**, puis **Terminer**. Vous voyez un nouvel onglet intitulé **SqlServerTable1**. Vous voyez également le jeu de données **SqlServerTable1** dans l’arborescence sur la gauche. 

   ![Sélection de SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
6. Sous l’onglet **Général** au bas de la fenêtre **Propriétés**, entrez **SqlServerDataset** dans le champ **Nom**.
    
   ![Nom du jeu de données source](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
7. Accédez à l’onglet **Connexion**, puis sélectionnez **+ Nouveau**. Vous créez une connexion à un magasin de données source (base de données SQL Server) dans cette étape. 

   ![Connexion à la source du jeu de données](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
8. Dans la fenêtre **Nouveau service lié**, sélectionnez **Nouveau runtime d’intégration**. Dans cette section, vous allez créer un runtime d’intégration auto-hébergé et l’associer à un ordinateur local avec la base de données SQL Server. Le runtime d’intégration auto-hébergé est le composant qui copie les données de la base de données SQL Server sur votre machine dans le stockage Blob. 

   ![Nouveau runtime d’intégration](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
9. Dans la fenêtre **Integration Runtime Setup** (Configuration du runtime d’intégration), sélectionnez **Réseau privé**, puis sélectionnez **Suivant**. 

   ![Sélection du réseau privé](./media/tutorial-hybrid-copy-portal/select-private-network.png)
10. Saisissez le nom du runtime d’intégration et sélectionnez **Suivant**.
    
    ![Nom du runtime d’intégration](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
11. Sélectionnez **Click here to launch the express setup for this computer** (Cliquez ici pour lancer l’installation rapide pour cet ordinateur) sous **Option 1 : installation rapide**. 

    ![Lien d’installation rapide](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
12. Dans la fenêtre **Installation rapide d'Integration Runtime (auto-hébergé)**, sélectionnez **Fermer**. 

    ![Installation rapide d'Integration Runtime (auto-hébergé)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
13. Dans la fenêtre **Integration Runtime Setup** (Installation du runtime d’intégration) du navigateur web, sélectionnez **Terminer**. 

    ![Installation du runtime d’intégration](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
14. Dans la fenêtre **New Linked Service** (Nouveau service lié), procédez comme suit :

    a. Dans le champ **Nom**, entrez **SqlServerLinkedService**.

    b. Vérifiez que le runtime d’intégration auto-hébergé que vous avez créé précédemment s’affiche sous **Connect via integration runtime** (Se connecter avec le runtime d’intégration).

    c. Entrez le nom de votre instance SQL Server dans le champ **Nom du serveur**. 

    d. Spécifiez le nom de la base de données avec la table **emp** dans le champ **Nom de la base de données**.

    e. Sous **Type d’authentification**, sélectionnez le type d’authentification approprié que Data Factory doit utiliser pour se connecter à votre base de données SQL Server.

    f. Dans les champs **Nom d’utilisateur** et **Mot de passe**, entrez le nom d’utilisateur et le mot de passe. Si vous avez besoin d’utiliser une barre oblique (\\) dans votre nom de serveur ou de compte d’utilisateur, faites-la précéder d’un caractère d’échappement (\\). Par exemple, utilisez *mydomain\\\\myuser*.

    g. Sélectionnez **Tester la connexion**. Effectuez cette étape pour confirmer que Data Factory peut se connecter à votre base de données SQL Server à l’aide du runtime d’intégration auto-hébergé que vous avez créé.

    h. Sélectionnez **Enregistrer** pour enregistrer le service lié.

       ![Paramètres du nouveau service lié](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
15. Vous devez être revenu à la fenêtre avec le jeu de données source ouvert. Sous l’onglet **Connexion** de la fenêtre **Propriétés**, procédez comme suit : 

    a. Vérifiez que vous voyez **SqlServerLinkedService** dans le champ **Service lié**.

    b. Sélectionnez **[dbo].[emp]** dans le champ **Table**.

    ![Jeu de données source - Informations de connexion](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
16. Accédez à l’onglet avec **SQLServerToBlobPipeline** ou sélectionnez **SQLServerToBlobPipeline** dans l’arborescence. 

    ![Onglet Pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
17. Accédez à l’onglet **Récepteur** au bas de la fenêtre **Propriétés**, puis sélectionnez **+ Nouveau**. 

    ![Onglet Récepteur](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
18. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**. Sélectionnez ensuite **Terminer**. Vous voyez un nouvel onglet ouvert pour le jeu de données. Vous voyez également le jeu de données dans l’arborescence. 

    ![Sélection du stockage Blob](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
19. Saisissez **AzureBlobDataset** dans le champ **Nom**.

    ![Nom du jeu de données récepteur](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
20. Accédez à l’onglet **Connexion** au bas de la fenêtre **Propriétés**. À côté de **Service lié**, sélectionnez **+ Nouveau**. 

    ![Bouton de nouveau service lié](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
21. Dans la fenêtre **New Linked Service** (Nouveau service lié), procédez comme suit :

    a. Dans le champ **Nom**, entrez **AzureStorageLinkedService**.

    b. Sous **Nom du compte de stockage**, sélectionnez votre compte de stockage.

    c. Pour tester la connexion à votre compte de stockage, sélectionnez **Tester la connexion**.

    d. Sélectionnez **Enregistrer**.

    ![Paramètres du service lié Stockage](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
22.  Vous devez être revenu à la fenêtre avec le jeu de données récepteur ouvert. Sous l’onglet **Connexion**, procédez comme suit : 

        a. Vérifiez que **AzureStorageLinkedService** est sélectionné dans le champ **Service lié**.

        b. Pour la partie **dossier** du **chemin d’accès du fichier**, saisissez **adftutorial/fromonprem**. Si le dossier de sortie n’existe pas dans le conteneur adftutorial, Data Factory crée automatiquement le dossier de sortie.

        c. Pour la partie **nom du fichier** du **chemin d’accès du fichier**, saisissez `@CONCAT(pipeline().RunId, '.txt')`.

     ![Connexion au jeu de données récepteur](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
23. Accédez à l’onglet avec le pipeline ouvert ou sélectionnez le pipeline dans l’arborescence. Vérifiez que **AzureBlobDataset** est sélectionné dans le champ **Jeu de données récepteur**. 

    ![Jeu de données récepteur sélectionné](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
24. Pour valider les paramètres du pipeline, cliquez sur **Valider** dans la barre d’outils du pipeline. Pour fermer le **rapport de validation de pipeline**, sélectionnez **Fermer**. 

    ![Valider le pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
25. Pour publier les entités que vous avez créées dans Data Factory, sélectionnez **Publish All** (Tout publier).

    ![Bouton Publier](./media/tutorial-hybrid-copy-portal/publish-button.png)
26. Patientez jusqu’à ce que la fenêtre contextuelle **Réussite de la publication** s’affiche. Pour vérifier l’état de la publication, sélectionnez le lien **Afficher les notifications** situé sur la gauche. Pour fermer la fenêtre de notification, sélectionnez **Fermer**. 

    ![Publication réussie](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Déclencher une exécution du pipeline
Sélectionnez **Déclencher** dans la barre d’outils du pipeline, puis **Déclencher maintenant**.

![Déclencher l’exécution du pipeline](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1. Accédez à l’onglet **Surveiller**. Vous voyez le pipeline que vous avez déclenché manuellement à l’étape précédente. 

    ![Surveiller des exécutions de pipelines](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Vous ne voyez qu’une seule exécution d’activité, car le pipeline ne contient qu’une seule activité. Pour voir plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Pour revenir à l’affichage des **exécutions du pipeline**, sélectionnez **Pipelines** au sommet de la page.

    ![Surveiller des exécutions d’activités](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Vérifier la sortie
Le pipeline crée automatiquement le dossier de sortie nommé *fromonprem* dans le conteneur d’objets blob `adftutorial`. Vérifiez que le fichier *dbo.emp.txt* se trouve dans le dossier de sortie. 

1. Dans le portail Azure, dans la fenêtre du conteneur **adftutorial**, cliquez sur **Actualiser** pour afficher le dossier de sortie.

    ![Dossier de sortie créé](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Sélectionnez `fromonprem` dans la liste des dossiers. 
3. Vérifiez que le fichier nommé `dbo.emp.txt` s’affiche.

    ![Fichier de sortie](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créez des services liés SQL Server et de stockage. 
> * Créez des jeux de données SQL Server et de stockage Blob.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

Pour obtenir la liste des magasins de données pris en charge par Data Factory, consultez l’article sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Pour découvrir comment copier des données en bloc d’une source vers une destination, passez au didacticiel suivant :

> [!div class="nextstepaction"]
>[Copier des données en bloc](tutorial-bulk-copy-portal.md)
