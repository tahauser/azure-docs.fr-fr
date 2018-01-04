---
title: Personnaliser une machine virtuelle Windows dans Azure | Microsoft Docs
description: "Découvrez comment utiliser l’extension de script personnalisé et le coffre Key Vault pour personnaliser des machines virtuelles Windows Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 17a6c243aaf73fcd88261870fbdd9e8c936471b8
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Comment personnaliser une machine virtuelle Windows dans Azure
Pour configurer des machines virtuelles de manière rapide et cohérente, une certaine forme d’automatisation est généralement souhaitée. Une approche courante pour personnaliser une machine virtuelle Windows consiste à utiliser [l’extension de script personnalisé pour Windows](extensions-customscript.md). Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser une extension de script personnalisé pour installer IIS
> * Créer une machine virtuelle qui utilise l’extension de script personnalisé
> * Afficher un site IIS en cours d’exécution après l’application de l’extension

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 


## <a name="custom-script-extension-overview"></a>Vue d’ensemble de l’extension de script personnalisé
L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension.

L’extension de script personnalisé s’intègre aux modèles Azure Resource Manager et peut être exécutée à l’aide de l’interface de ligne de commande Azure, de PowerShell, du portail Azure ou de l’API REST de machine virtuelle Azure.

Vous pouvez utiliser l’extension de script personnalisé avec les machines virtuelles Linux et Windows.


## <a name="create-virtual-machine"></a>Create virtual machine
Avant de créer une machine virtuelle, créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAutomate* dans l’emplacement *EastUS* :

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location EastUS
```

Définissez un nom d’utilisateur administrateur et un mot de passe pour les machines virtuelles avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Vous pouvez maintenant créer la machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée les composants de réseau virtuel requis, la configuration du système d’exploitation, puis une machine virtuelle nommée *myVM* :

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location EastUS -VM $vmConfig
```

Quelques minutes sont nécessaires à la création des ressources et de la machine virtuelle.


## <a name="automate-iis-install"></a>Automatiser l’installation d’IIS
Utilisez [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) pour installer l’extension de script personnalisé. L’extension exécute `powershell Add-WindowsFeature Web-Server` pour installer le serveur web IIS, puis met à jour la page *Default.htm* pour qu’elle affiche le nom d’hôte de la machine virtuelle :

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
```


## <a name="test-web-site"></a>Tester le site web
Obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP pour *myPublicIP* créée précédemment :

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Name myPublicIP | select IpAddress
```

Vous pouvez alors entrer l’adresse IP publique dans un navigateur web. Le site web s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibreur de charge a distribué le trafic comme dans l’exemple suivant :

![Site web IIS en cours d’exécution](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez automatisé l’installation d’IIS sur une machine virtuelle. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser une extension de script personnalisé pour installer IIS
> * Créer une machine virtuelle qui utilise l’extension de script personnalisé
> * Afficher un site IIS en cours d’exécution après l’application de l’extension

Passez au didacticiel suivant pour découvrir comment créer des images de machine virtuelle personnalisées.

> [!div class="nextstepaction"]
> [Créer des images de machine virtuelle personnalisées](./tutorial-custom-images.md)
