---
title: "Vérifier la connectivité avec Azure Network Watcher - portail Azure | Microsoft Docs"
description: "Cette page explique comment vérifier la connectivité avec Network Watcher à l’aide du portail Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 1f19da71731039e1a39c4440f925b1369886a993
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>Vérifier la connectivité avec Azure Network Watcher à l’aide du portail Azure

> [!div class="op_single_selector"]
> - [Portail](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [API REST Azure](network-watcher-connectivity-rest.md)

Découvrez comment utiliser la connectivité pour vérifier si une connexion TCP directe entre une machine virtuelle et un point de terminaison donné peut être établie.

## <a name="before-you-begin"></a>Avant de commencer

Cet article part du principe que vous disposez des ressources suivantes :

* Une instance de Network Watcher dans la région où vous souhaitez vérifier la connectivité.

* Des machines virtuelles avec lesquelles vérifier la connectivité.

> [!IMPORTANT]
> La vérification de la connectivité requiert une extension de machine virtuelle `AzureNetworkWatcherExtension`. Pour installer l’extension sur une machine virtuelle Windows, consultez la page [Azure Network Watcher Agent virtual machine extension for Windows](../virtual-machines/windows/extensions-nwa.md) (Extension de machine virtuelle Azure Network Watcher Agent pour Windows). Pour une machine virtuelle Linux, consultez la page [Azure Network Watcher Agent virtual machine extension for Linux](../virtual-machines/linux/extensions-nwa.md) (Extension de machine virtuelle Azure Network Watcher Agent pour Linux).

## <a name="check-connectivity-to-a-virtual-machine"></a>Vérifier la connectivité à une machine virtuelle

Cet exemple vérifie la connectivité à une machine virtuelle de destination sur le port 80.

Accédez à Network Watcher et cliquez sur **Vérification de la connectivité (préversion)**. Sélectionnez la machine virtuelle dont vous souhaitez vérifier la connectivité. Dans la section **Destination**, choisissez **Sélectionner une machine virtuelle** puis sélectionnez la machine virtuelle appropriée et le port à tester.

Une fois que vous cliquez sur **Vérifier**, la connectivité entre les machines virtuelles sur le port spécifié est vérifiée. Dans l’exemple, la machine virtuelle de destination est inaccessible et une liste de tronçons apparaît.

![Consulter les résultats de la connectivité d’une machine virtuelle][1]

## <a name="check-remote-endpoint-connectivity"></a>Vérifier la connectivité du point de terminaison distant

Pour vérifier la connectivité et la latence vers un point de terminaison distant, sélectionnez la case d’option **Spécifier manuellement** dans la section **Destination**, entrez l’URL et le port puis cliquez sur **Vérifier**.  Cette opération est utilisée pour les points de terminaison distants comme des sites web et des points de terminaison de stockage.

![Consulter les résultats de la connectivité d’un site web][2]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment automatiser les captures de paquets avec des alertes de machine virtuelle en consultant [Create an alert triggered packet capture (Créer une capture de paquets déclenchée par alerte)](network-watcher-alert-triggered-packet-capture.md)

Recherchez si certains types de trafic sont autorisés au sein ou en dehors de votre machine virtuelle en consultant [Check IP flow verify (Vérifier les flux IP)](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png
