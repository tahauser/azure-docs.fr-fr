---
title: "Exemples de script Azure CLI - Créer un disque managé à partir d’une capture instantanée | Microsoft Docs"
description: "Exemples de script Azure CLI - Créer un disque managé à partir d’une capture instantanée"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 3a6c741933d48c22ec38d98fee9591d45877ad84
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Créer un disque managé à partir d’une capture instantanée avec l’interface de ligne de commande

Ce script crée un disque managé à partir d’une capture instantanée. Utilisez-le pour restaurer une machine virtuelle à partir de captures instantanées de disques de système d’exploitation et de données. Créez des disques managés de système d’exploitation et de données à partir des captures instantanées respectives, puis créez une machine virtuelle en joignant les disques managés. Vous pouvez également restaurer les disques de données d’une machine virtuelle existante en joignant les disques de données créés à partir de captures instantanées.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un disque managé à partir d’une capture instantanée. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Obtient toutes les propriétés d’une capture instantanée en utilisant les propriétés de nom et de groupe de ressources de la capture instantanée. La propriété de l’identifiant est utilisée pour créer le disque managé.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Crée un disque managé à l’aide de l’identifiant d’une capture instantanée managée. |

## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle en joignant un disque managé en tant que disque de système d’exploitation](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle et de disques managés dans la [documentation relative aux machines virtuelles Linux Azure](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
