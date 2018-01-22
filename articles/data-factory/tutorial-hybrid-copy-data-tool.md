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
ms.openlocfilehash: aba8b13d47b2b9236a0d5cc868e53780e894c406
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-copy-data-tool"></a>Copier des données depuis une base de données SQL Server locale vers un stockage d’objets blob Azure à l’aide de l’outil Copier les données
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](tutorial-hybrid-copy-data-tool.md)

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Vous utilisez ensuite l’outil Copier les données pour créer un pipeline qui copie des données depuis une base de données SQL Server locale vers un stockage d’objets Blob Azure.

> [!NOTE]
> - Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).
>
> - Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline
> * Surveiller les exécutions de pipeline et d’activité.

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

5. Exécutez le script de requête suivant sur la base de données pour créer la table **emp** et y insérer quelques données d’exemple : dans l’arborescence, cliquez sur la base de données que vous avez créée, puis sélectionnez **Nouvelle requête**.

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

### <a name="azure-storage-account"></a>Compte de Stockage Azure
Dans ce didacticiel, vous utilisez un compte Stockage Azure à usage général (stockage d’objets Blob Azure spécifiquement) comme banque de données réceptrice/de destination. Si vous ne possédez pas de compte de stockage Azure à usage général, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour savoir comment en créer un. Le pipeline de la fabrique de données que vous créez dans ce didacticiel copie les données de la base de données SQL Server locale (source) dans ce stockage Blob Azure (récepteur). 

#### <a name="get-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce didacticiel, vous spécifiez le nom et la clé de votre compte Stockage Azure. Obtenez le nom et la clé de votre compte de stockage en procédant comme suit : 

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

