---
title: "Copier des données en bloc à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Factory et Copier l’activité	 pour copier en bloc les données d’une banque de données source dans une banque de données de destination."
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 5f0703a3cb2ce912bfc042d0717ad8d921ec43e3
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copier plusieurs tables en bloc à l’aide d’Azure Data Factory
Ce didacticiel montre **comment copier des tables d’Azure SQL Database dans Azure SQL Data Warehouse**. Vous pouvez appliquer le même modèle à d’autres scénarios de copie. Par exemple : copie de tables à partir de SQL Server/Oracle dans Azure SQL Database/Data Warehouse/Azure Blob, copie de différents chemins à partir de Blob dans des tables Azure SQL Database.

> [!NOTE]
> - Si vous débutez avec Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).
> - Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

À un niveau élevé, ce didacticiel implique les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer des services liés à Azure SQL Database, Azure SQL Data Warehouse, et Stockage Azure.
> * Créer des jeux de données Azure SQL Database et Azure SQL Data Warehouse.
> * Créer un pipeline pour rechercher les tables à copier et un autre pipeline pour effectuer l’opération de copie. 
> * Démarrer une exécution de pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

Ce didacticiel utilise le portail Azure. Pour en savoir plus sur l’utilisation d’autres outils/SDK pour créer une fabrique de données, consultez [Démarrages rapides](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Workflow de bout en bout
Dans ce scénario, vous disposez d’un certain nombre de tables dans Azure SQL Database que vous souhaitez copier dans SQL Data Warehouse. Voici l’ordre logique des étapes du workflow qui se produit dans les pipelines :

![Workflow](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Le premier pipeline recherche la liste des tables à copier dans les banques de données du récepteur.  Vous pouvez également conserver une table de métadonnées qui répertorie toutes les tables à copier dans la banque de données du récepteur. Le pipeline déclenche ensuite un autre pipeline qui itère chaque table dans la base de données et effectue l’opération de copie de données.
* Le second pipeline effectue la copie. Il prend la liste des tables comme paramètre. Pour chaque table dans la liste, copiez la table spécifique d’Azure SQL Database dans la table correspondante de SQL Data Warehouse à l’aide d’une [copie intermédiaire par le biais de Stockage Blob et PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) afin d’obtenir de meilleurs résultats. Dans cet exemple, le premier pipeline passe la liste des tables comme valeur pour le paramètre. 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis
* **Compte Stockage Azure**. Le compte Stockage Azure est utilisé comme stockage d’objets blob intermédiaire dans l’opération de copie en bloc. 
* **Base de données SQL Azure**. Cette base de données contient les données sources. 
* **Azure SQL Data Warehouse**. Cet entrepôt de données conserve les données copiées à partir de SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Préparer SQL Database et SQL Data Warehouse

**Préparer la base de données SQL Azure source** :

Créez une base de données SQL Azure avec l’exemple de données Adventure Works LT. Pour cela, suivez les instructions de l’article [Créer une base de données SQL Azure](../sql-database/sql-database-get-started-portal.md). Ce didacticiel copie toutes les tables de cet exemple de base de données dans un entrepôt de données SQL.

**Préparer le récepteur Azure SQL Data Warehouse** :

1. Si vous n’avez pas d’entrepôt de données Azure SQL Data Warehouse, consultez l’article [Créer un entrepôt de données SQL](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) pour la procédure à suivre.

2. Créez les schémas de table correspondants dans SQL Data Warehouse. Vous pouvez utiliser l’[utilitaire de migration](https://www.microsoft.com/download/details.aspx?id=49100) pour **migrer le schéma** d’Azure SQL Database vers Azure SQL Data Warehouse. Plus tard, vous utiliserez Azure Data Factory pour migrer/copier les données.

## <a name="azure-services-to-access-sql-server"></a>Services Azure pour accéder au serveur SQL

Pour SQL Database et SQL Data Warehouse, autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est **activé** pour votre serveur SQL Azure. Ce paramètre permet au service Data Factory de lire les données de votre base de données SQL Azure et de les écrire dans votre entrepôt de données SQL Azure. Pour vérifier et activer ce paramètre, procédez comme suit :

1. Cliquez sur le hub **Plus de services** situé à gauche, puis sur **Serveurs SQL**.
2. Sélectionnez votre serveur, puis cliquez sur **Pare-feu** sous **PARAMÈTRES**.
3. Dans la page **Paramètres de pare-feu**, cliquez sur **ACTIVER** pour **Autoriser l’accès aux services Azure**.

## <a name="create-a-data-factory"></a>Créer une fabrique de données
1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialBulkCopyDF** pour le **nom**. 
      
     ![Page Nouvelle fabrique de données](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialBulkCopyDF). Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
      Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V2 (préversion)** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
    ![Page d'accueil Data Factory](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
10. Cliquez sur la vignette **Créer et surveiller** pour lancer l’application de l’interface utilisateur de Data Factory dans un onglet séparé.
11. Dans la page **Prise en main**, basculez vers l’onglet **Modifier** dans le volet gauche comme illustré dans l’image suivante :  

    ![Page de prise en main](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Créez des services liés
Vous créez des services liés pour lier vos magasins de données et vos calculs à une fabrique de données. Un service lié comporte les informations de connexion utilisées par le service Data Factory pour se connecter au magasin de données lors de l’exécution. 

Dans ce didacticiel, vous liez vos magasins de données Azure SQL Database, Azure SQL Data Warehouse et Stockage Blob Azure à votre fabrique de données. Azure SQL Database correspond au magasin de données source. Azure SQL Data Warehouse correspond au magasin de données récepteur/de destination. Stockage Blob Azure permet pour reclasser les données avant leur chargement dans SQL Data Warehouse à l’aide de PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Créer le service lié Azure SQL Database pour la source
Dans cette étape, vous créez un service lié qui relie votre base de données Azure SQL à la fabrique de données. 

1. Cliquez sur **Connexions** au bas de la fenêtre, puis cliquez sur **+ Nouveau** dans la barre d’outils. 

    ![Bouton de nouveau service lié](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
2. Dans la fenêtre **Nouveau service lié**, sélectionnez **Azure SQL Database**, puis cliquez sur **Continuer**. 

    ![Sélectionner Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
3. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    1. Entrez **AzureSqlDatabaseLinkedService** pour **Nom**. 
    2. Sélectionnez votre serveur SQL Azure pour **Nom du serveur**
    3. Sélectionnez votre base de données SQL Azure pour **Nom de la base de données**. 
    4. Entrez le **nom de l’utilisateur** pour se connecter à la base de données SQL Azure. 
    5. Entrez le **mot de passe** correspondant à l’utilisateur. 
    6. Pour tester la connexion à la base de données SQL Azure à l’aide des informations spécifiées, cliquez sur **Tester la connexion**.
    7. Cliquez sur **Enregistrer**.

        ![Paramètres d’Azure SQL Database](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Créer le service lié Azure SQL Data Warehouse pour le récepteur

1. Dans l’onglet **Connexions**, cliquez à nouveau sur **+ Nouveau** dans la barre d’outils. 
2. Dans la fenêtre **Nouveau service lié**, sélectionnez **Azure SQL Data Warehouse**, puis cliquez sur **Continuer**. 
3. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    1. Entrez **AzureSqlDWLinkedService** pour **Nom**. 
    2. Sélectionnez votre serveur SQL Azure pour **Nom du serveur**
    3. Sélectionnez votre base de données SQL Azure pour **Nom de la base de données**. 
    4. Entrez le **nom de l’utilisateur** pour se connecter à la base de données SQL Azure. 
    5. Entrez le **mot de passe** correspondant à l’utilisateur. 
    6. Pour tester la connexion à la base de données SQL Azure à l’aide des informations spécifiées, cliquez sur **Tester la connexion**.
    7. Cliquez sur **Enregistrer**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Créer le service lié Stockage Azure intermédiaire
Dans ce didacticiel, vous allez utiliser Stockage Blob Azure comme zone intermédiaire pour améliorer les performances de copie de PolyBase.

1. Dans l’onglet **Connexions**, cliquez à nouveau sur **+ Nouveau** dans la barre d’outils. 
2. Dans la fenêtre **Nouveau service lié**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Continuer**. 
3. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    1. Entrez **AzureStorageLinkedService** pour **Nom**. 
    2. Sélectionnez votre **compte de stockage Azure** pour **Nom du compte de stockage**.
    4. Cliquez sur **Enregistrer**.


## <a name="create-datasets"></a>Créez les jeux de données
Dans ce didacticiel, vous créez des jeux de données (source et récepteur) qui spécifient l’emplacement de stockage des données. 

Le jeu de données d’entrée AzureSqlDatabaseDataset fait référence à AzureSqlDatabaseLinkedService. Le service lié spécifie la chaîne de connexion pour se connecter à la base de données. Le jeu de données spécifie le nom de la base de données et la table contenant les données sources. 

Le jeu de données de sortie AzureSqlDWDataset fait référence à AzureSqlDWLinkedService. Le service lié spécifie la chaîne de connexion pour se connecter à l’entrepôt de données. Le jeu de données spécifie la base de données et la table dans lesquelles les données sont copiées. 

Dans ce didacticiel, les tables SQL source et de destination ne sont pas codées en dur dans les définitions de jeu de données. Au lieu de cela, l’activité ForEach transmet le nom de la table lors de l’exécution à l’activité de copie. 

### <a name="create-a-dataset-for-source-sql-database"></a>Créer un jeu de données pour la base de données SQL source

1. Cliquez sur **+ (plus)** dans le volet gauche, puis cliquez sur **Jeu de données**. 

    ![Menu Nouveau jeu de données](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
2. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Azure SQL Database**, puis cliquez sur **Terminer**. Vous devez voir un nouvel onglet intitulé **AzureSqlTable1**. 
    
    ![Sélectionner Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
3. Dans la fenêtre de propriétés en bas, entrez **AzureSqlDatabaseDataset** pour **Nom**.

    ![Nom du jeu de données source](./media/tutorial-bulk-copy-portal/source-dataset-general.png)
4. Basculez vers l’onglet **Connexions**, et procédez comme suit : 

    1. Sélectionnez **AzureSqlDatabaseLinkedService** pour **Service lié**.
    2. Sélectionnez une table pour **Table**. Cette table est une table fictive. Vous spécifiez une requête sur le jeu de données source lors de la création d’un pipeline. La requête est utilisée pour extraire des données de la base de données SQL Azure. Vous pouvez également cocher la case **Modifier** et entrer **dummyName** comme nom de table. 

    ![Page de connexion du jeu de données source](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Créer un jeu de données pour le récepteur SQL Data Warehouse

1. Cliquez sur **+ (plus)** dans le volet gauche, puis cliquez sur **Jeu de données**. 
2. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Azure SQL Data Warehouse**, puis cliquez sur **Terminer**. Vous devez voir un nouvel onglet intitulé **AzureSqlDWTable1**. 
3. Dans la fenêtre de propriétés en bas, entrez **AzureSqlDWDataset** pour **Nom**.
4. Basculez vers l’onglet **Connexions** et sélectionnez **AzureSqlDatabaseLinkedService** pour **Service lié**.
5. Basculez vers l’onglet **Paramètres**, puis cliquez sur **+ Nouveau**

    ![Page de connexion du jeu de données source](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter-button.png)
6. Entrez **DWTableName** pour le nom du paramètre. Si vous copiez-collez ce nom à partir de la page, vérifiez qu’il n’y a aucun **espace de fin** à la fin de **DWTableName**. 
7. Dans la section **Parameterized properties** (Propriétés paramétrables), entrez `@{dataset().DWTableName}` pour la propriété **tableName**. La propriété **tableName** du jeu de données est définie sur la valeur qui est transmise en tant qu’argument pour le paramètre **DWTableName**. L’activité ForEach effectue une itération dans une liste de tables et les transmet une par une à l’activité de copie. 
   
    ![Nom du paramètre](./media/tutorial-bulk-copy-portal/dwtablename-tablename.png)

## <a name="create-pipelines"></a>Créer des pipelines
Dans ce didacticiel, vous créez deux pipelines : **IterateAndCopySQLTables** et **GetTableListAndTriggerCopyData**. 

Le pipeline **GetTableListAndTriggerCopyData** effectue deux étapes :

* Recherche la table système Azure SQL Database pour obtenir la liste des tables à copier.
* Déclenche le pipeline **IterateAndCopySQLTables** pour copier les données.

**GetTableListAndTriggerCopyData** utilise une liste de tables en tant que paramètre. Pour chaque table dans la liste, il copie les données de la table dans Azure SQL Database vers Azure SQL Data Warehouse en utilisant la copie intermédiaire et PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Créer le pipeline IterateAndCopySQLTables

1. Dans le volet gauche, cliquez sur **+ (plus)**, puis cliquez sur **Pipeline**.

    ![Menu Nouveau pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. Dans la fenêtre Propriétés, renommez le pipeline en **IterateAndCopySQLTables**. 

    ![Nom du pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-name.png)
3. Basculez vers l’onglet **Paramètres**, et effectuez les actions suivantes : 

    1. Cliquez sur **+ Nouveau**. 
    2. Entrez **tableList** pour le **nom** du paramètre.
    3. Sélectionnez **Objet** pour **Type**.

        ![Paramètre de pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
4. Dans la boîte à outils **Activités**, développez **Iteration & Conditions** (Itération et conditions), et glissez-déposez l’activité **ForEach** vers la surface du concepteur de pipeline. Vous pouvez également rechercher des activités dans la boîte à outils **Activités**. Dans la fenêtre **Propriétés** en bas, entrez **IterateSQLTables** pour **Nom**. 

    ![Nom de l’activité ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-name.png)
5. Basculez vers l’onglet **Paramètres**, et entrez `@pipeline().parameters.tableList` pour **Éléments**.

    ![Paramètres de l’activité ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)
6. Pour ajouter une activité enfant à l’activité **ForEach**, **double-cliquez** sur l’activité ForEach (ou) cliquez sur **Modifier (icône de crayon)**. Vous ne voyez les liens d’action d’une activité que si vous la sélectionnez. 

    ![Nom de l’activité ForEach](./media/tutorial-bulk-copy-portal/edit-for-each-activity.png)
7. Dans la boîte à outils **Activités**, développez **Flux de données**, et glissez-déposez l’activité **Copie** dans la surface du concepteur de pipeline, puis remplacez le nom dans la fenêtre Propriétés par **CopyData**. Notez le menu de navigation en haut. IterateAndCopySQLTable correspond au nom du pipeline et IterateSQLTables au nom de l’activité ForEach. Le concepteur se trouve dans l’étendue de l’activité. Pour revenir à l’éditeur de pipeline à partir de l’éditeur ForEach, cliquez sur le lien dans le menu de navigation. 

    ![Copie dans ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
8. Basculez vers l’onglet **Source**, et procédez comme suit :

    1. Sélectionnez **AzureSqlDatabaseDataset** pour **Jeu de données source**. 
    2. Sélectionnez l’option **Requête** pour **Requête utilisateur**. 
    3. Entrez la requête SQL suivante pour **Requête**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Copier les paramètres de la source](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
9. Basculez vers l’onglet **Réception**, et procédez comme suit : 

    1. Sélectionnez **AzureSqlDWDataset** pour **Jeu de données récepteur**.
    2. Développez **Polybase Settings** (Paramètres de Polybase), puis sélectionnez **Allow polybase** (Autoriser Polybase). 
    3. Décochez **Utiliser l’option de type par défaut**. 
    4. Entrez le script SQL suivant pour **Script de nettoyage**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Copier les paramètres du récepteur](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
10. Basculez vers l’onglet **Paramètres**, faites défiler vers le bas, si nécessaire, pour voir la section **Jeu de données récepteur** avec le paramètre **DWTableName**. Définissez la valeur de ce paramètre sur `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]`.

    ![Copier les paramètres du récepteur](./media/tutorial-bulk-copy-portal/copy-sink-parameters.png)
11. Basculez vers l’onglet **Paramètres**, et procédez comme suit : 

    1. Sélectionnez **Vrai** pour **Activer le mode de préproduction**.
    2. Sélectionnez **AzureStorageLinkedService** pour **Store Account Linked Service** (Service lié de compte de stockage).

        ![Activer le mode de préproduction](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)
12. Pour valider les paramètres du pipeline, cliquez sur **Valider**. Vérifiez qu’il n’y a aucune erreur de validation. Pour fermer le **Rapport de validation de pipeline**, cliquez sur **>>**.

    ![Rapport de validation de pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-validation-report.png)

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Créer le pipeline GetTableListAndTriggerCopyData

Ce pipeline exécute deux étapes :

* Recherche la table système Azure SQL Database pour obtenir la liste des tables à copier.
* Déclenche le pipeline « IterateAndCopySQLTables » pour copier les données.

1. Dans le volet gauche, cliquez sur **+ (plus)**, puis cliquez sur **Pipeline**.

    ![Menu Nouveau pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. Dans la fenêtre Propriétés, renommez le pipeline en **GetTableListAndTriggerCopyData**. 

    ![Nom du pipeline](./media/tutorial-bulk-copy-portal/second-pipeline-name.png)
3. Dans la boîte à outils **Activités**, développez **Général**, et glissez-déposez l’activité **Recherche** vers la surface du concepteur de pipeline, puis procédez comme suit :

    1. Entrez **LookupTableList** pour **Nom**. 
    2. Entrez **Récupérer la liste de tables de base de données SQL Azure** pour **Description**.

        ![Activité de recherche - page Général](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
4. Basculez vers la page **Paramètres**, et procédez comme suit :

    1. Sélectionnez **AzureSqlDatabaseDataset** pour **Jeu de données source**. 
    2. Sélectionnez **Requête** pour **Utiliser la requête**. 
    3. Entrez la requête SQL suivante pour **Requête**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    4. Décochez la case pour le champ **First row only** (Première ligne uniquement).

        ![Activité de recherche - page de paramètres](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
5. Glissez-déposez l’activité **Execute Pipeline** (Exécuter le pipeline) de la boîte à outils Activités vers la zone du concepteur de pipeline et configurez le nom sur **TriggerCopy**.

    ![Activité d’exécution du pipeline - page Général](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
6. Basculez vers la page **Paramètres**, et procédez comme suit : 

    1. Sélectionnez **IterateAndCopySQLTables** pour **Invoked pipeline** (Pipeline appelé). 
    2. Développez la section **Avancé**. 
    3. Cliquez sur **+ Nouveau** dans la section **Paramètres**. 
    4. Entrez **tableList** pour le **nom** du paramètre.
    5. Entrez `@activity('LookupTableList').output.value` pour la **valeur** du paramètre. Vous définissez la liste des résultats de l’activité de recherche en tant qu’entrée du deuxième pipeline. La liste de résultats contient la liste des tables dont les données doivent être copiées dans la destination. 

        ![Activité d’exécution du pipeline - page de paramètres](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
7. **Connectez** l’activité **Recherche** à l’activité **Execute Pipeline** (Exécuter le pipeline) en faisant glisser la **zone verte** associée à l’activité de recherche vers gauche de l’activité d’exécution du pipeline.

    ![Activités de connexion de la recherche et d’exécution du pipeline](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
8. Pour valider le pipeline, cliquez sur **Valider** dans la barre d’outils. Vérifiez qu’il n’y a aucune erreur de validation. Pour fermer le **Rapport de validation de pipeline**, cliquez sur **>>**.

    ![Deuxième pipeline - Rapport de validation de pipeline](./media/tutorial-bulk-copy-portal/second-pipeline-validation-report.png)
9. Pour publier des entités (jeux de données, pipelines, etc.) au service Data Factory, cliquez sur **Publier tout**. Patientez jusqu’à ce que la publication réussisse. 

    ![Bouton Publier](./media/tutorial-bulk-copy-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Déclencher une exécution du pipeline

1. Vérifiez que l’onglet **GetTableListAndTriggerCopyData** est actif. 
2. Cliquez sur **Déclencher**, puis cliquez sur **Déclencher maintenant**. 

    ![Déclencher maintenant](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1. Basculez vers l’onglet **Surveiller**. Cliquez sur **Actualiser** jusqu’à ce que vous voyiez les exécutions des deux pipelines de votre solution. Continuez à actualiser la liste jusqu’à ce que vous voyiez l’étape **Réussite**. 

    ![Exécutions de pipeline](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
2. Pour afficher les exécutions d’activités associées au pipeline GetTableListAndTriggerCopyData, cliquez sur le premier lien dans la colonne Actions de ce pipeline. Vous devez voir deux exécutions d’activités pour cette exécution du pipeline. 

    ![Exécutions d’activités](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
3. Pour afficher la sortie de l’activité **Recherche**, cliquez sur le lien dans la colonne **Sortie** de cette activité. Vous pouvez agrandir et restaurer la fenêtre **Sortie**. Après la révision, cliquez sur **X** pour fermer la fenêtre **Sortie**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
4. Pour revenir à la vue **Exécutions du pipeline**, cliquez sur le lien **Pipelines** en haut. Cliquez sur le lien **Afficher les exécutions d’activités** (premier lien dans la colonne **Actions**) pour le pipeline **IterateAndCopySQLTables**. Vous devez voir la sortie comme illustré sur l’image suivante : notez qu’il n’existe qu’une seule exécution d’activité **Copie** pour chaque table dans la sortie de l’activité **Recherche**. 

    ![Exécutions d’activités](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
5. Vérifiez que les données ont été copiées dans le SQL Data Warehouse cible que vous avez utilisé dans ce didacticiel. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer des services liés à Azure SQL Database, Azure SQL Data Warehouse, et Stockage Azure.
> * Créer des jeux de données Azure SQL Database et Azure SQL Data Warehouse.
> * Créer un pipeline pour rechercher les tables à copier et un autre pipeline pour effectuer l’opération de copie. 
> * Démarrer une exécution de pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

Passez au didacticiel suivant pour découvrir comment copier des données de manière incrémentielle d’une source vers une destination :
> [!div class="nextstepaction"]
>[Copier des données de façon incrémentielle](tutorial-incremental-copy-portal.md)