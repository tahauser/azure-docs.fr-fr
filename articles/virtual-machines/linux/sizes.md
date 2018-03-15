---
title: Tailles des machines virtuelles Linux dans Azure | Microsoft Docs
description: "Répertorie les différentes tailles disponibles pour les machines virtuelles Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jonbeck
ms.openlocfilehash: 3f2c44a6402845ddfbc5c6453968817d6e49f64f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Tailles des machines virtuelles Linux dans Azure
Cet article décrit les tailles et options disponibles pour les machines virtuelles Azure que vous pouvez utiliser pour exécuter vos applications et charges de travail Linux. Il expose également les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources. Cet article est également disponible pour les [machines virtuelles Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| type                     | Tailles           |    DESCRIPTION       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Usage général](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7,  | Ratio processeur/mémoire équilibré. Idéal pour le test et le développement, les bases de données petites à moyennes et les serveurs web au trafic faible à moyen. |
| [Optimisé pour le calcul](sizes-compute.md)        | Fsv2, Fs, F             | Ratio processeur/mémoire élevé. Convient pour les serveurs web au trafic moyen, les appareils réseau, les processus de traitement par lots et les serveurs d’application.        |
| [Mémoire optimisée](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Ratio mémoire/processeur élevé. Idéal pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire.                 |
| [Optimisé pour le stockage](sizes-storage.md)        | Ls                | Débit de disque et E/S élevés. Idéale pour les bases de données NoSQL, SQL et Big Data.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC, NCv2, NCv3, ND            | Machines virtuelles spécialisées, ciblées pour l’affichage de graphiques complexes et le montage vidéo, ainsi que pour la formation et l’inférence de modèles avec apprentissage approfondi. Disponible avec un ou plusieurs GPU.       |
| [Calcul haute performance](sizes-hpc.md) | H, A8-11          | Nos machines virtuelles les plus rapides et dotées des processeurs les plus puissants avec interfaces réseau haut débit en option (RDMA). 

<br>

- Pour plus d'informations sur la tarification des différentes tailles, consultez [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Pour la disponibilité des tailles de machine virtuelle dans les régions Azure, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).
- Pour connaître les limites générales des machines virtuelles Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../../azure-subscription-service-limits.md).
- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.


## <a name="rest-api"></a>API REST

Pour plus d’informations sur l’utilisation de l’API REST pour demander la taille des machines virtuelles, consultez les rubriques suivantes :

- [Répertorier les tailles disponibles des machines virtuelles pour le redimensionnement](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)
- [Répertorier les tailles disponibles des machines virtuelles pour un abonnement](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)
- [Répertorier les tailles de machine virtuelle disponibles dans un groupe à haute disponibilité](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)

## <a name="acu"></a>ACU

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.

## <a name="next-steps"></a>étapes suivantes

En savoir plus sur les différentes tailles de machines virtuelles qui sont disponibles :
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)



