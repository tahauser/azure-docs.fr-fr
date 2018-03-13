---
title: "Résoudre les problèmes associés aux connexions avec Azure Network Watcher - Portail Azure | Microsoft Docs"
description: "Découvrez comment utiliser la fonctionnalité de résolution des problèmes associés aux connexions d’Azure Network Watcher à l’aide du portail Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 8d3a537523cce3457c18c7563e885a3f7348326f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Résoudre les problèmes associés aux connexions avec Azure Network Watcher à l’aide du portail Azure.

> [!div class="op_single_selector"]
> - [Portail](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [API REST Azure](network-watcher-connectivity-rest.md)

Découvrez comment utiliser la résolution des problèmes associés aux connexions pour vérifier si une connexion TCP directe entre une machine virtuelle et un point de terminaison donné peut être établie.

## <a name="before-you-begin"></a>Avant de commencer

Cet article part du principe que vous disposez des ressources suivantes :

* Une instance de Network Watcher dans la région où vous souhaitez résoudre les problèmes associés à une connexion.
* Les machines virtuelles avec lesquelles résoudre les problèmes associés aux connexions.

> [!IMPORTANT]
> La résolution des problèmes associés à une connexion requiert une extension de machine virtuelle `AzureNetworkWatcherExtension`. Pour installer l’extension sur une machine virtuelle Windows, consultez la page [Azure Network Watcher Agent virtual machine extension for Windows](../virtual-machines/windows/extensions-nwa.md) (Extension de machine virtuelle d’agent Azure Network Watcher pour Windows). Pour une machine virtuelle Linux, consultez la page [Azure Network Watcher Agent virtual machine extension for Linux](../virtual-machines/linux/extensions-nwa.md) (Extension de machine virtuelle d’agent Azure Network Watcher pour Linux).

## <a name="check-connectivity-to-a-virtual-machine"></a>Vérifier la connectivité à une machine virtuelle

Cet exemple vérifie la connectivité à une machine virtuelle de destination sur le port 80.

Accédez à Network Watcher et cliquez sur **Résolution des problèmes de connexion**. Sélectionnez la machine virtuelle dont vous souhaitez vérifier la connectivité. Dans la section **Destination**, choisissez **Sélectionner une machine virtuelle** puis sélectionnez la machine virtuelle appropriée et le port à tester.

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