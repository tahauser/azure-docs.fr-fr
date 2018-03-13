---
title: "Créer une machine virtuelle Linux dans Azure avec plusieurs cartes réseau | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle Linux dotée de plusieurs cartes réseau avec Azure CLI 2.0 ou des modèles Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: e377459d205426b34c52336d9104400cf9d8030b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Guide de création d’une machine virtuelle Linux dans Azure avec plusieurs cartes d’interface réseau
Vous pouvez créer une machine virtuelle dans Azure, à laquelle sont attachées plusieurs interfaces réseau virtuelles (NIC). Un scénario courant consiste à avoir des sous-réseaux différents pour les connectivités frontale et principale, ou un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article décrit comment créer une machine virtuelle avec plusieurs cartes réseau attachées et comment ajouter ou supprimer des cartes réseau à partir d’une machine virtuelle existante. Comme le nombre de cartes réseau prises en charge varie suivant la [taille des machines virtuelles](sizes.md) , pensez à dimensionner la vôtre en conséquence.

Cet article explique comment créer une machine virtuelle avec plusieurs cartes réseau à l’aide d’Azure CLI 2.0. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Créer des ressources de support
Installez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec la commande [az login](/cli/azure/#az_login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *mystorageaccount* et *myVM*.

Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Créez le réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L’exemple suivant permet de créer un réseau virtuel nommé *myVnet* et un sous-réseau nommé *mySubnetFrontEnd* :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Créez un sous-réseau pour le trafic principal avec la commande [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). L’exemple suivant permet de créer un sous-réseau nommé *mySubnetBackEnd* :

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Créez un groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). L’exemple suivant crée un groupe de sécurité réseau nommé *myNetworkSecurityGroup* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Créer et configurer plusieurs cartes réseau
Créez deux cartes réseau avec la commande [az network nic create](/cli/azure/network/nic#az_network_nic_create). L’exemple suivant crée deux cartes réseau, nommées *myNic1* et *myNic2*, connectées au groupe de sécurité réseau et avec une carte réseau se connectant à chaque sous-réseau :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Créer une machine virtuelle et attacher les cartes réseau
Lorsque vous créez la machine virtuelle, spécifiez les cartes réseau que vous avez créées avec `--nics`. Vous devez également faire attention en définissant la taille de la machine virtuelle. Il existe des limites pour le nombre maximal de cartes réseau que vous pouvez ajouter à une machine virtuelle. En savoir plus sur les [tailles des machines virtuelles Linux](sizes.md). 

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVM* :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

## <a name="add-a-nic-to-a-vm"></a>Ajout d’une carte réseau à une machine virtuelle existante
Les étapes précédentes ont permis de créer une machine virtuelle avec plusieurs cartes réseau. Vous pouvez également ajouter des cartes réseau à une machine virtuelle existante avec Azure CLI 2.0. Comme le nombre de cartes réseau prises en charge varie suivant la [taille des machines virtuelles](sizes.md) , pensez à dimensionner la vôtre en conséquence. Si nécessaire, vous pouvez [redimensionner une machine virtuelle](change-vm-size.md).

Créez une autre carte réseau avec [az network nic create](/cli/azure/network/nic#az_network_nic_create). L’exemple suivant crée une carte réseau nommée *myNic3* connectée au sous-réseau principal et au groupe de sécurité réseau créés lors des étapes précédentes :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Pour ajouter une carte réseau à une machine virtuelle existante, libérez d’abord la machine virtuelle avec [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L’exemple suivant libère la machine virtuelle nommée *myVM* :


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Ajoutez la carte réseau avec [az vm nic add](/cli/azure/vm/nic#az_vm_nic_add). L’exemple suivant ajoute *myNic3* à *myVM* :

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Démarrez la machine virtuelle avec [az vm start](/cli/azure/vm#az_vm_start) :

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

## <a name="remove-a-nic-from-a-vm"></a>Suppression d’une carte réseau d’une machine virtuelle
Pour supprimer une carte réseau d’une machine virtuelle existante, libérez d’abord la machine virtuelle avec [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L’exemple suivant libère la machine virtuelle nommée *myVM* :

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Supprimez la carte réseau avec [az vm nic remove](/cli/azure/vm/nic#az_vm_nic_remove). L’exemple suivant supprime *myNic3* de *myVM* :

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Démarrez la machine virtuelle avec [az vm start](/cli/azure/vm#az_vm_start) :

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Créer plusieurs cartes réseau à l’aide de modèles Resource Manager
Les modèles Azure Resource Manager utilisent des fichiers JSON déclaratifs pour définir votre environnement. Vous pouvez consulter une [vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Grâce aux modèles Resource Manager, vous pouvez créer plusieurs instances d’une ressource pendant le déploiement, à l’image de la création de plusieurs cartes réseau. Utilisez *copy* pour spécifier le nombre d’instances à créer :

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

En savoir plus sur la [création de plusieurs instances à l’aide de *copy*](../../resource-group-create-multiple.md). 

Vous pouvez également utiliser `copyIndex()` pour ajouter ensuite un numéro à un nom de ressource permettant de créer `myNic1`, `myNic2`, etc. Voici un exemple d’ajout de la valeur d’index :

```json
"name": "[concat('myNic', copyIndex())]", 
```

Vous pouvez consulter un exemple complet de la [création de plusieurs cartes réseau à l’aide de modèles Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).


## <a name="configure-guest-os-for-multiple-nics"></a>Configurer plusieurs cartes réseau dans un système d’exploitation invité
Lorsque vous ajoutez plusieurs cartes réseau à une VM Linux, vous devez créer des règles de routage. Ces règles permettent à la machine virtuelle d’envoyer et de recevoir le trafic qui appartient à une carte réseau spécifique. Sinon, le trafic appartenant à *eth1*, par exemple, ne peut pas être traité correctement par l’itinéraire défini par défaut.

Pour corriger ce problème de routage, ajoutez d’abord deux tables de routage */etc/iproute2/rt_tables* comme suit :

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Pour que le changement devienne persistant et soit appliqué lors de l’activation de la pile réseau, modifiez */etc/sysconfig/network-scipts/ifcfg-eth0* et */etc/sysconfig/network-scipts/ifcfg-eth1*. Remplacez la ligne *« NM_CONTROLLED=yes »* par la ligne *« NM_CONTROLLED=no »*. Sans cette étape, le routage et les règles supplémentaires ne sont pas automatiquement appliquées.
 
Étendez ensuite les tables de routage. Supposons que nous ayons la configuration suivante en place :

*Routage*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*Interfaces*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

Vous créez ensuite les fichiers suivants et ajoutez les règles et les itinéraires appropriés dans chaque :

- */etc/sysconfig/network-scripts/rule-eth0*

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/route-eth0*

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/rule-eth1*

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- */etc/sysconfig/network-scripts/route-eth1*

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

Pour appliquer les modifications, redémarrez le service *réseau* comme suit :

```bash
systemctl restart network
```

Les règles de routage sont désormais correctement en place et vous pouvez vous connecter avec chaque interface selon les besoins.


## <a name="next-steps"></a>Étapes suivantes
Vérifiez les [tailles des machines virtuelles Linux](sizes.md) si vous créez une machine virtuelle avec plusieurs cartes réseau. Faites attention au nombre maximal de cartes réseau pris en charge par chaque taille de machine virtuelle. 
