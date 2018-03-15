---
title: "Réseau virtuel Azure | Microsoft Docs"
description: "Découvrez les concepts et les fonctionnalités du réseau virtuel Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/1/2018
ms.author: jdial
ms.openlocfilehash: fadc1994cd930df36387a5bfb302c00d66f74fad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="what-is-azure-virtual-network"></a>Présentation du réseau virtuel Azure

Le réseau virtuel Azure permet à de nombreuses ressources Azure de communiquer les unes avec les autres et Internet. Un réseau virtuel isole vos ressources des ressources des autres utilisateurs dans le cloud Azure. Vous pouvez connecter des réseaux virtuels à d’autres réseaux virtuels, ou à votre réseau local. 

Le réseau virtuel Azure fournit les fonctionnalités étendues suivantes :
- **[Isolation :](#isolation)** les réseaux virtuels sont isolés les uns des autres. Vous pouvez créer des réseaux virtuels distincts à des fins de développement, de test et de production, qui utilisent les mêmes blocs d’adresses CIDR (10.0.0.0/0, par exemple). À l’inverse, vous pouvez créer plusieurs réseaux virtuels qui utilisent différents blocs d’adresses CIDR et connecter les réseaux entre eux. Vous pouvez segmenter un réseau virtuel en plusieurs sous-réseaux. Azure fournit la résolution de noms interne pour les ressources déployées dans un réseau virtuel. Si nécessaire, vous pouvez configurer un réseau virtuel afin d’utiliser vos propres serveurs DNS au lieu d’utiliser la résolution de noms interne d’Azure.
- **[Communication Internet :](#internet)** par défaut, les ressources comme les machines virtuelles déployées dans un réseau virtuel ont accès à Internet. Vous pouvez également activer l’accès entrant à des ressources spécifiques, en fonction de vos besoins.
- **[Communication des ressources Azure :](#within-vnet)** les ressources Azure déployées dans un réseau virtuel peuvent communiquer entre elles en utilisant des adresses IP privées, même si les ressources sont déployées dans des sous-réseaux différents. Azure fournit un routage par défaut entre les sous-réseaux, les réseaux virtuels connectés et les réseaux locaux, sans que vous ayez à configurer et à gérer ces routes. Si vous le souhaitez, vous pouvez personnaliser le routage d’Azure.
- **[Connectivité de réseau virtuel :](#connect-vnets)** les réseaux virtuels peuvent se connecter entre eux, permettant ainsi aux ressources d’un réseau virtuel de communiquer avec les ressources d’un autre réseau virtuel.
- **[Connectivité locale :](#connect-on-premises)** un réseau virtuel peut être connecté à un réseau local, ce qui permet aux ressources de communiquer entre elles.
- **[Filtrage du trafic :](#filtering)**  vous pouvez filtrer le trafic réseau vers et depuis des ressources dans un réseau virtuel par adresse IP et par port source, par adresse IP et par port de destination, et par protocole.
- **[Routage :](#routing)** vous pouvez également remplacer le routage Azure par défaut en configurant votre propre routage ou en propageant des routes BGP via une passerelle réseau.

## <a name = "isolation"></a>Isolement et segmentation du réseau

Vous pouvez implémenter plusieurs réseaux virtuels au sein de chaque [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure et de chaque [région](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) Azure. Chaque réseau privé est isolé des autres réseaux virtuels. Pour chaque réseau virtuel, vous pouvez :
- Spécifier un espace d’adressage IP privé personnalisé à l’aide d’adresses (RFC 1918) publiques et privées. Azure attribue aux ressources d’un réseau virtuel une adresse IP privée à partir de l’espace d’adressage que vous attribuez.
- Segmenter le réseau virtuel en sous-réseaux et allouer une partie de l’espace d’adressage du réseau virtuel à chaque sous-réseau.
- Utilisez la résolution de noms fournie par Azure ou spécifiez votre propre serveur DNS pour une utilisation par les ressources d’un réseau virtuel. Pour plus d’informations sur la résolution de noms dans les réseaux virtuels, consultez [Résolution de noms pour les ressources dans les réseaux virtuels](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Communication Internet
Toutes les ressources d’un réseau virtuel peuvent communiquer en sortie vers Internet. Par défaut, l’adresse IP privée de la ressource est traduite via SNAT (Source Network Address Translated) en une adresse IP publique sélectionnée par l’infrastructure Azure. Pour en savoir plus sur la connectivité Internet sortante, consultez [Présentation des connexions sortantes dans Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pour empêcher une connexion Internet sortante, vous pouvez implémenter des itinéraires personnalisés ou un filtrage du trafic.

Pour pouvoir communiquer avec les ressources Azure à partir ou depuis Internet sans SNAT, une ressource doit être affectée à une adresse IP publique. Pour en savoir plus sur les adresses IP publiques, consultez [Adresses IP publiques](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Communication sécurisée entre les ressources Azure

Vous pouvez déployer des machines virtuelles au sein d’un réseau virtuel. Celles-ci communiquent avec les autres ressources d’un réseau virtuel via une interface réseau. Pour en savoir plus sur les interfaces réseau, consultez [Interfaces réseau](virtual-network-network-interface.md).

Vous pouvez également déployer plusieurs autres types de ressources Azure sur un réseau virtuel, comme des environnements Azure App Service et des groupes de machines virtuelles identiques Azure. Pour obtenir la liste complète des ressources Azure que vous pouvez déployer sur un réseau virtuel, consultez [Intégration des services de réseau virtuel pour les services Azure](virtual-network-for-azure-services.md).

Certaines ressources ne peuvent pas être déployées sur un réseau virtuel, mais vous permettent seulement de restreindre les communications avec des ressources au sein d’un réseau virtuel. Pour plus d’informations sur la façon de restreindre l’accès à des ressources, consultez [Points de terminaison du service de réseau virtuel](virtual-network-service-endpoints-overview.md). 

## <a name="connect-vnets"></a>Connecter des réseaux virtuels

Vous pouvez connecter des réseaux virtuels entre eux, ce qui permet aux ressources de ces réseaux virtuels de communiquer entre eux à l’aide d’une homologation. La bande passante et la latence de la communication entre les ressources de différents réseaux virtuels sont les mêmes que si les ressources se trouvaient dans le même réseau virtuel. Pour en savoir plus sur l’homologation, consultez [Homologation de réseaux virtuels](virtual-network-peering-overview.md).

## <a name="connect-on-premises"></a>Se connecter à un réseau local

Vous pouvez connecter votre réseau local à un réseau virtuel à l’aide de n’importe quelle combinaison des options suivantes :
- **Réseau privé virtuel (VPN) de point à site :** connexion établie entre un réseau virtuel et un ordinateur unique de votre réseau. Chaque PC qui doit établir une connexion avec un réseau virtuel doit configurer ses connexions indépendamment. Ce type de connexion est utile si vous n’êtes pas familiarisé avec Azure, ou pour les développeurs car elle nécessite peu voire pas de modifications de votre réseau existant. La connexion utilise le protocole SSTP pour fournir une communication chiffrée via Internet entre l’ordinateur et le réseau virtuel. La latence d’un réseau VPN de point à site est imprévisible, car le trafic transite par Internet.
- **VPN de site à site :** connexion établie entre votre appareil VPN et une passerelle VPN Azure déployée dans un réseau virtuel. Ce type de connexion permet à n’importe quelle ressource locale de votre choix à accéder à un réseau virtuel. La connexion s’effectue via un réseau VPN IPSec/IKE qui fournit une communication chiffrée via Internet entre votre appareil local et la passerelle VPN Azure. La latence d’une connexion de site à site est imprévisible car le trafic transite par Internet.
- **Azure ExpressRoute :** connexion établie entre votre réseau et Azure via un partenaire ExpressRoute. Cette connexion est privée. Le trafic ne transite pas par Internet. La latence d’une connexion ExpressRoute est prévisible, car le trafic ne transite pas par Internet.

Pour plus d’informations sur toutes les options de connexion précédentes, consultez [Diagrammes de topologie de connexion](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrer le trafic réseau
Vous pouvez filtrer le trafic réseau entre les sous-réseaux à l’aide d’une des deux options suivantes :
- **Groupes de sécurité réseau :** un groupe de sécurité réseau peut contenir plusieurs règles de sécurité entrantes et sortantes qui vous permettent de filtrer le trafic par source et adresse IP de destination, port et protocole. Vous pouvez appliquer un groupe de sécurité réseau à chaque interface réseau sur une machine virtuelle. Vous pouvez également appliquer un groupe de sécurité réseau au sous-réseau d’une interface réseau, ou à une autre ressource Azure. Pour en savoir plus sur les groupes de sécurité réseau, voir [Groupes de sécurité réseau](security-overview.md#network-security-groups).
- **Appliances virtuelles de réseau :** une appliance virtuelle de réseau est une machine virtuelle exécutant un logiciel qui remplit une fonction réseau, telle qu’un pare-feu. Consultez la liste des appliances virtuelles de réseau disponibles dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Il existe également des appliances virtuelles de réseau qui fournissent une optimisation du réseau étendu et d’autres fonctions de trafic réseau. Les appliances virtuelles de réseau sont généralement utilisées avec des itinéraires BGP ou définis par l’utilisateur. Vous pouvez également utiliser une appliance virtuelle de réseau pour filtrer le trafic entre des réseaux virtuels.

## <a name="routing"></a>Router le trafic réseau

Azure crée des tables de routage qui permettent par défaut aux ressources connectées à un sous-réseau d’un réseau virtuel de communiquer entre elles et avec Internet. Vous pouvez implémenter une ou les deux options suivantes pour remplacer les itinéraires par défaut créés par Azure :
- **Tables de routage :** vous pouvez créer des tables de routage personnalisées avec des itinéraires qui contrôlent où le trafic est acheminé pour chaque sous-réseau. Pour en savoir plus sur le routage personnalisé, consultez la section relative au [routage personnalisé](virtual-networks-udr-overview.md#user-defined).
- **Itinéraires BGP :** si vous connectez votre réseau virtuel à votre réseau local via une connexion ExpressRoute ou la passerelle VPN Azure, vous pouvez propager les itinéraires BGP à vos réseaux virtuels.

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant une vue d’ensemble du réseau virtuel Azure. Découvrez comment utiliser certaines fonctionnalités du réseau virtuel Azure en créant un réseau virtuel et en y déployant des machines virtuelles Azure.

> [!div class="nextstepaction"]
> [Création d'un réseau virtuel](quick-create-portal.md)
