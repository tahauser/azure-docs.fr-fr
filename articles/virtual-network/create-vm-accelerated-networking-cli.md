---
title: Créer une machine virtuelle Azure avec mise en réseau accélérée| Documents Microsoft
description: Apprenez à créer une machine virtuelle Linux avec mise en réseau accélérée.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 5c09ffe6867972e772334ae7ae1dd655cdac431f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Créer une machine virtuelle Linux avec mise en réseau accélérée

> [!IMPORTANT] 
> Les machines virtuelles doivent être créées en activant la mise en réseau accélérée. Cette fonctionnalité ne peut pas être activée sur les machines virtuelles existantes. Procédez comme suit pour activer la mise en réseau accélérée :
>   1. Supprimez la machine virtuelle.
>   2. Recréez une machine virtuelle en activant la mise en réseau accélérée.
>

Ce didacticiel explique comment créer une machine virtuelle Linux avec mise en réseau accélérée. Une mise en réseau accélérée permet d’opérer une virtualisation d’E/S d’une racine unique (SR-IOV) sur une machine virtuelle, ce qui améliore considérablement les performances de mise en réseau. Cette voie hautement performante court-circuite l’hôte à partir du chemin d’accès aux données, réduisant ainsi la latence, l’instabilité et l’utilisation du processeur pour servir les charges de travail réseau les plus exigeantes sur les types de machines virtuelles pris en charge. L’illustration suivante montre la communication entre deux machines virtuelles avec ou sans mise en réseau accélérée :

