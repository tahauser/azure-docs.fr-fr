---
title: Copier de façon incrémentielle une table en utilisant Azure Data Factory | Microsoft Docs
description: Dans ce didacticiel, vous allez créer un pipeline de fabrique de données Azure qui copie de façon incrémentielle les données d’une base de données SQL Azure dans un stockage Blob Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: 17ea97e34deb375123de12508c2c0845cd25c27a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Charger de façon incrémentielle les données d’une base de données SQL Azure dans un stockage Blob Azure
Dans ce didacticiel, vous allez créer une fabrique de données Azure avec un pipeline qui charge les données delta d’une table d’une base de données SQL Azure vers un stockage Blob Azure. 


> [!NOTE]
> Cet article s’applique à la version 2 d’Azure Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Préparer le magasin de données pour y stocker la valeur de limite.
> * Créer une fabrique de données.
> * créez des services liés. 
> * Créer des jeux de données source, récepteur et filigrane.
> * Créer un pipeline.
> * Exécuter le pipeline.
> * Surveiller l’exécution du pipeline. 
> * Passer en revue les résultats
> * Ajouter plus de données à la source.
> * Exécuter de nouveau le pipeline.
> * Surveiller la deuxième exécution du pipeline
> * Passer en revue les résultats de la deuxième exécution


## <a name="overview"></a>Vue d'ensemble
Voici le diagramme général de la solution : 

![Chargement incrémentiel de données](media\tutorial-Incremental-copy-portal\incrementally-load.png)

Voici les étapes importantes à suivre pour créer cette solution : 

1. **Sélectionner la colonne de limite**.
    Sélectionnez une colonne dans le magasin de données sources, qui peut servir à découper les enregistrements nouveaux ou mis à jour pour chaque exécution. Normalement, les données contenues dans cette colonne sélectionnée (par exemple, last_modify_time ou ID) continuent de croître à mesure que des lignes sont créées ou mises à jour. La valeur maximale de cette colonne est utilisée comme limite.

2. **Préparer un magasin de données pour stocker la valeur de limite**. Dans ce didacticiel, la valeur de filigrane est stockée dans une base de données SQL.
    
