---
title: "Convertir les disques non gérés d’une machine virtuelle Windows en disques gérés - Azure Managed Disks | Microsoft Docs"
description: "Conversion d’une machine virtuelle Windows qui utilise des disques non gérés pour qu’elle utilise des disques gérés à l’aide de PowerShell dans le modèle de déploiement Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: dd9ebaf9a1c8b3112623af4228efa0d9063c1e52
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertir les disques non gérés d’une machine virtuelle Windows en disques gérés

Si vos machines virtuelles Windows existantes utilisent des disques non gérés, vous pouvez les convertir pour qu’elles utilisent des disques gérés par le biais du service [Azure Managed Disks](managed-disks-overview.md). Ce processus convertit le disque du système d’exploitation ainsi que tous les autres disques de données attachés.

Cet article explique comment convertir des machines virtuelles à l’aide d’Azure PowerShell. Si vous devez installer ou mettre à niveau ce dernier, consultez [Installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Avant de commencer


* Consultez [Planification de la migration vers Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Consultez les [questions fréquentes (FAQ) sur la migration vers Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Convertir des machines virtuelles à instance unique
Cette section explique comment convertir vos machines virtuelles Azure à instance unique à partir de disques non gérés vers des disques gérés. (Si vos machines virtuelles sont dans un groupe à haute disponibilité, voir la section suivante.) 

1. Libérez la machine virtuelle à l’aide de l’applet de commande [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` : 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convertissez la machine virtuelle pour qu’elle utilise des disques gérés à l’aide de l’applet de commande [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Le processus suivant convertit la machine virtuelle précédente, y compris le disque du système d’exploitation et tous les disques de données :

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Démarrez la machine virtuelle une fois la conversion vers les disques gérés effectuée à l’aide de la commande [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). L’exemple suivant redémarre la machine virtuelle précédente :

  ```azurepowershell-interactive
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Convertir des machines virtuelles dans un groupe à haute disponibilité

Si les machines virtuelles que vous souhaitez convertir pour utiliser des disques gérés se trouvent dans un groupe à haute disponibilité, vous devez tout d’abord convertir ce dernier en groupe à haute disponibilité géré.

1. Convertissez le groupe à haute disponibilité à l’aide de l’applet de commande [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). L’exemple suivant met à jour le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Si la région où votre groupe à haute disponibilité se situe n’a que 2 domaines d’erreur gérés, mais que le nombre de domaines d’erreur non gérés est de 3, cette commande affiche une erreur semblable à « Le nombre de domaines d’erreur spécifié 3 doit être compris entre 1 et 2 ». Pour résoudre l’erreur, mettez à jour le domaine par défaut sur 2 et `Sku` sur `Aligned`, comme suit :

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Désallouer et convertir des machines virtuelles dans le groupe à haute disponibilité. Le script suivant libère chaque machine virtuelle à l’aide de l’applet de commande [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), la convertit à l’aide de [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) et la redémarre à l’aide de [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm) :

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Résolution de problèmes

Si une erreur survient lors de la conversion, ou si une machine virtuelle est dans un état d’échec en raison de problèmes dans une conversion précédente, exécutez l’applet de commande `ConvertTo-AzureRmVMManagedDisk` à nouveau. Généralement, une simple nouvelle tentative suffit à débloquer la situation.
Avant de lancer la conversion, assurez-vous que toutes les extensions de machine virtuelle sont dans l’état « Provisionnement réussi ». Sinon, la conversion échoue avec le code d’erreur 409.


## <a name="next-steps"></a>Étapes suivantes

[Convertir des disques gérés standard en premium](convert-disk-storage.md)

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](snapshot-copy-managed-disk.md).

