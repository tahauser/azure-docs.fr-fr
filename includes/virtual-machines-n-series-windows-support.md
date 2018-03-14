---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>NC, NCv2, NCv3 et série ND : pilotes NVIDIA Tesla

| SE | Pilote |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

> [!NOTE]
> Les liens de téléchargement de pilotes Tesla sont à jour au moment de la publication. Pour les pilotes les plus récents, visitez le site Web de [NVIDIA](http://www.nvidia.com/).
>

### <a name="nv-series---nvidia-grid-drivers"></a>Série NV : pilotes NVIDIA GRID

| SE | Pilote |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |

> [!NOTE]
> Microsoft redistribue les programmes d’installation du pilote NVIDIA GRID pour les machines virtuelles NV. Installez uniquement ces pilotes GRID sur des machines virtuelles Azure NV. Ces pilotes incluent les licences des logiciels GRID Virtual GPU dans Azure.
>