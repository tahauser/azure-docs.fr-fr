---
title: "Ouvrir des ports sur une machine virtuelle Linux avec Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Linux à l’aide du modèle de déploiement Azure Resource Manager et d’Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 91908b03522788d470fdb93121f620bfcdef9085
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Ouvrir des ports et des points de terminaison sur une machine virtuelle Linux dans Azure avec Azure CLI
Pour ouvrir un port ou créer un point de terminaison sur une machine virtuelle dans Azure, créez un filtre réseau sur un sous-réseau ou une interface réseau de machine virtuelle. Vous placez ces filtres, qui contrôlent le trafic entrant et sortant, dans un groupe de sécurité réseau associé à la ressource qui reçoit le trafic. Nous allons utiliser un exemple courant de trafic web sur le port 80. Cet article vous montre comment ouvrir un port sur une machine virtuelle avec Azure CLI 2.0. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](nsg-quickstart-nodejs.md).

Pour créer un groupe de sécurité réseau et des règles, la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) doit être installée et connectée à un compte Azure à l’aide de la commande [az login](/cli/azure/#az_login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myNetworkSecurityGroup* et *myVMnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Ouvrir rapidement un port pour une machine virtuelle
Si vous avez besoin d’ouvrir rapidement un port pour une machine virtuelle dans un scénario de développement et de test, vous pouvez utiliser la commande [az vm open-port](/cli/azure/vm#az_vm_open_port). Cette commande crée un groupe de sécurité réseau, ajoute une règle et l’applique à une machine virtuelle ou un sous-réseau. L’exemple suivant ouvre le port *80* sur la machine virtuelle *myVM* dans le groupe de ressources *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Pour mieux contrôler les règles, notamment pour définir une plage d’adresses IP source, effectuez les étapes supplémentaires dans cet article.


## <a name="create-a-network-security-group-and-rules"></a>Créer un groupe de sécurité réseau et les règles associées
Créez le groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). L’exemple suivant crée un groupe de sécurité réseau nommé *myNetworkSecurityGroup* à l’emplacement *eastus* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Ajoutez une règle avec la commande [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) pour autoriser le trafic HTTP sur votre serveur Web (ou adaptez en fonction de votre propre scénario, notamment l’accès SSH ou la connectivité de base de données). L’exemple suivant crée une règle nommée *myNetworkSecurityGroupRule* pour autoriser le trafic TCP sur le port 80 :

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Appliquer le groupe de sécurité réseau à la machine virtuelle
Associez le groupe de sécurité réseau à l’interface réseau (NIC) de votre machine virtuelle avec la commande [az network nic update](/cli/azure/network/nic#az_network_nic_update). L’exemple suivant associe une carte réseau existante nommée *myNic* au groupe de sécurité réseau nommé *myNetworkSecurityGroup* :

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Vous pouvez également associer votre groupe de sécurité réseau à un sous-réseau de réseau virtuel avec la commande [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) et non uniquement à l’interface réseau d’une seule machine virtuelle. L’exemple suivant associe un sous-réseau existant nommé *mySubnet* dans le réseau virtuel *myVnet* au groupe de sécurité réseau nommé *myNetworkSecurityGroup* :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](tutorial-virtual-network.md#secure-network-traffic).

Pour les applications Web hautement disponibles, vous devez placer vos machines virtuelles derrière un équilibreur de charge Azure. L’équilibreur de charge répartit le trafic entre les machines virtuelles, avec un groupe de sécurité réseau qui assure le filtrage du trafic. Pour plus d’informations, consultez [Guide pratique pour équilibrer la charge des machines virtuelles Linux dans Azure pour créer une application hautement disponible](tutorial-load-balancer.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

* [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Présentation du groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md)
