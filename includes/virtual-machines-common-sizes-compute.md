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
ms.openlocfilehash: 9602e8d73e5aca650dd20da34a9aa675b508ada7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
<!-- F-series, Fs-series* -->

Les tailles de machine virtuelle optimisées pour le calcul ont un ratio processeur/mémoire élevé et conviennent aux serveurs web, appliances réseau, processus de traitement par lots et serveurs d’applications à trafic moyen. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement.

La série Fsv2 repose sur le processeur Intel® Xeon® Platinum 8168, avec une fréquence de base de 2,7 GHz et une fréquence turbo monocœur maximale de 3,7 GHz. Les instructions Intel® AVX-512, une nouveauté sur les processeurs évolutifs Intel, fourniront jusqu'à 2X plus de performances pour les charges de travail de traitement vectoriel pour les opérations en virgule flottante simple ou double précision. En d’autres termes, elles sont très rapides pour les charges de travail de calcul. 

Affichant le coût le plus bas par heure, la série Fsv2 offre le meilleur rapport prix-performances de la gamme Azure si l’on considère les unités de calcul Azure (ACU) par processeur virtuel. 

La série F est basée sur le processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz pouvant aller jusqu’à 3,1 GHz avec Intel Turbo Boost Technology 2.0. Ce sont les mêmes performances d’UC que la série Dv2 de machines virtuelles.  

Les machines virtuelles de la série F sont un excellent choix pour les charges de travail qui exigent des processeurs plus rapides, mais n’ont pas besoin d’autant de mémoire ou de stockage temporaire par processeur virtuel.  Les charges de travail telles que l’analyse, les serveurs de jeux, les serveurs web et le traitement par lots tireront avantage de la série F.

La série Fs propose tous les avantages de la série F, en plus du stockage Premium.

## <a name="fsv2-series-sup1sup"></a>Série Fsv2 <sup>1</sup>

ACU : 195 - 210

| Taille             | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Nombre max de cartes réseau / Bande passante réseau attendue (Mbits/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | Modéré                                       |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | Modéré                                       |
| Standard_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | Élevé                                           |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | Élevé                                           |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | Extrêmement élevée                                 |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | Extrêmement élevée                                 |
| Standard_F72s_v2<sup>2, 3</sup> | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | Extrêmement élevée                                 |

<sup>1</sup> Machines virtuelles de série Fsv2 dotées de la technologie Hyper-Threading d’Intel®

<sup>2</sup> Avec plus de 64 processeurs virtuels, vous devez utiliser l’un des systèmes d’exploitation invités pris en charge suivants : Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 et Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 avec LIS 4.2.1.

<sup>3</sup> L’instance est isolée sur un matériel dédié à un client unique.

## <a name="fs-series-sup1sup"></a>Série Fs <sup>1</sup>

ACU : 210-250

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et en cache max : E/S par seconde / Mbits/s (taille du cache en Gio) | Débit de disque maximal sans mise en cache : E/S / Mbits/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbits/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4 000 / 32 (12) |3 200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8 000 / 64 (24) |6 400 / 96 |2 / 1 500 |
| Standard_F4s |4 |8 |16 |16 |16 000 / 128 (48) |12 800 / 192 |4 / 3 000 |
| Standard_F8s |8 |16 |32 |32 |32 000 / 256 (96) |25 600 / 384 |8 / 6 000 |
| Standard_F16s |16 |32 |64 |64 |64 000 / 512 (192) |51 200 / 768 |8 / 12000 |

Mbits/s = 10^6 octets par seconde, et Gio = 1024^3 octets.

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série Fs peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/virtual-machines/windows/premium-storage.md).


<br>

## <a name="f-series"></a>Série F

ACU : 210-250

| Taille         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (Mbits/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000           |


<br>


