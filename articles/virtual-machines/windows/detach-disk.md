---
title: "Détacher un disque de données d’une machine virtuelle Windows - Azure| Microsoft Docs"
description: "Apprenez à détacher un disque de données d’une machine virtuelle dans Azure à l’aide du modèle de déploiement Ressource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 7013e7ff3cb14dcad8e3e9a926bcee771180259d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Détachement d’un disque de données d’une machine virtuelle Windows
Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage.

> [!WARNING]
> Si vous détachez un disque, il n’est pas supprimé automatiquement. Si vous êtes abonné au stockage Premium, vous continuerez à engager des frais de stockage pour le disque. Pour plus d’informations, consultez [Tarification et facturation de Premium Storage](premium-storage.md#pricing-and-billing).
>
>

Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.

## <a name="detach-a-data-disk-using-the-portal"></a>Détacher un disque de données avec le portail

1. Dans le menu de gauche, sélectionnez **Machines virtuelles**.
2. Sélectionnez la machine virtuelle qui possède le disque de données que vous souhaitez détacher, puis cliquez sur **Arrêter** pour libérer la machine virtuelle.
3. Dans le volet de la machine virtuelle, sélectionnez **Disques**.
4. En haut du volet **Disques**, sélectionnez **Modifier**.
5. Dans le volet **Disques**, à l’extrême droite du disque de données que vous souhaitez détacher, cliquez sur le bouton de détachement ![image du bouton détacher](./media/detach-disk/detach.png).
5. Une fois que le disque a été supprimé, cliquez sur **Enregistrer** en haut du volet.
6. Dans le volet de la machine virtuelle, cliquez sur **Présentation**, puis cliquez sur le bouton **Démarrer** en haut du volet pour redémarrer la machine virtuelle.



Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.

## <a name="detach-a-data-disk-using-powershell"></a>Détacher un disque de données avec PowerShell
Dans cet exemple, la première commande récupère la machine virtuelle nommée **MyVM07** dans le groupe de ressources **RG11** à l’aide de la cmdlet [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) et la stocke dans la variable **$VirtualMachine**.

La deuxième ligne supprime le disque de données nommé DataDisk3 de la machine virtuelle à l’aide de la cmdlet [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

La troisième ligne met à jour l’état de la machine virtuelle à l’aide de la cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) pour terminer le processus de suppression du disque de données.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

Pour plus d’informations, consultez [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez réutiliser le disque de données, vous pouvez simplement [l’attacher à une autre machine virtuelle](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

