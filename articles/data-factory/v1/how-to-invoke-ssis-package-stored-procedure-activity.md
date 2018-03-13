---
title: "Appeler un package SSIS à l’aide d’Azure Data Factory - Activité de procédure stockée | Microsoft Docs"
description: "Cet article décrit comment appeler un package SQL Server Integration Services (SSIS) à partir d’un pipeline Azure Data Factory à l’aide de l’activité de procédure stockée."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 99e3365a846f35262489fdccd753b4ce2e50fa49
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Appeler un package SSIS à l’aide de l’activité de procédure stockée dans Azure Data Factory
Cet article décrit comment appeler un package SSIS à partir d’un pipeline Azure Data Factory à l’aide d’une activité de procédure stockée. 

> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible. Si vous utilisez la version 2 du service Data Factory, qui est en préversion publique, consultez [Appeler un package SSIS à l’aide de l’activité de procédure stockée dans la version 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>configuration requise

### <a name="azure-sql-database"></a>Base de données SQL Azure 
La procédure pas à pas dans cet article utilise une base de données Azure SQL qui héberge le catalogue SSIS. Vous pouvez également utiliser Azure SQL Managed Instance (préversion privée).

### <a name="create-an-azure-ssis-integration-runtime"></a>Créer un runtime d’intégration Azure-SSIS
Créez un runtime d’intégration Azure-SSIS si vous n’en avez pas en suivant les instructions pas à pas fournies dans le [Didacticiel : Déployer des packages SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Vous devez créer une fabrique de données de version 2 pour créer un runtime d’intégration Azure-SSIS. 

## <a name="azure-portal"></a>Portail Azure
Dans cette section, vous utilisez le portail Azure pour créer un pipeline Data Factory avec une activité de procédure stockée qui appelle un package SSIS.

### <a name="create-a-data-factory"></a>Créer une fabrique de données
La première étape consiste à créer une fabrique de données à l’aide du portail Azure. 

1. Accédez au [portail Azure](https://portal.azure.com). 
2. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page Nouvelle fabrique de données](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche pour le champ du nom, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory). Consultez l’article [Data Factory - Règles d’affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.

    `Data factory name ADFTutorialDataFactory is not available`
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
    Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V1** comme **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge par Data Factory sont affichés dans la liste déroulante. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
    ![Page d'accueil Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Cliquez sur la vignette **Créer et déployer** pour démarrer Data Factory Editor.

    ![Data Factory Editor](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Créer un service lié Azure SQL Database
Créez un service lié pour lier votre base de données Azure SQL qui héberge le catalogue SSIS à votre fabrique de données. Data Factory utilise les informations de ce service lié pour se connecter à la base de données SSISDB, et exécute une procédure stockée pour exécuter un package SSIS. 

1. Dans Data Factory Editor, cliquez sur **Nouvelle banque de données** dans le menu, puis sur **Azure SQL Database**. 

    ![Nouvelle banque de données -> Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. Dans le volet droit, effectuez les étapes suivantes :

    1. Remplacez `<servername>` par le nom de votre serveur Azure SQL. 
    2. Remplacez `<databasename>` par **SSISDB** (nom de la base de données du catalogue SSIS). 
    3. Remplacez `<username@servername>` par le nom de l’utilisateur qui a accès au serveur SQL Azure. 
    4. Remplacez `<password>` par le mot de passe de l’utilisateur. 
    5. Déployez le service lié en cliquant sur le bouton **Déployer** dans la barre d’outils. 

        ![Service lié pour base de données SQL Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Créer un jeu de données factice pour la sortie
Ce jeu de données de sortie est un jeu de données factice qui détermine la programmation du pipeline. Notez que la fréquence est définie sur Heure et l’intervalle sur 1. Ainsi, le pipeline s’exécute une fois par heure entre les heures de début et de fin du pipeline. 

1. Dans le volet gauche de Data Factory Editor, cliquez sur **... Plus** -> **Nouveau jeu de données** -> **Azure SQL**.

    ![Plus -> Nouveau jeu de données](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Copiez l’extrait de code JSON suivant dans l’éditeur JSON dans le volet droit. 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Dans la barre d’outils, cliquez sur **Déployer** . Cette action déploie le jeu de données sur le service Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Créer un pipeline avec une activité de procédure stockée 
Lors de cette étape, vous allez créer un pipeline avec une activité de procédure stockée. L’activité appelle la procédure stockée sp_executesql pour exécuter votre package SSIS. 

1. Dans le volet gauche, cliquez sur **... Plus**, puis sur **Nouveau pipeline**.
2. Copiez l’extrait de code JSON suivant dans l’éditeur JSON : 

    > [!IMPORTANT]
    > Remplacez &lt;folder name&gt;, &lt;project name&gt; et &lt;package name&gt; par des noms de dossier, projet et package dans le catalogue SSIS avant d’enregistrer le fichier.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Dans la barre d’outils, cliquez sur **Déployer** . Cette action déploie le pipeline sur le service Azure Data Factory. 

### <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.
La planification sur le jeu de données de sortie est définie sur « Toutes les heures ». L’heure de fin du pipeline est cinq heures après l’heure de début. Par conséquent, cinq séries de pipeline sont visibles. 

1. Fermez les fenêtres de l’éditeur pour afficher la page d’accueil de la fabrique de données. Cliquez sur la vignette **Surveiller et gérer**. 

    ![Vignette de diagramme](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Mettez à jour **l’Heure de début** et **l’Heure de fin** avec les valeurs **01/18/2018 08:30 AM** et **01/20/2018 08:30 AM**, puis cliquez sur **Appliquer**. Vous devez voir les **fenêtres d’activités** associées avec l’exécution du pipeline. 

    ![Fenêtres d’activités](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Pour plus d’informations sur la surveillance des pipelines, consultez [Surveiller et gérer les pipelines Azure Data Factory à l’aide de l’application de surveillance et gestion](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
Dans cette section, vous utilisez Azure PowerShell pour créer un pipeline Data Factory avec une activité de procédure stockée qui appelle un package SSIS.

Installez les modules Azure PowerShell les plus récents en suivant les instructions décrites dans [Comment installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Créer une fabrique de données
La procédure suivante décrit les étapes permettant de créer une fabrique de données. Vous créez un pipeline avec une activité de procédure stockée dans cette fabrique de données. L’activité de procédure stockée exécute une procédure stockée dans la base de données SSISDB pour exécuter votre package SSIS.

1. Définissez une variable pour le nom du groupe de ressources que vous utiliserez ultérieurement dans les commandes PowerShell. Copiez le texte de commande suivant dans PowerShell, spécifiez un nom pour le [groupe de ressources Azure](../../azure-resource-manager/resource-group-overview.md) entre des guillemets doubles, puis exécutez la commande. Par exemple : `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$ResourceGroupName` et exécutez à nouveau la commande
2. Pour créer le groupe de ressources Azure, exécutez la commande suivante : 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Si le groupe de ressources existe déjà, vous pouvez ne pas le remplacer. Affectez une valeur différente à la variable `$ResourceGroupName` et exécutez à nouveau la commande. 
3. Définissez une variable pour le nom de la fabrique de données. 

    > [!IMPORTANT]
    >  Mettez à jour le nom de la fabrique de données afin qu’il soit globalement unique. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Pour créer la fabrique de données, exécutez l’applet de commande suivante **New-AzureRmDataFactory**, à l’aide des propriétés Location et ResourceGroupName à partir de la variable $ResGrp : 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Notez les points suivants :

* Le nom de la fabrique de données Azure doit être un nom global unique. Si vous recevez l’erreur suivante, changez le nom, puis réessayez.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être un membre des rôles **contributeur** ou **propriétaire**, ou un **administrateur** de l’abonnement Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Créer un service lié Azure SQL Database
Créez un service lié pour lier votre base de données Azure SQL qui héberge le catalogue SSIS à votre fabrique de données. Data Factory utilise les informations de ce service lié pour se connecter à la base de données SSISDB, et exécute une procédure stockée pour exécuter un package SSIS. 

1. Créez un fichier JSON nommé **AzureSqlDatabaseLinkedService.json** dans le dossier **C:\ADF\RunSSISPackage** avec le contenu suivant : 

    > [!IMPORTANT]
    > Remplacez &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; et &lt;password&gt; par les valeurs de votre base de données Azure SQL avant d’enregistrer le fichier.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. Dans **Azure PowerShell**, basculez vers le dossier **C:\ADF\RunSSISPackage**.
3. Exécutez l’applet de commande **New-AzureRmDataFactoryLinkedService** pour créer le service lié : **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Créer un jeu de données de sortie
Ce jeu de données de sortie est un jeu de données factice qui détermine la programmation du pipeline. Notez que la fréquence est définie sur Heure et l’intervalle sur 1. Ainsi, le pipeline s’exécute une fois par heure entre les heures de début et de fin du pipeline. 

1. Créez un fichier OuputDataset.json avec le contenu suivant : 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Exécutez l’applet de commande **New-AzureRmDataFactoryDataset** pour créer un jeu de données. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Créer un pipeline avec une activité de procédure stockée 
Lors de cette étape, vous allez créer un pipeline avec une activité de procédure stockée. L’activité appelle la procédure stockée sp_executesql pour exécuter votre package SSIS. 

1. Créez un fichier JSON nommé **MyPipeline.json** dans le dossier **C:\ADF\RunSSISPackage** avec le contenu suivant :

    > [!IMPORTANT]
    > Remplacez &lt;folder name&gt;, &lt;project name&gt; et &lt;package name&gt; par des noms de dossier, projet et package dans le catalogue SSIS avant d’enregistrer le fichier.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Pour créer le pipeline **RunSSISPackagePipeline**, exécutez l’applet de commande **New-AzureRmDataFactoryPipeline**.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Surveiller l’exécution du pipeline.

2. Exécutez **Get-AzureRmDataFactorySlice** pour obtenir des détails sur toutes les tranches du jeu de données de sortie\**, qui est la table de sortie du pipeline.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Notez que la valeur StartDateTime que vous spécifiez ici est identique à l’heure de début que vous avez spécifiée dans le pipeline de JSON. 
3. Exécutez **Get-AzureRmDataFactoryRun** pour obtenir les détails des exécutions d’activité pour un segment spécifique.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Vous pouvez continuer d’exécuter cette applet de commande jusqu’à ce que le segment apparaisse dans l’état **Prêt** ou **En échec**. 

    Vous pouvez exécuter la requête suivante par rapport à la base de données SSISDB dans votre serveur Azure SQL pour vérifier que le package s’est exécuté. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’activité de procédure stockée, consultez l’article [Activité de procédure stockée](data-factory-stored-proc-activity.md).

