---
title: "Vérifier le trafic avec la vérification des flux IP Azure Network Watcher - Azure CLI | Microsoft Docs"
description: "Cet article explique comment savoir si le trafic en direction ou en provenance d’une machine virtuelle est autorisé ou refusé à l’aide de la ligne de commande Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f1355cd861722848211277250155c434da1e774d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Vérifiez si le trafic est autorisé ou refusé en direction ou en provenance d’une machine virtuelle avec le composant de vérification des flux IP d’Azure Network Watcher

> [!div class="op_single_selector"]
> - [portail Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST Azure](network-watcher-check-ip-flow-verify-rest.md)


La vérification des flux IP est une fonctionnalité de Network Watcher qui vous permet de vérifier si le trafic en direction ou en provenance d’une machine virtuelle est autorisé. Cette fonctionnalité est très utile pour définir si une machine virtuelle peut actuellement communiquer avec une ressource externe ou un serveur back-end. La vérification des flux IP peut être utilisée pour vérifier si les règles de votre groupe de sécurité réseau sont correctement configurées et pour résoudre les problèmes de flux bloqués par les règles de groupe de sécurité réseau. La vérification des flux IP permet aussi de s’assurer que le trafic que vous souhaitez bloquer est correctement bloqué par le groupe de sécurité réseau.

Cet article utilise l’interface Azure CLI 1.0 interplateforme, disponible pour Windows, Mac et Linux.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Créer une instance d’Azure Network Watcher](network-watcher-create.md) pour créer un Network Watcher ou que vous disposez d’une instance existante de Network Watcher. Ce scénario suppose également qu’un groupe de ressources avec une machine virtuelle valide existe et peut être utilisé.

## <a name="scenario"></a>Scénario

Ce scénario utilise la vérification des flux IP pour savoir si une machine virtuelle peut communiquer avec une adresse IP Bing connue. Si le trafic est refusé, la règle de sécurité refusant ce trafic est renvoyée. Pour plus d’informations sur la vérification des flux IP, consultez la page [IP Flow Verify Overview (Vue d’ensemble de la fonction de vérification des flux IP)](network-watcher-ip-flow-verify-overview.md)


## <a name="get-a-vm"></a>Obtenir une machine virtuelle

La vérification des flux IP permet de tester le trafic en direction ou en provenance d’une adresse IP sur une machine virtuelle en direction ou en provenance d’une destination distante. Un identifiant de machine virtuelle est requis pour l’applet de commande. Si vous connaissez déjà l’identifiant de la machine virtuelle à utiliser, vous pouvez ignorer cette étape.

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>Obtenir les cartes réseau

L’adresse IP d’une carte réseau sur la machine virtuelle est requise. Dans cet exemple, nous récupérons les cartes réseau sur une machine virtuelle. Si vous connaissez déjà l’adresse IP que vous souhaitez tester sur la machine virtuelle, vous pouvez ignorer cette étape.

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>Exécuter la vérification des flux IP

Maintenant que nous disposons des informations nécessaires pour exécuter l’applet de commande, nous pouvons exécuter l’applet de commande `network watcher ip-flow-verify` pour tester le trafic. Dans cet exemple, nous utilisons la première adresse IP sur la première carte réseau.

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> La vérification des flux IP nécessite l’attribution de la ressource de machine virtuelle.

## <a name="review-results"></a>Analyser les résultats

Après l’exécution de `network watcher ip-flow-verify`, les résultats sont renvoyés. L’exemple suivant présente les résultats renvoyés à partir de l’étape précédente.

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>Étapes suivantes

Si le trafic est bloqué alors qu’il ne devrait pas l’être, consultez [Gérer les groupes de sécurité réseau à partir du portail](../virtual-network/virtual-network-manage-nsg-arm-portal.md) afin de surveiller le groupe de sécurité réseau et les règles de sécurité définis.

Pour savoir comment auditer les paramètres de votre groupe de sécurité réseau, consultez [Auditing Network Security Groups (NSG) with Network Watcher (Audit des groupes de sécurité réseau avec Network Watcher)](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
