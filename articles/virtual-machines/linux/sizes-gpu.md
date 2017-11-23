---
title: Tailles des machines virtuelles Linux Azure - GPU | Microsoft Docs
description: "Répertorie les différentes tailles de GPU optimisées disponibles pour les machines virtuelles Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 645f69e71c5a13bb70edabfd22f51ed5df619693
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tailles de machine virtuelle à GPU optimisé

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Pour les étapes d’installation et de vérification des pilotes, consultez l’article sur l’[installation de pilotes de la série N pour Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Il est préférable de ne pas installer un serveur spécifique ou d’autres systèmes qui utilisent le pilote `Nouveau` sur les machines virtuelles Ubuntu NC. Avant d’installer les pilotes GPU NVIDIA, vous devez désactiver le pilote `Nouveau`.  

## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Calcul haute performance](sizes-hpc.md)

## <a name="next-steps"></a>Étapes suivantes
Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.