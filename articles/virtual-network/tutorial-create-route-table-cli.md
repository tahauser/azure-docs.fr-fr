---
title: Acheminer le trafic réseau - Azure CLI | Microsoft Docs
description: Découvrez comment acheminer le trafic réseau avec une table de routage à l’aide de l’interface de ligne de commande Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Acheminer le trafic réseau avec une table de routage à l’aide de l’interface de ligne de commande Azure

Par défaut, Azure achemine automatiquement le trafic entre tous les sous-réseaux au sein d’un réseau virtuel. Vous pouvez créer vos propres itinéraires pour remplacer le routage par défaut d’Azure. La possibilité de créer des itinéraires personnalisés est utile si, par exemple, vous souhaitez acheminer le trafic entre des sous-réseaux via un pare-feu. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer une table de routage
> * Créer un itinéraire
> * Associer une table de routage à un sous-réseau de réseau virtuel
> * Tester le routage
> * Résoudre les problèmes de routage

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite que vous exécutiez la version 2.0.28 d’Azure CLI, ou une version ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Créer une table de routage

Par défaut, Azure achemine le trafic entre tous les sous-réseaux dans un réseau virtuel. Pour en savoir plus sur les itinéraires par défaut d’Azure, consultez [Itinéraires système](virtual-networks-udr-overview.md). Pour remplacer le routage par défaut d’Azure, vous créez une table de routage qui contient des itinéraires et associez la table de routage à un sous-réseau de réseau virtuel.

