---
title: Fichier Include
description: Fichier Include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>Distributions et pilotes pris en charge

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC, NCv2, NCv3 et série ND : pilotes NVIDIA CUDA
| Distribution | Pilote |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 ou 7.4<br/><br/> CentOS 7.3 ou 7.4 | NVIDIA CUDA 9.1, branche pilote R390 |

> [!IMPORTANT]
> Assurez-vous que vous installez ou mettez à niveau en utilisant les derniers pilotes CUDA pour votre distribution. Les pilotes antérieurs à la version R390 peuvent présenter des problèmes avec les noyaux Linux mis à jour.
>

### <a name="nv-series---nvidia-grid-drivers"></a>Série NV : pilotes NVIDIA GRID

| Distribution | Pilote |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>Basé sur CentOS 7.3 | NVIDIA GRID 5.2, branche pilote R384|

> [!NOTE]
> Microsoft redistribue les programmes d’installation du pilote NVIDIA GRID pour les machines virtuelles NV. Installez uniquement ces pilotes GRID sur des machines virtuelles Azure NV. Ces pilotes incluent les licences des logiciels GRID Virtual GPU dans Azure.
>

> [!WARNING] 
> L’installation de logiciels tiers sur des produits Red Hat peut affecter les conditions de prise en charge de Red Hat. Consultez l’[article de la Base de connaissances Red Hat](https://access.redhat.com/articles/1067).
>
