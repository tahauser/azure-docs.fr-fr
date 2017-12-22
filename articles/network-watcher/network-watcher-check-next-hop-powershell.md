---
title: "Rechercher le tronçon suivant avec la fonction Tronçon suivant Azure Network Watcher - PowerShell | Microsoft Docs"
description: "Cet article explique comment rechercher le type de tronçon suivant et l’adresse IP avec la fonction Tronçon suivant par le biais de PowerShell."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Découvrez le type de tronçon suivant grâce à la fonction Tronçon suivant Azure Network Watcher par le biais de PowerShell

> [!div class="op_single_selector"]
> - [portail Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST Azure](network-watcher-check-next-hop-rest.md)

Tronçon suivant est une fonctionnalité de Network Watcher qui permet d’obtenir le type de tronçon suivant et l’adresse IP à partir d’une machine virtuelle spécifiée. Cette fonctionnalité est utile pour déterminer si le trafic sortant d’une machine virtuelle passe par une passerelle, Internet ou des réseaux virtuels pour atteindre sa destination.

## <a name="before-you-begin"></a>Avant de commencer

Dans ce scénario, vous allez utiliser le portail Azure pour rechercher le type de tronçon suivant et l’adresse IP.

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher. Ce scénario suppose également qu’un groupe de ressources avec une machine virtuelle valide existe et peut être utilisé.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article utilise Tronçon suivant, une fonctionnalité de Network Watcher qui détecte le type de tronçon suivant et l’adresse IP d’une ressource. Pour en savoir plus sur Tronçon suivant, consultez [Next Hop Overview (Vue d’ensemble de la fonctionnalité Tronçon suivant)](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Récupérer Network Watcher

La première étape consiste à récupérer l’instance Network Watcher. La variable `$networkWatcher` est transmise à l’applet de commande de vérification du tronçon suivant.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Obtenir une machine virtuelle

La fonctionnalité Tronçon suivant renvoie le tronçon suivant et l’adresse IP du tronçon suivant à partir d’une machine virtuelle. Un identifiant de machine virtuelle est requis pour l’applet de commande. Si vous connaissez déjà l’identifiant de la machine virtuelle à utiliser, vous pouvez ignorer cette étape.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> L’exécution de la fonctionnalité Tronçon suivant nécessite l’allocation de la ressource de machine virtuelle.

## <a name="get-the-network-interfaces"></a>Obtenir les interfaces réseau

L’adresse IP d’une carte réseau sur la machine virtuelle est requise. Dans cet exemple, nous récupérons les cartes réseau sur une machine virtuelle. Si vous connaissez déjà l’adresse IP que vous souhaitez tester sur la machine virtuelle, vous pouvez ignorer cette étape.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Obtenir le tronçon suivant

Nous appelons maintenant l’applet de commande `Get-AzureRmNetworkWatcherNextHop`. Nous transférons à l’applet de commande Network Watcher, l’identifiant de la machine virtuelle, l’adresse IP source et l’adresse IP de destination. Dans cet exemple, l’adresse IP de destination désigne une machine virtuelle sur un autre réseau virtuel. Les deux réseaux virtuels sont séparés par une passerelle réseau virtuelle.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Passer en revue les résultats

Une fois que vous avez terminé, les résultats sont présentés. L’adresse IP du tronçon suivant est renvoyée, ainsi que le type de ressource. Dans ce scénario, il s’agit de l’adresse IP publique de la passerelle réseau virtuel.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
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

