Avant de pouvoir créer une table de routage, créez un groupe de ressources avec [az group create](/cli/azure/group#az_group_create) pour toutes les ressources créées dans cet article. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Créez une table de routage avec [az network route-table create](/cli/azure/network/route#az_network_route_table_create). L’exemple suivant crée une table de routage nommée *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Créer un itinéraire

Une table de routage peut contenir plusieurs itinéraires, ou aucun. Créez un itinéraire dans la table de routage avec [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

L’itinéraire dirige tout le trafic destiné au préfixe d’adresse 10.0.1.0/24 via une appliance virtuelle réseau avec l’adresse IP 10.0.2.4. L’appliance virtuelle réseau et le sous-réseau avec le préfixe d’adresse spécifié sont créés dans des étapes ultérieures. L’itinéraire remplace le routage par défaut d’Azure, qui achemine le trafic entre les sous-réseaux directement. Chaque itinéraire spécifie un type de tronçon suivant. Le type de tronçon suivant indique à Azure comment acheminer le trafic. Dans cet exemple, le type de tronçon suivant est *VirtualAppliance*. Pour en savoir plus sur tous les types de tronçons suivants disponibles dans Azure et quand les utiliser, consultez [Types de tronçons suivants](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

Avant de pouvoir associer une table de routage à un sous-réseau, vous devez créer un réseau virtuel et un sous-réseau. Créez un réseau virtuel avec un sous-réseau en utilisant la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Créez deux sous-réseaux supplémentaires avec [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Vous pouvez associer une table de routage à plusieurs sous-réseaux, ou aucun. Un sous-réseau peut avoir zéro ou une table de routage associée. Le trafic sortant d’un sous-réseau est acheminé selon les itinéraires par défaut d’Azure et les itinéraires personnalisés que vous avez ajoutés à une table de routage que vous associez à un sous-réseau. Associez la table de routage *myRouteTablePublic* au sous-réseau *Public* avec [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

Avant de déployer des tables de routage à utiliser en production, il est recommandé de bien vous familiariser avec le [routage dans Azure](virtual-networks-udr-overview.md) et les [limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Tester le routage

Pour tester le routage, vous allez créer une machine virtuelle qui sert d’appliance virtuelle réseau par laquelle passe l’itinéraire que vous avez créé lors d’une étape précédente. Après avoir créé l’appliance virtuelle réseau, vous allez déployer une machine virtuelle dans les sous-réseaux *Public* et *Private*. Vous allez ensuite acheminer le trafic entre le sous-réseau *Public* et le sous-réseau *Private* via l’appliance virtuelle réseau.

### <a name="create-a-network-virtual-appliance"></a>Créer une appliance virtuelle réseau

Lors d’une étape précédente, vous avez créé un itinéraire qui indiquait une appliance virtuelle réseau comme type de tronçon suivant. Une machine virtuelle exécutant une application réseau est souvent appelée appliance virtuelle réseau. Dans les environnements de production, l’appliance virtuelle réseau que vous déployez est souvent une machine virtuelle préconfigurée. Plusieurs appliances virtuelles réseau sont disponibles dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Dans cet article, une machine virtuelle de base est créée. 

Créez une appliance virtuelle réseau dans le sous-réseau *DMZ* avec [az vm create](/cli/azure/vm#az_vm_create). Quand vous créez une machine virtuelle, Azure crée une adresse IP publique et l’affecte à la machine virtuelle, par défaut. Le paramètre `--public-ip-address ""` indique à Azure de ne pas créer d’adresse IP publique et de ne pas l’affecter à la machine virtuelle, car celle-ci n’a pas besoin de se connecter à Internet. Si des clés SSH n’existent pas déjà dans un emplacement de clé par défaut, la commande les crée. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

La création de la machine virtuelle prend quelques minutes. Ne passez pas à l’étape suivante tant qu’Azure n’a pas terminé la création de la machine virtuelle et retourné de sortie sur la machine virtuelle. Dans les environnements de production, l’appliance virtuelle réseau que vous déployez est souvent une machine virtuelle préconfigurée. Plusieurs appliances virtuelles réseau sont disponibles dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Vous devez activer le transfert IP pour chaque [interface réseau](virtual-network-network-interface.md) Azure attachée à une machine virtuelle qui transfère le trafic destiné à n’importe quelle adresse IP qui n’est pas affectée à l’interface réseau. Quand vous avez créé l’appliance virtuelle réseau dans le sous-réseau *DMZ*, Azure a automatiquement créé une interface réseau nommée *myVmNvaVMNic*, attaché l’interface réseau à la machine virtuelle et affecté l’adresse IP privée *10.0.2.4* à l’interface réseau. Activez le transfert IP pour l’interface réseau avec [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Dans la machine virtuelle, le système d’exploitation ou une application en cours d’exécution au sein de la machine virtuelle doit également être en mesure de transférer le trafic réseau. Quand vous déployez une appliance virtuelle réseau dans un environnement de production, l’appliance filtre, enregistre ou exécute généralement une autre fonction avant de transférer le trafic. Dans cet article, toutefois, le système d’exploitation transfère simplement tout le trafic qu’il reçoit. Activez le transfert IP au sein du système d’exploitation de la machine virtuelle avec [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set), qui exécute une commande qui effectue cette opération.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
L’exécution de la commande peut prendre jusqu’à une minute.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel pour pouvoir valider que le trafic provenant du sous-réseau *Public* est acheminé vers le sous-réseau *Private* via l’appliance virtuelle réseau dans une étape ultérieure. 

Créez une machine virtuelle dans le sous-réseau *Public* avec [az vm create](/cli/azure/vm#az_vm_create). Le paramètre `--no-wait` permet à Azure d’exécuter la commande en arrière-plan de façon que vous puissiez continuer jusqu’à la commande suivante. Pour simplifier cet article, un mot de passe est utilisé. Les clés sont généralement utilisées dans les déploiements en production. Si vous utilisez des clés, vous devez également configurer le transfert de l’agent SSH. Pour plus d’informations, consultez la documentation associée à votre client SSH. Remplacez `<replace-with-your-password>` dans la commande suivante par un mot de passe de votre choix.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Créez une machine virtuelle dans le sous-réseau *Private*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

La création de la machine virtuelle prend quelques minutes. Une fois la machine virtuelle créée, l’interface de ligne de commande Azure affiche des informations semblables à l’exemple suivant : 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Veuillez noter **publicIpAddress**. Cette adresse est utilisée pour accéder à la machine virtuelle à partir d’Internet dans une étape ultérieure.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Acheminer le trafic via une appliance virtuelle réseau

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle *myVmMgmt*. Remplacez *<publicIpAddress>* par l’adresse IP publique de votre machine virtuelle. Dans l’exemple ci-dessus, l’adresse IP est *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quand vous êtes invité à indiquer un mot de passe, entrez celui que vous avez sélectionné dans [Créer des machines virtuelles](#create-virtual-machines).

Utilisez la commande suivante pour installer la détermination d’itinéraire sur la machine virtuelle *myVmMgmt* :

```bash 
sudo apt-get install traceroute
```

Utilisez la commande suivante pour tester le routage du trafic réseau vers la machine virtuelle *myVmWeb* à partir de la machine virtuelle *myVmMgmt*.

```bash
traceroute myvmweb
```

La réponse ressemble à ce qui suit :

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Vous pouvez voir que le trafic est acheminé directement de la machine virtuelle *myVmMgmt* vers la machine virtuelle *myVmWeb*. Les itinéraires par défaut d’Azure acheminent directement le trafic entre les sous-réseaux. 

Utilisez la commande suivante pour établir la connexion SSH à la machine virtuelle *myVmWeb* à partir de la machine virtuelle *myVmMgmt* :

```bash 
ssh azureuser@myVmWeb
```

Utilisez la commande suivante pour installer la détermination d’itinéraire sur la machine virtuelle *myVmWeb* :

```bash 
sudo apt-get install traceroute
```

Utilisez la commande suivante pour tester le routage du trafic réseau vers la machine virtuelle *myVmMgmt* à partir de la machine virtuelle *myVmWeb*.

```bash
traceroute myvmmgmt
```

La réponse ressemble à ce qui suit :

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Vous pouvez voir que le premier tronçon est 10.0.2.4, ce qui correspond à l’adresse IP privée de l’appliance virtuelle réseau. Le second tronçon est 10.0.1.4, l’adresse IP privée de la machine virtuelle *myVmMgmt*. L’itinéraire ajouté à la table de routage *myRouteTablePublic* et associé au sous-réseau *Public* a contraint Azure à acheminer le trafic via l’appliance virtuelle réseau et non directement au sous-réseau *Private*.

Fermez les sessions SSH sur les machines virtuelles *myVmWeb* et *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Résoudre les problèmes de routage

Comme vous l’avez appris lors des étapes précédentes, Azure applique les itinéraires par défaut, que vous pouvez éventuellement remplacer par vos propres itinéraires. Parfois, le trafic peut ne pas être acheminé comme vous le voulez. Utilisez [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) pour déterminer la façon dont le trafic est acheminé entre deux machines virtuelles. Par exemple, la commande suivante teste le routage du trafic à partir de la machine virtuelle *myVmWeb* (10.0.0.4) vers la machine virtuelle *myVmMgmt* (10.0.1.4) :

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
La sortie suivante est retournée après une courte attente :

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

La sortie vous informe que l’adresse IP du tronçon suivant pour le trafic entre *myVmWeb* et *myVmMgmt* est 10.0.2.4 (la machine virtuelle *myVmNva*), que le type de tronçon suivant est *VirtualAppliance* et que la table de routage à l’origine du routage est *myRouteTablePublic*.

Les itinéraires effectifs pour chaque interface réseau sont une combinaison des itinéraires par défaut d’Azure et des itinéraires que vous définissez. Affichez tous les itinéraires effectifs pour une interface réseau dans une machine virtuelle avec [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Par exemple, pour afficher les itinéraires effectifs pour l’interface réseau *MyVmWebVMNic* dans la machine virtuelle *myVmWeb*, entrez la commande suivante :

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

Tous les itinéraires par défaut et l’itinéraire que vous avez ajouté lors d’une étape précédente sont retournés.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, utilisez [az group delete](/cli/azure/group#az_group_delete) pour le supprimer, ainsi que toutes les ressources qu’il contient.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une table de routage que vous avez associée à un sous-réseau. Vous avez créé une appliance virtuelle réseau qui a acheminé le trafic d’un sous-réseau public vers un sous-réseau privé. Alors que vous pouvez déployer de nombreuses ressources Azure dans un réseau virtuel, les ressources pour certains services Azure PaaS ne peuvent pas être déployées dans un réseau virtuel. Cependant, vous pouvez toujours restreindre l’accès aux ressources de certains services Azure PaaS au trafic provenant uniquement d’un sous-réseau de réseau virtuel. Passez au tutoriel suivant pour apprendre à restreindre l’accès réseau aux ressources Azure PaaS.

> [!div class="nextstepaction"]
> [Restreindre l’accès réseau aux ressources PaaS](virtual-network-service-endpoints-configure.md#azure-cli)
