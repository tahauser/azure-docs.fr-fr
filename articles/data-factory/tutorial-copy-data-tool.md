---
title: "Copier des données à l’aide de l’outil Copier les données d’Azure | Microsoft Docs"
description: "Créez une fabrique de données Azure, puis utilisez l’outil Copier les données pour copier des données depuis Stockage Blob Azure vers Azure SQL Database."
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
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>Copier des données à partir d’un objet blob Azure vers Azure SQL Database à l’aide de l’outil Copier les données
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](tutorial-copy-data-tool.md)

Dans ce didacticiel, vous utilisez le portail Azure pour créer une fabrique de données. Vous utilisez ensuite l’outil Copier les données pour créer un pipeline qui copie des données depuis un Stockage Blob Azure dans une base de données SQL. 

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

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Stockage Azure**. Vous utilisez le stockage blob comme magasins de données **source**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un.
* **Base de données SQL Azure**. Vous utilisez la base de données en tant que magasin de données **récepteur**. Si vous n’avez pas de base de données Azure SQL Database, consultez l’article [Création d’une base de données Azure SQL](../sql-database/sql-database-get-started-portal.md) pour savoir comme en créer une.

### <a name="create-a-blob-and-a-sql-table"></a>Créer un objet blob et une table SQL

À présent, préparez votre objet blob Azure et Azure SQL Database pour ce didacticiel, en effectuant les étapes suivantes :

#### <a name="create-a-source-blob"></a>Créer un objet blob source

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **inputEmp.txt** sur votre disque.

    ```
    John|Doe
    Jane|Doe
    ```

