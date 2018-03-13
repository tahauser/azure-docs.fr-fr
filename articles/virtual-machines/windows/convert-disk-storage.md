---
title: Convertir le stockage Managed Disks Azure de standard en premium, et vice versa | Microsoft Docs
description: "Comment convertir les disques gérés Azure de standard en premium, et vice versa, à l’aide de Microsoft Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 407cfe7d9eee4e226938f383c04bb359a17290fc
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Convertir le stockage Managed Disks Azure de standard en premium, et vice versa

Managed Disks propose deux options de stockage : [Premium](premium-storage.md) (SSD) et [Standard](standard-storage.md) (HDD). Il vous permet de basculer facilement entre les deux options avec une interruption minimale adaptée à vos besoins de performances. Cette fonctionnalité n’est pas disponible pour les disques non gérés. Toutefois, vous pouvez facilement [effectuer des conversions en disques gérés](convert-unmanaged-to-managed-disks.md) pour basculer facilement entre les deux options.

Cet article vous montre comment convertir des disques gérés de standard en premium, et vice versa, à l’aide de Microsoft Azure PowerShell. Si vous devez installer ou mettre à niveau ce dernier, consultez [Installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Avant de commencer

* La conversion nécessite un redémarrage de la machine virtuelle. Par conséquent, planifiez la migration de vos stockages sur disques au cours d’une fenêtre de maintenance préexistante. 
* Si vous utilisez des disques non gérés, tout d’abord [effectuez des conversions en disques gérés](convert-unmanaged-to-managed-disks.md) afin d’utiliser cet article pour basculer entre les deux options de stockage. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Convertir tous les disques gérés d’une machine virtuelle de standard en premium, et vice versa

Dans l’exemple suivant, nous montrons comment faire passer tous les disques d’une machine virtuelle du stockage standard au stockage premium. Pour utiliser des disques gérés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple passe également à une taille prenant en charge le stockage premium.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Convertir un disque géré de standard en premium, et vice versa

Pour votre charge de travail de développement/test, vous souhaiterez peut-être mélanger disques standard et disques premium pour réduire les coûts. Vous pouvez le faire en effectuant une mise à niveau vers le stockage premium, uniquement pour les disques qui nécessitent de meilleures performances. Dans l’exemple suivant, nous montrons comment faire passer un seul disque d’une machine virtuelle du stockage standard au stockage premium, et vice versa. Pour utiliser des disques gérés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple passe également à une taille prenant en charge le stockage premium.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
$vmResource = Get-AzureRmResource -ResourceId $disk.diskId

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](snapshot-copy-managed-disk.md).

