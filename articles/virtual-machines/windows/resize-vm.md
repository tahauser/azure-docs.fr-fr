---
title: Utiliser PowerShell pour redimensionner une machine virtuelle Windows dans Azure | Microsoft Docs
description: "Redimensionnez une machine virtuelle Windows créée avec le modèle de déploiement Resource Manager à l’aide d’Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: a3d4b6e5db8835b23b014b344f0c9daae68adc09
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="resize-a-windows-vm"></a>Redimensionner une machine virtuelle Windows
Cet article vous montre comment redimensionner une machine virtuelle Windows créée avec le modèle de déploiement Resource Manager à l’aide d’Azure PowerShell.

Une fois que vous avez créé une machine virtuelle, vous pouvez la mettre à l’échelle en modifiant sa taille. Dans certains cas, vous devez commencer par libérer la machine virtuelle. Cela peut se produire si la nouvelle taille n’est pas disponible sur le cluster matériel qui héberge actuellement la machine virtuelle.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Redimensionner une machine virtuelle Windows qui ne se trouve pas dans un groupe à haute disponibilité
1. Liste les tailles de machines virtuelles qui sont disponibles sur le cluster matériel sur lequel la machine virtuelle est hébergée. 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. Si la taille voulue n’est pas répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si la taille souhaitée n’est pas répertoriée, passez à l’étape 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Si la taille souhaitée n’est pas répertoriée, exécutez les commandes suivantes pour libérer la machine virtuelle, la redimensionner et la redémarrer.
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -Name $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> Le fait de libérer la machine virtuelle libère toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques de données et du système d’exploitation ne sont pas affectés. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Redimensionner une machine virtuelle Windows qui se trouve dans un groupe à haute disponibilité
Si la nouvelle taille d’une machine virtuelle se trouvant dans un groupe à haute disponibilité n’est pas disponible sur le cluster matériel qui l’héberge actuellement, toutes les machines virtuelles du groupe à haute disponibilité doivent être libérées pour redimensionner la machine virtuelle. Vous devrez peut-être mettre à jour la taille des autres machines virtuelles du groupe à haute disponibilité après le redimensionnement d’une des machines virtuelles. Pour redimensionner une machine virtuelle dans un groupe à haute disponibilité, procédez comme suit.

1. Liste les tailles de machines virtuelles qui sont disponibles sur le cluster matériel sur lequel la machine virtuelle est hébergée.
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. Si la taille voulue n’est pas répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si elle n’est pas répertoriée, passez à l’étape 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Si la taille souhaitée n’est pas répertoriée, passez aux étapes suivantes pour libérer toutes les machines virtuelles du groupe à haute disponibilité, redimensionner des machines virtuelles et les redémarrer.
4. Arrêtez toutes les machines virtuelles du groupe à haute disponibilité.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. Redimensionnez et redémarrez toutes les machines virtuelles du groupe à haute disponibilité.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>Étapes suivantes
* Pour une évolutivité supplémentaire, exécutez plusieurs instances de machine virtuelle et augmentez leur taille. Pour plus d’informations, consultez [Mise à l’échelle automatique des machines Windows dans un groupe de machines virtuelles identiques](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

