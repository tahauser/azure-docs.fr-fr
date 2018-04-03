---
title: Exemples Azure CLI 2.0 | Microsoft Docs
description: Exemples Azure CLI 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 69fc81d4d0569ee7a66fbda5ab500ef2ee15c694
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cli-20-samples-for-virtual-machine-scale-sets"></a>Exemples Azure CLI 2.0 pour les groupes de machines virtuelles identiques

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide d’Aure CLI 2.0.

| | |
|---|---|
|**Créer et gérer un groupe identique**||
| [Créer un groupe de machines virtuelles identiques](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un groupe de machines virtuelles identiques avec une configuration minimale. |
| [Créer un groupe identique à partir d’une image de machine virtuelle personnalisée](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | Crée un groupe de machines virtuelles identiques qui utilise une image de machine virtuelle personnalisée. |
| [Installer des applications sur un groupe identique](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | Utilisez l’extension de script personnalisé Azure pour installer une application web de base dans un groupe identique. |
|**Gérer le stockage**||
| [Créer et attacher des disques à un groupe identique](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Crée un groupe de machines virtuelles identiques avec des disques de données attachés. |
|**Gérer la mise à l’échelle et la redondance**||
| [Activer la mise à l’échelle automatique basée sur l’hôte](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une mise à l’échelle de machine virtuelle qui est configurée pour se mettre automatiquement à l’échelle en fonction de l’utilisation de l’UC. |
| [Créer un groupe identique sur une zone unique](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un groupe de machines virtuelles identiques qui utilise une zone de disponibilité unique. |
| [Créer un groupe identique redondant dans une zone](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un groupe de machines virtuelles identiques sur plusieurs zones de disponibilité. |
| | |