---
title: "Créer et gérer des machines virtuelles Windows avec le module Azure PowerShell | Microsoft Docs"
description: "Didacticiel : créer et gérer des machines virtuelles Windows avec le module Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c612a251105197ab2b46bf448ae39253e5a65f36
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Créer et gérer des machines virtuelles Windows avec le module Azure PowerShell

Les machines virtuelles fournissent un environnement informatique entièrement configurable et flexible. Ce didacticiel traite d’aspects de base du déploiement de machines virtuelles Azure, tels que la sélection d’une taille de machine virtuelle, la sélection d’une image de machine virtuelle et le déploiement d’une machine virtuelle. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle et vous y connecter
> * Sélectionner et utiliser des images de machine virtuelle
> * Afficher et utiliser des tailles de machine virtuelle spécifiques
> * Redimensionner une machine virtuelle
> * Consulter et comprendre l’état d’une machine virtuelle


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un groupe de ressources doit être créé avant les machines virtuelles. Dans cet exemple, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *EastUS*. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

Le groupe de ressources est spécifié lors de la création ou de la modification d’une machine virtuelle, qui peut être vue dans ce didacticiel.

## <a name="create-virtual-machine"></a>Create virtual machine

Une machine virtuelle doit être connectée à un réseau virtuel. Vous communiquez avec la machine virtuelle à l’aide d’une adresse IP publique via une carte réseau (NIC).

### <a name="create-virtual-network"></a>Création d’un réseau virtuel

Créez un sous-réseau avec [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) :

```azurepowershell-interactive
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) :

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Création d’une adresse IP publique

Créez une adresse IP publique avec [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) :

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Création de la carte réseau

Créez une carte réseau avec [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) :

```azurepowershell-interactive
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau

Un [groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md) (NSG) Azure contrôle le trafic entrant et sortant pour une ou plusieurs machines virtuelles. Les règles de groupe de sécurité réseau autorisent ou refusent le trafic réseau sur un port ou une plage de ports spécifique. Ces règles peuvent également inclure un préfixe d’adresse source afin que seul le trafic provenant d’une source prédéfinie puisse communiquer avec une machine virtuelle. Pour accéder au serveur web IIS que vous installez, vous devez ajouter une règle de groupe de sécurité réseau entrante.

Pour créer une règle de groupe de sécurité réseau entrante, utilisez [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig). L’exemple suivant crée une règle de groupe de sécurité réseau nommée *myNSGRule* qui ouvre le port *3389* pour la machine virtuelle :

```azurepowershell-interactive
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

Créez le groupe de sécurité réseau *myNSGRule* avec [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) :

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

Ajoutez le groupe de sécurité réseau au sous-groupe dans le réseau virtuel avec [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) :

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

Mettez à jour le réseau virtuel avec [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) :

```azurepowershell-interactive
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Create virtual machine

Lorsque vous créez une machine virtuelle, plusieurs options sont disponibles, comme l’image de système d’exploitation, les informations d’identification d’administration ou le dimensionnement des disques. Dans cet exemple, une machine virtuelle est créée avec le nom *myVM* ; elle exécute la dernière version de Windows Server 2016 Datacenter.

Définissez le nom d’utilisateur et le mot de passe pour le compte d’administrateur sur la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Créez la configuration initiale de la machine virtuelle avec [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) :

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Ajoutez les informations relatives au système d’exploitation à la configuration de la machine virtuelle avec [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) :

```azurepowershell-interactive
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Ajoutez les informations relatives à l’image à la configuration de la machine virtuelle avec [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) :

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

Ajoutez les paramètres du disque du système d’exploitation à la configuration de la machine virtuelle avec [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) :

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

Ajoutez la carte réseau que vous avez créée précédemment à la configuration de la machine virtuelle avec [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) :

```azurepowershell-interactive
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Créez la machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>Se connecter à une machine virtuelle

Une fois le déploiement terminé, créez une connexion Bureau à distance avec la machine virtuelle.

Exécutez les commandes suivantes pour renvoyer l’adresse IP publique de la machine virtuelle. Prenez note de cette adresse IP, afin de pouvoir vous y connecter ultérieurement avec votre navigateur de manière à tester la connectivité web.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

Exécutez la commande suivante sur la machine locale pour créer une session Bureau à distance avec la machine virtuelle. Remplacez l’adresse IP par l’adresse *publicIPAddress* de votre machine virtuelle. À l'invite, saisissez les informations d’identification que vous avez utilisées lors de la création de la machine virtuelle.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Comprendre les images de machine virtuelle

La Place de marché Azure comprend de nombreuses images de machine virtuelle qui permettent de créer une machine virtuelle. Dans les étapes précédentes, une machine virtuelle a été créée à l’aide de l’image Windows Server 2016 Datacenter. Dans cette étape, le module PowerShell est utilisé pour rechercher d’autres images Windows dans la place de marché, qui peuvent également servir de base pour les nouvelles machines virtuelles. Ce processus consiste à rechercher l’éditeur, l’offre et le nom de l’image (SKU). 

Utilisez la commande [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) pour retourner une liste d’éditeurs d’images.  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Utilisez la commande [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) pour retourner une liste d’offres d’images. Cette commande permet de filtrer la liste retournée en fonction de l’éditeur spécifié. 

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

La commande [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) filtre ensuite les résultats en fonction du nom de l’éditeur et de l’offre pour retourner une liste de noms d’images.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Ces informations peuvent être utilisées pour déployer une machine virtuelle avec une image spécifique. Cet exemple définit le nom de l’image sur l’objet de machine virtuelle. Reportez-vous aux exemples indiqués précédemment dans ce didacticiel pour effectuer les étapes de déploiement.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>Comprendre les tailles de machine virtuelle

