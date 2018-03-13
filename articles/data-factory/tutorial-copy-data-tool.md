---
title: "Copier des données à l’aide de l’outil Copier des données d’Azure | Microsoft Docs"
description: "Créez une fabrique de données Azure, puis utilisez l’outil Copier les données pour copier des données depuis Stockage Blob Azure vers une base de données SQL."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 5b636128d0df5a404df7aa6b2cfdce016e36681f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copier des données à partir du Stockage Blob Azure vers une base de données SQL en utilisant l’outil Copier les données
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Version 1 – Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](tutorial-copy-data-tool.md)

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Vous utilisez ensuite l’outil Copier les données pour créer un pipeline qui copie des données depuis Stockage Blob Azure vers une base de données SQL. 

> [!NOTE]
> Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).
>
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 de Data Factory, qui est généralement disponible, consultez la [Mise en route de Data Factory, version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


Dans ce didacticiel, vous effectuerez les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

## <a name="prerequisites"></a>configuration requise

* **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte de stockage Azure** : utilisez le stockage d’objets blob en tant que magasin de données _sources_. Si vous ne possédez pas de compte de stockage Azure, consultez les instructions dans [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Azure SQL Database** : utilisez une base de données SQL comme magasin de données _sink_. Si vous n’avez pas de base de données SQL, consultez les instructions dans [Créer une base de données SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Créer un objet blob et une table SQL

Préparez votre stockage d'objets blob et votre base de données SQL pour ce didacticiel effectuant les étapes suivantes.

#### <a name="create-a-source-blob"></a>Créer un objet blob source

1. Lancez le **Bloc-notes**. Copiez le texte suivant et enregistrez-le dans un fichier nommé **inputEmp.txt** sur votre disque :

    ```
    John|Doe
    Jane|Doe
    ```

2. Créez un conteneur nommé **adfv2tutorial** et chargez le fichier inputEmp.txt dedans. Vous pouvez utiliser différents outils pour effectuer ces tâches, comme l’[Explorateur Stockage Azure](http://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Créer une table SQL de récepteur

1. Utilisez le script SQL suivant pour créer une table nommée **dbo.emp** dans votre base de données SQL :

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

2. Autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur exécutant SQL Server. Ce paramètre permet à Data Factory d’écrire des données dans votre instance de SQL server. Pour vérifier et activer ce paramètre, procédez comme suit :

    a. Sur la gauche, sélectionnez **Plus de services**, puis choisissez **Serveurs SQL**.

    b. Sélectionnez votre serveur, puis **PARAMÈTRES** > **Pare-feu**.

    c. Sur la page **Paramètres du pare-feu**, configurez l’option **Autoriser l’accès aux services Azure** sur **ACTIVER**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu sur la gauche, sélectionnez **+ Nouveau** > **Données + Analytique** > **Data Factory** : 
   
   ![Création de la nouvelle fabrique de données](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** sous **Nom**. 
      
     ![Nouvelle fabrique de données](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Le nom de votre fabrique de données doit être un _nom global unique_. Vous pouvez recevoir le message d’erreur suivant :
   
   ![Message d’erreur de nouvelle fabrique de données](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Si vous recevez un message d’erreur concernant la valeur du nom, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_**ADFTutorialDataFactory**. Pour savoir comment nommer les artefacts Data Factory, consultez la rubrique [Data Factory - Règles d'affectation des noms](naming-rules.md).
3. Sélectionnez l’**abonnement** Azure dans lequel vous créez la nouvelle fabrique de données. 
4. Pour **Groupe de ressources**, réalisez l’une des opérations suivantes :
     
    a. Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.

    b. Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 
         
    Pour plus d'informations sur les groupes de ressources, consultez [Utiliser des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).

5. Sous **Version**, sélectionnez **V2 (préversion)** pour la version.
6. Sous **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (tels que le Stockage Azure et SQL Database) et les services de calcul (comme Azure HDInsight) utilisés par votre fabrique de données peuvent se trouver dans d’autres emplacements et régions.
7. Sélectionnez **Épingler au tableau de bord**. 
8. Sélectionnez **Créer**.
9. Sur le tableau de bord, la vignette **Déploiement de Data Factory** affiche l’état du processus.

    ![Vignette Déploiement de Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Une fois la création terminée, la page d’accueil **Data Factory** s’affiche.
   
    ![Page d'accueil Data Factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Pour lancer l’interface utilisateur d’Azure Data Factory dans un onglet séparé, cliquez sur la vignette **Créer et surveiller**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Sur la page **Prise en main**, sélectionnez la vignette **Copier des données** pour démarrer l’outil Copier des données. 

   ![Vignette de l’outil Copier les données](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. Sur la page **Propriétés**, sous **Nom de la tâche**, saisissez **CopyFromBlobToSqlPipeline**. Sélectionnez ensuite **Suivant**. L’interface utilisateur de Data Factory crée un pipeline avec le nom spécifié. 

    ![Page Propriétés](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. Sur la page **Magasin de données sources**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Suivant**. Les données sources sont dans un stockage d’objets Blob. 

    ![Page Magasin de données sources](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. Sur la page **Spécifier le compte de stockage Blob Azure**, procédez comme suit :

    a. Sous **Nom de la connexion**, entrez **AzureStorageLinkedService**.

    b. Sélectionnez votre nom de compte de stockage dans la liste déroulante **Nom du compte de stockage**.

    c. Sélectionnez **Suivant**. 

    ![Spécification du compte de stockage](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    Un service lié rattache un magasin de données ou une ressource de calcul à une fabrique de données. Dans ce cas, vous créez un service lié de stockage pour lier votre compte de stockage à la fabrique de données. Le service lié comporte les informations de connexion utilisées par Data Factory pour établir la connexion au stockage Blob lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) qui contient les données sources. 

5. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :
    
    a. Naviguez jusqu’au dossier **adfv2tutorial/input**.

    b. Sélectionnez le fichier **inputEmp.txt**.

    c. Sélectionnez **Choisir**. Vous pouvez également double-cliquer sur le fichier **inputEmp.txt**.

    d. Sélectionnez **Suivant**. 

    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. Sur la page **Paramètres de format de fichier**, notez que l’outil détecte automatiquement les séparateurs de ligne et de colonne. Sélectionnez **Suivant**. Vous pouvez également afficher un aperçu des données et un schéma des données d’entrée sur cette page. 

    ![Paramètres de format de fichier](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. Dans la page de la **banque de données de destination**, cliquez sur la vignette **Azure SQL Database**, puis sélectionnez **Suivant**.

    ![Banque de données de destination](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. Sur la page **Spécifier la base de données SQL Azure**, procédez comme suit : 

    a. Sous **Nom de la connexion**, entrez **AzureSqlDatabaseLinkedService**.

    b. Sous **Nom du serveur**, sélectionnez votre instance SQL Server.

    c. Sous **Nom de la base de données**, sélectionnez votre base de données SQL.

    d. Sous **Nom d’utilisateur**, entrez le nom de l’utilisateur.

    e. Sous **Mot de passe**, entrez le mot de passe de l’utilisateur.

    f. Sélectionnez **Suivant**. 

    ![Spécifier la base de données SQL](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    Un jeu de données doit être associé à un service lié. Le service lié comporte la chaîne de connexion utilisée par Data Factory pour établir la connexion à la base de données SQL lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) dans lequel les données sont copiées.

9. Sur la page **Mappage de table**, sélectionnez la table **[dbo].[emp]**, puis sélectionnez **Suivant**. 

    ![Mappage de table](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. Sur la page **Mappage de schéma**, notez que les deux premières colonnes dans le fichier d’entrée sont mappées aux colonnes **FirstName** et **LastName** de la table **emp**.

    ![Page Mappage de schéma](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. Sur la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page Paramètres](./media/tutorial-copy-data-tool/settings-page.png)
12. Sur la page **Résumé**, vérifiez les paramètres, puis sélectionnez **Suivant**.

    ![Page de résumé](./media/tutorial-copy-data-tool/summary-page.png)
13. Sur la page **Déploiement**, sélectionnez **Surveiller** pour surveiller le pipeline (tâche).

    ![Page Déploiement](./media/tutorial-copy-data-tool/deployment-page.png)
14. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution d’activité et de réexécuter le pipeline. Sélectionnez **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions de pipelines](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Il n’y a qu’une seule activité (activité de copie) dans le pipeline, vous ne voyez donc qu’une seule entrée. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Pour revenir à l’affichage des **exécutions du pipeline**, sélectionnez le lien **Pipelines** en haut. Sélectionnez **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions d’activités](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Sélectionnez l’onglet **Modifier** sur la gauche pour basculer en mode éditeur. Vous pouvez mettre à jour les services, jeux de données et pipelines liés créés par l’outil à l’aide de l’éditeur. Sélectionnez **Code** pour afficher le code JSON de l’entité actuellement ouverte dans l’éditeur. Pour plus de détails sur la modification de ces entités dans l’interface utilisateur de Data Factory, consultez [la version du portail Azure de ce didacticiel](tutorial-copy-data-portal.md).

    ![Onglet Modifier](./media/tutorial-copy-data-tool/edit-tab.png)
17. Vérifiez que les données sont insérées dans la table **emp** dans votre base de données SQL.

    ![Vérifier la sortie SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>Étapes suivantes
Le pipeline dans cet exemple copie des données d’un stockage d’objets blob vers une base de données SQL. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud : 

> [!div class="nextstepaction"]
>[Copier des données locales vers le cloud](tutorial-hybrid-copy-data-tool.md)
