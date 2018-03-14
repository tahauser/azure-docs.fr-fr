---
title: "Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment créer un runtime d’intégration Azure-SSIS afin de pouvoir exécuter un package SSIS dans le cloud Azure."
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 879489dffbf713b5fadb72a58638e462938aaf26
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory
Cet article explique pas à pas comment configurer un runtime d’intégration Azure-SSIS dans Azure Data Factory. Vous pouvez ensuite utiliser SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer des packages SQL Server Integration Services (SSIS) sur ce runtime dans Azure.

Le [Didacticiel : déployer des packages SQL Server Integration Services (SSIS) pour Azure](tutorial-create-azure-ssis-runtime-portal.md) montre comment créer un runtime d’intégration Azure-SSIS à l’aide d’Azure SQL Database comme magasin pour le catalogue SSIS. Cet article s’appuie sur le didacticiel et vous montre comment effectuer les opérations suivantes : 

- Utiliser une instance gérée d’Azure SQL (préversion privée) pour l’hébergement d’un catalogue SSIS (base de données SSISDB).
- Joindre une IR Azure SSIS à un réseau virtuel (VNet) Azure. Pour obtenir des informations conceptuelles sur la jonction d’une IR Azure SSIS à un réseau virtuel et la configuration d’un réseau virtuel dans le portail Azure, consultez l’article [Joindre une IR Azure SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).


## <a name="overview"></a>Vue d'ensemble
Cet article présente différentes façons de provisionner un runtime d’intégration Azure-SSIS :

