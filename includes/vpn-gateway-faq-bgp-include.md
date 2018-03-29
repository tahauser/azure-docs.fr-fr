---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dfc75a64e8bd28d7aba9984e5a1d5720330f1da3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Le protocole BGP est-il pris en charge sur toutes les références de passerelle VPN Azure ?
Non, le protocole BGP est pris en charge sur les passerelles VPN Azure **VpnGw1**, **VpnGw2**, **VpnGw3**, **Standard** et **HighPerformance**. La référence **De base** N’EST PAS prise en charge.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Puis-je utiliser le protocole BGP avec les passerelles VPN Azure basées sur des stratégies ?
Non. Le protocole BGP est pris en charge uniquement sur les passerelles VPN basées sur des itinéraires.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Puis-je utiliser des NSA (numéros de système autonomes) privés ?
Oui, vous pouvez utiliser vos propres NSA publics ou privés pour vos réseaux locaux et les réseaux virtuels Azure.

### <a name="are-there-asns-reserved-by-azure"></a>Existe-t-il des NSA réservés par Azure ?
Oui. Les NSA suivants sont réservés par Azure pour les homologations internes et externes :

* NSA publics : 8074, 8075, 12076
* NSA privés : 65515, 65517, 65518, 65519, 65520

Vous ne pouvez pas spécifier ces NSA pour vos périphériques VPN locaux lors de la connexion à des passerelles VPN Azure.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Existe-t-il d’autres NSA que je ne peux pas utiliser ?
Oui, les NSA ci-après sont [réservés par l’IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) et ne sont pas configurables sur votre passerelle VPN Azure :

23456, 64496-64511, 65535-65551 et 429496729.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Puis-je utiliser le même NSA pour les réseaux VPN locaux et les réseaux virtuels Azure ?
Non. Vous devez attribuer des NSA différents à vos réseaux locaux et vos réseaux virtuels Azure si vous les interconnectez avec le protocole BGP. Le NSA 65515 est attribué aux passerelles VPN Azure par défaut, et ce, que le protocole BGP soit activé ou non pour la connectivité entre les sites locaux. Vous pouvez remplacer cette valeur par défaut en attribuant un NSA différent lors de la création de la passerelle VPN, ou modifier le NSA après avoir créé la passerelle. Vous devez affecter vos NSA locaux aux passerelles de réseau local Azure correspondantes.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Quels préfixes d’adresse les passerelles VPN Azure publieront-elles pour moi ?
La passerelle VPN Azure publiera les itinéraires suivants pour vos périphériques BGP locaux :

* préfixes d’adresse de votre réseau virtuel ;
* préfixes d’adresse de chaque passerelle de réseau local connectée à la passerelle VPN Azure ;
* itinéraires obtenus à partir d’autres sessions d’homologation BGP connectées à la passerelle VPN Azure, **à l’exception de l’itinéraire par défaut ou des itinéraires se chevauchant avec un préfixe de réseau virtuel**.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Puis-je publier l’itinéraire par défaut (0.0.0.0/0) vers les passerelles VPN Azure ?
Oui.

Notez que cela dirigera tout le trafic de sortie du réseau virtuel vers votre site local et empêchera les machines virtuelles du réseau virtuel d’accepter des communications publiques directement à partir d’Internet, par exemple RDP ou SSH d’Internet vers les machines virtuelles.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Puis-je publier les mêmes préfixes que ceux de mon adresse de réseau virtuel ?

Non. La publication des mêmes préfixes que ceux de votre adresse de réseau virtuel est bloquée ou filtrée par la plateforme Azure. Toutefois, vous pouvez publier un préfixe qui soit un sur-ensemble des éléments de votre réseau virtuel. 

