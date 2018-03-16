---
title: "Redéploiement des machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Redéploiement des machines virtuelles Linux dans Azure pour atténuer les problèmes de connexion SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: 484e23f7f5800faf4a1d83c2386e0c766c3a1f2d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Redéployer une machine virtuelle vers un nouveau nœud Azure
Si vous êtes confronté à des problèmes d’accès SSH ou d’accès des applications à une machine virtuelle Linux dans Azure, vous pouvez tenter de redéployer la machine virtuelle. Lorsque vous redéployez une machine virtuelle, celle-ci est déplacée vers un nouveau nœud au sein de l’infrastructure Azure, puis remise en service. Toutes les options de configuration et les ressources associées sont conservées. Cet article vous montre comment redéployer une machine virtuelle à l’aide de l’interface de ligne de commande Azure ou du portail Azure.

> [!NOTE]
> Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour. 

Vous pouvez redéployer une machine virtuelle à l’aide d’une des options suivantes. Choisissez une seule option pour redéployer votre machine virtuelle :

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Portail Azure](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Utiliser Azure CLI 2.0
Installez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à votre compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

Redéployez votre machine virtuelle avec la commande [az vm redeploy](/cli/azure/vm#az_vm_redeploy). L’exemple suivant permet de redéployer la machine virtuelle *myVM* dans le groupe de ressources *myResourceGroup* :

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Utilisation de la CLI Azure 1.0
Installez la dernière version [d’Azure CLI 1.0](../../cli-install-nodejs.md) et connectez-vous à votre compte Azure. Vérifiez que vous êtes en mode Resource Manager (`azure config mode arm`).

L’exemple suivant permet de redéployer la machine virtuelle *myVM* dans le groupe de ressources *myResourceGroup* :

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez rechercher une aide spécifique sur le [dépannage des connexions SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou sur les [étapes de dépannage détaillées des connexions SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous rencontrez des problèmes de connexion à votre machine virtuelle. Vous pouvez également lire des informations sur les [problèmes de dépannage des applications](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous ne pouvez pas accéder à une application exécutée sur votre machine virtuelle.

