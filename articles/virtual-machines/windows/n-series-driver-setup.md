---
title: Configuration du pilote série N Azure pour Windows | Microsoft Docs
description: Procédure de configuration des pilotes GPU NVIDIA pour les machines virtuelles série N exécutant Windows dans Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61bcbe23ec3afd7091084dd0102010a9b8ef0b7c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Configuration des pilotes GPU NVIDIA pour les machines virtuelles série N exécutant Windows Server
Pour tirer parti des fonctionnalités GPU des machines virtuelles série N Azure exécutant Windows Server 2016 ou Windows Server 2012 R2, les pilotes graphiques NVIDIA doivent être installés. Cet article vous offre des étapes de configuration de pilote lorsque vous avez déployé une machine virtuelle de série N. Des informations de configuration du pilote sont également disponibles pour [les machines virtuelles Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour obtenir les spécifications de base, les capacités de stockage et les informations relatives aux disques, consultez [GPU Windows VM sizes](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Tailles de machine virtuelle Windows GPU). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Installation du pilote

1. Connectez-vous à chaque machine virtuelle série N à l’aide du Bureau à distance.

2. Téléchargez, extrayez et installez le pilote pris en charge pour votre système d’exploitation Windows.

Sur les machines virtuelles NV Azure, le redémarrage est nécessaire après l’installation du pilote. Sur les machines virtuelles NC, le redémarrage n’est pas nécessaire.

## <a name="verify-driver-installation"></a>Vérification de l’installation du pilote

Vous pouvez vérifier l’installation du pilote dans le Gestionnaire de périphériques. L’exemple suivant illustre une configuration réussie de la carte Tesla K80 sur une machine virtuelle NC Azure.

![Propriétés du pilote GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Pour interroger l’état de l’appareil GPU, exécutez l’utilitaire de ligne de commande [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installé avec le pilote.

1. Ouvrez une invite de commandes et apportez vos modifications dans le répertoire **C:\Program Files\NVIDIA Corporation\NVSMI**.

2. Exécutez `nvidia-smi`. Si le pilote est installé, vous verrez un résultat du type suivant. **GPU-Util** affiche **0 %** sauf si vous exécutez actuellement une charge de travail GPU sur la machine virtuelle. La version de votre pilote et vos détails de GPU peuvent différer de ceux indiqués.

![État de l’appareil NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Connectivité réseau RDMA

La connectivité réseau RDMA peut être activée sur des machines virtuelles de série N compatibles RDMA, comme les machines NC24r déployées dans le même groupe à haute disponibilité ou groupe identique de machines virtuelles. L’extension HpcVmDrivers doit être ajoutée pour installer les pilotes d’appareils réseau Windows nécessaires à la connectivité RDMA. Pour ajouter l’extension de machine virtuelle sur une machine virtuelle de série N compatible RDMA, utilisez les cmdlets [Azure PowerShell](/powershell/azure/overview) pour Azure Resource Manager.

Pour installer la dernière version 1.1 de l’extension HpcVMDrivers sur une machine virtuelle existante prenant en charge RDMA et nommée myVM dans la région États-Unis de l'Ouest :
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Pour plus d’informations, consultez [Extensions et fonctionnalités de machine virtuelle pour Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Le réseau RDMA prend en charge le trafic MPI (Message Passing Interface) pour les applications exécutées avec [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) ou Intel MPI 5.x. 


## <a name="next-steps"></a>Étapes suivantes

* Les développeurs qui créent des applications avec accélération GPU pour les GPU Tesla NVIDIA peuvent également télécharger et installer le [kit d’outils 9.1 CUDA](https://developer.nvidia.com/cuda-downloads). Pour plus d’informations, consultez le [Guide d’installation de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