2. Utilisez des outils comme l’[explorateur Stockage Azure](http://storageexplorer.com/) pour créer le conteneur **adfv2tutorial** et charger le fichier **inputEmp.txt** sur ce dernier.

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

2. Autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur SQL Azure pour que le service Data Factory puisse écrire des données sur votre serveur SQL Azure. Pour vérifier et activer ce paramètre, procédez comme suit :

    1. Cliquez sur le hub **Plus de services** situé à gauche, puis sur **Serveurs SQL**.
    2. Sélectionnez votre serveur, puis cliquez sur **Pare-feu** sous **PARAMÈTRES**.
    3. Dans la page **Paramètres de pare-feu**, cliquez sur **ACTIVER** pour **Autoriser l’accès aux services Azure**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page de nouvelle fabrique de données](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
     ![Page de nouvelle fabrique de données](./media/tutorial-copy-data-tool/name-not-available-error.png)
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

    ![mosaïque déploiement de fabrique de données](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
   ![Page d'accueil Data Factory](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. Cliquez sur le titre **Créer et surveiller** pour lancer l’interface utilisateur de Azure Data Factory dans un onglet séparé. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Utiliser l’outil Copier les données pour créer un pipeline

1. Sur la page de prise en main, cliquez sur la vignette **Copier des données** pour lancer l’outil Copier les données. 

   ![Vignette de l’outil Copier les données](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. Dans la page **Propriétés** de l’outil Copier les données, spécifiez **CopyFromBlobToSqlPipeline** pour le **nom de la tâche**, puis cliquez sur **Suivant**. L’interface utilisateur de Data Factory crée un pipeline avec le nom que vous spécifiez comme nom de la tâche. 

    ![Outil Copier les données - Page Propriétés](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. Dans la page **Magasin de données source**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Suivant**. Les données source sont dans un stockage Blob Azure. 

    ![Page Magasin de données sources](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. Dans la page **Spécifier le compte de stockage Blob Azure**, procédez comme suit : 
    1. Saisissez **AzureStorageLinkedService** comme **Nom de la connexion**.
    2. Sélectionnez votre **nom de compte de stockage** dans la liste déroulante.
    3. Cliquez sur **Suivant**. 

        ![Spécifier un compte de stockage Blob](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        Un service lié rattache un magasin de données ou une ressource de calcul à une fabrique de données. Dans ce cas, vous créez un service lié de stockage Azure pour lier votre compte de Stockage Azure à la fabrique de données. Le service lié comporte les informations de connexion utilisées par le service Data Factory pour établir la connexion au stockage Blob lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) qui contient les données sources. 
5. Sur la page **Choisir le fichier ou le dossier de sortie**, procédez comme suit :
    
    1. Accédez au dossier **adfv2tutorial/input**.
    2. Sélectionnez **inputEmp.txt**
    3. Cliquez sur **Choisir**. Vous pouvez également double-cliquer sur **inputEmp.txt**. 
    4. Cliquez sur **Suivant**. 

    ![Choisir le fichier ou le dossier d’entrée](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. Dans la page **Paramètres de format de fichier**, notez que l’outil détecte automatiquement les séparateurs de ligne et de colonne, puis cliquez sur **Suivant**. Vous pouvez également afficher un aperçu des données et un schéma des données d’entrée sur cette page. 

    ![Page Paramètres de format de fichier](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. Sur la page **Magasin de données de destination**, sélectionnez **Base de données SQL Azure**, puis sur **Suivant**. 

    ![Page Magasin de données de destination](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. Sur la page **Spécifier la base de données SQL Azure**, procédez comme suit : 

    1. Spécifiez **AzureSqlDatabaseLinkedService** comme **nom de la connexion**. 
    2. Sélectionnez votre serveur SQL Azure comme **nom de serveur**.
    3. Sélectionnez votre base de données SQL Azure pour le **nom de la base de données**.
    4. Spécifiez le nom de l’utilisateur comme **nom d’utilisateur**.
    5. Spécifiez le mot de passe de l’utilisateur comme **mot de passe**.
    6. Cliquez sur **Suivant**. 

        ![Spécifier la base de données SQL Azure](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        Un jeu de données doit être associé à un service lié. Le service lié comporte la chaîne de connexion utilisée par le service Data Factory pour établir la connexion à la base de données SQL Azure lors de l’exécution. Le jeu de données spécifie le conteneur, le dossier et le fichier (facultatif) dans lequel les données sont copiées.
9.  Sur la page **Mappage de table**, sélectionnez **[dbo].[emp]**, puis cliquez sur **Suivant**. 

    ![Page Mappage de table](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. Sur la page **Mappage du schéma**, notez que les deux premières colonnes dans le fichier d’entrée sont mappées aux colonnes **FirstName** et **LastName** de la table **emp**. 

    ![Page Mappage de schéma](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. Dans la page **Paramètres**, cliquez sur **Suivant**. 

    ![Page Paramètres](./media/tutorial-copy-data-tool/settings-page.png)
12. Dans la page **Résumé**, passez en revue les paramètres, puis cliquez sur **Suivant**.

    ![Page de résumé](./media/tutorial-copy-data-tool/summary-page.png)
13. Dans la page **Déploiement**, cliquez sur **Surveiller** pour surveiller le pipeline (tâche).

    ![Page Déploiement](./media/tutorial-copy-data-tool/deployment-page.png)
14. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution d’activité et de réexécuter le pipeline. Cliquez sur **Actualiser** pour actualiser la liste. 

    ![Surveiller des exécutions de pipelines](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Pour afficher des exécutions d’activité associées à l’exécution de pipelines, cliquez sur le lien **Voir les exécutions d’activités** dans la colonne **Actions**. Il n’y a qu’une seule activité (activité de copie) dans le pipeline, vous ne voyez donc qu’une seule entrée. Pour afficher les détails de l’opération de copie, cliquez sur le lien **Détails** (icône en forme de lunettes) dans la colonne **Actions**. Pour revenir à l’affichage des exécutions de pipelines, cliquez sur le lien **Pipelines** en haut. Pour actualiser la vue, cliquez sur **Actualiser**. 

    ![Surveiller des exécutions d’activités](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Cliquez sur l’onglet **Modifier** sur la gauche pour basculer en mode éditeur. Vous pouvez mettre à jour les services, jeux de données et pipelines liés créés par l’outil à l’aide de l’éditeur. Cliquez sur **Code** pour afficher le code JSON associé à l’entité ouverte dans l’éditeur. Pour plus de détails sur la modification de ces entités dans l’interface utilisateur de Data Factory, consultez [la version du portail Azure de ce didacticiel](tutorial-copy-data-portal.md).

    ![Onglet Modifier](./media/tutorial-copy-data-tool/edit-tab.png)
17. Vérifiez que les données sont insérées dans la table **emp** dans votre base de données SQL Azure. 

    ![Vérifier la sortie SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>étapes suivantes
Le pipeline dans cet exemple copie des données d’un stockage blob Azure vers une base de données SQL Azure. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Utiliser l’outil Copier les données pour créer un pipeline
> * Surveiller les exécutions de pipeline et d’activité.

Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud : 

> [!div class="nextstepaction"]
>[Copier des données locales vers le cloud](tutorial-hybrid-copy-data-tool.md)