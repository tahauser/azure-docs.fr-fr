---
title: "Comment joindre un runtime d’intégration Azure-SSIS à un VNET | Microsoft Docs"
description: "Découvrez comment joindre un runtime d’intégration Azure-SSIS à un réseau virtuel Azure."
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
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: f40f0551ed65a42bcacf2307cbec462fd5c3ac25
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel
Vous devez joindre le runtime d’intégration Azure-SSIS (IR) à un réseau virtuel Azure (VNet) si l’une des conditions suivantes est satisfaite : 

- Vous hébergez la base de données du catalogue SSIS sur une instance SQL Server Managed (préversion privée) qui fait partie d’un réseau virtuel.
- Vous souhaitez vous connecter à des magasins de données sur site à partir de packages SSIS en cours d’exécution sur un runtime d’intégration Azure-SSIS.

 Azure Data Factory version 2 (préversion) vous permet de joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel classique ou Azure Resource Manager. 

 > [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Accéder aux banques de données locales
Si les packages SSIS accèdent uniquement aux banques de données cloud publiques, vous n’avez pas besoin de joindre le runtime d’intégration Azure-SSIS à un réseau virtuel. Si les packages SSIS accèdent aux banques de données locales, vous devez joindre le runtime d’intégration Azure-SSIS à un réseau virtuel qui est connecté au réseau local. Si le catalogue SSIS est hébergé dans Azure SQL Database qui ne se trouve pas dans le réseau virtuel, vous devez ouvrir les ports appropriés. Si le catalogue SSIS est hébergé dans une instance Azure SQL Managed qui se trouve dans un réseau virtuel classique ou Azure Resource Manager, vous pouvez joindre le runtime d’intégration Azure-SSIS à ce même réseau (ou) à un autre réseau virtuel à condition qu’il possède une connexion de type VNet vers VNet avec celui sur lequel l’instance Azure SQL Managed Instance est installée. Pour plus d’informations, lisez les sections suivantes.

Voici quelques points importants à prendre en compte : 

- S’il n’existe pas encore de réseau virtuel connecté à votre réseau local, vous devez commencer par créer un [réseau virtuel Azure Resource Manager](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vnet) ou un [réseau virtuel classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une [connexion de passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) de site à site à partir de ce réseau virtuel, vers votre réseau local.
- S’il existe déjà un réseau virtuel Azure Resource Manager ou un réseau virtuel classique connecté à votre réseau local, au même emplacement que votre runtime d’intégration Azure-SSIS, vous pouvez joindre ce dernier.
- S’il existe déjà un réseau virtuel classique connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS, vous devez commencer par créer un [réseau virtuel classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une connexion du type [réseau virtuel classique vers classique](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). Vous pouvez également créer un [réseau virtuel Azure Resource Manager](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vnet) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une connexion du type [réseau virtuel classique vers Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
- S’il existe déjà un réseau virtuel Azure Resource Manager connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS, vous devez commencer par créer un [réseau virtuel Azure Resource Manager](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vnet) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une connexion du type réseau virtuel Azure Resource Manager vers Azure Resource Manager. Vous pouvez également créer un [réseau virtuel classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pour le runtime d’intégration Azure-SSIS à joindre. Ensuite, configurez une connexion du type [réseau virtuel classique vers Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Serveur Domain Name Services (DNS) 
Si vous avez besoin d’utiliser votre propre serveur Domain Name Services (DNS) dans un réseau virtuel joint par votre runtime d’intégration Azure-SSIS, suivez les instructions pour [vérifier que les nœuds de votre runtime d’intégration Azure-SSIS dans le réseau virtuel peuvent résoudre les points de terminaison Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Groupe de sécurité réseau
Si vous avez besoin d’implémenter un groupe de sécurité réseau (NSG) dans un réseau virtuel joint par votre runtime d’intégration Azure-SSIS, autorisez le trafic entrant et sortant via les ports suivants :

| Ports | Direction | Protocole de transfert | Objectif | Destination source entrante/sortant |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | Trafic entrant | TCP | Les services Azure utilisent ces ports pour communiquer avec les nœuds de votre runtime d’intégration Azure-SSIS dans le réseau virtuel. | Internet | 
| 443 | Règle de trafic sortant | TCP | Les nœuds de votre runtime d’intégration Azure-SSIS dans le réseau virtuel utilisent ce port pour accéder aux services Azure, par exemple le stockage Azure, le hub d’événements, etc. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Règle de trafic sortant | TCP | Les nœuds de votre runtime d’intégration Azure-SSIS dans le réseau virtuel utilisent ces ports pour accéder à la base de données SSISDB hébergée par le serveur Azure SQL Database (ne s’applique pas à la base de données SSISDB hébergée par une instance Azure SQL Managed). | Internet | 

## <a name="configure-vnet"></a>Configurer le réseau virtuel
Vous devez d’abord configurer le réseau virtuel à l’aide d’une des manières suivantes (script ou portail Azure) pour attacher un runtime d’intégration Azure-SSIS au réseau virtuel. 

### <a name="script-to-configure-vnet"></a>Script de configuration du réseau virtuel 
Ajoutez le script suivant afin de configurer automatiquement les paramètres/autorisations du réseau virtuel pour le runtime d’intégration Azure-SSIS et l’attacher au réseau virtuel.

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

### <a name="use-portal-to-configure-a-classic-vnet"></a>Utiliser le portail pour configurer un réseau virtuel classique
Pour configurer le réseau virtuel, le plus simple consiste à exécuter le script. Si vous ne bénéficiez d’aucun accès pour configurer le réseau virtuel ou si la configuration automatique échoue, le propriétaire de ce réseau virtuel ou vous pouvez tenter une configuration manuelle en suivant les étapes ci-après :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels (classiques)**.
3. Filtrez et sélectionnez votre **réseau virtuel** dans la liste. 
4. Dans la page des réseaux virtuels (classiques) sélectionnez **Propriétés**. 

    ![ID de ressource de réseau virtuel classique](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Cliquez sur le bouton Copier au niveau de **ID DE RESSOURCE** pour copier l’ID de ressource du réseau classique dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers.
6. Cliquez sur **Sous-réseaux** dans le menu de gauche, puis vérifiez que le nombre des **adresses disponibles** est bien supérieur à celui des nœuds dans votre runtime d’intégration Azure-SSIS.

    ![Nombre d’adresses disponibles dans le réseau virtuel](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Joignez **MicrosoftAzureBatch** au rôle **Contributeur de machines virtuelles classiques** pour le réseau virtuel. 
    1. Cliquez sur Contrôle d’accès (IAM) dans le menu de gauche, puis sur **Ajouter** dans la barre d’outils.
    
        ![Contrôle d’accès -> Ajouter](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. Dans la page **Ajouter des autorisations**, sélectionnez **Contributeur de machines virtuelles classiques** pour **Rôle**. Copiez/collez **ddbf3205-c6bd-46ae-8127-60eb93363864** dans les zones de texte **Sélectionner**, puis sélectionnez **Microsoft Azure Batch** dans la liste des résultats de recherche. 
    
        ![Ajouter des autorisations -> Rechercher](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Cliquez sur Enregistrer pour enregistrer les paramètres et fermer la page.
    
        ![Enregistrer les paramètres d’accès](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Confirmez que **MicrosoftAzureBatch** apparaît bien dans la liste des contributeurs.
    
        ![Confirmer l’accès AzureBatch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Vérifiez que ce fournisseur Azure Batch est bien enregistré dans l’abonnement Azure dans lequel se trouve le réseau virtuel ou procédez à l’inscription du fournisseur Azure Batch. Si vous possédez déjà un compte Azure Batch dans votre abonnement, ce dernier est inscrit pour Azure Batch.
    1. Dans le portail Azure, cliquez sur **Abonnements** dans le menu de gauche. 
    2. Sélectionnez votre **abonnement**. 
    3. Cliquez sur **Fournisseurs de ressources** sur la gauche, puis confirmez que `Microsoft.Batch` est un fournisseur inscrit. 
    
        ![confirmation inscription batch](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Si `Microsoft.Batch` ne figure pas dans la liste, il vous faut l’enregistrer. Pour cela, [créez un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement. Vous pourrez le supprimer par la suite. 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>Utiliser le portail pour configurer un réseau virtuel Azure Resource Manager
Pour configurer le réseau virtuel, le plus simple consiste à exécuter le script. Si vous ne bénéficiez d’aucun accès pour configurer le réseau virtuel ou si la configuration automatique échoue, le propriétaire de ce réseau virtuel ou vous pouvez tenter une configuration manuelle en suivant les étapes ci-après :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Plus de services**. Filtrez et sélectionnez **Réseaux virtuels**.
3. Filtrez et sélectionnez votre **réseau virtuel** dans la liste. 
4. Dans la page des réseaux virtuels, sélectionnez **Propriétés**. 
5. Cliquez sur le bouton Copier au niveau de **ID DE RESSOURCE** pour copier l’ID de ressource du réseau virtuel dans le Presse-papiers. Enregistrez dans OneNote ou un fichier l’ID se trouvant dans le Presse-papiers.
6. Cliquez sur **Sous-réseaux** dans le menu de gauche, puis vérifiez que le nombre des **adresses disponibles** est bien supérieur à celui des nœuds dans votre runtime d’intégration Azure-SSIS.
5. Vérifiez que ce fournisseur Azure Batch est bien enregistré dans l’abonnement Azure dans lequel se trouve le réseau virtuel ou procédez à l’inscription du fournisseur Azure Batch. Si vous possédez déjà un compte Azure Batch dans votre abonnement, ce dernier est inscrit pour Azure Batch.
    1. Dans le portail Azure, cliquez sur **Abonnements** dans le menu de gauche. 
    2. Sélectionnez votre **abonnement**. 
    3. Cliquez sur **Fournisseurs de ressources** sur la gauche, puis confirmez que `Microsoft.Batch` est un fournisseur inscrit. 
    
        ![confirmation inscription batch](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Si `Microsoft.Batch` ne figure pas dans la liste, il vous faut l’enregistrer. Pour cela, [créez un compte Azure Batch vide](../batch/batch-account-create-portal.md) dans votre abonnement. Vous pourrez le supprimer par la suite.

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Créer un runtime d’intégration Azure-SSIS et l’attacher à un réseau virtuel
Vous pouvez créer un runtime d’intégration Azure-SSIS et l’attacher au réseau virtuel en même temps. Pour obtenir la totalité du script et des instructions permettant de créer un runtime d’intégration Azure-SSIS et de l’attacher à un réseau virtuel en même temps, consultez la section [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Attacher un runtime d’intégration Azure-SSIS à un réseau virtuel
Le script dans l’article [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md) vous explique comment créer un runtime d’intégration Azure-SSIS et l’attacher à un réseau virtuel dans le même script. Si vous avez un runtime d’intégration Azure-SSIS, effectuez les opérations suivantes pour l’attacher au réseau virtuel. 

1. Arrêtez le runtime d’intégration Azure-SSIS.
2. Configurez le runtime d’intégration Azure-SSIS pour l’attacher au réseau virtuel. 
3. Démarrez le runtime d’intégration Azure-SSIS. 

## <a name="define-the-variables"></a>Définir les variables

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
# OPTIONAL: specify your VNet ID and the subnet name. 
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Arrêter le runtime d’intégration Azure-SSIS
Arrêtez le runtime d’intégration Azure-SSIS avant de l’attacher à un réseau virtuel. Cette commande libère tous ses nœuds et arrête la facturation.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Configurer les paramètres du réseau virtuel pour le runtime d’intégration Azure-SSIS à attacher
Inscrivez-vous au fournisseur de ressources Azure Batch :

```powershell
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

### <a name="configure-the-azure-ssis-ir"></a>Configurer le runtime d’intégration Azure-SSIS
Exécutez la commande Set-AzureRmDataFactoryV2IntegrationRuntime pour configurer le runtime d’intégration Azure-SSIS à attacher au réseau virtuel : 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

## <a name="start-the-azure-ssis-ir"></a>Démarrer le runtime d’intégration Azure-SSIS
Exécutez la commande suivante pour démarrer le runtime d’intégration Azure-SSIS : 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Cette commande dure de **20 à 30 minutes**.

## <a name="next-steps"></a>étapes suivantes
Pour plus d’informations sur le runtime Azure-SSIS, voir les rubriques suivantes : 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, y compris sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : deploy SSIS packages to Azure](tutorial-deploy-ssis-packages-azure.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Procédures : Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Créer un runtime d’intégration Azure-SSIS). Cet article s’appuie sur le didacticiel et fournit des instructions sur la façon d’utiliser Azure SQL Managed Instance (préversion privée) et d’associer le runtime d’intégration à un VNet. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment le faire évoluer en lui ajoutant des nœuds supplémentaires. 