1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page Nouvelle fabrique de données](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
     ![Page Nouvelle fabrique de données](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
      Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V2 (préversion)** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements/régions.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
   ![Page d'accueil Data Factory](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
10. Cliquez sur le titre **Créer et surveiller** pour lancer l’interface utilisateur (IU) d’Azure Data Factory dans un onglet séparé. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Dans la page de prise en main, cliquez sur la vignette **Copier les données** pour lancer l’outil Copier les données. 

   ![Vignette de l’outil Copier les données](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. Dans la page **propriétés** de l’outil Copier les données, spécifiez **CopyFromOnPremSqlToAzureBlobPipeline** pour le **nom de la tâche**, puis cliquez sur **Suivant**. L’outil Copier les données crée un pipeline avec le nom que vous spécifiez dans ce champ. 
    
   ![Page Propriétés](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. Dans la page **Banque de données sources**, sélectionnez **SQL Server**, puis cliquez sur **Suivant**. Il peut être nécessaire de faire défiler l’écran vers le bas pour visualiser **SQL Server** dans la liste. 

   ![Page Banque de données sources](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. Entrez **SqlServerLinkedService** pour le **Nom de la connexion**, puis cliquez sur le lien **Créer un runtime d’intégration**. Vous devez créer un runtime d’intégration auto-hébergé, le télécharger sur votre machine et l’inscrire auprès du service Data Factory. Le runtime d’intégration auto-hébergé copie des données entre votre environnement local et le cloud Azure.

   ![Lien Créer un runtime d’intégration](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. Dans la boîte de dialogue **Créer un runtime d’intégration**, entrez **TutorialIntegration Runtime** pour le champ **Nom**, puis cliquez sur **Créer**. 

   ![Boîte de dialogue Créer un runtime d’intégration](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Cliquez sur **Lancer le programme d’installation express**. Cette action installe le runtime d’intégration sur votre machine et l’inscrit auprès du service Data Factory. Vous pouvez également utiliser l’option d’installation manuelle pour télécharger le fichier d’installation, l’exécuter et utiliser la clé pour inscrire le runtime d’intégration. 

    ![Lien Créer un runtime d’intégration](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Exécutez l’application téléchargée. Vous voyez l’**état** de l’installation express dans la fenêtre. 

    ![État de l’installation express](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Vérifiez que **TutorialIntegrationRuntime** est sélectionné pour le champ **Runtime d’intégration**.

    ![Runtime d’intégration sélectionné](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. Dans **Specify the on-premises SQL Server database** (Spécifier la base de données SQL Server locale), procédez comme suit : 

    1. Entrez **OnPremSqlLinkedService** pour le **Nom de la connexion**.
    2. Entrez le nom de votre SQL Server local pour le **Nom du serveur**.
    3. Entrez le nom de votre base de données locale pour le **Nom de la base de données**. 
    4. Sélectionnez authentification appropriée pour le **Type d’authentification**.
    5. Entrez le nom d’utilisateur ayant accès au SQL Server local pour le **Nom d’utilisateur**.
    6. Entrez le **Mot de passe** de l’utilisateur. 
10. Dans la page **Sélectionner les tables à partir desquelles copier les données ou utiliser une requête personnalisée**, sélectionnez la table **[dbo].[emp]** dans la liste, puis cliquez sur **Suivant**. 

    ![Sélectionner la table emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. Dans la page **Banque de données de destination**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Suivant**.

    ![Sélectionner le stockage Blob Azure](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. Dans la page **Specify the Azure Blob storage account** (Spécifier le compte de stockage d’objets blob Azure), procédez comme suit : 

    1. Entrez **AzureStorageLinkedService** pour le **Nom de la connexion**.
    2. Sélectionnez votre compte de stockage Azure dans la liste déroulante pour le **Nom du compte de stockage**. 
    3. Cliquez sur **Suivant**.

        ![Sélectionner le stockage Blob Azure](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. Dans la page **Choose the output file or folder** (Choisir le fichier ou le dossier de sortie), procédez comme suit : 
    
    1. Entrez **adftutorial/fromonprem** pour le **Chemin d’accès du dossier**. Vous avez créé le conteneur **adftutorial** dans le cadre des conditions préalables. Si le dossier de sortie n’existe pas, le service Data Factory le crée automatiquement. Vous pouvez également utiliser le bouton **Parcourir** pour naviguer dans le stockage d’objets blob et ses conteneurs/dossiers. Notez que le nom du fichier de sortie est défini sur **dbo.emp** par défaut.
        
        ![Choisir le fichier ou le dossier de sortie](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. Dans la page **File format settings** (Paramètres de format de fichier), cliquez sur **Suivant**. 

    ![Page de paramètres de format de fichier](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. Dans la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page Paramètres](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Dans la page **Résumé**, passez en revue les valeurs de tous les paramètres, puis cliquez sur **Suivant**. 

    ![Page de résumé](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. Dans la page **Déploiement**, cliquez sur **Surveiller** pour surveiller le pipeline ou la tâche que vous avez créé.

    ![Page Déploiement](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. Dans l’onglet **Surveiller**, vous pouvez afficher l’état du pipeline que vous avez créé. Les liens dans la colonne **Action** vous permettent de visualiser les exécutions d’activités associées à l’exécution du pipeline et de réexécuter le pipeline. 

    ![Exécutions de pipeline](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Cliquez sur le lien **Afficher les exécutions d’activités** dans la colonne **Actions** pour voir les exécutions d’activités associées à l’exécution du pipeline. Pour afficher les détails de l’opération de copie, cliquez sur le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Vous pouvez revenir à la vue des exécutions du pipeline en cliquant sur **Pipelines** en haut.

    ![Exécutions d’activités](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Vérifiez que le fichier de sortie apparaît bien dans le dossier **fromonprem** du conteneur **adftutorial**.   
 
    ![Objet blob de sortie](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Cliquez sur l’onglet **Modifier** sur la gauche pour basculer en mode éditeur. Vous pouvez mettre à jour les services, jeux de données et pipelines liés créés par l’outil à l’aide de l’éditeur. Cliquez sur **Code** pour afficher le code JSON associé à l’entité ouverte dans l’éditeur. Pour plus de détails sur la modification de ces entités dans l’interface utilisateur de Data Factory, consultez [la version du portail Azure de ce didacticiel](tutorial-copy-data-portal.md).

    ![Onglet Modifier](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>étapes suivantes
Le pipeline dans cet exemple copie des données depuis une base de données SQL Server locale vers un stockage Blob Azure. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline
> * Surveiller les exécutions de pipeline et d’activité.

Pour obtenir la liste des magasins de données pris en charge par Data Factory, consultez l’article [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Passez au didacticiel suivant pour découvrir comment copier des données en bloc d’une source vers une destination :

> [!div class="nextstepaction"]
>[Copier des données en bloc](tutorial-bulk-copy-portal.md)