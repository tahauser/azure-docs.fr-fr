---
title: "Calcul des scores de test d’évaluation pour les machines virtuelles Windows | Microsoft Docs"
description: "Comparer les scores de test d’évaluation de calcul SPECint pour les machines virtuelles Azure exécutant Windows Server"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cynthn
ms.openlocfilehash: 37eaafdbcee87e6f6a6164dffcabaa7e9efa65ca
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Calcul des scores de test d’évaluation pour les machines virtuelles Windows
Les scores de test d’évaluation SPECInt suivants montrent les performances de calcul pour la gamme de machines virtuelles hautes performances d’Azure exécutant Windows Server. Les scores de test d’évaluation de calcul sont également disponibles pour [les machines virtuelles Linux](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="a-series---compute-intensive"></a>Série A - Calcul intensif
| Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Taux de base moy. | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2,6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2,6 GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2,6 GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2,6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Série Dv2
| Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Taux de base moy. | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |130.5 |4.4 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2,4 GHz |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2,4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>Série G, Série GS
| Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Taux de base moy. | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |31 |71.8 |6.5 |
| Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |5 |133.4 |13.0 |
| Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |6 |242.3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |15 |398.9 |6.0 |
| Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>Série H
| Taille | Processeurs virtuels | Nœuds NUMA | UC | Exécutions | Taux de base moy.  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |297,4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |575,8 |6,8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |297,0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |572,2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 @ 3,2 GHz |5 |573,2 |2,9 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 @ 3,2 GHz |7 |569,6 |2.8 |

## <a name="about-specint"></a>À propos de SPECint
Les chiffres Windows ont été calculés en exécutant [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) sur Windows Server. SPECint a été exécuté à l’aide de l’option de taux de base (SPECint_rate2006), avec une copie par processeur virtuel. SPECint se compose de 12 tests distincts, chacun exécuté trois fois, en prenant la valeur médiane de chaque test et en les pondérant pour former un score composite. Ces tests ont été exécutés sur plusieurs machines virtuelles pour fournir les scores moyens indiqués.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les capacités de stockage, les détails du disque et d’autres considérations relatives au choix d’une taille de machine virtuelle, consultez [Tailles des machines virtuelles](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

