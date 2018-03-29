---
title: Guide pratique pour planifier un runtime d’intégration Azure SSIS | Microsoft Docs
description: Cet article explique comment planifier le démarrage et l’arrêt d’un runtime d’intégration Azure SSIS à l’aide d’Azure Automation et de Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 01/25/2018
ms.author: douglasl
ms.openlocfilehash: cc9ab244c784cab608a75092b542dea0a6f69f22
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Guide pratique pour planifier le démarrage et l’arrêt d’un runtime d’intégration Azure SSIS 
L’exécution d’un runtime d’intégration (IR) Azure SSIS (SQL Server Integration Services) a un coût. Il est donc souhaitable de n’exécuter le runtime d’intégration que quand vous devez exécuter des packages SSIS dans Azure et de l’arrêter quand vous n’en avez plus besoin. Vous pouvez utiliser l’interface utilisateur de Data Factory ou Azure PowerShell pour [démarrer ou arrêter un runtime d’intégration Azure SSIS manuellement](manage-azure-ssis-integration-runtime.md). Cet article explique comment planifier le démarrage et l’arrêt d’un runtime d’intégration (IR) Azure SSIS à l’aide d’Azure Automation et de Azure Data Factory. Voici les étapes générales décrites dans cet article :

1. **Créer et tester un runbook Azure Automation.** Dans cette étape, vous créez un runbook PowerShell avec le script qui démarre ou arrête un runtime d’intégration Azure SSIS. Ensuite, vous testez le runbook dans des scénarios de démarrage (START) et d’arrêt (STOP) afin de vérifier que le runtime d’intégration démarre ou s’arrête. 
2. **Créer deux planifications pour le runbook.** Pour la première planification, vous configurez le runbook avec START (Démarrer) comme opération. Pour la seconde planification, configurez le runbook avec STOP (Arrêter) comme opération. Pour les deux planifications, vous spécifiez la cadence à laquelle le runbook est exécuté. Par exemple, vous pouvez planifier l’exécution du premier pour 8h00 chaque jour et celle du second pour 23h00 chaque jour. Quand le premier runbook s’exécute, il démarre le runtime d’intégration Azure SSIS. Quand le second runbook s’exécute, il arrête le runtime d’intégration Azure SSIS. 
3. **Créer deux Webhooks pour le runbook**, un pour l’opération START (Démarrer) et l’autre pour l’opération STOP (Arrêter). Vous utilisez les URL de ces Webhooks au moment de configurer les activités web dans un pipeline Data Factory. 
4. **Créer un pipeline Data Factory**. Le pipeline que vous créez se compose de trois activités. La première activité **Web** appelle le premier Webhook pour démarrer le runtime d’intégration Azure SSIS. L’activité **Procédure stockée** exécute un script SQL qui exécute le package SSIS. La seconde activité **Web** arrête le runtime d’intégration Azure SSIS. Pour plus d’informations sur l’appel d’un package SSIS à partir d’un pipeline Data Factory à l’aide de l’activité Procédure stockée, consultez [Appeler un package SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). Ensuite, vous créez un déclencheur de planification pour planifier l’exécution du pipeline à la cadence que vous spécifiez.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, consultez [Appeler un package SSIS à l’aide de l’activité de procédure stockée dans la version 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas déjà provisionné de runtime d’intégration Azure SSIS, provisionnez-le en suivant les instructions indiquées dans ce [didacticiel](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Créer et tester un runbook Azure Automation
Dans cette section, vous allez effectuer les étapes suivantes : 

1. Créer un compte Azure Automation.
2. Créer un runbook PowerShell dans le compte Azure Automation. Le script PowerShell associé au runbook démarre ou arrête un runtime d’intégration Azure SSIS en fonction de la commande que vous spécifiez pour le paramètre OPERATION. 
3. Tester le runbook dans des scénarios de démarrage et d’arrêt afin de vérifier qu’il fonctionne. 

### <a name="create-an-azure-automation-account"></a>Créer un compte Azure Automation
Si vous n’avez pas de compte Azure Automation, créez-en un en suivant les instructions indiquées dans cette étape. Pour obtenir des instructions détaillées, consultez [Créer un compte Azure Automation](../automation/automation-quickstart-create-account.md). Dans le cadre de cette étape, vous créez un compte **d’identification Azure** (principal de service dans votre annuaire Azure Active Directory), puis l’ajoutez au rôle **Contributeur** de votre abonnement Azure. Assurez-vous qu’il s’agit du même abonnement que celui contenant la fabrique de données à laquelle appartient le runtime d’intégration Azure SSIS. Azure Automation utilise ce compte pour s’authentifier auprès d’Azure Resource Manager et agir sur vos ressources. 

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Connectez-vous au [portail Azure](https://portal.azure.com/).    
3. Sélectionnez **Nouveau** dans le menu de gauche, sélectionnez **Surveillance + gestion**, puis sélectionnez **Automation**. 

    ![Nouveau -> Surveillance + gestion -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. Dans la fenêtre **Ajouter un compte Automation**, effectuez les étapes suivantes : 

    1. Spécifiez un **nom** pour le compte Automation. 
    2. Sélectionnez **l’abonnement** qui contient la fabrique de données à laquelle appartient le runtime d’intégration Azure SSIS. 
    3. Pour **Groupe de ressources**, sélectionnez **Créer** pour créer un groupe de ressources, ou sélectionnez **Existant** pour sélectionner un groupe de ressources existant. 
    4. Sélectionnez un **emplacement** pour le compte Automation. 
    5. Vérifiez que **Créer un compte d’identification** est défini sur **Oui**. Un principal de service est créé dans votre annuaire Azure Active Directory. Il est ajouté au rôle **Contributeur** de votre abonnement Azure.
    6. Sélectionnez **Épingler au tableau de bord** afin qu’il apparaisse dans le tableau de bord du portail. 
    7. Sélectionnez **Créer**. 

        ![Nouveau -> Surveillance + gestion -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Vous voyez **l’état du déploiement** sur le tableau de bord et dans les notifications. 
    
    ![Déploiement d’Automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Une fois correctement créée, la page d’accueil du compte Automation apparaît. 

    ![Page d’accueil Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Importer des modules Data Factory

1. Sélectionnez **Modules** dans la section **RESSOURCES PARTAGÉES** dans le menu de gauche, puis vérifiez si **AzureRM.Profile** et **AzureRM.DataFactoryV2** apparaissent dans la liste des modules. Si ce n’est pas le cas, sélectionnez **Parcourir la galerie** dans la barre d’outils.

    ![Page d’accueil Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-modules.png)
2. Dans la fenêtre **Parcourir la galerie**, dans la fenêtre de recherche, tapez **AzureRM.Profile**, puis appuyez sur **Entrée**. Sélectionnez **AzureRM.Profile** dans la liste. Ensuite, cliquez sur **Importer** dans la barre d’outils. 

    ![Sélectionner AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/select-azurerm-profile.png)
1. Dans la fenêtre **Importer**, sélectionnez l’option **J’accepte de mettre à jour tous les modules Azure**, puis cliquez sur **OK**.  

    ![Importer AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/import-azurerm-profile.png)
4. Fermez les fenêtres pour revenir à la fenêtre **Modules**. L’état de l’importation doit apparaître dans la liste. Sélectionnez **Actualiser** pour actualiser la liste. Patientez jusqu’à ce que la colonne **ÉTAT** ait pour valeur **Disponible**.

    ![État de l’importation](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-profile.png)
1. Répétez les étapes pour importer le module **AzureRM.DataFactoryV2**. Vérifiez que l’état de ce module est défini sur **Disponible** avant de continuer. 

    ![État d’importation final](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-datafactoryv2.png)

### <a name="create-a-powershell-runbook"></a>Créer un runbook PowerShell
La procédure suivante indique comment créer un runbook PowerShell. Le script associé au runbook démarre ou arrête un runtime d’intégration Azure SSIS en fonction de la commande que vous spécifiez pour le paramètre **OPERATION**. Cette section ne fournit pas tous les détails de la création d’un runbook. Pour plus d’informations, consultez l’article [Créer un runbook](../automation/automation-quickstart-create-runbook.md).

1. Basculez vers l’onglet **Runbooks**, puis sélectionnez **+ Ajouter un runbook** dans la barre d’outils. 

    ![Bouton Ajouter un runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Sélectionnez **Créer un runbook**, puis effectuez les étapes suivantes : 

    1. Pour **Nom**, tapez **StartStopAzureSsisRuntime**.
    2. Pour **Type de runbook**, sélectionnez **PowerShell**.
    3. Sélectionnez **Créer**.
    
        ![Bouton Ajouter un runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Copiez/collez le script suivant dans la fenêtre du script du runbook. Enregistrez, puis publiez le runbook à l’aide des boutons **Enregistrer** et **Publier** de la barre d’outils. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Add-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![Modifier un runbook PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. Testez le runbook en sélectionnant le bouton **Démarrer** dans la barre d’outils. 

    ![Bouton Démarrer le runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. Dans la fenêtre **Démarrer le runbook**, effectuez les étapes suivantes : 

    1. Pour **RESOURCEGROUPNAME**, entrez le nom du groupe de ressources contenant la fabrique de données à laquelle appartient le runtime d’intégration Azure SSIS. 
    2. Pour **DATAFACTORYNAME**, entrez le nom de la fabrique de données à laquelle appartient le runtime d’intégration Azure SSIS. 
    3. Pour **AZURESSISNAME**, entrez le nom du runtime d’intégration Azure SSIS. 
    4. Pour **OPERATION**, entrez **START** (Démarrer). 
    5. Sélectionnez **OK**.  

        ![Fenêtre Démarrer le runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. Dans la fenêtre du travail, sélectionnez la vignette **Résultat**. Dans la fenêtre de résultat du travail, attendez qu’apparaisse le message **##### Completed #####** (Terminé), après le message **##### Starting #####** (Démarrage en cours). Le démarrage d’un runtime d’intégration Azure SSIS prend 20 minutes environ. Fermez la fenêtre **Travail** et revenez à la fenêtre **Runbook**.

    ![Runtime d’intégration Azure SSIS - Démarré](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Répétez les deux étapes précédentes, mais en utilisant **STOP** (Arrêter) pour **OPERATION**. Redémarrez le runbook en sélectionnant le bouton **Démarrer** dans la barre d’outils. Spécifiez le nom du groupe de ressources, le nom de la fabrique de données et le nom du runtime d’intégration Azure SSIS. Pour **OPERATION**, entrez **STOP** (Arrêter). 

    Dans la fenêtre de résultat du travail, attendez qu’apparaisse le message **##### Completed #####** (Terminé), après le message **##### Stopping #####** (Arrêt en cours). L’arrêt d’un runtime d’intégration Azure SSIS prend moins de temps que son démarrage. Fermez la fenêtre **Travail** et revenez à la fenêtre **Runbook**.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Créer des planifications pour que le runbook démarre ou arrête le runtime d’intégration Azure SSIS
Dans la section précédente, vous avez créé un runbook Azure Automation qui peut démarrer ou arrêter un runtime d’intégration Azure SSIS. Dans cette section, vous créez deux planifications pour le runbook. Quand vous configurez la première planification, vous spécifiez START (Démarrer) pour le paramètre OPERATION. De même, quand vous configurez la seconde planification, vous spécifiez STOP (Arrêter) pour le paramètre OPERATION. Pour obtenir des instructions détaillées sur la création de planifications, consultez [Création d’une planification](../automation/automation-schedules.md#creating-a-schedule).

1. Dans la fenêtre **Runbook**, sélectionnez **Planifications**, puis sélectionnez **+ Ajouter une planification** dans la barre d’outils. 

    ![Runtime d’intégration Azure SSIS - Démarré](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. Dans la fenêtre **Planifier un runbook**, effectuez les étapes suivantes : 

    1. Sélectionnez **Associer une planification à votre runbook**. 
    2. Sélectionnez **Créer une planification**.
    3. Dans la fenêtre **Nouvelle planification**, tapez **Démarrer le runtime d’intégration tous les jours** pour **Nom**. 
    4. Dans la section **Démarrages**, pour le moment, spécifiez une heure postérieure de quelques minutes à l’heure actuelle. 
    5. Pour **Périodicité**, sélectionnez **Récurrent**. 
    6. Dans la section **Tous les**, sélectionnez **Jour**. 
    7. Sélectionnez **Créer**. 

        ![Planifier le démarrage du runtime d’intégration Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Basculez vers l’onglet **Paramètres et valeurs pour l’exécution**. Spécifiez le nom du groupe de ressources, le nom de la fabrique de données et le nom du runtime d’intégration Azure SSIS. Pour **OPERATION**, entrez **START** (Démarrer). Sélectionnez **OK**. Resélectionnez **OK** pour voir la planification dans la page **Planifications** du runbook. 

    ![Planifier le démarrage du runtime d’intégration Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Répétez les deux étapes précédentes pour créer une planification nommée **Arrêter le runtime d’intégration tous les jours**. Cette fois, spécifiez une heure postérieure d’au moins 30 minutes à l’heure que vous avez spécifiée pour la planification **Démarrer le runtime d’intégration tous les jours**. Pour **OPERATION**, spécifiez **STOP** (Arrêter). 
5. Dans la fenêtre **Runbook**, sélectionnez **Travaux** dans le menu de gauche. Vous devez voir les travaux créés par les planifications aux heures spécifiées et leurs états. Vous pouvez afficher les détails sur le travail, tels que sa sortie, similaire à celle que vous avez obtenue quand vous avez testé le runbook. 

    ![Planifier le démarrage du runtime d’intégration Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Une fois le test terminé, désactivez les planifications en les modifiant et en sélectionnant **NON** pour **Activé**. Sélectionnez **Planifications** dans le menu de gauche, sélectionnez **Démarrer le runtime d’intégration tous les jours/Arrêter le runtime d’intégration tous les jours**, puis sélectionnez **Non** pour **Activé**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Créer des Webhooks pour démarrer et arrêter le runtime d’intégration Azure SSIS
Suivez les instructions indiquées dans [Création d’un webhook](../automation/automation-webhooks.md#creating-a-webhook) pour créer deux Webhooks pour le runbook. Pour le premier, spécifiez START (Démarrer) pour OPERATION, et STOP (Arrêter) pour le second. Enregistrez les URL des deux Webhooks quelque part (par exemple, dans un fichier texte ou un bloc-notes OneNote). Vous utilisez ces URL au moment de configurer les activités web dans le pipeline Data Factory. L’illustration suivante montre un exemple de création d’un Webhook qui démarre le runtime d’intégration Azure SSIS :

1. Dans la fenêtre **Runbook**, sélectionnez **Webhooks** dans le menu de gauche, puis sélectionnez **+ Ajouter un Webhook** dans la barre d’outils. 

    ![Webhooks -> Ajouter un Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. Dans la fenêtre **Ajouter un Webhook**, sélectionnez **Créer un Webhook**, puis effectuez les actions suivantes : 

    1. Pour **Nom**, entrez **StartAzureSsisIR**. 
    2. Vérifiez que le paramètre **Activé** a la valeur **Oui**. 
    3. Copiez **l’URL** et enregistrez-la quelque part. Cette étape est importante. Vous ne verrez plus l’URL. 
    4. Sélectionnez **OK**. 

        ![Fenêtre de création d’un Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Basculez vers l’onglet **Paramètres et valeurs pour l’exécution**. Spécifiez le nom du groupe de ressources, le nom de la fabrique de données et le nom du runtime d’intégration Azure SSIS. Pour **OPERATION**, entrez **START** (Démarrer). Cliquez sur **OK**. Cliquez sur **Créer**. 

    ![Webhook - Paramètres et paramètres d’exécution](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Répétez les trois étapes précédentes pour créer un autre Webhook nommé **StopAzureSsisIR**. N’oubliez pas de copier l’URL. Quand vous spécifiez les paramètres et les paramètres d’exécution, entrez **STOP** (Arrêter) pour **OPERATION**. 

Vous devez avoir deux URL, une pour le Webhook **StartAzureSsisIR** et l’autre pour le Webhook **StopAzureSsisIR**. Vous pouvez envoyer une requête HTTP POST à ces URL pour démarrer ou arrêter votre runtime d’intégration Azure SSIS. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Créer et planifier un pipeline Data Factory qui démarre/arrête le runtime d’intégration
Cette section montre comment utiliser une activité web pour appeler les Webhooks que vous avez créés dans la section précédente.

Le pipeline que vous créez se compose de trois activités. 

1. La première activité **Web** appelle le premier Webhook pour démarrer le runtime d’intégration Azure SSIS. 
2. L’activité **Procédure stockée** exécute un script SQL qui exécute le package SSIS. La seconde activité **Web** arrête le runtime d’intégration Azure SSIS. Pour plus d’informations sur l’appel d’un package SSIS à partir d’un pipeline Data Factory à l’aide de l’activité Procédure stockée, consultez [Appeler un package SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). 
3. La seconde activité **Web** appelle le Webhook pour arrêter le runtime d’intégration Azure SSIS. 

Après avoir créé et testé le pipeline, vous créez un déclencheur de planification et l’associez au pipeline. Le déclencheur de planification définit une planification pour le pipeline. Supposons que vous créez un déclencheur qui est planifié pour s’exécuter tous les jours à 23h00. Le déclencheur exécute le pipeline à 23h00 chaque jour. Le pipeline démarre le runtime d’intégration Azure SSIS, exécute le package SSIS, puis arrête le runtime d’intégration Azure SSIS. 

### <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Connectez-vous au [portail Azure](https://portal.azure.com/).    
2. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
   ![Nouveau -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Sur la page **Nouvelle fabrique de données**, saisissez **ADFTutorialDataFactory** comme **nom**. 
      
     ![Page Nouvelle fabrique de données](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, votrenomMyAzureSsisDataFactory), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :
     
      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
      Pour plus d'informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md).  
4. Sélectionnez **V2 (préversion)** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge pour la création de fabriques de données sont affichés dans la liste.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.
8. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.
   
   ![Page d'accueil Data Factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Cliquez sur **Créer et surveiller** pour lancer l’interface utilisateur (IU) de Data Factory dans un onglet séparé.

### <a name="create-a-pipeline"></a>Créer un pipeline

1. Dans la page **Bien démarrer**, cliquez sur **Créer un pipeline**. 

   ![Page de prise en main](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. Dans la boîte à outils **Activités**, développez **Général**, puis glissez-déposez l’activité **Web** vers la surface du concepteur de pipeline. Sous l’onglet **Général** de la fenêtre **Propriétés**, remplacez le nom de l’activité par **StartIR**.

   ![Première activité Web - Onglet Général](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Passez dans l’onglet **Paramètres** de la fenêtre **Propriétés**, puis effectuez les actions suivantes : 

    1. Pour **URL**, collez l’URL du Webhook qui démarre le runtime d’intégration Azure SSIS. 
    2. Pour **Méthode**, sélectionnez **POST**. 
    3. Pour **Corps**, entrez `{"message":"hello world"}`. 
   
        ![Première activité Web - Onglet Paramètres](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)
5. Glissez-déposez l’activité Procédure stockée depuis la section **Général** de la boîte à outils **Activités**. Nommez l’activité **RunSSISPackage**. 
6. Passez dans l’onglet **Compte SQL** de la fenêtre **Propriétés**. 
7. Pour **Service lié**, cliquez sur **+ Nouveau**.
8. Dans la fenêtre **Nouveau service lié**, effectuez les actions suivantes : 

    1. Sélectionnez **Azure SQL Database** comme **Type**.
    2. Dans le champ **Nom du serveur**, sélectionnez votre serveur SQL Azure qui héberge la base de données **SSISDB**. Le processus de provisionnement du runtime d’intégration Azure SSIS crée un catalogue SSIS (base de données SSISDB) sur le serveur SQL Azure que vous spécifiez.
    3. Sélectionnez **SSISDB** comme **Nom de la base de données**.
    4. Dans **Nom d’utilisateur**, entrez le nom de l’utilisateur qui a accès à la base de données.
    5. Dans **Mot de passe**, entrez le mot de passe de l’utilisateur. 
    6. Testez la connexion à la base de données en cliquant sur le bouton **Tester la connexion**.
    7. Enregistrez le service lié en cliquant sur le bouton **Enregistrer**.
9. Dans la fenêtre **Propriétés**, basculez vers l’onglet **Procédure stockée** à partir de l’onglet **Compte SQL** et effectuez les étapes suivantes : 

    1. Pour **Nom de la procédure stockée**, sélectionnez **Modifier**, puis entrez **sp_executesql**. 
    2. Sélectionnez **+ Nouveau** dans la section **Paramètres de procédure stockée**. 
    3. Comme **Nom** du paramètre, entrez **stmt**. 
    4. Comme **Type** de paramètre, entrez **String**. 
    5. Comme **Valeur** du paramètre, entrez la requête SQL suivante :

        Dans la requête SQL, spécifiez les valeurs correctes pour les paramètres **folder_name**, **project_name** et **package_name**. 

        ```sql
        DECLARE       @return_value int, @exe_id bigint, @err_msg nvarchar(150)

        -- Wait until Azure-SSIS IR is started
        WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
        BEGIN
            WAITFOR DELAY '00:00:01';
        END

        EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
            @project_name=N'YourProject', @package_name=N'YourPackage',
            @use32bitruntime=0, @runincluster=1, @useanyworker=1,
            @execution_id=@exe_id OUTPUT 

        EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

        EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

        -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/failed (4)/
        -- pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
        IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
        BEGIN
            SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
            RAISERROR(@err_msg, 15, 1)
        END

        ```
10. Connectez l’activité **Web** à l’activité **Procédure stockée**. 

    ![Connecter les activités Web et Procédure stockée](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

11. Glissez-déposez une autre activité **Web** à droite de l’activité **Procédure stockée**. Nommez l’activité **StopIR**. 
12. Passez dans l’onglet **Paramètres** de la fenêtre **Propriétés**, puis effectuez les actions suivantes : 

    1. Pour **URL**, collez l’URL du Webhook qui arrête le runtime d’intégration Azure SSIS. 
    2. Pour **Méthode**, sélectionnez **POST**. 
    3. Pour **Corps**, entrez `{"message":"hello world"}`.  
4. Connectez l’activité **Procédure stockée** à la dernière activité **Web**.

    ![Pipeline complet](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
5. Validez les paramètres du pipeline en cliquant sur **Valider** dans la barre d’outils. Fermez la fenêtre **Rapport de validation de pipeline** en cliquant sur le bouton **>>**. 

    ![Valider le pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Série de tests du pipeline

1. Sélectionnez **Série de tests** dans la barre d’outils du pipeline. Le résultat apparaît dans la fenêtre **Résultat** dans le volet inférieur. 

    ![Série de tests](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. Dans la page **Runbook** de votre compte Azure Automation, vous pouvez vérifier que les travaux se sont exécutés pour démarrer et arrêter le runtime d’intégration Azure SSIS. 

    ![Travaux de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Lancez SQL Server Management Studio. Dans la fenêtre **Se connecter au serveur**, effectuez les actions suivantes : 

    1. Pour **Nom du serveur**, spécifiez **&lt;votre base de données SQL Azure&gt;.database.windows.net**.
    2. Sélectionnez **Options >>**.
    3. Pour **Se connecter à la base de données**, sélectionnez **SSISDB**.
    4. Sélectionnez **Connecter**. 
    5. Développez **Catalogues Integration Services** -> **SSISDB** -> Votre dossier -> **Projets** -> Votre projet SSIS -> **Packages**. 
    6. Cliquez avec le bouton droit sur votre package SSIS, puis sélectionnez **Rapports** -> **Rapports standard** -> **Toutes les exécutions**. 
    7. Vérifiez que le package SSIS s’est exécuté. 

        ![Vérifier l’exécution du package SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Planifier le pipeline 
Le pipeline fonctionnant comme prévu, vous pouvez créer un déclencheur pour l’exécuter à une cadence spécifiée. Pour plus d’informations sur l’association d’un déclencheur de planification à un pipeline, consultez [Déclencher le pipeline selon une planification](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Dans la barre d’outils du pipeline, sélectionnez **Déclencheur**, puis **Nouveau/Modifier**. 

    ![Déclencheur -> Nouveau/Modifier](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. Dans la fenêtre **Ajouter des déclencheurs**, sélectionnez **+ Nouveau**.

    ![Ajouter des déclencheurs - Nouveau](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. Dans la fenêtre **Nouveau déclencheur**, effectuez les actions suivantes : 

    1. Pour **Nom**, spécifiez un nom pour le déclencheur. Dans l’exemple suivant, **Run daily** est le nom du déclencheur. 
    2. Pour **Type**, sélectionnez **Planification**. 
    3. Pour **Date de début**, sélectionnez une date et une heure de début. 
    4. Pour **Périodicité**, spécifiez la cadence du déclencheur. Dans l’exemple suivant, la cadence est quotidienne, une fois. 
    5. Pour **Fin**, vous pouvez spécifier la date et l’heure en sélectionnant l’option **À la date**. 
    6. Sélectionnez **Activé**. Le déclencheur est activé dès que vous publiez la solution sur la fabrique de données. 
    7. Sélectionnez **Suivant**.

        ![Déclencheur -> Nouveau/Modifier](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. Dans la page **Paramètres d’exécution du déclencheur**, lisez l’avertissement, puis cliquez sur **Terminer**. 
5. Publiez la solution sur la fabrique de données en sélectionnant **Publier tout** dans le volet gauche. 

    ![Publier tout](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. Pour surveiller les exécutions du déclencheur et du pipeline, utilisez l’onglet **Surveiller** sur la gauche. Pour obtenir des instructions détaillées, consultez [Surveiller le pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Exécutions de pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. Pour voir les exécutions d’activité associées à l’exécution d’un pipeline, sélectionnez le premier lien (**Voir les exécutions d’activités**) dans la colonne **Actions**. Affichez les trois exécutions d’activités associées à chaque activité dans le pipeline (première activité Web, activité Procédure stockée et deuxième activité Web). Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Pipelines** en haut.

    ![Exécutions d’activités](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. Vous pouvez également afficher les exécutions de déclencheur en sélectionnant **Exécutions du déclencheur** dans la liste déroulante en regard **d’Exécutions du pipeline** en haut. 

    ![Exécutions de déclencheur](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants de la documentation relative à SSIS : 

- [Déployer, exécuter et surveiller un package SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter au catalogue SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Planifier l’exécution des packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)