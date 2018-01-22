---
title: "Copier des données depuis SQL Server vers le stockage Blob à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment copier les données d’un magasin de données local dans le cloud Azure en utilisant le runtime d’intégration auto-hébergé d’Azure Data Factory."
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
ms.openlocfilehash: 7b734a76545dbcbddac3c7ad7beae60d662a9129
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Didacticiel : Copier des données depuis une base de données SQL Server locale vers un compte de stockage d’objets blob Azure
Dans ce didacticiel, vous allez utiliser l’interface utilisateur de Azure Data Factory PowerShell pour créer un pipeline Data Factory qui copie les données d’une base de données SQL Server locale dans un stockage Blob Azure. Vous allez créer et utiliser un runtime d’intégration auto-hébergé, qui déplace les données entre les banques de données locales et cloud. 

> [!NOTE]
> Cet article s’applique à la version 2 d’Azure Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Cet article ne fournit pas de présentation détaillée du service Data Factory. Pour plus d’informations, consultez [Présentation d’Azure Data Factory](introduction.md). 

Dans ce didacticiel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créer des services liés SQL Server et au Stockage Azure. 
> * Créer des jeux de données SQL Server et Blob Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

## <a name="prerequisites"></a>configuration requise
### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="azure-roles"></a>Rôles Azure
Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être un membre des rôles *contributeur* ou *propriétaire*, ou un *administrateur* de l’abonnement Azure. 

Dans le portail Azure, cliquez sur votre nom d’utilisateur dans le coin supérieur droit, puis sélectionnez **Autorisations** pour afficher les autorisations dont vous disposez dans l’abonnement. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié. Pour des exemples d’instructions concernant l’ajout d’un utilisateur à un rôle, consultez l’article [Ajout de rôles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 et 2017
Dans le cadre de ce didacticiel, vous utilisez une base de données SQL Server locale comme magasin de données *source*. Le pipeline de la fabrique de données que vous créez dans ce didacticiel copie les données de cette base de données SQL Server locale (source) dans un stockage Blob Azure (récepteur). Créez ensuite une table nommée **emp** dans votre base de données SQL Server, puis insérez quelques exemples d’entrées dans la table. 

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

### <a name="azure-storage-account"></a>Compte de Stockage Azure
Dans ce didacticiel, vous utilisez un compte Stockage Azure à usage général (stockage d’objets Blob Azure spécifiquement) comme banque de données réceptrice/de destination. Si vous ne possédez pas de compte Stockage Azure à usage général, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account). Le pipeline de la fabrique de données que vous créez dans ce didacticiel copie les données de la base de données SQL Server locale (source) dans ce stockage Blob Azure (récepteur). 

#### <a name="get-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce didacticiel, vous spécifiez le nom et la clé de votre compte Stockage Azure. Procédez comme suit pour obtenir le nom et la clé de votre compte de stockage : 

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec vos nom d’utilisateur et mot de passe Azure. 

