---
title: Créer un réseau virtuel Azure comprenant plusieurs sous-réseaux - Azure CLI | Microsoft Docs
description: Découvrez comment créer un réseau virtuel comprenant des sous-réseaux à l’aide d’Azure CLI.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Créer un réseau virtuel comprenant des sous-réseaux à l’aide d’Azure CLI

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé entre elles et avec Internet. La création de plusieurs sous-réseaux dans un réseau virtuel permet de segmenter votre réseau afin que vous puissiez filtrer ou contrôler le flux du trafic entre les sous-réseaux. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer un sous-réseau
> * Tester la communication réseau entre les machines virtuelles

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L’exemple suivant permet de créer un réseau virtuel nommé *myVirtualNetwork* avec le préfixe d’adresse *10.0.0.0/16*. La commande crée un sous-réseau nommé *Public* avec le préfixe d’adresse *10.0.0.0/24*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Comme aucun emplacement n’a été spécifié dans la commande précédente, Azure crée le réseau virtuel dans le même emplacement que celui où le groupe de ressources *myResourceGroup* se trouve. Les **préfixes d’adresse** et le **préfixe de sous-réseau** sont spécifiés dans la notation CIDR. Le préfixe d’adresse spécifié inclut les adresses IP 10.0.0.0-10.0.255.254. Le préfixe spécifié pour le sous-réseau doit figurer dans le préfixe d’adresse défini pour le réseau virtuel. Azure DHCP attribue des adresses IP à partir d’un préfixe d’adresse de sous-réseau à des ressources déployées dans un sous-réseau. Azure attribue uniquement les adresses 10.0.0.4-10.0.0.254 à des ressources déployées dans le sous-réseau **Public**, car Azure réserve les quatre premières adresses (10.0.0.0-10.0.0.3 pour le sous-réseau, dans cet exemple) et la dernière adresse (10.0.0.255 pour le sous-réseau, dans cet exemple) dans chaque sous-réseau.

## <a name="create-a-subnet"></a>Créer un sous-réseau

Créez un sous-réseau avec [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). L’exemple suivant crée un sous-réseau nommé *Private* dans le réseau virtuel *myVirtualNetwork* avec le préfixe d’adresse *10.0.1.0/24*. Le préfixe d’adresse doit figurer dans le préfixe d’adresse défini pour le réseau virtuel et ne peut pas chevaucher le préfixe d’adresse des autres sous-réseaux du réseau virtuel.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

