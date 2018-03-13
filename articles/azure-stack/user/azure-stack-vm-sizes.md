---
title: Tailles de machine virtuelle prises en charge dans Azure Stack | Microsoft Docs
description: "Référence pour les tailles de machine virtuelle prises en charge dans Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: brenduns
ms.openlocfilehash: b1a95d2510640887c6edf8e04cd7b1c7fff25275
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="virtual-machine-sizes-supported-in-azure-stack"></a>Tailles de machine virtuelle prises en charge dans Azure Stack

Cet article répertorie les tailles de machine virtuelle prises en charge dans Azure Stack. 


## <a name="general-purpose"></a>Usage général

### <a name="basic-a"></a>De base A
|Taille - Taille\Nom |Processeurs virtuels     |Mémoire | Taille max. du disque temporaire | Débit de disque du système d’exploitation max. : (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Débit de disque de données max. (E/S par seconde) |
|-----------------|-----|---------|---------|-----|------|-----------|
|**A0\Basic_A0**  |1    |768 Mo   | 20 Go   |300  | 300  |1 / 1x300  |
|**A1\Basic_A1**  |1    |1,75 Go  | 40 Go   |300  | 300  |2 / 2x300  |
|**A2\Basic_A2**  |2    |3,5 Go   | 60 Go   |300  | 300  |4 / 4x300  |
|**A3\Basic_A3**  |4    |7 Go     | 120 Go  |300  | 300  |8 / 8x300  |
|**A4\Basic_A4**  |8    |14 Go    | 240 Go  |300  | 300  |6 / 16X300 |

### <a name="standard-a"></a>Standard A 
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1 |0,768 |20  |500 |500 |1 x 500  |2 / 100  |
|**Standard_A1** |1 |1,75  |70  |500 |500 |2 x 500  |2 / 500  |
|**Standard_A2** |2 |3,5   |135 |500 |500 |4 x 500  |2 / 500  |
|**Standard_A3** |4 |7     |285 |500 |500 |8 x 500  |2 / 1 000 |
|**Standard_A4** |8 |14    |605 |500 |500 |16 x 500 |4 / 2 000 |
|**Standard_A5** |2 |14    |135 |500 |500 |4 x 500  |2 / 500  |
|**Standard_A6** |4 |28    |285 |500 |500 |8 x 500  |2 / 1 000 |
|**Standard_A7** |8 |56    |605 |500 |500 |16 x 500 |4 / 2 000 |


### <a name="d-series"></a>Série D
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |
|----------------|----|----|-----|----|------|------------|---------|
|**Standard_D1** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |2 / 500  |
|**Standard_D2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 / 1 000 |
|**Standard_D3** |4   |14  |200  |500 |12 000 |16 / 16 x 500 |4 / 2 000 |
|**Standard_D4** |8   |28  |400  |500 |24 000 |32 / 32 x 500 |8 / 4 000 |


### <a name="ds-series"></a>Série DS
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3,5 |7    |1 000 |4000  |4 / 4x2 300   |2 / 500  |
|**Standard_DS2** |2   |7   |14   |1 000 |8000  |8 / 8x2 300   |2 / 1 000 |
|**Standard_DS3** |4   |14  |28   |1 000 |16000 |16 / 16x2 300 |4 / 2 000 |
|**Standard_DS4** |8   |28  |56   |1 000 |32000 |32 / 32x2 300 |8 / 4 000 |

### <a name="dv2-series"></a>Série Dv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |2 / 750  |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 / 1 500 |
|**Standard_D3_v2** |4   |14  |200  |500 |12 000 |16 / 16 x 500 |4 / 3 000 |
|**Standard_D4_v2** |8   |28  |400  |500 |24 000 |32 / 32 x 500 |8 / 6 000 |
|**Standard_D5_v2** |16  |56  |800  |500 |48 000 |64 / 64 x 500 |8 / 6 000 - 12 000 |

### <a name="dsv2-series"></a>Séries DSv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1 000 |4000  |4 / 4x2 300   |2 / 750  |
|**Standard_DS2_v2** |2   |7   |14  |1 000 |8000  |8 / 8x2 300   |2 / 1 500 |
|**Standard_DS3_v2** |4   |14  |28  |1 000 |16000 |16 / 16x2 300 |4 / 3 000 |
|**Standard_DS4_v2** |8   |28  |56  |1 000 |32000 |32 / 32x2 300 |8 / 6 000 |
|**Standard_DS5_v2** |16  |56  |112 |1 000 |64 000 |64 / 64x2 300 |8 / 6 000 - 12 000 |


## <a name="memory-optimized"></a>Mémoire optimisée

### <a name="mo-d"></a>Série D
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |
|------------------|---|----|----|--------|------|------------|---------|
|**Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8 x 500   |2 / 1 000 |
|**Standard_D12**  |4  |28  |200 |500     |12 000 |16 / 16 x 500 |4 / 2 000 |
|**Standard_D13**  |8  |56  |400 |500     |24 000 |32 / 32 x 500 |8 / 4 000|
|**Standard_D14**  |16 |112 |800 |500     |48 000 |64 / 64 x 500 |8 / 6 000 - 8 000 |

### <a name="mo-ds"></a>Série DS
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1 000    |8000  |8 / 8x2 300   |2 / 1 000 |
|**Standard_DS12**  |4  |28  |56  |1 000    |12 000 |16 / 16x2 300 |4 / 2 000 |
|**Standard_DS13**  |8  |56  |112 |1 000    |32000 |32 / 32x2 300 |8 / 4 000 |
|**Standard_DS14**  |16 |112 |224 |1 000    |64 000 |64 / 64x2 300 |8 / 6 000 - 12 000 |

### <a name="mo-dv2"></a>Série Dv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8 x 500    |2 / 1 500 |
|**Standard_D12_v2** |4   |28  |200  |500 |12 000  |16 / 16 x 500  |4 / 3 000 |
|**Standard_D13_v2** |8   |56  |400  |500 |24 000  |32 / 32 x 500  |8 / 6 000 |
|**Standard_D14_v2** |16  |112 |800  |500 |48 000  |64 / 64 x 500  |8 / 6 000 - 12 000 |


### <a name="mo-dsv2"></a>Série DSv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max. de cartes réseau / bande passante réseau attendue (Mbits/s) |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1 000 |8000   |8 / 8x2 300    |2 / 1 500 |
|**Standard_DS12_v2** |4   |28  |56   |1 000 |16000  |16 / 16x2 300  |2 / 3 000 |
|**Standard_DS13_v2** |8   |56  |112  |1 000 |32000  |32 / 32x2 300  |4 / 6 000 |
|**Standard_DS14_v2** |16  |112 |224  |1 000 |64 000  |64 / 64x2 300  |8 / 12000 |


## <a name="next-steps"></a>Étapes suivantes

[Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md)
