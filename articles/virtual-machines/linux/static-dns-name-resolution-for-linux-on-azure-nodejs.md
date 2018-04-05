---
title: Utilisation de DNS interne pour la résolution de noms de machines virtuelles dans Azure | Microsoft Docs
description: Utilisation de DNS interne pour la résolution de noms de machines virtuelles dans Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: 6072374cd7a37df6d7cc75479e24996dff3ee667
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Utilisation de DNS interne pour la résolution de noms de machines virtuelles dans Azure

Cet article explique comment définir des noms DNS internes statiques pour les machines virtuelles Linux à l’aide de cartes réseau virtuelles (VNic) et de noms d’étiquette DNS. Les noms DNS statiques sont utilisés pour les services d’infrastructure permanents comme un serveur de builds Jenkins, qui est utilisé pour ce document, ou un serveur Git.

Les conditions requises sont :

* [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)
* [des fichiers de clés SSH publiques et privées](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface Azure CLI nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="quick-commands"></a>Commandes rapides

Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes nécessaires. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, [à partir de cette section](#detailed-walkthrough).  

Configuration requise : groupe de ressources, réseau virtuel, groupe de sécurité réseau avec SSH entrant, sous-réseau.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Créer une carte réseau virtuelle avec un nom DNS interne statique

L’indicateur de l’interface de ligne de commande `-r` sert à définir l’étiquette DNS, qui fournit le nom DNS statique pour la carte réseau virtuelle.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Déployer la machine virtuelle dans le réseau virtuel, le groupe de sécurité réseau et connecter la carte réseau virtuelle

`-N` connecte la carte réseau virtuelle à la nouvelle machine virtuelle lors du déploiement sur Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas

Une infrastructure complète d’intégration et de déploiement en continu (CiCd) sur Azure nécessite que certains serveurs soient du type statique ou durable.  Il est recommandé que les ressources Azure telles que les réseaux virtuels et les groupes de sécurité réseau soient des ressources statiques et durables qui sont rarement déployées.  Une fois un réseau virtuel déployé, il peut être réutilisé par de nouveaux redéploiements, sans impact négatif sur l’infrastructure.  L’ajout d’un serveur de référentiel Git et d’un serveur d’automatisation Jenkins à ce réseau statique dote vos environnements de développement ou de test de l’intégration et du déploiement en continu (CiCd).  

Les noms DNS internes ne peuvent être résolus qu’à l’intérieur d’un réseau virtuel Azure.  Étant donné que les noms DNS sont internes, ils ne peuvent pas être résolus par le biais de l’Internet extérieur, offrant ainsi une sécurité supplémentaire pour l’infrastructure.

_Remplacez les exemples par votre propre attribution de noms._

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Un groupe de ressources est nécessaire pour organiser tous les éléments créés dans cette procédure pas à pas.  Pour plus d’informations sur les groupes de ressources Azure, consultez l’article [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Créez le réseau virtuel.

La première étape consiste à créer un réseau virtuel dans lequel lancer les machines virtuelles.  Le réseau virtuel contient un sous-réseau pour cette procédure pas à pas.  Pour plus d’informations sur les réseaux virtuels Azure, consultez la section [Créer un réseau virtuel](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Créer le groupe de sécurité réseau

Le sous-réseau se trouvant derrière un groupe de sécurité réseau existant, nous développons le groupe de sécurité en premier.  Les groupes de sécurité réseau Azure s’apparentent à un pare-feu au niveau de la couche réseau.  Pour plus d’informations sur les groupes de sécurité réseau Azure, consultez la section [Comment créer des groupes de sécurité réseau dans l’interface de ligne de commande Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Ajouter une règle d’autorisation SSH entrante

La machine virtuelle Linux a besoin d’un accès à partir d’internet. Une règle autorisant le trafic entrant sur le port 22 à être acheminé à travers le réseau jusqu’au port 22 de la machine virtuelle Linux est donc créée.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Ajouter un sous-réseau au réseau virtuel

Les machines virtuelles d’un réseau virtuel doivent se trouver dans un sous-réseau.  Chaque réseau virtuel peut présenter plusieurs sous-réseaux.  Créez le sous-réseau et associez-le au groupe de sécurité réseau afin d’ajouter un pare-feu.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Le sous-réseau est désormais ajouté au sein du réseau virtuel et associé au groupe de sécurité réseau et à la règle dédiée.

## <a name="creating-static-dns-names"></a>Création de noms DNS statiques

Azure est très flexible, mais pour utiliser des noms DNS pour la résolution de noms de machines virtuelles, vous devez les créer sous forme de cartes réseau virtuelles l’aide de l’étiquetage DNS.  Les cartes réseau virtuelles sont importantes, car vous pouvez les connecter à différentes machines virtuelles. La carte réseau virtuelle est ainsi conservée en tant que ressource statique, tandis que les machines virtuelles peuvent être temporaires.  À l’aide de l’étiquetage DNS sur la carte réseau virtuelle, nous sommes en mesure d’activer la résolution de noms simple à partir d’autres machines virtuelles dans le réseau virtuel.  L’utilisation de noms pouvant être résolus permet à d’autres machines virtuelles d’accéder au serveur d’automatisation à l’aide du nom DNS `Jenkins` ou au serveur Git en tant que `gitrepo`.  Créez une carte réseau virtuelle et associez-la au sous-réseau créé à l’étape précédente.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Déployer la machine virtuelle dans le réseau virtuel et le groupe de sécurité réseau

Nous disposons désormais d’un réseau virtuel, d’un sous-réseau intégré et d’un groupe de sécurité réseau jouant le rôle de pare-feu. Ce dernier protège notre sous-réseau en bloquant l’ensemble du trafic entrant, à l’exception du port 22 pour SSH.  La machine virtuelle peut désormais être déployée au sein de cette infrastructure réseau existante.

À l’aide de l’interface CLI Azure, et de la commande `azure vm create`, la machine virtuelle Linux est déployée dans le groupe de ressources Azure, le réseau virtuel, le sous-réseau et la carte réseau virtuelle Azure existants.  Pour plus d’informations sur l’utilisation de l’interface CLI pour déployer une machine virtuelle complète, consultez la section [Création d’un environnement Linux complet à l’aide de l’interface CLI Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

En utilisant les indicateurs CLI pour appeler les ressources existantes, nous indiquons à Azure de déployer la machine virtuelle au sein du réseau existant.  Encore une fois, lorsqu’un réseau virtuel et un sous-réseau ont été déployés, ils peuvent être conservés en tant que ressources statiques ou permanentes à l’intérieur de votre région Azure.  

## <a name="next-steps"></a>Étapes suivantes
* [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Création d’une machine virtuelle Linux sur Azure à l’aide de modèles](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
