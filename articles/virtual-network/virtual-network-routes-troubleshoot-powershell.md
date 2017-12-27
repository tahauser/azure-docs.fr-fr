---
title: "Résoudre les problèmes relatifs aux itinéraires - PowerShell | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes liés aux itinéraires dans le modèle de déploiement Azure Resource Manager à l’aide d’Azure PowerShell."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: ac7f8ddaf84ba94075a9c9c3195bd57534c6821b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Résoudre des problèmes d’itinéraires à l’aide d’Azure PowerShell
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

### <a name="view-effective-routes-for-a-network-interface"></a>Afficher les itinéraires effectifs pour une interface réseau
Pour afficher les itinéraires agrégés appliqués à une carte d’interface réseau, procédez comme suit :

1. Démarrez une session Azure PowerShell et connectez-vous à Azure. Si vous n’êtes pas familiarisé avec Azure PowerShell, lisez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) . L’opération *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* doit être affectée à votre compte pour l’interface réseau. Pour savoir comment affecter des opérations à des comptes, consultez [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. La commande suivante renvoie tous les itinéraires appliqués à une carte d’interface réseau nommée *VM1-NIC1* dans le groupe de ressources *RG1*.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Si vous ne connaissez pas le nom d’une carte d’interface réseau, entrez la commande suivante pour récupérer les noms de toutes les cartes d’interface réseau d’un groupe de ressources.*
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   La sortie suivante ressemble à la sortie pour chaque itinéraire appliqué au sous-réseau auquel la carte d’interface réseau est connectée :
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   Notez ce qui suit dans la sortie :
   
   * **Name**: le nom de l’itinéraire effectif peut être vide, sauf spécification explicite, pour les itinéraires définis par l’utilisateur. 
   * **State**: indique l’état de l’itinéraire effectif. Les valeurs possibles sont « Actif » ou « Non valide ».
   * **AddressPrefixes**: spécifie le préfixe d’adresse de l’itinéraire effectif en notation CIDR. 
   * **nextHopType**: indique le saut suivant pour l’itinéraire donné. Les valeurs possibles sont *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering* ou *Null*. La valeur *Null* pour **nextHopType** dans un itinéraire défini par l’utilisateur peut indiquer un itinéraire non valide. Par exemple, si **nextHopType** est *VirtualAppliance* et si la machine virtuelle d’appliance virtuelle de réseau n’est pas dans en service ou en cours d’exécution. Si **nextHopType** est *VPNGateway* et s’il n’existe aucune passerelle en service ou en cours d’exécution dans le réseau virtuel donné, l’itinéraire peut devenir non valide.
   * **NextHopIpAddress**: spécifie l’adresse IP du saut suivant de l’itinéraire effectif.
   
   La commande suivante retourne les itinéraires dans un tableau plus lisible :
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   La sortie suivante est une partie de la sortie reçue dans le cadre du scénario décrit précédemment :
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. Aucun itinéraire n’est répertorié vers le réseau virtuel *WestUS-VNet3* (préfixe 10.10.0.0/16)** à partir de *WestUS-VNet1* (préfixe 10.9.0.0/16) dans la sortie de l’étape précédente. Comme le montre l’illustration suivante, le lien VNET Peering avec le réseau virtuel *WestUS-VNet3* est à l’état *déconnecté*.
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    Le lien bidirectionnel pour l’homologation est rompu, ce qui explique pourquoi la machine virtuelle VM1 n’a pas pu se connecter à la machine virtuelle VM3 dans le réseau virtuel *WestUS-VNet3* . Configurez de nouveau un lien VNET Peering bidirectionnel pour les réseaux virtuels *WestUS-VNet1* et *WestUS-VNet3*. La sortie retournée une fois le lien VNET Peering correctement établi est la suivante :
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    Après avoir déterminé le problème, vous pouvez ajouter, supprimer, ou modifier des itinéraires et router des tables. Pour afficher la liste des commandes utilisées pour ce faire, tapez la commande suivante :
   
        Get-Help *-AzureRmRouteConfig

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
  * Des règles du groupe de sécurité réseau peuvent avoir une incidence sur les flux de trafic. Pour plus d’informations, consultez [Résoudre les problèmes relatifs aux groupes de sécurité réseau](virtual-network-nsg-troubleshoot-powershell.md) .