![Opérateurs de comparaison](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sans mise en réseau accélérée, tout le trafic réseau en direction et en provenance de la machine virtuelle doit transiter par l’hôte et le commutateur virtuel. Le commutateur virtuel fournit au trafic réseau toutes les stratégies, telles que les groupes de sécurité réseau, les listes de contrôle d’accès, l’isolation et d’autres services de réseau virtualisé. Pour plus d’informations sur les commutateurs virtuels, voir [Vue d’ensemble de la virtualisation de réseau Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Dans le cas d’une mise en réseau accélérée, le trafic réseau parvient à la carte réseau de la machine virtuelle avant d’être transféré vers celle-ci. Toutes les stratégies réseau que le commutateur virtuel applique sont déchargées et appliquées dans le matériel. L’application de la stratégie au niveau du matériel permet à la carte réseau de transférer le trafic directement à la machine virtuelle, en ignorant l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu’il a appliquées dans l’hôte.

Les avantages d’une mise en réseau accélérée s’appliquent uniquement à la machine virtuelle activée. Pour des résultats optimaux, il convient d’activer cette fonctionnalité sur au moins deux machines virtuelles connectées au même réseau virtuel Azure Virtual Network. Lors de la communication entre réseaux virtuels ou d’une connexion locale, cette fonctionnalité a une incidence minimale sur la latence globale.

## <a name="benefits"></a>Avantages
* **Latence plus faible/Nombre supérieur de paquets par seconde (pps) :** la suppression du commutateur virtuel du chemin de données évite que les paquets liés au traitement des stratégies séjournent sur l’hôte, ce qui augmente le nombre de paquets pouvant être traités dans la machine virtuelle.
* **Instabilité réduite :** le traitement du commutateur virtuel dépend de la quantité de stratégie qui doit être appliquée et de la charge de travail du processeur qui effectue le traitement. Le déchargement des stratégies sur le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en supprimant l’hôte dans la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.
* **Utilisation réduite du processeur :** ignorer le commutateur virtuel dans l’hôte réduit l’utilisation du processeur pour le traitement du trafic réseau.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
* **Ubuntu 16.04** : version 4.11.0-1013 ou une version du noyau supérieure
* **SLES 12 SP3** : version 4.4.92-6.18 ou une version ultérieure du noyau
* **RHEL 7.4** : version 7.4: 7.4.2017120423 ou une version du noyau supérieure
* **CentOS 7.4** : version 7.4: 7.4.20171206 ou une version du noyau supérieure

## <a name="supported-vm-instances"></a>Instances de machines virtuelles prises en charge
La mise en réseau accélérée est prise en charge dans la plupart des instances d’usage général et optimisées pour le calcul (4 processeurs virtuels ou plus). Dans des instances du type D/DSv3 ou E/ESv3 qui acceptent l’hyperthreading, la mise en réseau accélérée est prise en charge dans des instances de machine virtuelle comptant au minimum 8 processeurs virtuels.  Les séries acceptées sont : D/DSv2, D/DSv3, E/ESv3, F/Fs/Fsv2 et Ms/Mms. 

Pour plus d’informations sur les instances de machine virtuelle, consultez la section [Tailles des machines virtuelles Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Régions
Disponible dans toutes les régions Azure publiques à l’exception de l’Asie-Pacifique.   Le cloud Azure Government n’est pas encore pris en charge.

## <a name="limitations"></a>Limites
Les limitations suivantes existent lors de l’utilisation de cette fonctionnalité :

* **Création d’interface réseau :** la mise en réseau accélérée ne peut être activée que pour une nouvelle carte réseau. Elle ne peut pas être activée pour une carte réseau existante.
* **Création de machine virtuelle :** une carte réseau avec mise en réseau accélérée activée ne peut être attachée à une machine virtuelle que lors de la création de celle-ci. Une carte réseau ne peut pas être attachée à une machine virtuelle existante. Si la machine virtuelle est ajoutée à un groupe à haute disponibilité, la mise en réseau accélérée doit être également activée sur toutes les machines virtuelles de ce groupe.
* **Déploiement via Azure Resource Manager uniquement :** aucun déploiement des machines virtuelles (classiques) n’est possible avec la mise en réseau accélérée.

Bien que cet article fournit des étapes pour créer une machine virtuelle avec mise en réseau accélérée à l’aide de l’interface CLI d’Azure, vous pouvez également [Créer une machine virtuelle avec mise en réseau accélérée via le portail Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Lors de la création d’une machine virtuelle avec un système d’exploitation et une taille de machine virtuelle pris en charge dans le portail, sous **Paramètres**, sélectionnez **Activé** sous **Mise en réseau accélérée**. Une fois la machine virtuelle créée, vous devez suivre les instructions de [Confirmer l’activation de la mise en réseau accélérée](#confirm-that-accelerated-networking-is-enabled).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Installez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login). Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myNic* et *myVm*.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *centralus* :

```azurecli
az group create --name myResourceGroup --location centralus
```

Vous devez sélectionner une région de Linux prise en charge et répertoriée dans [Mise en réseau accélérée Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L’exemple suivant crée un réseau virtuel nommé *myVnet* avec un sous-réseau :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau
Créez un groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). L’exemple suivant crée un groupe de sécurité réseau nommé *myNetworkSecurityGroup* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Le groupe de sécurité réseau contient plusieurs règles par défaut, dont l’une désactive tous les accès entrants à partir d’Internet. Ouvrez un port pour autoriser l’accès SSH à la machine virtuelle avec [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) :

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Créer une interface réseau avec mise en réseau accélérée

Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Une adresse IP publique n’est pas nécessaire si vous ne prévoyez pas d’accéder à l’ordinateur virtuel depuis Internet. Par contre, elle l’est pour procéder selon les étapes mentionnées dans cet article.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Créez une interface réseau avec [az network nic create](/cli/azure/network/nic#az_network_nic_create), puis activez la mise en réseau accélérée. L’exemple suivant permet de créer une interface réseau nommée *myNic* dans le sous-réseau *mySubnet* du réseau virtuel *myVnet* et de lui associer le groupe de sécurité réseau *myNetworkSecurityGroup* :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nic"></a>Créer une machine virtuelle et attacher la carte réseau
Lorsque vous créez la machine virtuelle, spécifiez la carte réseau que vous avez générée avec `--nics`. Vous devez sélectionner une taille et une distribution répertoriées dans [Mise en réseau accélérée Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVM* avec l’image UbuntuLTS et une taille qui prend en charge la mise en réseau accélérée (*Standard_DS4_v2*) :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Pour obtenir la liste de toutes les tailles de machine virtuelle et leurs caractéristiques, consultez [Tailles de machines virtuelles Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Une fois que la machine virtuelle est créée, une sortie similaire à la sortie suivante est renvoyée. Veuillez noter **publicIpAddress**. Cette adresse sera utilisée pour accéder à la machine virtuelle dans les étapes suivantes.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Confirmer l’activation de la mise en réseau accélérée

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle. Remplacez `<your-public-ip-address>` par l’adresse IP publique affectée à la machine virtuelle que vous avez créée, puis remplacez *azureuser* si vous avez utilisé une valeur différente pour `--admin-username` au moment de la création de la machine virtuelle.

```bash
ssh azureuser@<your-public-ip-address>
```

À partir de l’interpréteur de commandes Bash, entrez `uname -r`, puis confirmez que la version du noyau est bien l’une des versions suivantes ou une version supérieure :

* **Ubuntu 16.04** : 4.11.0-1013
* **SLES SP3** : 4.4.92-6.18
* **RHEL** : 7.4.2017120423
* **CentOS** : 7.4.20171206


Utilisez la commande `lspci` pour confirmer que l’appareil Mellanox VF est exposé à la machine virtuelle. Le résultat renvoyé ressemble à la sortie suivante :

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Utilisez la commande `ethtool -S eth0 | grep vf_` pour rechercher l’activité sur VF (fonction virtuelle). Si vous recevez une sortie semblable à l’exemple suivant, la mise en réseau accélérée est donc activée et elle fonctionne.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
La mise en réseau accélérée est maintenant activée pour votre machine virtuelle.
