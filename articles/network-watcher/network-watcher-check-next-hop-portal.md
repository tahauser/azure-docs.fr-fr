---
title: "Rechercher le tronçon suivant avec la fonction Tronçon suivant d’Azure Network Watcher - Portail Azure | Microsoft Docs"
description: "Cet article explique comment rechercher le type de tronçon suivant et l’adresse IP à l’aide de la fonction Tronçon suivant par le biais du Portail Azure."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Découvrez le type de tronçon suivant grâce à la fonction Tronçon suivant d’Azure Network Watcher dans le Portail Azure.

> [!div class="op_single_selector"]
> - [Portail Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST Azure](network-watcher-check-next-hop-rest.md)

Tronçon suivant est une fonctionnalité de Network Watcher qui permet d’obtenir le type de tronçon suivant et l’adresse IP à partir d’une machine virtuelle spécifiée. Cette fonctionnalité est utile pour déterminer si le trafic sortant d’une machine virtuelle passe par une passerelle, Internet ou des réseaux virtuels pour atteindre sa destination.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher. Ce scénario suppose également qu’un groupe de ressources avec une machine virtuelle valide existe et peut être utilisé.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article utilise la fonctionnalité Tronçon suivant pour rechercher le type de tronçon suivant et l’adresse IP d’une ressource. Pour en savoir plus sur Tronçon suivant, consultez [Next Hop Overview (Vue d’ensemble de la fonctionnalité Tronçon suivant)](network-watcher-next-hop-overview.md).

Dans ce scénario, vous allez :

* Récupérer le tronçon suivant à partir d’une machine virtuelle

## <a name="get-next-hop"></a>Obtenir le tronçon suivant

### <a name="step-1"></a>Étape 1 :

Accédez à votre ressource Network Watcher dans le Portail Azure.

### <a name="step-2"></a>Étape 2

Dans le volet de navigation, cliquez sur **Tronçon suivant**, sélectionnez la machine virtuelle et l’interface réseau, renseignez les adresses IP source et de destination, puis cliquez sur le bouton **Tronçon suivant**.

> [!NOTE]
> L’exécution de la fonctionnalité Tronçon suivant nécessite l’allocation de la ressource de machine virtuelle.

![Vue d’ensemble de l’écran d’obtention du tronçon suivant][1]

### <a name="step-3"></a>Étape 3

Une fois la tâche exécutée, les résultats sont présentés. L’adresse IP et le type d’appareil sur lequel figure le saut suivant s’affichent. La section ci-après présente les valeurs pouvant être renvoyées dans le portail.

**Type de tronçon suivant**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Aucun

Si un routage personnalisé a été utilisé pour acheminer ce trafic, le routage défini par l’utilisateur (UDR) apparaît avec les résultats.

![Résultats d’obtention du tronçon suivant][2]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment programmer la révision des paramètres de votre groupe de sécurité réseau sur la page [NSG Auditing with Network Watcher (Audit du Groupe de sécurité réseau avec Network Watcher)](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png














