---
title: "Résoudre les problèmes relatifs aux itinéraires - Portail | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes liés aux itinéraires dans le modèle de déploiement Azure Resource Manager à l’aide du portail Azure."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: e0b835f4cbad9855bfb7ddccf2d9bf5b4bf88231
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>Résoudre des problèmes d’itinéraire à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
>
>

Si vous rencontrez des problèmes de connectivité réseau en relation avec votre machine virtuelle Azure, il se peut que les itinéraires aient une incidence sur les flux de trafic de votre machine virtuelle. Cet article donne une vue d’ensemble des fonctionnalités de diagnostic des itinéraires pour vous aider à mieux résoudre les problèmes.

Des tables d’itinéraires sont associées aux sous-réseaux, qui s’appliquent à toutes les interfaces réseau de ce sous-réseau. Les types d’itinéraire suivants peuvent être appliqués à chaque interface réseau :

* **Itinéraires système :** par défaut, chaque sous-réseau créé dans un réseau virtuel Azure a des tables d’itinéraires système qui permettent le trafic de réseau virtuel local, le trafic local au moyen de passerelles VPN et le trafic Internet. Il existe également des itinéraires système pour les réseaux virtuels homologués.
* **Itinéraires BGP :** propagés aux interfaces réseau par le biais d’ExpressRoute ou des connexions VPN de site à site. Pour plus d’informations sur les itinéraires BGP, consultez les articles [BGP avec passerelles VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) et [Présentation d’ExpressRoute](../expressroute/expressroute-introduction.md).
* **Itinéraires définis par l’utilisateur :** si vous utilisez des appliances virtuelles réseau ou effectuez un tunneling forcé du trafic vers un réseau local par le biais d’un VPN de site à site, il se peut que des itinéraires définis par l’utilisateur soient associés à votre table d’itinéraires de sous-réseau. Si vous n’êtes pas familiarisé avec les itinéraires définis par l’utilisateur, consultez [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined) .

Avec les différents itinéraires applicables à une interface réseau, il peut être difficile de déterminer quels itinéraires agrégés sont efficaces. Pour vous aider à résoudre les problèmes de connectivité réseau de machine virtuelle, vous pouvez afficher tous les itinéraires effectifs pour une interface réseau dans le modèle de déploiement Azure Resource Manager.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Utilisation d’itinéraires effectifs pour résoudre des problèmes de flux de trafic de machine virtuelle
Cet article utilise le scénario suivant en tant qu’exemple afin d’illustrer la manière de dépanner les itinéraires effectifs pour une interface réseau :

Une machine virtuelle (*VM1*) connectée au réseau virtuel (*VNet1*, préfixe 10.9.0.0/16) ne parvient pas à se connecter à une machine virtuelle (VM3) dans un réseau virtuel nouvellement homologué (*VNet3*, préfixe 10.10.0.0/16). Aucun itinéraire défini par l’utilisateur ou BGP n’est appliqué à l’interface réseau VM1-NIC1 connectée à la machine virtuelle. Seuls les itinéraires système sont appliqués.

Cet article explique comment déterminer la cause de l’échec de connexion, en utilisant la fonctionnalité d’itinéraires effectifs du modèle de déploiement de gestion des ressources Azure.
Si l’exemple utilise uniquement les itinéraires système, les mêmes étapes permettent de détecter des échecs de connexions entrantes et sortantes sur tout type d’itinéraire.

> [!NOTE]
> Si votre machine virtuelle dispose de plusieurs cartes réseau, vérifiez les itinéraires effectifs pour chacune d’elles afin de diagnostiquer les problèmes de connectivité réseau en relation avec la machine virtuelle.
>
>

