---
title: "Créer une machine virtuelle à partir d’une image managée dans Azure | Microsoft Docs"
description: "Créez une machine virtuelle Windows à partir d’une image managée généralisée à l’aide d’Azure PowerShell ou du portail Azure, dans le modèle de déploiement Gestionnaire des ressources."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>Créer une machine virtuelle à partir d’une image gérée

Vous pouvez créer plusieurs machines virtuelles à partir d’une image de machine virtuelle managée à l’aide de PowerShell ou du portail Azure. Une image de machine virtuelle managée contient les informations nécessaires pour créer une machine virtuelle, y compris le disque du système d’exploitation et les disques de données. Les disques durs virtuels qui composent l’image, y compris les disques du système d’exploitation et les disques de données, sont stockés en tant que disques managés. 

Vous devez déjà avoir [créé une image de machine virtuelle managée](capture-image-resource.md) à utiliser pour créer la nouvelle machine virtuelle. 

## <a name="use-the-portal"></a>Utiliser le portail 

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Toutes les ressources**. Vous pouvez trier les ressources par **Type** pour rechercher facilement vos images.
3. Sélectionnez l’image à utiliser dans la liste. La page **Vue d’ensemble** de l’image s’ouvre.
4. Cliquez sur **+ Créer machine virtuelle** dans le menu.
5. Saisissez les informations de la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous avez entrés vous serviront pour vous connecter à la machine virtuelle. Lorsque vous avez terminé, cliquez sur **OK**. Vous pouvez créer la nouvelle machine virtuelle dans un groupe de ressources existant ou sélectionner **Créer nouveau** pour créer un nouveau groupe de ressources pour stocker la machine virtuelle.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. 
7. Sous **Paramètres**, procédez aux modifications nécessaires et cliquez sur **OK**. 
8. Dans la page Résumé, vous pouvez voir le nom de votre image dans la liste **Image privée**. Cliquez sur **Ok** pour démarrer le déploiement de la machine virtuelle.


## <a name="use-powershell"></a>Utiliser PowerShell

### <a name="prerequisites"></a>Composants requis

Assurez-vous que vous disposez de la dernière version des modules PowerShell AzureRM.Compute et AzureRM.Network. Ouvrez une invite PowerShell en tant qu’administrateur, puis exécutez la commande suivante pour les installer.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](/powershell/azure/overview).



### <a name="collect-information-about-the-image"></a>Collecter des informations sur l’image

Nous devons tout d’abord collecter des informations de base sur l’image et créer une variable pour celle-ci. Cet exemple utilise une image de machine virtuelle gérée nommée **myImage** qui se trouve dans le groupe de ressources **myResourceGroup** dans l’emplacement **Ouest-Centre des États-Unis**. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

Créez le sous-réseau. Cet exemple crée un sous-réseau nommé **mySubnet** avec le préfixe d’adresse **10.0.0.0/24**.  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Création du réseau virtuel. Cet exemple crée un réseau virtuel nommé **myVnet** avec le préfixe d’adresse **10.0.0.0/16**.  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Création d'une adresse IP publique et une interface réseau

Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

Créez une adresse IP publique. Cet exemple crée une adresse IP publique nommée **myPip**. 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
Créez la carte réseau. Cet exemple crée une carte réseau nommée **myNic**. 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer le groupe de sécurité réseau et une règle RDP

Pour pouvoir vous connecter à votre machine virtuelle avec le protocole RDP, vous devez disposer d’une règle de sécurité réseau (NSG) qui autorise l’accès RDP sur le port 3389. 

Cet exemple crée un groupe de sécurité réseau nommé **myNsg** qui contient une règle nommée **myRdpRule** autorisant le trafic RDP sur le port 3389. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Créer une variable pour le réseau virtuel

Créez une variable pour le réseau virtuel terminé. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>Obtenir les informations d’identification pour la machine virtuelle

L’applet de commande suivante ouvre une fenêtre dans laquelle vous allez entrer un nouveau nom d’utilisateur et un mot de passe à utiliser comme compte d’administrateur local pour accéder à distance à la machine virtuelle. 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Définir des variables pour le nom de la machine virtuelle, le nom de l’ordinateur et la taille de la machine virtuelle

Créez des variables pour le nom de la machine virtuelle et le nom de l’ordinateur. Cet exemple définit **myVM** en tant que nom de la machine virtuelle et **myComputer** en tant que nom d’ordinateur.

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

Définissez la taille de la machine virtuelle. Cet exemple crée une machine virtuelle de taille **Standard_DS1_v2**. Pour plus d’informations, consultez la documentation relative aux [tailles de machines virtuelles](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

Ajoutez le nom et la taille de la machine virtuelle à sa configuration.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Définir l’image de machine virtuelle en tant qu’image source pour la nouvelle machine virtuelle

Définissez l’image source à l’aide de l’ID de l’image de machine virtuelle gérée.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>Définissez la configuration du système d’exploitation et ajoutez la carte réseau.

Indiquez le type de stockage (PremiumLRS ou StandardLRS) et la taille du disque du système d’exploitation. Cet exemple définit le type de compte sur **PremiumLRS**, la taille du disque sur **128 Go** et la mise en cache du disque sur **Lecture/écriture**.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>Création de la machine virtuelle

Créez la nouvelle machine virtuelle à l’aide de la configuration que nous avons créée et stockée dans la variable **$vm**.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée
Lorsque vous avez terminé, vous devez voir la machine virtuelle nouvellement créée dans le [Portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** ou en utilisant les commandes PowerShell suivantes :

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes
Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, voir [Créer et gérer des machines virtuelles Windows avec le module Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