3. **Créer un pipeline avec le flux de travail suivant** : 
    
    Le pipeline de cette solution compte les activités suivantes :
  
    * Créez deux activités de recherche. Servez-vous de la première activité de recherche pour récupérer la dernière valeur de filigrane. Utilisez la deuxième activité de recherche pour récupérer la nouvelle valeur de filigrane. Ces valeurs de filigrane sont transmises à l’activité de copie. 
    * Créez une activité de copie qui copie les lignes du magasin de données source dont la valeur de la colonne de filigrane est supérieure à l’ancienne valeur de filigrane et inférieure à la nouvelle. Elle copie ensuite les données delta du magasin de données source dans un stockage d’objets blob sous la forme d’un nouveau fichier. 
    * Créez une activité StoredProcedure qui met à jour la valeur de filigrane pour le pipeline s’exécutant la prochaine fois. 


Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* **Base de données SQL Azure**. Vous utilisez la base de données comme magasin de données source. Si vous ne disposez pas d’une base de données SQL, consultez [Créer une base de données Azure SQL Database](../sql-database/sql-database-get-started-portal.md) pour connaître la procédure à suivre pour en créer une.
* **Stockage Azure**. Vous utilisez le stockage d’objets blob comme magasin de données récepteur. Si vous ne possédez pas de compte de stockage, consultez l’article [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un. Créez un conteneur sous le nom adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Créer une table de source de données dans votre base de données SQL
1. Ouvrez SQL Server Management Studio. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur la base de données, puis choisissez **Nouvelle requête**.

2. Exécutez la commande SQL suivante sur votre base de données SQL pour créer une table sous le nom `data_source_table` comme magasin de source de données : 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Dans ce didacticiel, vous allez utiliser LastModifytime comme colonne de filigrane. Les données contenues dans le magasin de source de données sont indiquées dans le tableau suivant :

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Créer une autre table dans la base de données SQL pour stocker la valeur de filigrane supérieure
1. Exécutez la commande SQL suivante sur votre base de données SQL pour créer une table sous le nom `watermarktable` pour stocker la valeur de filigrane :  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Définissez la valeur par défaut du filigrane supérieur avec le nom de table du magasin de données source. Dans ce didacticiel, le nom de table est data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Passez en revue les données contenues dans la table `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Output: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Créer une procédure stockée dans la base de données SQL 

Exécutez la commande suivante pour créer une procédure stockée dans votre base de données SQL :

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-incremental-copy-portal/new-azure-data-factory-menu.png)
2. Sur la page **Nouvelle fabrique de données**, entrez **ADFTutorialOnPremDF** comme **nom**. 
      
     ![Page Nouvelle fabrique de données](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si vous voyez un point d’exclamation rouge avec l’erreur suivante, changez le nom de la fabrique de données (par exemple, votrenomADFIncCopyTutorialDF), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
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

    ![mosaïque déploiement de fabrique de données](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
   ![Page d'accueil Data Factory](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. Cliquez sur la vignette **Créer et surveiller** pour lancer l’interface utilisateur d’Azure Data Factory dans un onglet séparé.

## <a name="create-a-pipeline"></a>Créer un pipeline
Dans ce didacticiel, vous allez créer un pipeline avec deux activités de recherche, une activité de copie et une activité StoredProcedure chaînées dans le même pipeline. 

1. Sur la page **Prise en main** de l’interface utilisateur de Data Factory, cliquez sur la vignette **Créer un pipeline**. 

   ![Page de prise en main de l’interface utilisateur de Data Factory](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. Sur la page **Général** de la fenêtre **Propriétés** pour le pipeline, entrez le nom **IncrementalCopyPipeline**. 

   ![Nom du pipeline](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. Vous allez ajouter la première activité de recherche pour obtenir l’ancienne valeur de filigrane. Dans la boîte à outils **Activités**, Développez **Général**, puis faites glisser et déposez une activité **Recherche** sur la surface du concepteur de pipeline. Changez le nom de l’activité par **LookupOldWaterMarkActivity**.

   ![Première activité de recherche - nom](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Basculez vers l’onglet **Paramètres**, puis cliquez sur **+ Nouveau** comme **jeu de données source**. Dans cette étape, vous créez un jeu de données pour représenter des données dans la **table filigrane**. Cette table contient l’ancien filigrane utilisé dans l’opération de copie précédente. 

   ![Menu de nouveau jeu de données - ancien filigrane](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Base de données SQL Azure**, puis cliquez sur **Terminer**. Vous voyez un nouvel onglet ouvert pour le jeu de données. 

   ![Sélectionner une base de données SQL Azure](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. Dans la fenêtre Propriétés pour le jeu de données, entrez **WatermarkDataset** comme **nom**.

   ![Jeu de données de filigrane - nom](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. Basculez vers l’onglet **Connexion**, puis cliquez sur **+ Nouveau** pour établir une connexion (créer un service lié) à votre base de données SQL Azure. 

   ![Bouton de nouveau service lié](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. Dans la fenêtre **Nouveau service lié**, procédez comme suit :

    1. Entrez **AzureSqlDatabaseLinkedService** comme **nom**. 
    2. Sélectionnez votre serveur SQL Azure comme **nom de serveur**.
    3. Entrez le **nom d’utilisateur** pour accéder au serveur SQL Azure. 
    4. Entrez le **mot de passe** correspondant à l’utilisateur. 
    5. Pour tester la connexion à la base de données SQL Azure, cliquez sur **Tester la connexion**.
    6. Cliquez sur **Enregistrer**.
    7. Dans l’onglet **Connexion**, vérifiez que **AzureSqlDatabaseLinkedService** est sélectionné comme **service lié**.
       
        ![Fenêtre du nouveau service lié](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. Sélectionnez **[dbo].[ watermarktable]** comme **Table**. Si vous souhaitez afficher un aperçu des données de la table, cliquez sur **Aperçu des données**.

    ![Jeu de données de filigrane - paramètres de connexion](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. Basculez vers l’éditeur de pipeline en cliquant sur l’onglet du pipeline en haut ou bien en cliquant sur le nom du pipeline dans l’arborescence à gauche. Dans la fenêtre Propriétés pour l’activité de **recherche**, vérifiez que **WatermarkDataset** est sélectionné dans le champ **Jeu de données source**. 

    ![Pipeline - ancien jeu de données de filigrane](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. Dans la boîte à outils **Activités**, développez **Général**et faites glisser et déposez une autre activité de **recherche** sur la surface du concepteur de pipeline, puis saisissez le nom **LookupNewWaterMarkActivity** dans l’onglet **Général** de la fenêtre Propriétés. Cette activité de recherche obtient la nouvelle valeur de filigrane à partir de la table avec les données sources à copier vers la destination. 

    ![Deuxième activité de recherche - nom](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. Dans la fenêtre Propriétés pour la deuxième activité de **recherche**, basculez vers l’onglet **Paramètres**, puis cliquez sur **Nouveau**. Vous créez un jeu de données pour pointer vers la table source qui contient la nouvelle valeur du filigrane (valeur maximale de LastModifyTime). 

    ![Deuxième activité de recherche - nouveau jeu de données](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Base de données SQL Azure**, puis cliquez sur **Terminer**. Vous voyez un nouvel onglet ouvert pour ce jeu de données. Vous voyez également le jeu de données dans l’arborescence. 
15. Dans l’onglet **Général** de la fenêtre Propriétés, entrez **SourceDataset** comme **nom**. 

    ![Jeu de données source - nom](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. Basculez vers l’onglet **Connexions**, et procédez comme suit : 

    1. Sélectionnez **AzureSqlDatabaseLinkedService** comme **service lié**.
    2. Sélectionnez **[dbo].[ data_source_table]** comme table. Vous allez spécifier une requête sur ce jeu de données plus loin dans le didacticiel. La requête a la priorité sur la table spécifiée à cette étape. 

        ![Deuxième activité de recherche - nouveau jeu de données](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. Basculez vers l’éditeur de pipeline en cliquant sur l’onglet du pipeline en haut ou bien en cliquant sur le nom du pipeline dans l’arborescence à gauche. Dans la fenêtre Propriétés pour l’activité de **recherche**, vérifiez que **SourceDataset** est sélectionné dans le champ **Jeu de données source**. 
18. Sélectionnez **Requête** pour le champ **Utiliser une requête** et saisissez la requête suivante : vous sélectionnez uniquement la valeur maximale de **LastModifytime** à partir de **data_table_source**. Si vous n’avez pas cette requête, le jeu de données obtient toutes les lignes de la table étant donné que vous avez spécifié le nom de table (data_source_table) dans la définition du jeu de données.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Deuxième activité de recherche - requête](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Dans la boîte à outils **Activités**, développez **Flux de données**et glissez-déposez l’activité de **copie** à partir de la boîte à outils Activités et saisissez le nom  **IncrementalCopyActivity**. 

    ![Activité de copie - nom](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. **Connecter les deux activités de recherche à l’activité de copie** en faisant glisser le **bouton vert** attaché aux activités de recherche vers l’activité de copie. Relâchez le bouton de la souris lorsque la couleur de bordure de l’activité de copie passe en bleu. 

    ![Connexion des activités de recherche à l’activité de copie](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Sélectionnez l’**activité de copie** et vérifiez que vous vouez les propriétés de l’activité dans la fenêtre **Propriétés**. 

    ![Propriétés de l’activité de copie](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. Basculez vers l’onglet **Source** dans la fenêtre**Propriétés**, et procédez comme suit :

    1. Sélectionnez **SourceDataset** pour le champ **Jeu de données source**. 
    2. Sélectionnez **Requête** pour le champ **Utiliser la requête**. 
    3. Saisissez la requête SQL suivante dans le champ **Requête**. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Activité de copie - source](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Basculez vers l’onglet **Récepteur**, puis cliquez sur **+ Nouveau** pour le champ **Jeu de données récepteur**. 

    ![Bouton Nouveau jeu de données](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. Dans ce didacticiel, le magasin de données récepteur est de type Stockage Blob Azure. Par conséquent, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer** dans la fenêtre **Nouveau jeu de données**. 

    ![Sélectionner Stockage Blob Azure](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. Dans l’onglet **Général** de la fenêtre Propriétés pour le jeu de données, entrez **SinkDataset** comme **nom**. 

    ![Jeu de données récepteur - nom](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. Basculez vers l’onglet **Connexion**, puis cliquez sur **+ Nouveau**. Dans cette étape, vous créez une connexion (service lié) à votre **stockage Blob Azure**.

    ![Jeu de données récepteur - nouvelle connexion](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. Dans la fenêtre **Nouveau service lié**, procédez comme suit : 

    1. Entrez **AzureStorageLinkedService** comme **Nom**. 
    2. Sélectionnez votre compte de stockage Azure comme **Nom du compte de stockage**.
    3. Cliquez sur **Enregistrer**. 

        ![Service lié Stockage Azure - paramètres](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. Dans l’onglet **Connexions**, procédez comme suit :

    1. Vérifiez que **AzureStorageLinkedService** est sélectionné comme **service lié**. 
    2. Pour la partie **dossier** du champ **chemin d’accès du fichier**, entrez **adftutorial/incrementalcopy**. **adftutorial** est le nom du conteneur d’objets blob et **incrementalcopy** est le nom du dossier. Cet extrait de code suppose que vous disposez d’un conteneur d’objets blob nommé adftutorial dans le stockage d’objets blob. Créez le conteneur s’il n’existe pas ou attribuez-lui le nom d’un conteneur existant. Azure Data Factory crée automatiquement le dossier de sortie **incrementalcopy** s’il n’existe pas. Vous pouvez également utiliser le bouton **Parcourir** pour le **chemin d’accès du fichier** afin d’accéder à un dossier dans un conteneur d’objets blob. .RunId, '.txt')`.
    3. Pour la partie **Nom de fichier** dans le champ **Chemin d’accès du fichier**, saisissez `@CONCAT('Incremental-', pipeline().RunId, '.txt')`. Le nom de fichier est généré dynamiquement à l’aide de l’expression. Chaque exécution de pipeline possède un ID unique. L’activité de copie utilise l’ID d’exécution pour générer le nom de fichier. 

        ![Jeu de données récepteur - paramètres de connexion](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. Basculez vers l’éditeur de **pipeline** en cliquant sur l’onglet du pipeline en haut ou bien en cliquant sur le nom du pipeline dans l’arborescence à gauche. 
29. Dans la boîte à outils **Activités**, développez **Général**, et faites glisser et déposez l’activité **Procédure stockée** de la boîte à outils **Activités** sur la surface du concepteur de pipeline. **Connectez** le résultat vert (succès) de l’activité de **copie** à l’activité **Procédure stockée**. 
    
    ![Activité de copie - source](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. Sélectionnez **Activité de procédure de stockage** dans le concepteur de pipeline, remplacez son nom par **StoredProceduretoWriteWatermarkActivity**. 

    ![Activité de procédure stockée - nom](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. Basculez vers l’onglet **Compte SQL** et sélectionnez *AzureSqlDatabaseLinkedService** pour **Service lié**. 

    ![Activité de procédure stockée - Compte SQL](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. Basculez vers l’onglet **Procédure stockée**, et procédez comme suit : 

    1. Pour **Nom de la procédure stockée**, sélectionnez **sp_write_watermark**. 
    2. Pour spécifier des valeurs correspondant aux paramètres de procédure stockée, cliquez sur **Import parameter** (Paramètre d’importation), puis entrez les valeurs suivantes pour les paramètres : 

        | NOM | type | Valeur | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Chaîne | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Activité de procédure stockée- paramètres de procédure stockée](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Pour valider les paramètres du pipeline, cliquez sur **Valider** dans la barre d’outils. Vérifiez qu’il n’y a aucune erreur de validation. Pour fermer la fenêtre **Rapport de validation de pipeline**, cliquez sur >>.   

    ![Valider le pipeline](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. Publiez des entités (services liés, jeux de données et pipelines) pour le service Azure Data Factory en sélectionnant le bouton **Publier tout**. Patientez jusqu’à voir le message de réussite de la publication. 

    ![Bouton Publier](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Déclencher une exécution du pipeline
1. Cliquez sur **Déclencher** dans la barre d’outils, cliquez sur **Déclencher maintenant**. 

    ![Bouton Déclencher maintenant](./media/tutorial-incremental-copy-portal/trigger-now.png)
2. Dans la fenêtre **Exécution du pipeline**, sélectionnez **Terminer**. 

## <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

1. Basculez vers l’onglet **Surveiller** sur la gauche. Vous pouvez voir l’état de l’exécution du pipeline déclenchée par le déclencheur manuel. Cliquez sur le bouton **Actualiser** pour actualiser la liste. 
    
    ![Exécutions de pipeline](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. Pour voir des exécutions d’activité associées à l’exécution de ce pipeline, cliquez sur le premier lien (**Voir les exécutions d’activités**) dans la colonne **Actions**. Vous pouvez revenir à la vue précédente en cliquant sur **Pipelines** en haut. Cliquez sur le bouton **Actualiser** pour actualiser la liste.

    ![Exécutions d’activités](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>Passer en revue les résultats.
1. Connectez-vous à votre compte de Stockage Azure à l’aide des outils tels que [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Vérifiez qu’un fichier de sortie est créé dans le dossier **incrementalcopy** du conteneur **adftutorial**.

    ![Premier fichier de sortie](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Ouvrez le fichier de sortie et notez que toutes les données sont copiées à partir de la **data_source_table** dans le fichier de l’objet blob. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Vérifiez la valeur la plus récente dans `watermarktable`. Vous constatez que la valeur de filigrane a été mise à jour.

    ```sql
    Select * from watermarktable
    ```
    
    Voici la sortie :
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>Ajouter plus de données à la source

Insérez de nouvelles données dans la base de données SQL (magasin de source de données).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

Données mises à jour dans la base de données SQL :

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Déclencher une autre exécution de pipeline
1. Basculez vers l’onglet **Modifier**. Cliquez sur le pipeline dans l’arborescence s’il n’est pas ouvert dans le concepteur. 

    ![Bouton Déclencher maintenant](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. Cliquez sur **Déclencher** dans la barre d’outils, cliquez sur **Déclencher maintenant**. 

    ![Bouton Déclencher maintenant](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>Surveiller la deuxième exécution du pipeline

1. Basculez vers l’onglet **Surveiller** sur la gauche. Vous pouvez voir l’état de l’exécution du pipeline déclenchée par le déclencheur manuel. Cliquez sur le bouton **Actualiser** pour actualiser la liste. 
    
    ![Exécutions de pipeline](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. Pour voir des exécutions d’activité associées à l’exécution de ce pipeline, cliquez sur le premier lien (**Voir les exécutions d’activités**) dans la colonne **Actions**. Vous pouvez revenir à la vue précédente en cliquant sur **Pipelines** en haut. Cliquez sur le bouton **Actualiser** pour actualiser la liste.

    ![Exécutions d’activités](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>Vérifier la deuxième sortie

1. Dans le stockage d’objets blob, vous constatez qu’un autre fichier a été créé. Dans ce didacticiel, le nom du nouveau fichier est `Incremental-<GUID>.txt`. Ouvrez ce fichier. Vous constatez alors qu’il contient deux lignes d’enregistrements.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Vérifiez la valeur la plus récente dans `watermarktable`. Vous constatez que la valeur de filigrane a été de nouveau mise à jour.

    ```sql
    Select * from watermarktable
    ```
    Exemple de sortie : 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Préparer le magasin de données pour y stocker la valeur de limite.
> * Créer une fabrique de données.
> * créez des services liés. 
> * Créer des jeux de données source, récepteur et filigrane.
> * Créer un pipeline.
> * Exécuter le pipeline.
> * Surveiller l’exécution du pipeline. 
> * Passer en revue les résultats
> * Ajouter plus de données à la source.
> * Exécuter de nouveau le pipeline.
> * Surveiller la deuxième exécution du pipeline
> * Passer en revue les résultats de la deuxième exécution

Dans ce didacticiel, le pipeline a copié les données d’une table unique d’une base de données SQL vers un stockage d’objets blob. Passez au didacticiel suivant pour découvrir comment copier les données de plusieurs tables d’une base de données SQL Server locale vers une base de données SQL. 

> [!div class="nextstepaction"]
>[Charger de façon incrémentielle des données provenant de plusieurs tables dans SQL Server vers Azure SQL Database](tutorial-incremental-copy-multiple-tables-portal.md)



