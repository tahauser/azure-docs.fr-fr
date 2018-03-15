---
title: Choisir des tailles de machines virtuelles pour des pools Azure Batch | Microsoft Docs
description: "Quelle taille de machine virtuelle choisir parmi celles disponibles pour les nœuds de calcul dans des pools Azure Batch"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: addd1e9314a754b40cc5d49c0299f007580f512f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Choisir une taille de machine virtuelle pour des nœuds de calcul dans un pool Azure Batch

Lorsque vous sélectionnez une taille de nœud pour un pool Azure Batch, vous avez le choix entre quasiment toutes les tailles de machine virtuelle disponibles dans Azure. Azure propose une gamme de tailles pour les machines virtuelles Windows et Linux pour différentes charges de travail. 

Quelques exceptions et limites s’appliquent quant au choix d’une taille de machine virtuelle :
* Certaines familles ou tailles de machine virtuelle ne sont pas prises en charge dans Batch. 
* Certaines tailles de machine virtuelle sont limitées et doivent être activées explicitement avant de pouvoir être allouées.


## <a name="supported-vm-families-and-sizes"></a>Tailles et familles de machine virtuelle prises en charge

### <a name="pools-in-virtual-machine-configuration"></a>Pools dans la configuration de la machine virtuelle

Les pools Batch dans la configuration de la machine virtuelle prennent en charge toutes les tailles de machine virtuelle ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *sauf* les suivantes :

| Famille  | Tailles non prises en charge  |
|---------|---------|
| Série A de base | Basic_A0 (A0) |
| Série A | Standard_A0 |
| Série B | Tous |
| Série Fsv2 <sup>*</sup> | Tous |

<sup>*</sup>Les tailles de cette série sont sur la feuille de route pour une prise en charge future.

### <a name="pools-in-cloud-service-configuration"></a>Pools dans la configuration de service cloud

Les pools Batch dans la configuration de service cloud prennent en charge toutes les [tailles de machine virtuelle pour Services cloud](../cloud-services/cloud-services-sizes-specs.md), *sauf* les suivantes :

| Famille  | Tailles non prises en charge  |
|---------|---------|
| Série A | Très petite |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Familles de machines virtuelles limitées
Les familles de machines virtuelles suivantes peuvent être allouées dans des pools Batch, mais vous devez demander une augmentation du quota spécifique (consultez [cet article](batch-quota-limit.md#increase-a-quota)) :
* Série NCv2
* Série NCv3
* Série ND

Ces tailles sont utilisables uniquement avec les pools dans la configuration de machine virtuelle.

## <a name="size-considerations"></a>Considérations en matière de taille

* **Exigences pour l’application** : tenez compte des caractéristiques et des exigences de l’application que vous allez exécuter sur les nœuds. Des aspects tels que la nature multithread de l’application et le volume de mémoire utilisé vous aideront à déterminer la taille de nœud la mieux adaptée et la plus rentable. Pour les applications CUDA ou [charges de travail MPI](batch-mpi.md) avec plusieurs instances, pensez aux tailles de machine virtuelles [compatibles GPU](../virtual-machines/linux/sizes-hpc.md) ou [HPC](../virtual-machines/linux/sizes-gpu.md), respectivement. (Consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](batch-pool-compute-intensive-sizes.md).) 

* **Tâches par nœud** : la taille du nœud est souvent sélectionnée en supposant qu’une tâche s’exécute sur un nœud à la fois. Cependant, plusieurs tâches (et par conséquent, plusieurs instances d’application) peuvent [s’exécuter en parallèle](batch-parallel-node-tasks.md) sur les nœuds de calcul lors de l’exécution du travail. Dans ce cas, il est courant de choisir une taille de nœud multicœur pour prendre en charge la demande accrue de l’exécution parallèle des tâches.

* **Charger des niveaux pour différentes tâches** : tous les nœuds dans un pool ont la même taille. Si vous prévoyez d’exécuter des applications dont la configuration système requise et/ou les niveaux de charge diffèrent, nous vous recommandons d’utiliser des pools distincts. 

* **Disponibilité de la région** : une famille ou une taille de machines virtuelles peut ne pas être disponible dans les régions où vous créez vos comptes Batch. Pour vérifier qu’une taille est disponible, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

* **Quotas** : les [quotas de cœurs](batch-quota-limit.md#resource-quotas) dans votre compte Batch peuvent limiter le nombre de nœuds d’une taille donnée que vous pouvez ajouter à un pool Batch. Pour demander une augmentation du quota, consultez [cet article](batch-quota-limit.md#increase-a-quota). 

* **Configuration du pool** : en général, vous avez plusieurs options de taille de machine virtuelle lorsque vous créez un pool dans la configuration de machine virtuelle, en comparaison avec la configuration de service cloud.

## <a name="next-steps"></a>étapes suivantes

* Pour obtenir une présentation détaillée de Batch, consultez [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).
* Pour plus d’informations sur l’utilisation de tailles de machine virtuelle nécessitant beaucoup de ressources système, consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](batch-pool-compute-intensive-sizes.md). 


