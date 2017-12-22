---
title: "Vérifier le trafic avec la vérification des flux IP Azure Network Watcher - Portail Azure | Microsoft Docs"
description: "Cet article explique comment savoir si le trafic en direction ou en provenance d’une machine virtuelle est autorisé ou refusé"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8333ccfd1e4cc917c8af4b3006292e43b77ecc7f
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


La vérification des flux IP est une fonctionnalité de Network Watcher qui vous permet de vérifier si le trafic en direction ou en provenance d’une machine virtuelle est autorisé. La validation peut être exécutée pour le trafic entrant ou sortant. Cette fonctionnalité est très utile pour définir si une machine virtuelle peut actuellement communiquer avec une ressource externe ou une autre ressource. La vérification des flux IP peut être utilisée pour vérifier si les règles de votre groupe de sécurité réseau sont correctement configurées et pour résoudre les problèmes de flux bloqués par les règles de groupe de sécurité réseau. La vérification des flux IP permet aussi de s’assurer que le trafic que vous souhaitez bloquer est correctement bloqué par le groupe de sécurité réseau.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Créer une instance d’Azure Network Watcher](network-watcher-create.md) pour créer un Network Watcher ou que vous disposez d’une instance existante de Network Watcher. Ce scénario suppose également qu’un groupe de ressources avec une machine virtuelle valide existe et peut être utilisé.

## <a name="scenario"></a>Scénario

Ce scénario utilise la vérification des flux IP pour vérifier si une machine virtuelle peut communiquer avec une autre machine via le port 443. Si le trafic est refusé, la règle de sécurité refusant ce trafic est renvoyée. Pour plus d’informations sur la vérification des flux IP, consultez la page [IP Flow Verify Overview (Vue d’ensemble de la fonction de vérification des flux IP)](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>Exécuter la vérification des flux IP

Accédez à Network Watcher et cliquez sur **Vérification du flux IP**. Sélectionnez la machine virtuelle et l’interface réseau à partir desquelles vous souhaitez vérifier le trafic. Entrez les informations de filtrage supplémentaires, puis cliquez sur **Vérifier**.

Une fois que vous cliquez sur **Vérifier**, le flux basé sur les critères que vous avez fournis est vérifié. Le résultat est **Accès autorisé** ou **Accès refusé**. Si l’accès est refusé, le groupe de sécurité réseau (NSG) et la règle de sécurité qui bloquent le trafic sont indiqués. Si le refus du trafic correspond au comportement attendu, cela signifie que la règle a réussi.

> [!NOTE]
> La vérification des flux IP nécessite l’attribution de la ressource de machine virtuelle.

Comme vous pouvez le constater dans l’image suivante, le trafic HTTP sortant a été autorisé.

![vue d’ensemble Vérification du flux IP][1]

Comme indiqué dans l’image suivante, le trafic est défini sur entrant et le port d’entrée est remplacé par 123. Le trafic est maintenant refusé : le message « Accès refusé » est indiqué avec le groupe de sécurité réseau et la règle de sécurité qui refusent le trafic.

![résultats du flux IP][2]

## <a name="next-steps"></a>Étapes suivantes

Si le trafic est bloqué alors qu’il ne devrait pas l’être, consultez [Gérer les groupes de sécurité réseau à partir du portail](../virtual-network/virtual-network-manage-nsg-arm-portal.md) afin de surveiller le groupe de sécurité réseau et les règles de sécurité définis.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













