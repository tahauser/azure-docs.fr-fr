---
title: Tailles des machines virtuelles Windows Azure - HPC | Microsoft Docs
description: Répertorie les différentes tailles disponibles pour les machines virtuelles de calcul haute performance Windows dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: 6f2c72689811d26f95a64fdf5f473606f3ea3f7d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="high-performance-compute-vm-sizes"></a>Tailles de machines virtuelles de calcul haute performance

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Système d’exploitation** : Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 ou version ultérieure, bibliothèque Intel MPI 5.x

  Les implémentations MPI prises en charge utilisent l’interface Microsoft Network Direct pour la communication entre les instances. 

* **Espace d’adressage réseau RDMA** : le réseau RDMA dans Azure réserve l’espace d’adressage 172.16.0.0/16. Si vous exécutez des applications MPI sur des instances déployées dans un réseau virtuel Azure, assurez-vous que l’espace d’adressage du réseau virtuel ne chevauche pas le réseau RDMA.

* **Extension de machine virtuelle HpcVmDrivers** : sur des machines virtuelles prenant en charge RDMA, ajoutez l’extension HpcVmDrivers pour installer les pilotes d’appareils réseau Windows nécessaires à la connectivité RDMA. (Dans certains déploiements des instances A8 et A9, l’extension HpcVmDrivers est ajoutée automatiquement.) Pour ajouter l’extension de machine virtuelle sur une machine virtuelle, vous pouvez utiliser les cmdlets [Azure PowerShell](/powershell/azure/overview). 

  
  La commande suivante installe la dernière version 1.1 de l’extension HpcVMDrivers sur une machine virtuelle existante prenant en charge RDMA et nommée *myVM* déployée dans le groupe de ressources nommé *myResourceGroup* dans la région *Ouest des États-Unis* :

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Pour plus d’informations, consultez [Extensions et fonctionnalités de la machine virtuelle](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vous pouvez également utiliser les extensions pour les machines virtuelles déployées dans le [modèle de déploiement classique](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>Utilisation de HPC Pack

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solution gratuite de gestion des travaux et des clusters HPC de Microsoft, vous offre un moyen de créer un cluster de calcul dans Azure pour exécuter des applications MPI basées sur Windows et d’autres charges de travail HPC. HPC Pack 2012 R2 et les versions ultérieures incluent un environnement d’exécution pour MS-MPI qui utilise le réseau RDMA Azure en cas de déploiement sur des machines virtuelles prenant en charge RDMA.



## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](../virtual-machines-windows-sizes-memory.md)
- [Optimisé pour le stockage](../virtual-machines-windows-sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir les listes de vérification à mettre en œuvre afin d’utiliser les instances nécessitant beaucoup de ressources système avec HPC Pack sur Windows Server, consultez [Configuration d’un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Pour utiliser des instances nécessitant beaucoup de ressources système lors de l’exécution d’applications MPI avec Azure Batch, consultez [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../../batch/batch-mpi.md).

- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.




