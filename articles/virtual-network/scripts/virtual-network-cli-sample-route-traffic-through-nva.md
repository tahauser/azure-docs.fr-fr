---
title: Exemple de script Azure CLI - Acheminer le trafic via une appliance virtuelle réseau | Microsoft Docs
description: Exemple de script Azure CLI - Acheminer le trafic via une appliance virtuelle réseau de pare-feu.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 8506d67152a21586b2854891674d50f89d1a643c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Acheminer le trafic via une appliance virtuelle réseau

Cet exemple de script permet de créer un réseau virtuel avec des sous-réseaux frontaux et principaux. Il crée également une machine virtuelle sur laquelle le transfert IP est activé pour acheminer le trafic entre les deux sous-réseaux. Après avoir exécuté le script, vous pouvez déployer un logiciel réseau, telle qu’une application de pare-feu, sur la machine virtuelle.

Vous pouvez exécuter le script à partir d’Azure [Cloud Shell](https://shell.azure.com/bash) ou à partir d’une installation Azure CLI locale. Si vous utilisez l’interface CLI localement, ce script nécessite que vous exécutiez la version 2.0.28 ou une version ultérieure. Pour trouver la version installée, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous exécutez la CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un réseau virtuel et les groupes de sécurité réseau. Chaque commande du tableau suivant renvoie à une documentation spécifique :

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Crée un réseau virtuel et un sous-réseau frontal Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Crée des sous-réseaux principaux et DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Crée une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Crée une interface réseau virtuelle et active le transfert IP pour celle-ci. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Crée un groupe de sécurité réseau (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Crée des règles NSG qui autorisent des ports HTTP et HTTPS entrants sur la machine virtuelle. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Associe les NSG et les tables de routage aux sous-réseaux. |
| [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create)| Crée une table de routage pour tous les itinéraires. |
| [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create)| Créer des itinéraires pour acheminer le trafic entre les sous-réseaux et Internet via la machine virtuelle. |
| [az vm create](/cli/azure/vm#az_vm_create) | Crée une machine virtuelle et lui associe la carte d’interface réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, ainsi que toutes ses ressources. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI pour réseau virtuel dans [Exemples CLI pour réseau virtuel](../cli-samples.md).
