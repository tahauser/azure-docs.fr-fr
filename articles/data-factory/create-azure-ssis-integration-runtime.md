---
title: "Créer un runtime d’intégration autohébergé dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l'activité de procédure stockée SQL Server pour appeler une procédure stockée dans une base de données SQL Azure ou un entrepôt Azure SQL Data Warehouse à partir d'un pipeline Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: fcd2547112eb966420f33cec4939c83606029444
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory
Cet article explique pas à pas comment configurer un runtime d’intégration Azure-SSIS dans Azure Data Factory. Vous pouvez ensuite utiliser SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer des packages SQL Server Integration Services (SSIS) sur ce runtime dans Azure.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

Le didacticiel : [Didacticiel : déployer des packages SQL Server Integration Services (SSIS) pour Azure](tutorial-deploy-ssis-packages-azure.md) vous montre comment créer un runtime d’intégration (IR) à l’aide d’Azure SQL Database comme magasin pour le catalogue SSIS. Cet article s’appuie sur le didacticiel et vous montre comment effectuer les opérations suivantes : 

- Utiliser une instance gérée d’Azure SQL (préversion privée) pour l’hébergement d’un catalogue SSIS (base de données SSISDB).
- Joindre une IR Azure SSIS à un réseau virtuel (VNet) Azure. 

Pour obtenir des informations conceptuelles sur la jonction d’une IR Azure SSIS à un réseau virtuel et la configuration d’un réseau virtuel dans le portail Azure, consultez l’article [Joindre une IR Azure SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). 

## <a name="prerequisites"></a>Composants requis

- **Abonnement Azure**. Si vous ne disposez d’aucun abonnement, vous pouvez créer un compte d’[essai gratuit](http://azure.microsoft.com/pricing/free-trial/).
- Un **serveur de base de données SQL Azure** ou une **instance gérée SQL Server (préversion privée) (préversion privée étendue)**. Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Ce serveur héberge la base de données du catalogue SSIS (SSISDB). Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux d’exécution dans SSISDB sans dépasser les régions Azure. Notez le niveau tarifaire de votre serveur SQL Azure. Pour obtenir la liste des niveaux tarifaires pris en charge pour Azure SQL Database, consultez [Limites de ressources pour SQL Database](../sql-database/sql-database-resource-limits.md).
- **Réseau virtuel (VNet) Classic (facultatif)**. Vous devez disposer d’un réseau virtuel (VNet) Azure si au moins une des conditions suivantes est vraie :
    - Vous hébergez la base de données du catalogue SSIS sur une instance SQL Server gérée (préversion privée) qui fait partie d’un réseau virtuel.
    - Vous souhaitez vous connecter à des magasins de données sur site à partir de packages SSIS en cours d’exécution sur un runtime d’intégration Azure-SSIS.
- **Azure PowerShell**. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps). Vous utilisez PowerShell pour exécuter un script afin de configurer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans le cloud. 

> [!NOTE]
> Pour obtenir la liste des régions prises en charge par Azure Data Factory version 2 et Azure-SSIS Integration Runtime, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/). Développez **Données + Analytique** pour afficher **Data Factory V2** et **SSIS Integration Runtime**.


## <a name="create-variables"></a>Créer des variables
Définissez des variables à utiliser dans le script de ce didacticiel :

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
$AzureSSISNodeSize = "Standard_A4_v2" # In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeNumber = 2 # In public preview, only 1-10 nodes are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 # In public preview, only 1-8 parallel executions per node are supported.

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## Remove these two variables if you are using Azure SQL Database. 
## These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In public preview, only classic virtual network (VNet) is supported.
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In public preview, only classic VNet is supported.

```

## <a name="validate-the-connection-to-database"></a>Valider la connexion à la base de données
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

## <a name="log-in-and-select-subscription"></a>Se connecter et sélectionner un abonnement
Ajoutez le code suivant au script pour vous connecter et sélectionner votre abonnement Azure : 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel
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
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Créer une fabrique de données
Exécutez la commande suivante pour créer la fabrique de données :

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Créer un runtime d’intégration
Exécutez la commande suivante pour créer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans Azure : 

Si vous utilisez **Azure SQL Database** pour héberger la base de données SSISDB (catalogue SSIS) : 


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

Si vous utilisez **Azure SQL Managed Instance (préversion privée)** pour héberger la base de données SSISDB :

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

## <a name="start-integration-runtime"></a>Démarrer le runtime d’intégration
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

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS
Utilisez maintenant SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer vos packages SSIS dans Azure. Connectez-vous à votre serveur SQL Azure qui héberge le catalogue SSIS (SSISDB). Le nom du serveur SQL Azure est au format : &lt;nom_serveur&gt;.database.windows.net (pour la base de données SQL Azure). Consultez l’article [Déployer des packages](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) pour obtenir des instructions. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres rubriques relatives au runtime d’intégration Azure SSIS dans cette documentation :

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, y compris sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : deploy SSIS packages to Azure](tutorial-deploy-ssis-packages-azure.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment le faire évoluer en lui ajoutant des nœuds supplémentaires. 
- [Join an Azure-SSIS IR to a VNet](join-azure-ssis-integration-runtime-virtual-network.md) (Attacher un runtime d’intégration Azure-SSIS à un VNet). Cet article fournit des informations conceptuelles sur la façon d’attacher un runtime d’intégration Azure-SSIS à un réseau virtuel Azure (VNet). Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 