### <a name="view-effective-routes-for-a-virtual-machine"></a>Afficher les itinéraires effectifs pour une machine virtuelle
Pour afficher les itinéraires agrégés qui s’appliquent à une machine virtuelle, procédez comme suit :

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com. L’opération *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* doit être affectée à votre compte pour l’interface réseau. Pour savoir comment affecter des opérations à des comptes, consultez [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Cliquez sur **Tous les services**, puis sur **Machines virtuelles** dans la liste qui s’affiche.
3. Sélectionnez une machine virtuelle à dépanner dans la liste qui s’affiche. Un panneau de machine virtuelle avec des options s’affiche.
4. Cliquez sur **Diagnostiquer et résoudre les problèmes**, puis sélectionnez un problème courant. Pour cet exemple, le problème **Je ne peux pas me connecter à ma machine virtuelle Windows** est sélectionné.

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. Des étapes s’affichent sous le problème, comme illustré dans l’image suivante :

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Cliquez sur *Itinéraires effectifs* dans la liste des étapes recommandées.
6. Le panneau **Itinéraires effectifs** s’affiche, comme illustré dans l’image suivante :

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Si votre machine virtuelle ne comporte qu’une seule carte réseau, elle est sélectionnée par défaut. Si vous avez plusieurs cartes réseau, sélectionnez celle pour laquelle vous souhaitez afficher les itinéraires effectifs.

   > [!NOTE]
   > Si la machine virtuelle associée à la carte réseau n’est pas en cours d’exécution, les itinéraires effectifs ne s’affichent pas. Uniquement les 200 premiers itinéraires effectifs s’affichent dans le portail. Pour obtenir la liste complète, cliquez sur **Télécharger**. Vous pouvez filtrer davantage les résultats à partir du fichier .csv téléchargé.
   >
   >

    Notez ce qui suit dans la sortie :

   * **Source**: indique le type d’itinéraire. Les itinéraires système sont affichés en tant que *Default*, les itinéraires définis par l’utilisateur en tant que *User* et les itinéraires de passerelle (statiques ou BGP) en tant que *VPNGateway*.
   * **State**: indique l’état de l’itinéraire effectif. Les valeurs possibles sont *Actif* ou *Non valide*.
   * **AddressPrefixes**: spécifie le préfixe d’adresse de l’itinéraire effectif en notation CIDR.
   * **nextHopType**: indique le saut suivant pour l’itinéraire donné. Les valeurs possibles sont *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering* ou *Null*. La valeur *Null* pour **nextHopType** dans un itinéraire défini par l’utilisateur peut indiquer un itinéraire non valide. Par exemple, si **nextHopType** est *VirtualAppliance* et si la machine virtuelle d’appliance virtuelle de réseau n’est pas dans en service ou en cours d’exécution. Si **nextHopType** est *VPNGateway* et s’il n’existe aucune passerelle en service ou en cours d’exécution dans le réseau virtuel donné, l’itinéraire peut devenir non valide.
7. Aucun itinéraire n’est répertorié vers le réseau virtuel *WestUS-VNET3* (préfixe 10.10.0.0/16) à partir de *WestUS-VNet1* (préfixe 10.9.0.0/16) dans l’image à l’étape précédente. Dans l’illustration suivante, le lien d’homologation est en état *déconnecté* :

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Le lien bidirectionnel pour l’homologation est rompu, ce qui explique pourquoi la machine virtuelle VM1 n’a pas pu se connecter à la machine virtuelle VM3 dans le réseau virtuel *WestUS-VNet3* .
8. L’illustration suivante montre les itinéraires après établissement du lien d’homologation bidirectionnelle :

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Pour d’autres scénarios de dépannage relatifs à l’évaluation du tunneling forcé et des itinéraires, consultez la section [Considérations](virtual-network-routes-troubleshoot-portal.md#considerations) de cet article.

### <a name="view-effective-routes-for-a-network-interface"></a>Afficher les itinéraires effectifs pour une interface réseau
Si le flux de trafic réseau est affecté pour une carte réseau particulière, vous pouvez afficher la liste complète des itinéraires effectifs sur une carte réseau directement. Pour afficher les itinéraires agrégés appliqués à une carte réseau, procédez comme suit :

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. Cliquez sur **Tous les services**, puis sur **Interfaces réseau**.
3. Recherchez dans la liste le nom d’une carte réseau ou sélectionnez-le dans la liste qui s’affiche. Dans cet exemple, la carte **VM1-NIC1** est sélectionnée.
4. Sélectionnez **Itinéraires effectifs** dans le panneau **Interface réseau**, comme illustré dans l’image suivante :

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    Par défaut, l’ **Étendue** est l’interface réseau sélectionnée.

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>Afficher les itinéraires effectifs pour une table d’itinéraires
Lorsque vous modifiez des itinéraires définis par l’utilisateur dans une table d’itinéraires, vous pouvez examiner l’impact des itinéraires ajoutés sur une machine virtuelle particulière. Une table d’itinéraires peut être associée à un nombre quelconque de sous-réseaux. Vous pouvez maintenant afficher tous les itinéraires effectifs pour toutes les cartes réseau auxquelles s’applique une table d’itinéraires donnée, sans avoir à modifier le contexte dans l panneau d’une table d’itinéraires donnée.

Pour cet exemple, un itinéraire défini par l’utilisateur (*UDRoute*) est spécifié dans une table d’itinéraires (*UDRouteTable*). Cet itinéraire envoie tout le trafic Internet de *Subnet1* dans le réseau virtuel *WestUS-VNet1*, via une appliance virtuelle de réseau, vers *Subnet2* dans le même réseau virtuel. L’itinéraire est illustré dans l’image suivante :

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Pour afficher les itinéraires agrégés d’une table d’itinéraires, procédez comme suit :

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. Cliquez sur **Tous les services**, puis sur **Tables d’itinéraires**.
3. Recherchez dans la liste la table d’itinéraires dont vous souhaitez afficher les itinéraires agrégés, puis sélectionnez-la. Dans cet exemple, la table **UDRouteTable** est sélectionnée. Un panneau pour la table d’itinéraires sélectionnée s’affiche, comme illustré dans l’image suivante :

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. Sélectionnez **Itinéraires effectifs** dans le panneau **Table d’itinéraires**. L’ **étendue** est définie sur la table d’itinéraires que vous avez sélectionnée.
5. Une table d’itinéraires peut être appliquée à plusieurs sous-réseaux. Sélectionnez le **Sous-réseau** à vérifier dans la liste. Dans cet exemple, le sous-réseau **Subnet1** est sélectionné.
6. Sélectionnez une **Interface réseau**. Toutes les cartes réseau connectées au sous-réseau sélectionné sont répertoriées. Dans cet exemple, la carte **VM1-NIC1** est sélectionnée.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > Si la carte réseau n’est pas associée à une machine virtuelle en cours d’exécution, aucun itinéraire effectif n’est affiché.
   >
   >

## <a name="considerations"></a>Considérations
Quelques éléments à prendre en compte lors de l’examen de la liste d’itinéraires retournée :

* Les itinéraires sont basés sur la correspondance de préfixe la plus longue parmi les itinéraires définis par l’utilisateur, BGP et système. S’il existe plusieurs itinéraires avec la même correspondance de préfixe la plus longue, un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :

  * Itinéraire défini par l’utilisateur
  * Itinéraire BGP
  * Itinéraire système (par défaut)

    Avec des itinéraires effectifs, vous voyez uniquement les itinéraires effectifs qui constituent la correspondance de préfixe la plus longue en fonction de tous les itinéraires disponibles. En montrant comment les itinéraires sont réellement évalués pour une carte réseau donnée, il est beaucoup plus facile de dépanner des itinéraires spécifiques qui peuvent avoir une incidence sur la connectivité de votre machine virtuelle.
* Si vous avez des itinéraires définis par l’utilisateur et envoyez du trafic vers une appliance virtuelle de réseau, avec *VirtualAppliance* en tant que **nextHopType**, confirmez que le transfert IP est activé sur l’appliance virtuelle de réseau recevant le trafic, ou les paquets sont ignorés.
* Si le tunneling forcé est activé, tout le trafic Internet sortant est routé vers le site local. Selon la manière dont le site local gère ce trafic, l’accès RDP/SSH à partir d’Internet à votre machine virtuelle peut ne pas fonctionner avec ce paramètre.
  Le tunneling forcé peut être activé :
  * si vous utilisez un VPN de site à site, en établissant un itinéraire défini par l’utilisateur avec nextHopType en tant que passerelle VPN ;
  * si un itinéraire par défaut est annoncé sur BGP.
* Pour que le trafic VNet Peering fonctionne correctement, un itinéraire système avec **nextHopType** *VNetPeering* doit exister pour la plage de préfixes du réseau virtuel homologué. Si un itinéraire de ce type n’existe pas et si le lien d’homologation du réseau virtuel semble correct :
  * Patientez quelques secondes, puis recommencez s’il s’agit d’un lien d’homologation récemment établi. Il faut parfois plus de temps pour propager les itinéraires à toutes les interfaces réseau dans un sous-réseau.
  * Des règles du groupe de sécurité réseau peuvent avoir une incidence sur les flux de trafic. Pour plus d’informations, consultez [Résoudre les problèmes relatifs aux groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md) .
