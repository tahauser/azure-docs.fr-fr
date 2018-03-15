---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 34b38ff02d401e87be10f1f72cb2025b66317c9e
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
Les tailles de machine virtuelle au GPU optimisé sont des machines virtuelles spécialisées disponibles avec des GPU NVIDIA uniques ou multiples. Ces tailles sont conçues pour des charges de travail de visualisation, mais également de calcul et d’affichage graphique intensifs. Cet article fournit des informations sur le nombre et le type de GPU, de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement. 

* Les tailles des cœurs **NC, NCv2, NCv3 et ND** sont optimisées pour les applications nécessitant des ressources réseau et de calcul importantes, les algorithmes, notamment les applications et simulations CUDA et OpenCL, l’intelligence artificielle et le Deep Learning. 
* Les tailles des cœurs **NV** sont optimisées et conçues pour la visualisation à distance, la diffusion en continu, les jeux, le codage et les scénarios VDI utilisant des infrastructures comme OpenGL et DirectX.  


## <a name="nc-series"></a>Série NC

Les machines virtuelles de série NC sont optimisées par la carte [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf). Les utilisateurs peuvent exploiter plus rapidement leurs données en tirant parti de CUDA pour les applications d’exploration d’énergie, de simulations de crash, de rendu avec lancer de rayon, de formation approfondie et bien plus encore. La configuration NC24r fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.


| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6. |56 | 380 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1 440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1 440 | 4 | 64 | 4 |

1 GPU = une moitié de carte K80.

*Prenant en charge RDMA

## <a name="ncv2-series"></a>Série NCv2

Les machines virtuelles de série NCv2 sont optimisées par les GPU [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf). Ces GPU peuvent fournir des performances de calcul deux fois supérieures à celles de la série NC. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. La configuration NC24rs v2 fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) dans votre abonnement est défini au départ sur 0 dans chaque région. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6. |112 | 336 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 672 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 1344 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 1344 | 4 | 32 | 8 |

1 GPU = une carte P100.

*Prenant en charge RDMA

## <a name="ncv3-series"></a>Série NCv3

Les machines virtuelles de série NCv3 sont optimisées par les GPU [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf). Ces GPU peuvent fournir des performances de calcul une fois et demie supérieures à celles de la série NCv2. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. La configuration NC24rs v3 fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) dans votre abonnement est défini au départ sur 0 dans chaque région. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6. |112 | 336 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 672 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 1344 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 1344 | 4 | 32 | 8 |

1 GPU = une carte V100.

*Prenant en charge RDMA

## <a name="nd-series"></a>Série ND

Les machines virtuelles de la série ND sont une nouveauté de la famille de GPU. Elles sont spécialement conçues pour les charges de travail d’intelligence artificielle et de Deep Learning. Elles offrent d’excellentes performances pour l’apprentissage et l’inférence. Les instances ND sont optimisées par des GPU [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Ces instances offrent d’excellentes performances pour les opérations à virgule flottante simple précision, et pour les charges de travail d’intelligence artificielle utilisant Microsoft Cognitive Toolkit, TensorFlow, Caffe et d’autres infrastructures. La série ND offre également une taille de mémoire GPU beaucoup plus importante (24 Go), ce qui permet d’adapter des modèles de réseaux neuronaux beaucoup plus volumineux. À l’instar de la série NC, la série ND offre une configuration avec un réseau à faible latence secondaire et à haut débit grâce à l’accès direct à la mémoire à distance (RDMA), ainsi que la connectivité InfiniBand, de sorte que vous pouvez exécuter des travaux de formation à grande échelle s’étendant sur de nombreux GPU.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) par région dans votre abonnement est défini au départ sur 0. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6. |112 | 336 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 672 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 1344 | 4 | 32 | 8 |
| Standard_ND24rs* |24 |1448 | 1344 | 4 | 32 | 8 |

1 GPU = une carte P40.

*Prenant en charge RDMA

## <a name="nv-series"></a>Série NV

La série NV est optimisée par des GPU [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID pour les applications de bureautique accélérées et les bureaux virtuels où les clients peuvent visualiser leurs données ou simulations. Les utilisateurs peuvent visualiser leurs flux de travail nécessitant beaucoup de graphismes sur les instances NV afin d’obtenir des fonctionnalités graphiques de qualité supérieure et exécuter par ailleurs des charges de travail simple précision comme le codage et le rendu. 

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6. |56 |380 | 1 | 24 | 1 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 |
| Standard_NV24 |24 |224 |1 440 | 4 | 64 | 4 |

1 GPU = une moitié de carte M60.


