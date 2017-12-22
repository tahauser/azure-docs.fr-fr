---
title: "Rechercher le tronçon suivant avec la fonction Tronçon suivant Azure Network Watcher - Azure CLI 1.0 | Microsoft Docs"
description: "Cet article explique comment rechercher le type de tronçon suivant et l’adresse IP avec la fonction Tronçon suivant dans l’interface de ligne de commande Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: e849f7952962d177f40ce99307ef1c305e089827
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-10"></a>Découvrez le type de tronçon suivant grâce à la fonction Tronçon suivant Azure Network Watcher dans l’interface de ligne de commande Azure 1.0

> [!div class="op_single_selector"]
> - [Portail Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST Azure](network-watcher-check-next-hop-rest.md)

Tronçon suivant est une fonctionnalité de Network Watcher qui permet d’obtenir le type de tronçon suivant et l’adresse IP à partir d’une machine virtuelle spécifiée. Cette fonctionnalité est utile pour déterminer si le trafic sortant d’une machine virtuelle passe par une passerelle, Internet ou des réseaux virtuels pour atteindre sa destination.

Cet article utilise l’interface Azure CLI 1.0 interplateforme, disponible pour Windows, Mac et Linux.

## <a name="before-you-begin"></a>Avant de commencer

Dans ce scénario, vous allez utiliser l’interface de ligne de commande Azure pour rechercher le type de tronçon suivant et l’adresse IP.

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher. Ce scénario suppose également qu’un groupe de ressources avec une machine virtuelle valide existe et peut être utilisé.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article utilise Tronçon suivant, une fonctionnalité de Network Watcher qui détecte le type de tronçon suivant et l’adresse IP d’une ressource. Pour en savoir plus sur Tronçon suivant, consultez [Next Hop Overview (Vue d’ensemble de la fonctionnalité Tronçon suivant)](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Obtenir le tronçon suivant

Pour obtenir le tronçon suivant, appelez l’applet de commande `azure netowrk watcher next-hop`. Nous transférons à l’applet de commande le groupe de ressources Network Watcher, Network Watcher, l’identifiant de la machine virtuelle, l’adresse IP source et l’adresse IP de destination. Dans cet exemple, l’adresse IP de destination désigne une machine virtuelle sur un autre réseau virtuel. Les deux réseaux virtuels sont séparés par une passerelle réseau virtuelle. 

```azurecli
azure network watcher next-hop -g resourceGroupName -n networkWatcherName -t targetResourceId -a <source-ip> -d <destination-ip>
```

> [!NOTE]
Si la machine virtuelle possède plusieurs cartes réseau et si le transfert IP est activé sur l’une des cartes réseau, le paramètre de la carte réseau (-i nic-id) doit être spécifié. Sinon, il est facultatif.

## <a name="review-results"></a>Passer en revue les résultats

Une fois que vous avez terminé, les résultats sont présentés. L’adresse IP du tronçon suivant est renvoyée, ainsi que le type de ressource.

```
data:    Next Hop Ip Address             : 10.0.1.2
info:    network watcher next-hop command OK
```

La liste suivante indique les valeurs de NextHopType actuellement disponibles :

**Type de tronçon suivant**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Aucun

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment programmer la révision des paramètres de votre groupe de sécurité réseau sur la page [NSG Auditing with Network Watcher (Audit du Groupe de sécurité réseau avec Network Watcher)](network-watcher-nsg-auditing-powershell.md)
