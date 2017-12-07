---
title: "Activer Azure Active Directory Domain Services à l’aide de PowerShell | Microsoft Docs"
description: "Activer Azure Active Directory Domain Services à l’aide de PowerShell"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: 054d3b3347ef2ce9fdbfa8ff8103cf4aa15bae20
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Activer Azure Active Directory Domain Services à l’aide de PowerShell
Cet article vous montre comment activer les services de domaine Azure Active Directory (AD) avec PowerShell.

## <a name="task-1-install-the-required-powershell-modules"></a>Tâche 1 : Installer les modules PowerShell nécessaires

### <a name="install-and-configure-azure-ad-powershell"></a>Installation et configuration d'Azure AD PowerShell
Suivez les instructions de l’article pour [Installer le module PowerShell Azure AD et vous connecter à Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installation et configuration d'Azure PowerShell
Suivez les instructions de l’article pour [Installer le module Azure PowerShell et vous connecter à votre abonnement Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Tâche 2 : Créer le principal du service requis dans votre annuaire Azure AD
Saisissez la commande PowerShell suivante pour créer le principal du service requis pour Azure AD Domain Services dans votre annuaire Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Tâche 3 : Créer et configurer le groupe « AAD DC Administrators »
La tâche suivante consiste à créer le groupe administrateur qui sera utilisé pour déléguer les tâches d’administration sur votre domaine géré.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Maintenant que vous avez créé le groupe, ajoutez-lui quelques utilisateurs.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Tâche 4 : Inscrire le fournisseur de ressources Azure AD Domain Services
Saisissez la commande PowerShell suivante pour inscrire le fournisseur de ressources pour Azure AD Domain Services :
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Tâche 5 : Créer un groupe de ressources
Saisissez la commande PowerShell suivante pour créer un groupe de ressources :
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Vous pouvez créer le réseau virtuel et le domaine géré Azure AD Domain Services dans ce groupe de ressources.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Tâche 6 : Créer et configurer le réseau virtuel
Créez maintenant le réseau virtuel dans lequel Azure AD Domain Services doit être activé. Veillez à utiliser un sous-réseau dédié pour Azure AD Domain Services. Ne déployez pas de machines virtuelles de charge de travail dans ce sous-réseau dédié.

Tapez les commandes PowerShell suivantes pour créer un réseau virtuel avec un sous-réseau dédié pour Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Tâche 7 : Provisionner un domaine managé par Azure AD Domain Services
Saisissez la commande PowerShell suivante pour activer Azure AD Domain Services pour votre annuaire :

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **N’oubliez pas les étapes de configuration supplémentaires après avoir déployé votre domaine géré.**
> Après la configuration de votre domaine géré, vous devez effectuer les tâches suivantes :
> * **[Mettez à jour les paramètres DNS](active-directory-ds-getting-started-dns.md)** pour le réseau virtuel afin que les machines virtuelles puissent trouver le domaine géré pour l’authentification ou la jonction de domaine.
* **[Activez la synchronisation de mots de passe avec Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md)**, pour que les utilisateurs puissent se connecter au domaine géré à l’aide des informations d’identification d’entreprise.
>


## <a name="powershell-script"></a>Script PowerShell
Le script PowerShell utilisé pour effectuer toutes les tâches répertoriées dans cet article se trouve ci-dessous. Copiez le script et enregistrez-le dans un fichier avec une extension « .ps1 ». Exécutez le script dans PowerShell ou à l’aide de PowerShell Integrated Scripting Environment (ISE).

> [!NOTE]
> **Autorisations requises pour exécuter ce script** Pour activer Azure AD Domain Services, vous devez être administrateur global pour l’annuaire Azure AD. En outre, vous devez disposer au moins des privilèges « Contributeur » sur le réseau virtuel dans lequel activer Azure AD Domain Services.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Login-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **N’oubliez pas les étapes de configuration supplémentaires après avoir déployé votre domaine géré.**
> Après la configuration de votre domaine géré, vous devez effectuer les tâches suivantes :
> * Mettez à jour les paramètres DNS pour le réseau virtuel afin que les machines virtuelles puissent trouver le domaine géré pour l’authentification ou la jonction de domaine.
* Activez la synchronisation de mots de passe avec Azure AD Domain Services, pour que les utilisateurs puissent se connecter au domaine géré à l’aide des informations d’identification d’entreprise.
>

## <a name="next-steps"></a>Étapes suivantes
Une fois votre domaine géré créé, effectuez les tâches de configuration suivantes afin de pouvoir utiliser le domaine géré :

* [Mettre à jour les paramètres de serveur DNS pour le réseau virtuel pour pointer vers votre domaine géré](active-directory-ds-getting-started-dns.md)
* [Activer la synchronisation de mot de passe pour votre domaine géré](active-directory-ds-getting-started-password-sync.md)