Avant de déployer des sous-réseaux et réseaux virtuels Azure pour une utilisation en production, il est recommandé de vous familiariser soigneusement avec les [considérations](manage-virtual-network.md#create-a-virtual-network) et [limites de réseau virtuel](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) de l’espace d’adressage. Une fois que les ressources sont déployées dans des sous-réseaux, certaines modifications de réseau virtuel et de sous-réseau (p. ex. la modification des plages d’adresses) peuvent nécessiter le redéploiement des ressources Azure existantes, déployées dans les sous-réseaux.

## <a name="test-network-communication"></a>Tester la communication réseau

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé entre elles et avec Internet. Une machine virtuelle est l’un des types de ressource que vous pouvez déployer dans un réseau virtuel. Créez deux machines virtuelles dans le réseau virtuel pour pouvoir tester ultérieurement la communication réseau entre eux et Internet.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez une machine virtuelle avec [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVmWeb* dans le sous-réseau *Public*. Le paramètre `--no-wait` permet à Azure d’exécuter la commande en arrière-plan de façon que vous puissiez continuer jusqu’à la commande suivante. Pour simplifier ce didacticiel, un mot de passe est utilisé. Les clés sont généralement utilisées dans les déploiements en production. Si vous utilisez des clés, vous devez également configurer le transfert de l’agent SSH pour effectuer les étapes restantes. Pour plus d’informations sur le transfert de l’agent SSH, consultez la documentation associée à votre client SSH. Remplacez `<replace-with-your-password>` dans la commande suivante par un mot de passe de votre choix.

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

Azure a automatiquement attribué 10.0.0.4 comme adresse IP privée de la machine virtuelle, 10.0.0.4 étant la première adresse IP disponible dans le sous-réseau *Public*. 

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
La création de la machine virtuelle prend quelques minutes. Une fois la machine virtuelle créée, Azure CLI retourne une sortie similaire à celle-ci : 

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

Dans le résultat de l’exemple, vous pouvez constater que **privateIpAddress** est *10.0.1.4*. Azure a créé une [interface réseau](virtual-network-network-interface.md), l’a associée à la machine virtuelle, a attribué une adresse IP privée et une **macAddress** à l’interface réseau. Azure DHCP a automatiquement attribué 10.0.1.4 à l’interface réseau, car il s’agit de la première adresse IP disponible dans le sous-réseau *Private*. Les adresses IP et MAC privées restent attribuées à l’interface réseau jusqu’à ce qu’elle soit supprimée. 

Veuillez noter **publicIpAddress**. Cette adresse est utilisée pour accéder à la machine virtuelle à partir d’Internet dans une étape ultérieure. Bien qu’il ne soit pas obligatoire qu’une machine virtuelle ait une adresse IP publique attribuée, Azure attribue par défaut une adresse IP publique à chacune des machines virtuelles que vous créez. Pour communiquer à partir d’Internet vers une machine virtuelle, une adresse IP publique doit être attribuée à la machine virtuelle. Toutes les machines virtuelles peuvent communiquer en sortie avec Internet, qu’une adresse IP publique soit attribuée à la machine virtuelle ou non. Pour en savoir plus sur les connexions Internet sortantes dans Azure, consultez [Connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Les machines virtuelles créées dans cet article ont une [interface réseau](virtual-network-network-interface.md) avec une adresse IP attribuée dynamiquement à l’interface réseau. Une fois que vous avez déployé la machine virtuelle, vous pouvez [ajouter des adresses IP publiques et privées, ou modifier la méthode d’attribution d’adresse IP pour qu’elle soit définie sur « statique »](virtual-network-network-interface-addresses.md#add-ip-addresses). Vous pouvez [ajouter des interfaces réseau](virtual-network-network-interface-vm.md#vm-add-nic), jusqu’à la limite prise en charge par le [taille de machine virtuelle](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que vous sélectionnez lorsque vous créez une machine virtuelle. Vous pouvez également [activer la virtualisation d’E/S racine unique](create-vm-accelerated-networking-cli.md) pour une machine virtuelle, mais uniquement lorsque vous créez une machine virtuelle avec une taille de machine virtuelle qui prend en charge la fonctionnalité.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Communiquer entre les machines virtuelles et avec Internet

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle *myVmMgmt*. Remplacez `<publicIpAddress>` par l’adresse IP publique de votre machine virtuelle. Dans l’exemple précédent, l’adresse IP publique est *13.90.242.231*. Lorsque vous êtes invité à indiquer un mot de passe, entrez le mot de passe que vous avez utilisé dans [Créer des machines virtuelles](#create-virtual-machines).

```bash 
ssh azureuser@<publicIpAddress>
```

Pour des raisons de sécurité, il est courant de limiter le nombre de machines virtuelles qui peuvent être connectées à distance à un réseau virtuel. Dans ce didacticiel, la machine virtuelle *myVmMgmt* est utilisée pour gérer la machine virtuelle *myVmWeb* dans le réseau virtuel. Utilisez la commande suivante pour établir la connexion SSH à la machine virtuelle *myVmWeb* à partir de la machine virtuelle *myVmMgmt* :

```bash 
ssh azureuser@myVmWeb
```

Pour communiquer avec la machine virtuelle *myVmMgmt* à partir de la machine virtuelle *myVmWeb*, entrez la commande suivante à partir d’une invite de commandes :

```
ping -c 4 myvmmgmt
```

Le résultat ressemble à ce qui suit :
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
Vous pouvez constater que l’adresse de la machine virtuelle *myVmMgmt* est 10.0.1.4. 10.0.1.4 était la première adresse IP disponible dans la plage d’adresses du sous-réseau *Private* sur lequel vous avez déployé la machine virtuelle *myVmMgmt* à l’étape précédente.  Vous voyez que le nom de domaine complet de l’ordinateur virtuel est *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*. Bien que la partie *hxehizax3z1udjnrx1r4gr30pg* du nom de domaine soit différente pour votre machine virtuelle, les autres parties du nom de domaine sont les mêmes. Par défaut, toutes les machines virtuelles Azure utilisent le service Azure DNS par défaut. Toutes les machines virtuelles au sein d’un réseau virtuel peuvent résoudre les noms de toutes les autres machines virtuelles dans le même réseau virtuel à l’aide du service DNS par défaut d’Azure. Au lieu d’utiliser le service DNS par défaut d’Azure, vous pouvez utiliser votre propre serveur DNS ou la fonctionnalité de domaine privé du service Azure DNS. Pour plus d’informations, consultez [Résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) ou [Utilisation d’Azure DNS pour les domaines privés](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Utilisez les commandes suivantes pour installer le serveur web nginx sur la machine virtuelle *myVmWeb* :

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Une fois que nginx est installé, fermez la session SSH *myVmWeb*, qui vous laisse à l’invite pour la machine virtuelle *myVmMgmt*. Entrez la commande suivante pour récupérer l’écran d’accueil nginx à partir de la machine virtuelle *myVmWeb*.

```bash
curl myVmWeb
```

L’écran d’accueil nginx est renvoyé.

Fermez la session SSH avec la machine virtuelle *myVmMgmt*.

Quand Azure a créé la machine virtuelle *myVmWeb*, une adresse IP publique nommée *myVmWebPublicIP* a été également créée et attribuée à la machine virtuelle. Récupérez l’adresse publique Azure attribuée avec [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

À partir de votre propre ordinateur, entrez la commande suivante, en remplaçant `<publicIpAddress>` par l’adresse renvoyée par la commande précédente :

```bash
curl <publicIpAddress>
```

La tentative de création d’une boucle de l’écran d’accueil nginx à partir de votre propre ordinateur échoue. Cet échec se produit car, quand les machines virtuelles ont été déployées, Azure a créé par défaut un groupe de sécurité réseau pour chaque machine virtuelle. 

Un groupe de sécurité réseau contient des règles de sécurité qui autorisent ou rejettent le trafic réseau entrant et sortant en fonction du port et de l’adresse IP. Le groupe de sécurité réseau par défaut créé par Azure permet la communication sur tous les ports entre les ressources dans le même réseau virtuel. Pour les machines virtuelles Linux, le groupe de sécurité réseau par défaut refuse tout le trafic entrant à partir d’Internet sur tous les ports, à l’exception du port TCP 22 (SSH). Par conséquent, par défaut, vous pouvez également créer une session SSH directement sur la machine virtuelle *myVmWeb* à partir d’Internet, même si vous ne souhaitez pas ouvrir le port 22 à un serveur web. Étant donné que la commande `curl` communique via le port 80, la communication depuis Internet échoue, car aucune règle n’est présente dans le groupe de sécurité réseau par défaut autorisant le trafic sur le port 80.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, utilisez [az group delete](/cli/azure/group#az_group_delete) pour le supprimer, ainsi que toutes les ressources qu’il contient.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment déployer un réseau virtuel comprenant plusieurs sous-réseaux. Vous avez également appris que, lorsque vous créez une machine virtuelle Linux, Azure crée une interface réseau qu’il associe à la machine virtuelle et crée un groupe de sécurité réseau qui autorise uniquement le trafic sur le port 22, à partir d’Internet. Passez au didacticiel suivant pour apprendre à filtrer le trafic réseau vers des sous-réseaux, plutôt que vers des machines virtuelles individuelles.

> [!div class="nextstepaction"]
> [Filtrer le trafic réseau vers des sous-réseaux](./virtual-networks-create-nsg-arm-cli.md)
