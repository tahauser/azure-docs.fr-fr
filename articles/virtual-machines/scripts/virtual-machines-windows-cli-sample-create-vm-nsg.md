---
title: "Exemple de script Azure CLI - Créer deux machines virtuelles avec un groupe de sécurité réseau interne et un groupe de sécurité réseau externe | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer deux machines virtuelles avec un groupe de sécurité réseau interne et un groupe de sécurité réseau externe"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: a74eead266451d15ea22538e19c5f12bf04c08b8
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Sécuriser le trafic réseau entre les machines virtuelles

Ce script crée deux machines virtuelles et sécurise le trafic entrant vers les deux machines. Une machine virtuelle est accessible sur Internet et dispose d’un groupe de sécurité réseau configuré pour autoriser le trafic sur le port 3389 et le port 80. La seconde machine virtuelle n’est pas accessible sur Internet et elle dispose d’un groupe de sécurité réseau configuré pour autoriser uniquement le trafic provenant de la première machine virtuelle. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Crée un réseau virtuel et un sous-réseau Azure. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Crée un sous-réseau. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Crée la machine virtuelle et l’associe à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_update) | Met à jour une règle de groupe de sécurité réseau. Dans cet exemple, la règle principale est mise à jour pour transférer le trafic uniquement à partir du sous-réseau frontal. |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_list) | Renvoie les informations relatives à une règle de groupe de sécurité réseau. Dans cet exemple, le nom de la règle est stocké dans une variable à utiliser ultérieurement dans le script. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
