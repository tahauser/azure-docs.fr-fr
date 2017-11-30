---
title: Gestion des disques Azure avec Azure PowerShell | Microsoft Docs
description: Didacticiel - Gestion des disques Azure avec Azure PowerShell
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
ms.openlocfilehash: b994cfd09156ae8e1662f4947241aa1a4672df98
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="manage-azure-disks-with-powershell"></a>Gestion des disques Azure avec PowerShell

Les machines virtuelles utilisent des disques pour stocker leur système d’exploitation, leurs applications et leurs données. Lorsque vous créez une machine virtuelle, il est important de choisir une taille de disque et une configuration appropriées à la charge de travail prévue. Ce didacticiel décrit le déploiement et la gestion des disques de machine virtuelle. Vous en apprendrez davantage sur les points suivants :

> [!div class="checklist"]
> * Disques de système d’exploitation et disques temporaires
> * Disques de données
> * Disques Standard et Premium
> * Performances des disques
> * Attachement et préparation des disques de données

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="default-azure-disks"></a>Disques Azure par défaut

Lorsqu’une machine virtuelle Azure est créée, deux disques sont automatiquement attachés à celle-ci. 

**Disque de système d’exploitation** : la taille des disques de système d’exploitation peut atteindre 1 To ; ces disques hébergent le système d’exploitation des machines virtuelles.  Le disque de système d’exploitation se voit attribuer la lettre de lecteur *c:* par défaut. La configuration de la mise en cache de disque de système d’exploitation est optimisée pour les performances du système d’exploitation. Le disque de système d’exploitation **ne doit pas** héberger d’applications ou de données. Pour héberger ce type de contenu, utilisez plutôt un disque de données, qui est décrit plus loin dans cet article.

**Disque temporaire** : les disques temporaires utilisent un disque SSD qui se trouve sur le même hôte Azure que la machine virtuelle. Les disques temporaires sont extrêmement performants et peuvent être utilisés pour des opérations telles que le traitement de données temporaires. Toutefois, si la machine virtuelle est déplacée vers un nouvel hôte, toutes les données stockées sur un disque temporaire sont supprimées. La taille du disque temporaire est déterminée par la taille de la machine virtuelle. Les disques temporaires se voient attribuer la lettre de lecteur *d:* par défaut.

### <a name="temporary-disk-sizes"></a>Tailles du disque temporaire

| Type | Taille de la machine virtuelle | Taille maximale du disque temporaire (Go) |
|----|----|----|
| [Usage général](sizes-general.md) | Séries A et D | 800 |
| [Optimisé pour le calcul](sizes-compute.md) | Série F | 800 |
| [Mémoire optimisée](../virtual-machines-windows-sizes-memory.md) | Séries D et G | 6144 |
| [Optimisé pour le stockage](../virtual-machines-windows-sizes-storage.md) | Série L | 5630 |
| [GPU](sizes-gpu.md) | Série N | 1 440 |
| [Hautes performances](sizes-hpc.md) | Séries A et H | 2000 |

## <a name="azure-data-disks"></a>Disques de données Azure

Des disques de données supplémentaires peuvent être ajoutés pour installer des applications et stocker des données. Les disques de données doivent être utilisés dans les cas où un stockage des données durable et réactif est souhaité. Chaque disque de données possède une capacité maximale de 1 To. La taille de la machine virtuelle détermine le nombre de disques de données pouvant être attachés à cette machine virtuelle. Pour chaque processeur virtuel de la machine virtuelle, deux disques de données peuvent être attachés. 

### <a name="max-data-disks-per-vm"></a>Disques de données max. par machine virtuelle

| Type | Taille de la machine virtuelle | Disques de données max. par machine virtuelle |
|----|----|----|
| [Usage général](sizes-general.md) | Séries A et D | 32 |
| [Optimisé pour le calcul](sizes-compute.md) | Série F | 32 |
| [Mémoire optimisée](../virtual-machines-windows-sizes-memory.md) | Séries D et G | 64 |
| [Optimisé pour le stockage](../virtual-machines-windows-sizes-storage.md) | Série L | 64 |
| [GPU](sizes-gpu.md) | Série N | 48 |
| [Hautes performances](sizes-hpc.md) | Séries A et H | 32 |

