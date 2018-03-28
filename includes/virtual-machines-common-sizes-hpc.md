---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ee145e5784ae6da6cce22f1b21f9a5d45335ea8b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
Les tailles A8 à A11 et celles de la série H sont également appelées *instances nécessitant beaucoup de ressources système*. Le matériel qui exécute ces tailles a été conçu et optimisé pour les applications nécessitant beaucoup de ressources système et réseau, notamment les applications en cluster pour des calculs complexes, la modélisation et les simulations. La série A8-A11 utilise un processeur Intel Xeon E5-2670 cadencé à 2,6 GHZ, et la série H un processeur Intel Xeon E5-2667 v3 cadencé à 3,2 GHz.  Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement. 

Les machines virtuelles de la série H sont les machines virtuelles de calcul haute performance les plus récentes, destinées à répondre à des besoins de calcul de haut niveau, par exemple en relation avec la modélisation moléculaire et la dynamique des fluides. Les machines virtuelles à 8 et 16 processeurs virtuels reposent sur la technologie de processeur Intel Haswell E5-2667 V3 avec mémoire DDR4 et stockage temporaire SSD. 

En plus de la puissance substantielle du processeur, la série H offre différentes options pour des réseaux RDMA à faible latence, en utilisant FDR InfiniBand et plusieurs configurations de mémoire pouvant satisfaire des exigences de calcul nécessitant une mémoire conséquente.



## <a name="h-series"></a>Série H

ACU : 290-300

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de disque max : E/S par seconde | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1 000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1 000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR, qui garantit une très faible latence et une large bande passante.

<br>



## <a name="a-series---compute-intensive-instances"></a>Série A - Instances de calcul intensif

ACU : 225

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (HDD) : Gio | Disques de données max. | Débit de disque de données max : E/S par seconde | Nombre max de cartes réseau|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup>Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR, qui garantit une très faible latence et une large bande passante.

<br>