Par exemple, votre réseau virtuel peut utiliser l’espace d’adresse 10.0.0.0/16 et vous pouvez publier 10.0.0.0/8. Par contre, vous ne pouvez pas publier 10.0.0.0/16 ou 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Puis-je utiliser le protocole BGP avec des connexions entre réseaux virtuels ?
Oui. Vous pouvez utiliser le protocole BGP pour les connexions entre sites locaux et entre réseaux virtuels.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Puis-je combiner des connexions BGP et non-BGP pour mes passerelles VPN Azure ?
Oui. Vous pouvez combiner des connexions BGP et non-BGP pour la même passerelle VPN Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>La passerelle VPN Azure prend-elle en charge le routage de transit BGP ?
Oui. Le routage de transit BGP est pris en charge. Cependant, les passerelles VPN Azure ne publient **PAS** les routes par défaut sur les autres pairs BGP. Pour activer le routage de transit via plusieurs passerelles VPN Azure, vous devez activer le protocole BGP sur toutes les connexions intermédiaires entre réseaux virtuels.

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Puis-je créer plusieurs tunnels entre ma passerelle VPN Azure et mon réseau local ?
Oui. Vous pouvez établir plusieurs tunnels VPN S2S entre une passerelle VPN Azure et votre réseau local. Tous ces tunnels seront comptabilisés par rapport au nombre total de tunnels pour vos passerelles VPN Azure et vous devez activer le protocole BGP sur les deux tunnels.

Par exemple, si vous établissez deux tunnels redondants entre votre passerelle VPN Azure et l’un de vos réseaux locaux, 2 tunnels seront utilisés sur le quota total de votre passerelle VPN Azure (10 pour la référence Standard et 30 pour la référence HighPerformance).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Puis-je avoir plusieurs tunnels entre deux réseaux virtuels Azure avec protocole BGP ?
Oui, mais au moins une des passerelles de réseau virtuel doit être dans une configuration active-active.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Puis-je utiliser BGP pour les passerelles VPN S2S dans une configuration de coexistence entre des passerelles ExpressRoute/S2S ?
Oui. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Quelle adresse la passerelle VPN Azure utilise-t-elle pour l’IP d’homologue BGP ?
La passerelle VPN Azure alloue une adresse IP unique à partir de la plage GatewaySubnet définie pour le réseau virtuel. Par défaut, il s’agit de l’avant-dernière adresse de la plage. Par exemple, si votre GatewaySubnet est 10.12.255.0/27 (de 10.12.255.0 à 10.12.255.31), l’adresse IP d’homologue BGP sur la passerelle VPN Azure sera 10.12.255.30. Vous pouvez trouver ces informations lorsque vous affichez la liste des informations de passerelle VPN Azure.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quelles sont les conditions requises concernant les adresses IP d’homologue BGP sur mon périphérique VPN ?
Votre adresse d’homologue BGP local **NE DOIT PAS** être identique à l’adresse IP publique de votre périphérique VPN. Utilisez une adresse IP différente de l’IP d’homologue BGP pour votre périphérique VPN. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur le périphérique. Spécifiez cette adresse sur la passerelle de réseau local correspondante, représentant l’emplacement.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Que dois-je spécifier comme préfixes d’adresse pour la passerelle de réseau local lorsque j’utilise le protocole BGP ?
La passerelle de réseau local Azure spécifie les préfixes d’adresse initiaux pour le réseau local. Avec le protocole BGP, vous devez allouer le préfixe de l’hôte (préfixe /32) de votre adresse IP d’homologue BGP en tant qu’espace d’adressage pour ce réseau local. Si votre adresse IP d’homologue BGP est 10.52.255.254, vous devez spécifier « 10.52.255.254/32 » comme espace localNetworkAddressSpace de la passerelle de réseau local, représentant ce réseau local. Ainsi, vous vous assurez que la passerelle VPN Azure établit la session BGP via le tunnel VPN S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Que dois-je ajouter à mon périphérique VPN local pour la session d’homologation BGP ?
Vous devez ajouter un itinéraire hôte de l’adresse IP d’homologue BGP Azure sur votre périphérique VPN pointant vers le tunnel VPN S2S IPsec. Par exemple, si l’adresse IP d’homologue VPN Azure est « 10.12.255.30 », vous devez ajouter un itinéraire hôte pour « 10.12.255.30 » avec l’interface de tronçon suivant de l’interface de tunnel IPsec correspondante sur votre périphérique VPN.
