---
title: "Créer des images de machine virtuelle personnalisées avec Azure PowerShell | Microsoft Docs"
description: "Tutoriel : créez une image de machine virtuelle personnalisée à l’aide d’Azure PowerShell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/07/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7001e5df235d65c531b9102f879bde9693c4f853
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Créer une image personnalisée d’une machine virtuelle Azure à l’aide de PowerShell

Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des configurations comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. Ce didacticiel explique comment créer votre propre image personnalisée d’une machine virtuelle Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Exécuter Sysprep et généraliser les machines virtuelles
> * Créer une image personnalisée
> * Créer une machine virtuelle à partir d’une image personnalisée
> * Répertorier toutes les images dans votre abonnement
> * Supprimer une image

Ce didacticiel requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Avant de commencer

Les étapes ci-dessous expliquent comment prendre une machine virtuelle existante et la transformer en une image personnalisée réutilisable que vous pouvez utiliser pour créer de nouvelles instances de machines virtuelles.

Pour exécuter l’exemple dans ce didacticiel, vous devez disposer d’une machine virtuelle. Si nécessaire, cet [exemple de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) peut en créer une pour vous. Au cours du didacticiel, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.

## <a name="prepare-vm"></a>Préparer la machine virtuelle

Pour créer une image de machine virtuelle, vous devez préparer la machine virtuelle en la généralisant, en la libérant et en la marquant comme généralisée dans Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Généraliser la machine virtuelle Windows à l’aide de Sysprep

Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).


1. Connectez-vous à la machine virtuelle.
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par *%windir%\system32\sysprep*, puis exécutez *sysprep.exe*.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez *Entrer en mode OOBE (Out-of-Box Experience)* et vérifiez que la case *Généraliser* est cochée.
4. Dans **Options d’arrêt**, sélectionnez *Arrêter*, puis cliquez sur **OK**.
5. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. **Ne redémarrez pas la machine virtuelle**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Libérer la machine virtuelle et la marquer comme généralisée

Pour créer une image, la machine virtuelle doit être libérée et marquée comme généralisée dans Azure.

Libérez la machine virtuelle à l’aide de [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Définissez l’état de la machine virtuelle sur `-Generalized` à l’aide de [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Création de l’image

Créez à présent une image de la machine virtuelle à l’aide de [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) et [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). L’exemple suivant crée une image nommée *myimage* à partir d’une machine virtuelle nommée *myimage*.

Accédez à la machine virtuelle. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Créez la configuration de l’image.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Créez l’image.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Créer des machines virtuelles à partir de l’image

Maintenant que vous avez une image, vous pouvez créer une ou plusieurs nouvelles machines virtuelles à partir de l’image. La création d’une machine virtuelle à partir d’une image personnalisée est très similaire à la création d’une machine virtuelle à l’aide d’une image de la Place de marché. Quand vous utilisez une image de la Place de marché, vous devez fournir les informations sur l’image, le fournisseur de l’image, l’offre, la référence et la version. Avec une image personnalisée, vous devez simplement fournir l’ID de la ressource d’image personnalisée. 

Dans le script suivant, nous créons une variable *$image* pour stocker des informations sur l’image personnalisée à l’aide de [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) et nous utilisons ensuite [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) et spécifions l’ID à l’aide de la variable *$image* que nous venons de créer. 

Le script crée une machine virtuelle nommée *myVMfromImage* à partir de notre image personnalisée dans un groupe de ressources nommé *myResourceGroupFromImage* dans l’emplacement *Ouest des États-Unis*.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>Gestion d’image 

Voici quelques exemples de tâches d’images de gestion courantes et comment les exécuter à l’aide de PowerShell.

Répertoriez toutes les images par nom.

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Supprimez une image. Cet exemple supprime l’image nommée *myOldImage* à partir du groupe *myResourceGroup*.

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous montré comment créer une image de machine virtuelle. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Exécuter Sysprep et généraliser les machines virtuelles
> * Créer une image personnalisée
> * Créer une machine virtuelle à partir d’une image personnalisée
> * Répertorier toutes les images dans votre abonnement
> * Supprimer une image

Passez au didacticiel suivant pour en savoir plus sur les machines virtuelles hautement disponibles.

> [!div class="nextstepaction"]
> [Créer des machines virtuelles hautement disponibles](tutorial-availability-sets.md)