## <a name="vm-disk-types"></a>Type de disque de machine virtuelle

Azure propose deux types de disque.

### <a name="standard-disk"></a>Disque Standard

Le stockage Standard s’appuie sur des disques durs et offre un stockage économique qui n’en est pas moins performant. Les disques Standard constituent la solution idéale pour une charge de travail de développement et de test économique.

### <a name="premium-disk"></a>Disque Premium

Les disques Premium reposent sur un disque SSD à faible latence et hautes performances. Ils conviennent parfaitement aux machines virtuelles exécutant une charge de travail en production. Le stockage Premium prend en charge les machines virtuelles des séries DS, DSv2, GS et FS. Les disques Premium sont de trois types (P10, P20, P30), la taille du disque détermine le type de disque. Lorsque vous sélectionnez une taille de disque, la valeur est arrondie au type suivant. Par exemple, si la taille est inférieure à 128 Go le type de disque sera P10, entre 129 Go et 512 Go, le type de disque sera P20, et au-delà de 512 Go, le type de disque sera P30. 

### <a name="premium-disk-performance"></a>Performances du disque Premium

|Type de disque de stockage Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Taille du disque (arrondie) | 128 Go | 512 Go | 1 024 Go (1 To) |
| IOPS par disque | 500 | 2 300 | 5 000 |
Débit par disque | 100 Mo/s | 150 Mo/s | 200 Mo/s |

Bien que le tableau ci-dessus identifie le nombre max. d’E/S par seconde par disque, un niveau de performances plus élevé est possible en entrelaçant plusieurs disques de données. Par exemple, 64 disques de données peuvent être attachés à la machine virtuelle Standard_GS5. Si chacun de ces disques est de type P30, vous pouvez atteindre un nombre maximum d’E/S par seconde de 80 000. Pour plus d’informations sur le nombre maximal d’E/S par seconde par machine virtuelle, consultez [Types et tailles des machines virtuelles](./sizes.md).

## <a name="create-and-attach-disks"></a>Créer et attacher des disques

Pour exécuter l’exemple dans ce didacticiel, vous devez disposer d’une machine virtuelle. Si nécessaire, cet [exemple de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) peut en créer une pour vous. Au cours du didacticiel, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.

Créez la configuration initiale avec [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). L’exemple suivant configure un disque d’une taille de 128 Go.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig -Location EastUS -CreateOption Empty -DiskSizeGB 128
```

Créez le disque de données avec la commande [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myDataDisk -Disk $diskConfig
```

Obtenez la machine virtuelle que vous souhaitez ajouter au disque de données avec la commande [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Ajoutez le disque de données à la configuration de la machine virtuelle avec la commande [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk -VM $vm -Name myDataDisk -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1
```

Mettez à jour la machine virtuelle avec la commande [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="prepare-data-disks"></a>Préparer les disques de données

Une fois qu’un disque a été attaché à la machine virtuelle, le système d’exploitation doit être configuré pour utiliser le disque. L’exemple suivant montre comment configurer manuellement le premier disque ajouté à la machine virtuelle. Ce processus peut également être automatisé à l’aide de [l’extension de script personnalisé](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuration manuelle

Créez une connexion RDP avec la machine virtuelle. Ouvrez PowerShell et exécutez ce script.

```azurepowershell-interactive
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances concernant les disques de machine virtuelle, notamment concernant les points suivants :

> [!div class="checklist"]
> * Disques de système d’exploitation et disques temporaires
> * Disques de données
> * Disques Standard et Premium
> * Performances des disques
> * Attachement et préparation des disques de données

Passez au didacticiel suivant pour en apprendre davantage sur l’automatisation de la configuration de machine virtuelle.

> [!div class="nextstepaction"]
> [How to customize a Linux virtual machine on first boot](./tutorial-automate-vm-deployment.md) (Comment personnaliser une machine virtuelle Linux au premier démarrage)