2. Dans le volet gauche, sélectionnez **Plus de services**, filtrez à l’aide du mot-clé **Stockage**, puis sélectionnez **Comptes de stockage**.

    ![Rechercher le compte de stockage](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Dans la liste des comptes de stockage, appliquez un filtre pour votre compte de stockage (si nécessaire), puis sélectionnez votre compte de stockage. 

4. Dans la fenêtre **Compte de stockage**, sélectionnez **Clés d’accès**.

    ![Obtenir le nom et la clé du compte de stockage](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Dans les zones **Nom du compte de stockage** et **key1**, copiez les valeurs, puis collez-les dans le bloc-notes ou un autre éditeur pour une utilisation ultérieure dans le didacticiel. 

#### <a name="create-the-adftutorial-container"></a>Créer le conteneur adftutorial 
Dans cette section, vous allez créer un conteneur d’objets blob nommé **adftutorial** dans votre stockage Blob Azure. 

1. Dans la fenêtre **Compte de stockage**, basculez vers **Vue d’ensemble**, puis sélectionnez **Objets blob**. 

    ![Sélection de l’option Objets blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Dans la fenêtre **Service Blob**, sélectionnez **Conteneur**. 

    ![Bouton d’ajout de conteneur](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Dans la fenêtre **Nouveau conteneur**, dans la zone **Nom** , entrez **adftutorial**, puis sélectionnez **OK**. 

    ![Saisie du nom du conteneur](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Cliquez sur **adftutorial** dans la liste des conteneurs.  

    ![Sélection du conteneur](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Gardez la fenêtre **conteneur** de **adftutorial** ouverte. Elle vous permet de vérifier la sortie à la fin du didacticiel. Data Factory crée automatiquement le dossier de sortie de ce conteneur, de sorte que vous n’avez pas besoin d’en créer.

    ![Fenêtre de conteneur](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Créer une fabrique de données
Dans cette étape, vous créez une fabrique de données et lancez l’interface utilisateur de Azure Data Factory pour créer un pipeline dans la fabrique de données. 

1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page de nouvelle fabrique de données](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
     ![Page de nouvelle fabrique de données](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
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

    ![mosaïque déploiement de fabrique de données](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
   ![Page d'accueil Data Factory](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. Cliquez sur le titre **Créer et surveiller** pour lancer l’interface utilisateur de Azure Data Factory dans un onglet séparé. 


## <a name="create-a-pipeline"></a>Créer un pipeline

1. Sur la page **Prise en main**, cliquez sur **Créer un pipeline**. Un pipeline est automatiquement créé pour vous. Le pipeline apparaît dans l’arborescence et son éditeur s’ouvre. 

   ![Page de prise en main](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. Dans l’onglet **Général** de la fenêtre **Propriétés** en bas, puis entrez **SQLServerToBlobPipeline** comme **nom**.

   ![Nom du pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. Dans la boîte à outils **Activités**, développez **Flux de données**et glissez-déposez l’activité de **copie** vers la surface du concepteur de pipeline. Définissez le nom de l’activité sur **CopySqlServerToAzureBlobActivity**.

   ![Nom de l’activité](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. Dans la fenêtre Propriétés, basculez vers l’onglet **Source**, puis cliquez sur **+ Nouveau**.

   ![Nouveau jeu de données source - bouton](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. Dans la fenêtre **Nouveau jeu de données**, recherchez **SQL Server**, sélectionnez **SQL Server**, puis cliquez sur **Terminer**. Vous voyez un nouvel onglet intitulé **SqlServerTable1**. Vous voyez également le jeu de données **SqlServerTable1** dans l’arborescence sur la gauche. 

   ![Sélectionner SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. Dans l’onglet **Général** de la fenêtre Propriétés, entrez **SqlServerDataset** comme **Nom**.
    
   ![Jeu de données source - nom](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. Basculez vers l’onglet **Connexions**, puis cliquez sur **+ Nouveau**. Vous créez une connexion à un magasin de données source (base de données SQL Server) dans cette étape. 

   ![Jeu de données source - bouton Nouvelle connexion](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. Dans la fenêtre **Nouveau service lié**, cliquez sur **Nouveau runtime d’intégration**. Dans cette section, vous allez créer un runtime d’intégration auto-hébergé et l’associer à un ordinateur local avec la base de données SQL Server. Le runtime d’intégration auto-hébergé est le composant qui copie les données de la base de données SQL Server sur votre machine dans le stockage Blob Azure. 

   ![Nouveau runtime d’intégration](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. Dans la fenêtre **Configuration du runtime d’intégration**, sélectionnez **Réseau privé**, puis cliquez sur **Suivant**. 

   ![Sélectionner un réseau privé](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. Saisissez le nom du runtime d’intégration et cliquez sur **Suivant**.  
    
   ![Runtime d’intégration - nom](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. Cliquez sur **Cliquez ici pour lancer l’installation rapide pour cet ordinateur** dans la section **Option 1 : installation rapide**. 

   ![Cliquer sur le lien d’installation rapide](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. Dans la fenêtre **Installation rapide du runtime d’intégration (auto-hébergé)**, cliquez sur **Fermer**. 

   ![Installation du runtime d’intégration - réussie](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. Dans la fenêtre **Installation du runtime d’intégration**, cliquez sur **Terminer**. Vous devez alors être revenu à la fenêtre **Nouveau Service lié**.

   ![Installation du runtime d’intégration - terminée](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. Dans la fenêtre **Nouveau service lié**, procédez comme suit :

    1. Entrez **SqlServerLinkedService** comme **nom**.
    2. Vérifiez que le runtime d’intégration auto-hébergé que vous avez créé précédemment s’affiche pour **Se connecter avec le runtime d’intégration**.
    3. Spécifiez le nom de votre SQL Server pour le **Nom du serveur**. 
    4. Spécifiez le nom de la base de données avec la table **emp** pour le champ **Nom de la base de données**. 
    5. Sélectionnez le **type d’authentification** approprié que Data Factory doit utiliser pour se connecter à votre base de données SQL Server. 
    6. Saisissez le **nom d’utilisateur** et le **mot de passe**. Si vous avez besoin d’utiliser une barre oblique (\\) dans votre nom de serveur ou de compte d’utilisateur, faites-la précéder d’un caractère d’échappement (\\). Par exemple, utilisez *mydomain\\\\myuser*. 
    7. Cliquez sur **Tester la connexion**. Effectuez cette étape pour confirmer que le service Data Factory peut se connecter à votre base de données SQL Server à l’aide du runtime d’intégration auto-hébergé que vous avez créé. 
    8. Pour enregistrer le service lié, cliquez sur **Enregistrer**.

       ![Service lié SQL Server - paramètres](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. Vous devez être revenu à la fenêtre avec le jeu de données source ouvert. Dans la **connexion** de la fenêtre **Propriétés**, procédez comme suit : 

    1. Vérifiez que vous voyez **SqlServerLinkedService** comme **Service lié**. 
    2. Sélectionnez **[dbo].[emp]** comme **Table**.

        ![Jeu de données source - Informations de connexion](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. Basculez vers l’onglet avec le SQLServerToBlobPipeline (ou) cliquez sur **SQLServerToBlobPipeline** dans l’arborescence. 

    ![Onglet Pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. Basculez vers l’onglet **Récepteur** dans la fenêtre **Propriétés**, puis cliquez sur **+ Nouveau**. 

    ![Jeu de données récepteur - Bouton Nouveau](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer**. Vous voyez un nouvel onglet ouvert pour le jeu de données. Vous voyez également le jeu de données dans l’arborescence. 

    ![Sélectionner Stockage Blob Azure](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. Saisissez **AzureBlobDataset** comme **Nom**.

    ![Jeu de données récepteur - nom](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. Basculez vers l’onglet **Connexion** dans la fenêtre **Propriétés**, puis cliquez sur **+ Nouveau** pour **Service lié**. 

    ![Nouveau service lié - bouton](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. Dans la fenêtre **Nouveau service lié**, procédez comme suit :

    1. Entrez **AzureStorageLinkedService** comme **Nom**.
    2. Sélectionnez votre compte de stockage Azure pour le **Nom du compte de stockage**. 
    3. Tester la connexion à votre compte de stockage Azure en cliquant sur **Tester la connexion**.
    4. Cliquez sur **Enregistrer**.

        ![Service lié Stockage Azure - paramètres](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  Vous devez être revenu à la fenêtre avec le jeu de données récepteur ouvert. Dans l’onglet **Connexions**, procédez comme suit : 

        1. Vérifiez que **AzureStorageLinkedService** est sélectionné comme **service lié**.
        2. Saisissez **adftutorial/fromonprem** pour la partie **dossier** du **chemin d’accès du fichier**. Si le dossier de sortie n’existe pas dans le container adftutorial, le service Data Factory crée automatiquement le dossier de sortie.
        3. Saisissez `@CONCAT(pipeline().RunId, '.txt')` pour la partie **Nom du fichier** du **chemin d’accès du fichier**.

            ![Jeu de données récepteur - connexion](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. Basculez vers l’onglet avec le pipeline ouvert (ou) cliquez sur le **pipeline** dans l’**arborescence**. Vérifiez que **AzureBlobDataset** est sélectionné comme **jeu de données récepteur**. 

    ![Jeu de données récepteur sélectionné ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. Pour valider les paramètres du pipeline, cliquez sur Valider sur la barre d’outils pour le pipeline. Fermez le **rapport de validation de canal** en cliquant sur le **X** dans le coin droit. 

    ![Valider le pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. Publiez les entités que vous avez créé pour le service Azure Data Factory en cliquant sur **Publier**.

    ![Bouton Publier](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. Patientez jusqu’à voir la fenêtre contextuelle **Publication réussie**. Vous pouvez également vérifier l’état de la publication en cliquant sur le lien **Afficher les notifications** situé sur la gauche. Fermez la fenêtre de notification en cliquant sur le **X**. 

    ![Publication réussie](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Déclencher une exécution du pipeline
Cliquez sur **Déclencher** dans la barre d’outils pour le pipeline, cliquez sur **Déclencher maintenant**.

![Déclencher maintenant](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1. Basculez vers l’onglet **Surveiller**. Vous voyez le pipeline que vous avez déclenché manuellement à l’étape précédente. 

    ![Exécutions de pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Pour afficher des exécutions d’activité associées à l’exécution de pipelines, cliquez sur le lien **Voir les exécutions d’activités** dans la colonne **Actions**. Vous ne voyez qu’une seule exécution d’activité étant donné que le pipeline ne contient qu’une seule activité. Pour afficher les détails de l’opération de copie, cliquez sur le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Vous pouvez revenir à la vue des exécutions du pipeline en cliquant sur **Pipelines** en haut.

    ![Exécutions d’activités](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Vérifier la sortie
Le pipeline crée automatiquement le dossier de sortie nommé *fromonprem* dans le conteneur d’objets blob `adftutorial`. Vérifiez que le fichier *dbo.emp.txt* se trouve dans le dossier de sortie. 

1. Dans le portail Azure, dans la fenêtre du conteneur **adftutorial**, cliquez sur **Actualiser** pour afficher le dossier de sortie.

    ![Dossier de sortie créé](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Sélectionnez `fromonprem` dans la liste des dossiers. 
3. Vérifiez que le fichier nommé `dbo.emp.txt` s’affiche.

    ![Fichier de sortie](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créez un runtime d’intégration auto-hébergé.
> * Créer des services liés SQL Server et au Stockage Azure. 
> * Créer des jeux de données SQL Server et Blob Azure.
> * Créer un pipeline avec une activité de copie pour déplacer les données.
> * Démarrer une exécution de pipeline.
> * Surveiller l’exécution du pipeline.

Pour obtenir la liste des magasins de données pris en charge par Data Factory, consultez l’article [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Passez au didacticiel suivant pour découvrir comment copier des données en bloc d’une source vers une destination :

> [!div class="nextstepaction"]
>[Copier des données en bloc](tutorial-bulk-copy-portal.md)