Une taille de machine virtuelle détermine la quantité de ressources de calcul comme le processeur, le processeur graphique (GPU) et la mémoire qui sont mises à la disposition de la machine virtuelle. Les machines virtuelles doivent être créées avec une taille adaptée à la charge de travail attendue. Si la charge de travail augmente, une machine virtuelle existante peut être redimensionnée.

### <a name="vm-sizes"></a>Tailles de machine virtuelle

Le tableau suivant classe les tailles en fonction des cas d’utilisation.  

| Type                     | Tailles           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Usage général         |DSv2, Dv2, DS, D, Av2, A0-7| Ratio processeur/mémoire équilibré. Idéale pour le développement/test et pour les petites et moyennes applications et solutions de données.  |
| Optimisé pour le calcul      | Fs, F             | Ratio processeur/mémoire élevé. Convient pour les applications au trafic moyen, les appliances réseau et les processus de traitement par lots.        |
| Mémoire optimisée       | GS, G, DSv2, DS, Dv2, D   | Ratio mémoire/processeur élevé. Idéale pour les bases de données relationnelles, les caches moyens à grands et l’analytique en mémoire.                 |
| Optimisé pour le stockage       | Ls                | Débit de disque et E/S élevés. Idéale pour les bases de données NoSQL, SQL et Big Data.                                                         |
| GPU           | NV, NC            | Machines virtuelles spécialisées conçues pour les opérations graphiques lourdes et la retouche vidéo.       |
| Hautes performances | H, A8-11          | Nos machines virtuelles dotées des processeurs les plus puissants avec interfaces réseau haut débit en option (RDMA). 


### <a name="find-available-vm-sizes"></a>Rechercher les tailles de machines virtuelles disponibles

Pour afficher la liste des tailles de machines virtuelles disponibles dans une région particulière, utilisez la commande [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```azurepowershell-interactive
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>Redimensionner une machine virtuelle

Après avoir déployé une machine virtuelle, vous pouvez la redimensionner pour augmenter ou diminuer l’allocation des ressources.

Avant de redimensionner une machine virtuelle, vérifiez si la taille souhaitée est disponible dans le cluster de machines virtuelles actuel. La commande [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) retourne une liste de tailles. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

Si la taille souhaitée est disponible, la machine virtuelle peut être redimensionnée à partir d’un état sous tension, mais elle est redémarrée au cours de l’opération.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

Si la taille souhaitée ne figure pas dans le cluster actuel, la machine virtuelle doit être libérée avant de procéder au redimensionnement. Lorsque la machine virtuelle est de nouveau sous tension, notez que toutes les données du disque temporaire sont supprimées et que l’adresse IP publique est modifiée, sauf si une adresse IP statique est utilisée. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>États d’alimentation de machine virtuelle

Une machine virtuelle Azure peut présenter différents états d’alimentation. Cet état représente l’état actuel de la machine virtuelle du point de vue de l’hyperviseur. 

### <a name="power-states"></a>États d’alimentation

| État d’alimentation | Description
|----|----|
| Starting | Indique que la machine virtuelle est en cours de démarrage. |
| Exécution | Indique que la machine virtuelle est en cours d’exécution. |
| En cours d’arrêt | Indique que la machine virtuelle est en cours d’arrêt. | 
| Arrêté | Indique que la machine virtuelle est arrêtée. Notez que les machines virtuelles à l’état arrêté entraînent toujours des frais de calcul.  |
| Libération | Indique que la machine virtuelle est en cours de libération. |
| Libéré | Indique que la machine virtuelle est totalement supprimée de l’hyperviseur, mais reste disponible dans le plan de contrôle. Les machines virtuelles à l’état Libéré n’entraînent pas de frais de calcul. |
| - | Indique que l’état d’alimentation de la machine virtuelle est inconnu. |

### <a name="find-power-state"></a>Rechercher un état d’alimentation

Pour récupérer l’état d’une machine virtuelle spécifique, utilisez la commande [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Veillez à spécifier le nom valide d’une machine virtuelle et d’un groupe de ressources. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName myResourceGroupVM `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Output:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Tâches de gestion

Pendant le cycle de vie d’une machine virtuelle, vous souhaiterez exécuter des tâches de gestion telles que le démarrage, l’arrêt ou la suppression d’une machine virtuelle. En outre, vous souhaiterez peut-être créer des scripts pour automatiser les tâches répétitives ou complexes. À l’aide d’Azure PowerShell, de nombreuses tâches courantes de gestion peuvent être exécutées à partir de la ligne de commande ou dans des scripts.

### <a name="stop-virtual-machine"></a>Arrêt d’une machine virtuelle

Arrêtez et libérez une machine virtuelle avec [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) :

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

Si vous souhaitez conserver la machine virtuelle dans un état approvisionné, utilisez le paramètre -StayProvisioned.

### <a name="start-virtual-machine"></a>Démarrage d’une machine virtuelle

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>Supprimer un groupe de ressources

La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré les tâches de base de création et de gestion de machines virtuelles, notamment :

> [!div class="checklist"]
> * Créer une machine virtuelle et vous y connecter
> * Sélectionner et utiliser des images de machine virtuelle
> * Afficher et utiliser des tailles de machine virtuelle spécifiques
> * Redimensionner une machine virtuelle
> * Consulter et comprendre l’état d’une machine virtuelle

Passez au didacticiel suivant pour en savoir plus sur les disques de machine virtuelle.  

> [!div class="nextstepaction"]
> [Créer et gérer des disques de machine virtuelle](./tutorial-manage-data-disk.md)