- [Portail Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Modèle Azure Resource Manager](#azure-resource-manager-template)

Quand vous créez un runtime d’intégration Azure-SSIS, Data Factory se connecte à votre base de données SQL Azure pour préparer la base de données du catalogue SSIS (SSISDB). Le script configure les autorisations et les paramètres pour votre réseau virtuel, s’il est spécifié, et relie la nouvelle instance du runtime d’intégration Azure-SSIS au réseau virtuel.

Si vous approvisionnez une instance d’un runtime d’intégration Azure-SSIS, les composants Azure Feature Pack pour SSIS et Access Redistributable sont également installés. Ces composants fournissent la connectivité aux fichiers Excel et Access et à diverses sources de données Azure, en plus des sources de données prises en charge par les composants intégrés. Vous ne pouvez pas installer de composants tiers pour SSIS à l’heure actuelle (notamment des composants tiers de Microsoft, tels que les composants Oracle et Teradata par Attunity et les composants BI SAP).

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure**. Si vous ne disposez d’aucun abonnement, vous pouvez créer un compte d’[essai gratuit](http://azure.microsoft.com/pricing/free-trial/).
- Un **serveur de base de données SQL Azure** ou une **instance gérée SQL Server (préversion privée) (préversion privée étendue)**. Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Ce serveur héberge la base de données du catalogue SSIS (SSISDB). Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux d’exécution dans SSISDB sans dépasser les régions Azure. Notez le niveau tarifaire de votre serveur SQL Azure. Pour obtenir la liste des niveaux tarifaires pris en charge pour Azure SQL Database, consultez [Limites de ressources pour SQL Database](../sql-database/sql-database-resource-limits.md).

    Vérifiez que votre serveur Azure SQL Database ou SQL Server Managed Instance (préversion privée étendue) n’a pas de catalogue SSIS (base de données SSIDB). Le provisionnement du runtime d’intégration Azure-SSIS ne prend pas en charge l’utilisation d’un catalogue SSIS existant.
- **Réseau virtuel Azure Resource Manager ou classique (facultatif)**. Vous devez disposer d’un réseau virtuel (VNet) Azure si au moins une des conditions suivantes est vraie :
    - Vous hébergez la base de données du catalogue SSIS sur une instance SQL Server gérée (préversion privée) qui fait partie d’un réseau virtuel.
    - Vous souhaitez vous connecter à des magasins de données sur site à partir de packages SSIS en cours d’exécution sur un runtime d’intégration Azure-SSIS.
- **Azure PowerShell**. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps). Vous utilisez PowerShell pour exécuter un script afin de configurer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans le cloud. 

> [!NOTE]
> - Vous pouvez créer une fabrique de données de version 2 dans les régions suivantes : Est des États-Unis, Est des États-Unis 2, Asie du Sud-Est et Europe de l’Ouest. 
> - Vous pouvez créer un runtime d’intégration Azure-SSIS dans les régions suivantes : Est des États-Unis, Est des États-Unis 2, Centre des États-Unis, Europe du Nord, Europe de l’Ouest et Est de l’Australie.

## <a name="azure-portal"></a>Portail Azure
Dans cette section, vous utilisez le portail Azure, plus précisément l’interface utilisateur de Data Factory, pour créer un runtime d’intégration Azure-SSIS. 

### <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Connectez-vous au [portail Azure](https://portal.azure.com/).    
3. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 
   
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

### <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure SSIS

1. Dans la page de prise en main, cliquez sur la vignette **Configurer un runtime d’intégration SSIS**. 

   ![Vignette Configurer un runtime d’intégration SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Sur la page **Paramètres généraux** de **Configuration du runtime d’intégration**, procédez comme suit : 

   ![Paramètres généraux :](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Spécifiez le **nom** du runtime d’intégration.
    2. Sélectionnez l’**emplacement** du runtime d’intégration. Seuls les emplacements pris en charge sont affichés.
    3. Sélectionnez la **taille du nœud** devant être configuré avec le runtime SSIS.
    4. Spécifiez le **nombre de nœuds** dans le cluster.
    5. Cliquez sur **Suivant**. 
1. Dans les **Paramètres SQL**, suivez les étapes suivantes : 

    ![Paramètres SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Spécifiez l’**abonnement** Azure comprenant le serveur SQL Azure. 
    2. Sélectionnez votre serveur SQL Azure comme **point de terminaison de serveur de base de données du catalogue**.
    3. Saisissez le nom d’**administrateur**.
    4. Saisissez le **mot de passe** d’administrateur.  
    5. Sélectionnez le **niveau de service** pour la base de données SSISDB. La valeur par défaut est De base.
    6. Cliquez sur **Suivant**. 
1.  Sur la page **Paramètres avancés**, sélectionnez une valeur pour **Exécutions parallèles maximales par nœud**.   

    ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Cette étape est **facultative**. Si vous disposez d’un réseau virtuel (classique ou Azure Resource Manager) auquel vous souhaitez joindre le runtime d’intégration, sélectionnez l’option **Sélectionner un réseau virtuel auquel joindre le runtime d’intégration Azure-SSIS et autoriser les services Azure à configurer les paramètres/autorisations du réseau virtuel** et procédez comme suit : 

    ![Paramètres avancés avec un réseau virtuel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Pour **Abonnement**, indiquez l’**abonnement** comprenant le réseau virtuel. 
    2. Pour type, indiquez le **type** de réseau virtuel (classique ou Azure Resource Manager). 
    3. Pour **Nom du réseau virtuel**, sélectionnez le nom de votre **réseau virtuel**. 
    4. Pour **Nom du sous-réseau**, sélectionnez le nom du **sous-réseau** dans le réseau virtuel.
1. Cliquez sur **Terminer** pour démarrer la création du runtime d’intégration Azure-SSIS. 

    > [!IMPORTANT]
    > - Ce processus prend environ 20 minutes
    > - Le service Data Factory se connecte à votre base de données SQL Azure pour préparer la base de données du catalogue SSIS (SSISDB). Le script configure les autorisations et les paramètres pour votre réseau virtuel, s’il est spécifié, et relie la nouvelle instance du runtime d’intégration Azure-SSIS au réseau virtuel.
7. Dans la fenêtre **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Cliquez sur **Actualiser** pour actualiser l’état. 

    ![État de la création](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Utilisez les liens sous la colonne **Actions** pour arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté. 

    ![Runtime d’intégration Azure SSIS - actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtimes d’intégration Azure SSIS sur le portail

1. Dans l’interface utilisateur de Azure Data Factory, basculez vers l’onglet **Modifier**, cliquez sur **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration** pour afficher les runtimes d’intégration existants dans votre Data Factory. 
    ![Afficher les runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Cliquez sur **Nouveau** pour créer un nouveau runtime d’intégration Azure SSIS. 

    ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Pour créer un runtime d’intégration Azure-SSIS, cliquez sur **Nouveau** comme indiqué sur l’image. 
3. Dans la fenêtre de configuration de runtime d’intégration, sélectionnez **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis cliquez sur **Suivant**.

    ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Consultez la section [Approvisionner un runtime d’intégration Azure SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS.

## <a name="azure-powershell"></a>Azure PowerShell
Dans cette section, vous utilisez Azure PowerShell pour créer un runtime d’intégration Azure-SSIS.

### <a name="create-variables"></a>Créer des variables
Définissez des variables à utiliser dans le script de ce didacticiel :

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"

# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, North Europe, West Europe, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Se connecter et sélectionner un abonnement
Ajoutez le code suivant au script pour vous connecter et sélectionner votre abonnement Azure : 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Valider la connexion à la base de données
Ajoutez le script suivant pour valider votre point de terminaison de serveur de base de données Azure SQL server.database.windows.net ou d’instance gérée Azure SQL (préversion privée). 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

### <a name="configure-virtual-network"></a>Configurer un réseau virtuel
Ajoutez le script suivant afin de configurer automatiquement les paramètres/autorisations du réseau virtuel pour le runtime d’intégration Azure-SSIS.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Créer une fabrique de données
Exécutez la commande suivante pour créer la fabrique de données :

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Créer un runtime d’intégration
Exécutez la commande suivante pour créer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans Azure : utilisez le script de la section en fonction du type de base de données (Azure SQL Database ou Azure SQL Managed Instance (version préliminaire privée)) que vous utilisez. 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Azure SQL Database pour héberger la base de données SSISDB (catalogue SSIS) 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode
```

Vous n’avez pas besoin de valider des valeurs pour VNetId et Subnet, sauf si vous avez besoin d’un accès aux données locales, autrement dit, si vous avez des sources de données/destinations de données locales dans vos packages SSIS. Vous devez valider la valeur du paramètre CatalogPricingTier. Pour obtenir la liste des niveaux tarifaires pris en charge pour Azure SQL Database, consultez [Limites de ressources pour SQL Database](../sql-database/sql-database-resource-limits.md).

#### <a name="azure-sql-managed-instance-private-preview-to-host-the-ssisdb-database"></a>Azure SQL Managed Instance (préversion privée) pour héberger la base de données SSISDB

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Vous devez valider les valeurs des paramètres VnetId et Subnet avec l’instance gérée d’Azure SQL (préversion privée) jointe à un réseau virtuel. Le paramètre CatalogPricingTier ne s’applique pas pour l’instance de gérée d’Azure SQL. 

### <a name="start-integration-runtime"></a>Démarrer le runtime d’intégration
Exécutez la commande suivante pour démarrer le runtime d’intégration Azure-SSIS : 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Cette commande dure de **20 à 30 minutes**. 


### <a name="full-script"></a>Script complet
Voici le script complet qui crée un runtime d’intégration Azure-SSIS et le joint à un réseau virtuel. Ce script suppose que vous utilisez Azure SQL Managed Instance (MI) pour héberger le catalogue SSIS. 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
Dans cette section, vous utilisez un modèle Azure Resource Manager pour créer un runtime d’intégration Azure-SSIS. Voici un exemple de procédure : 

1. Créez un fichier JSON avec le modèle Resource Manager suivant. Remplacez les valeurs entre chevrons (espaces réservés) par vos propres valeurs. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Pour déployer le modèle Resource Manager, exécutez la commande New-AzureRmResourceGroupDeployment comme indiqué dans l’exemple suivant. Dans cet exemple, ADFTutorialResourceGroup est le nom du groupe de ressources. ADFTutorialARM.json est le fichier qui contient la définition JSON pour la fabrique de données et le runtime d’intégration Azure-SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Cette commande crée la fabrique de données et crée un runtime d’intégration Azure-SSIS dans cette fabrique, mais elle ne démarre pas le runtime d’intégration. 
3. Pour démarrer le runtime d’intégration Azure-SSIS, exécutez la commande Start-AzureRmDataFactoryV2IntegrationRuntime : 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS
Utilisez maintenant SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer vos packages SSIS dans Azure. Connectez-vous à votre serveur SQL Azure qui héberge le catalogue SSIS (SSISDB). Le nom du serveur SQL Azure est au format : &lt;nom_serveur&gt;.database.windows.net (pour la base de données SQL Azure). Consultez l’article [Déployer des packages](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) pour obtenir des instructions. 

## <a name="next-steps"></a>étapes suivantes
Consultez les autres rubriques relatives au runtime d’intégration Azure SSIS dans cette documentation :

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, y compris sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment le faire évoluer en lui ajoutant des nœuds supplémentaires. 
- [Join an Azure-SSIS IR to a VNet](join-azure-ssis-integration-runtime-virtual-network.md) (Attacher un runtime d’intégration Azure-SSIS à un VNet). Cet article fournit des informations conceptuelles sur la façon d’attacher un runtime d’intégration Azure-SSIS à un réseau virtuel Azure (VNet). Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 