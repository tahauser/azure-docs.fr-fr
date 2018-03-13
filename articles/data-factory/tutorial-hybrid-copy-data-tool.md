---
title: "Copier des données locales à l’aide de l’outil Copier les données d’Azure | Microsoft Docs"
description: "Créez une fabrique de données Azure, puis utilisez l’outil Copier les données pour copier des données depuis une base de données SQL Server locale vers un stockage d’objets Blob Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: 77090d9a61945c9edc42cde7d647c75e91f54dd6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copier des données depuis une base de données SQL Server locale vers un stockage Blob Azure à l’aide de l’outil Copier les données
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](tutorial-hybrid-copy-data-tool.md)

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Vous utilisez ensuite l’outil Copier les données pour créer un pipeline qui copie des données depuis une base de données SQL Server locale vers un stockage Blob Azure.

> [!NOTE]
> - Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).
>
> - Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 de Data Factory, qui est généralement disponible, consultez la [Mise en route de Data Factory, version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Dans ce didacticiel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>configuration requise
### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="azure-roles"></a>Rôles Azure
Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être un membre des rôles *contributeur* ou *propriétaire*, ou un *administrateur* de l’abonnement Azure. 

Pour afficher les autorisations dont vous disposez dans l’abonnement, accédez au portail Azure. Dans l’angle supérieur droit, sélectionnez votre nom d’utilisateur, puis **Autorisations**. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié. Pour des exemples d’instructions concernant l’ajout d’un utilisateur à un rôle, consultez l’article [Ajout de rôles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 et 2017
Dans le cadre de ce didacticiel, vous utilisez une base de données SQL Server locale comme magasin de données *source*. Le pipeline de la fabrique de données que vous allez créer dans ce didacticiel copie les données de cette base de données SQL Server locale (source) dans un stockage Blob (récepteur). Créez ensuite un tableau nommé **emp** dans votre base de données SQL Server, puis insérez-y quelques exemples d’entrées. 

1. Exécutez SQL Server Management Studio. S’il n’est pas déjà installé sur votre machine, accédez à [Télécharger SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Connectez-vous à votre instance SQL Server à l’aide de vos informations d’identification. 

3. Créez un exemple de base de données. Dans l’arborescence, cliquez avec le bouton droit sur **Bases de données**, puis sur **Nouvelle base de données**. 
 
4. Dans la fenêtre **Nouvelle base de données**, entrez un nom pour la base de données, puis cliquez sur **OK**. 

5. Pour créer la table **emp** et y insérer quelques données d’exemple, exécutez le script de requête suivant sur la base de données. Dans l’arborescence, cliquez avec le bouton droit sur la base de données créée, puis sur **Nouvelle requête**.

    ```sql
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

### <a name="azure-storage-account"></a>Compte Azure Storage
Dans ce didacticiel, vous utilisez un compte de stockage Azure à usage général (stockage Blob plus spécifiquement) comme banque de données réceptrice/de destination. Si vous ne possédez pas de compte de stockage à usage général, consultez la section [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour savoir comment en créer un. Le pipeline de la fabrique de données que vous créez dans ce didacticiel copie les données de la base de données SQL Server locale (source) dans ce stockage Blob (récepteur). 

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

1. Dans la fenêtre **Compte de stockage**, basculez vers **Vue d’ensemble**, puis sélectionnez **Objets blob**. 

    ![Sélection de l’option Objets blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Dans la fenêtre **Service Blob**, sélectionnez **Conteneur**. 

    ![Bouton Conteneur](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Dans la fenêtre **Nouveau conteneur**, dans la zone **Nom** , entrez **adftutorial**, puis sélectionnez **OK**. 

    ![Nouveau conteneur](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Cliquez sur **adftutorial** dans la liste des conteneurs.

    ![Sélection de conteneurs](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Gardez la fenêtre **Conteneur** de **adftutorial** ouverte. Elle vous permet de vérifier la sortie à la fin du didacticiel. Data Factory crée automatiquement le dossier de sortie de ce conteneur, de sorte que vous n’avez pas besoin d’en créer.

    ![Fenêtre de conteneur](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu sur la gauche, sélectionnez **Nouveau** > **Données + Analytique** > **Data Factory**. 
   
   ![Création de la nouvelle fabrique de données](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** dans le champ **Nom**. 
      
     ![Nouvelle fabrique de données](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   Le nom de la fabrique de données doit être un *nom global unique*. Si le message d’erreur suivant s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Azure Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les règles Data Factory.
  
   ![Nouveau nom de fabrique de données](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
      Pour plus d'informations sur les groupes de ressources, consultez l’article qui traite de l’[utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).
5. Sous **Version**, sélectionnez **V2 (préversion)**.
6. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (tels que le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par Data Factory peuvent se trouver dans d’autres emplacements/régions.
7. Sélectionnez **Épingler au tableau de bord**. 
8. Sélectionnez **Créer**.
9. Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** :

    ![Vignette Déploiement de Data Factory](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
10. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
    ![Page d'accueil Data Factory](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
11. Sélectionnez **Créer et surveiller** pour lancer l’interface utilisateur de Data Factory dans un onglet séparé. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Sur la page **Prise en main**, sélectionnez **Copier des données** pour lancer l’outil Copier des données. 

   ![Vignette de l’outil Copier les données](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. Sur la page **Propriétés** de l’outil Copier les données, spécifiez **CopyFromOnPremSqlToAzureBlobPipeline** dans le champ **Nom de la tâche**. Sélectionnez ensuite **Suivant**. L’outil Copier les données crée un pipeline avec le nom que vous spécifiez dans ce champ. 
    
   ![Nom de la tâche](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. Sur la page **Banque de données sources**, sélectionnez **SQL Server**, puis sélectionnez **Suivant**. Vous devrez peut-être faire défiler l’écran vers le bas pour voir **SQL Server** dans la liste. 

   ![Sélection de SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. Dans le champ **Nom de la connexion**, entrez **SqlServerLinkedService**. Sélectionnez le lien **Créer un runtime d’intégration**. Vous devez créer un runtime d’intégration auto-hébergé, le télécharger sur votre machine et l’inscrire auprès de Data Factory. Le runtime d’intégration auto-hébergé copie des données entre votre environnement local et le cloud.

   ![Créer un runtime d’intégration auto-hébergé](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. Dans la boîte de dialogue **Créer un runtime d’intégration**, entrez **TutorialIntegration Runtime** dans le champ **Nom**. Sélectionnez ensuite **Créer**. 

   ![Nom du runtime d’intégration](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Sélectionnez **Launch express setup on this computer** (Lancer le programme d’installation rapide sur cet ordinateur). Cette action installe le runtime d’intégration sur votre machine et l’inscrit auprès de Data Factory. Vous pouvez également utiliser l’option d’installation manuelle pour télécharger le fichier d’installation, l’exécuter et utiliser la clé pour inscrire le runtime d’intégration. 

    ![Lien Lancer le programme d’installation rapide sur cet ordinateur](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Exécutez l’application téléchargée. Vous pouvez voir l’état de l’installation rapide dans la fenêtre. 

    ![État de l’installation express](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Vérifiez que **TutorialIntegrationRuntime** est sélectionné dans le champ **Runtime d’intégration**.

    ![Runtime d’intégration sélectionné](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. Dans **Specify the on-premises SQL Server database** (Définir la base de données SQL Server locale), procédez comme suit : 

    a. Entrez **OnPremSqlLinkedService** dans le champ **Nom de la connexion**.

    b. Entrez le nom de votre instance SQL Server locale dans le champ **Nom du serveur**.

    c. Entrez le nom de votre base de données locale dans le champ **Nom de la base de données**.

    d. Sélectionnez l’authentification appropriée sous **Type d’authentification**.

    e. Entrez le nom d’utilisateur ayant accès au SQL Server local dans le champ **Nom d’utilisateur**.

    f. Entrez le **mot de passe** correspondant à l’utilisateur. 
10. Sur la page **Sélectionner les tables à partir desquelles copier les données ou utiliser une requête personnalisée**, sélectionnez la table **[dbo].[emp]** dans la liste, puis sélectionnez **Suivant**. 

    ![Sélection de la table emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. Dans la page **Magasin de données de destination**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Suivant**.

    ![Sélection du stockage Blob](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. Sur la page **Spécifier le compte de stockage Blob Azure**, procédez comme suit : 

    a. Dans le champ **Nom de la connexion**, entrez **AzureStorageLinkedService**.

    b. Sélectionnez votre compte de stockage dans la liste déroulante sous **Nom du compte de stockage**. 

    c. Sélectionnez **Suivant**.

    ![Spécification du compte de stockage](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. Sur la page **Choisir le fichier ou le dossier de sortie**, dans le champ **Chemin d’accès du dossier**, entrez **adftutorial/fromonprem**. Vous avez créé le conteneur **adftutorial** dans le cadre des conditions préalables. Si le dossier de sortie n’existe pas, Data Factory le crée automatiquement. Vous pouvez également utiliser le bouton **Parcourir** pour parcourir le stockage d’objets blob et ses conteneurs/dossiers. Notez que le nom du fichier de sortie est défini sur **dbo.emp** par défaut.
        
    ![Choisir le fichier ou le dossier de sortie](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. Sur la page **Paramètres de format de fichier**, cliquez sur **Suivant**. 

    ![Page Paramètres de format de fichier](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. Sur la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page Paramètres](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Sur la page **Résumé**, vérifiez la valeur de tous les paramètres, puis sélectionnez **Suivant**. 

    ![Page de résumé](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (ou tâche) que vous avez créé.

    ![Page Déploiement](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. Sous l’onglet **Surveiller**, vous pouvez afficher l’état du pipeline que vous avez créé. Vous pouvez utiliser les liens dans la colonne **Action** pour visualiser les exécutions d’activités associées à l’exécution du pipeline et pour réexécuter le pipeline. 

    ![Surveiller des exécutions de pipelines](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions** pour voir les exécutions d’activités associées à l’exécution du pipeline. Pour voir plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Pour revenir à l’affichage des **exécutions du pipeline**, sélectionnez **Pipelines** au sommet de la page.

    ![Surveiller des exécutions d’activités](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Vérifiez que le fichier de sortie apparaît bien dans le dossier **fromonprem** du conteneur **adftutorial**. 
 
    ![Objet blob de sortie](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Sélectionnez l’onglet **Modifier** sur la gauche pour basculer en mode éditeur. Vous pouvez mettre à jour les services, jeux de données et pipelines liés créés par l’outil à l’aide de l’éditeur. Sélectionnez **Code** pour afficher le code JSON associé à l’entité ouverte dans l’éditeur. Pour plus de détails sur la modification de ces entités dans l’interface utilisateur de Data Factory, consultez [la version du portail Azure de ce didacticiel](tutorial-copy-data-portal.md).

    ![Onglet Modifier](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Étapes suivantes
Le pipeline dans cet exemple copie des données depuis une base de données SQL Server locale vers un stockage Blob. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

Pour obtenir la liste des magasins de données pris en charge par Data Factory, consultez l’article sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Pour découvrir comment copier des données en bloc d’une source vers une destination, passez au didacticiel suivant :

> [!div class="nextstepaction"]
>[Copier des données en bloc](tutorial-bulk-copy-portal.md)
