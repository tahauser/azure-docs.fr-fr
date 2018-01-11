---
title: "Valider le débit VPN vers un réseau virtuel Microsoft Azure | Microsoft Docs"
description: "L’objectif de ce document est d’aider les utilisateurs à valider le débit du réseau de leurs ressources locales vers une machine virtuelle."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2017
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: e7e3c641791e7c72f5c2d6f8ecf674d1d7ee7ffa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Comment valider un débit VPN sur un réseau virtuel

La connexion à la passerelle VPN vous permet d'établir une connectivité entre les locaux sécurisée entre votre réseau virtuel Azure et votre infrastructure informatique locale.

Cet article montre comment valider le débit du réseau des ressources locales vers une machine virtuelle Azure. Il fournit également des instructions de dépannage.

>[!NOTE]
>Cet article a pour objet de vous aider à diagnostiquer et résoudre les problèmes courants. Si vous ne parvenez pas à résoudre le problème en utilisant les informations suivantes, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Vue d'ensemble

La connexion à la passerelle VPN implique les composants suivants :

- Appareils VPN sur site (afficher la liste des [appareils VPN validés)](vpn-gateway-about-vpn-devices.md#devicetable).
- Internet public
- Passerelle VPN Azure
- Microsoft Azure

Le diagramme suivant illustre la connectivité logique d’un réseau local vers un réseau virtuel Azure par VPN.

![Connectivité logique du réseau client au réseau MSFT via le VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcul des valeurs maximales attendues en entrée/sortie

1.  Déterminez les exigences de débit de base de votre application.
2.  Déterminez les limites de débit de votre passerelle VPN Azure. Pour de l’aide, consultez la section « Débit agrégé par référence SKU et type de VPN » de [Planification et conception de la passerelle VPN](vpn-gateway-plan-design.md).
3.  Déterminez les [instructions de débit de machine virtuelle Azure](../virtual-machines/virtual-machines-windows-sizes.md) pour la taille de votre machine virtuelle.
4.  Déterminez la bande passante de votre fournisseur d’accès à Internet (FAI).
5.  Calculez le débit prévu - Bande passante la plus basse entre machine virtuelle et passerelle FAI * 0,8.

Si le débit calculé ne répond pas aux exigences de débit de base de votre application, vous devez augmenter la bande passante de la ressource que vous avez identifiée comme goulot d’étranglement. Pour redimensionner une passerelle VPN Azure, consultez [Modification d’une référence SKU de passerelle](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Pour redimensionner une machine virtuelle, consultez [Redimensionner une machine virtuelle](../virtual-machines/virtual-machines-windows-resize-vm.md). Si bande passante Internet n’est pas celle attendue, vous souhaiterez également contacter votre fournisseur d’accès à Internet.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validation du débit réseau à l’aide d’outils de performances

Cette validation doit être effectuée pendant les heures creuses, car la saturation de débit de tunnel VPN pendant le test empêche les résultats précis.

L’outil que nous utilisons pour ce test est iPerf, qui fonctionne sur Windows et Linux et possède des modes client et serveur. Il est limité à 3 Gbit/s pour les machines virtuelles Windows.

Cet outil n’effectue pas d’opérations de lecture/écriture sur le disque. Il produit uniquement le trafic TCP généré automatiquement d’une extrémité à l’autre. Il génère des statistiques basées sur le test qui mesure la bande passante disponible entre les nœuds du serveur et le client. Lorsque vous testez entre deux nœuds, un joue le rôle du serveur et l’autre celui de client. Une fois ce test terminé, nous vous recommandons d’inverser les rôles pour tester le débit du téléchargement (download et upload) sur les deux nœuds.

### <a name="download-iperf"></a>Télécharger iPerf
Téléchargez [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Pour plus d’informations, consultez la [Documentation d’iPerf](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Les produits tiers mentionnés dans cet article sont fabriqués par des sociétés indépendantes de Microsoft. Microsoft ne formule aucune garantie, expresse ou implicite, concernant les performances ou la fiabilité de ces produits.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Exécutez iPerf (iperf3.exe)
1. Activez une règle NSG/ACL autorisant le trafic (pour le test d’adresses IP publiques sur une machine virtuelle Azure).

2. Sur les deux nœuds, activez une exception de pare-feu pour le port 5001.

    **Windows :** Exécutez la commande suivante, en tant qu’administrateur :

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Pour supprimer la règle lorsque le test est terminé, exécutez la commande suivante :

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Linux Azure :** les images Linux Azure sont dotées de pare-feu permissifs. Si une application écoute sur un port, le trafic est autorisé. Les images personnalisées qui sont sécurisées peuvent nécessiter l’ouverture explicite des ports. Les pare-feu de couche système courants pour Linux comprennent `iptables`, `ufw` et `firewalld`.

3. Sur le nœud serveur, accédez au répertoire dans lequel iperf3.exe est extrait. Exécutez ensuite iPerf en mode serveur et configurez-le pour écouter sur le port 5001 avec les commandes suivantes :

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. Sur le nœud client, accédez au répertoire où l’outil iperf est extrait et exécutez la commande suivante :

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Le client produit du trafic sur le port 5001 vers le serveur pendant 30 secondes. L’indicateur -P indique que nous utilisons 32 connexions simultanées pour le nœud serveur.

    L’écran suivant présente la sortie de cet exemple :

    ![Sortie](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (FACULTATIF) Pour conserver les résultats des tests, exécutez cette commande :

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Après avoir effectué les étapes précédentes, exécutez les mêmes étapes en inversant les rôles, afin que le nœud du serveur soit désormais le client et vice versa.

## <a name="address-slow-file-copy-issues"></a>Résolution des problèmes de copie lente des fichiers
Vous pouvez rencontrer des problèmes de copie trop lente de fichiers lorsque vous utilisez l’Explorateur Windows ou le glisser-déplacer via une session RDP. Ce problème est généralement dû à un ou deux des facteurs suivants :

- Les applications de copie de fichiers, comme l’Explorateur Windows et le protocole RDP, n’utilisent pas plusieurs threads lors de la copie des fichiers. Pour de meilleures performances, utilisez une application de copie de fichiers multi-thread, comme [Richcopy](https://technet.microsoft.com/en-us/magazine/2009.04.utilityspotlight.aspx), pour copier des fichiers à l’aide de 16 ou 32 threads. Pour modifier le nombre de threads pour la copie de fichiers dans Richcopy, cliquez sur **Action** > **Options de copie** > **Copie de fichiers**.<br><br>
![Problèmes de copie lente de fichiers](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- La vitesse en lecture/écriture du disque de la machine virtuelle est insuffisante. Pour plus d'informations, consultez [Dépannage Azure Storage](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interface externe avec appareil local
Si l’adresse Internet de l’appareil VPN est incluse dans la définition du [réseau local](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) dans Azure, vous pourriez avoir du mal à utiliser le VPN ou subir des déconnexions occasionnelles ou des problèmes de performances.

## <a name="checking-latency"></a>Vérification de la latence
Utilisez tracert pour le suivi jusqu’à l’appareil Microsoft Azure Edge pour déterminer s’il existe des retards supérieurs à 100 ms entre les tronçons.

À partir du réseau local, exécutez *tracert* pour l’adresse IP virtuelle de la machine virtuelle ou passerelle Azure. Quand le résultat contient seulement *, vous savez que vous avez atteint Azure Edge. Lorsque des noms DNS qui incluent « MSN » sont renvoyés, vous savez que vous avez atteint l’épine dorsale Microsoft.<br><br>
![Vérification de la latence](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations ou d'aide, consultez les liens suivants :

- [Optimiser le débit du réseau des machines virtuelles Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Support Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
