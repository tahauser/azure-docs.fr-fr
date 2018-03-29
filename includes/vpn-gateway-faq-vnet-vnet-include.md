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
ms.openlocfilehash: d562e58b86a6d5c18b10683dc4c14cf9c28c809b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Le FAQ relatif aux connexions de réseau virtuel à réseau virtuel s’applique aux connexions de passerelle VPN. Si vous recherchez des informations sur l’homologation de réseaux virtuels, voir [Homologation de réseaux virtuels](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Le trafic entre réseaux virtuels est-il facturé par Azure ?

Lorsque vous utilisez une connexion de passerelle VPN, le trafic de réseau virtuel à réseau virtuel au sein d’une même région est gratuit dans les deux sens. Le trafic de réseau virtuel à réseau virtuel de sortie entre différentes régions est facturé au tarif du transfert de données sortantes entre réseaux virtuels en fonction des régions sources. Pour plus d’informations, consultez la [page sur la tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Si vous vous connectez à vos réseaux virtuels à l’aide de l’homologation de réseaux virtuels, plutôt qu’à l’aide de passerelles VPN, consultez la [page sur la tarification de réseau virtuel](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Le trafic de réseau virtuel à réseau virtuel transite-t-il sur Internet ?

Non. Le trafic de réseau virtuel à réseau virtuel transite sur la dorsale principale de Microsoft Azure, et non sur Internet.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Le trafic de réseau virtuel à réseau virtuel est-il sécurisé ?

Oui, il est protégé par le chiffrement IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Ai-je besoin d’un périphérique VPN pour interconnecter des réseaux virtuels ?

Non. L’interconnexion de plusieurs réseaux virtuels Azure ne requiert pas de périphérique VPN, sauf si une connectivité intersite est nécessaire.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Mes réseaux virtuels doivent-ils se trouver dans la même région ?

Non. Les réseaux virtuels peuvent être situés dans des régions (emplacements) identiques ou différentes.

### <a name="if-the-vnets-are-not-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-ad-tenant"></a>Si les réseaux virtuels ne figurent pas dans le même abonnement, les abonnements doivent-ils être associés au même locataire Active Directory ?

Non.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Puis-je utiliser la connexion de réseau virtuel à réseau virtuel pour connecter des réseaux virtuels dans des instances Azure séparées ? 

Non. La connexion de réseau virtuel à réseau virtuel prend en charge la connexion de réseaux virtuels au sein de la même instance Azure. Par exemple, vous ne pouvez pas créer une connexion entre une instance Azure publique et les instances Azure du gouvernement chinois, allemand ou américain. Pour ces scénarios, envisagez l’utilisation d’une connexion VPN de site à site.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Puis-je utiliser une connexion de réseau virtuel à réseau virtuel en plus de connexions multisites ?

Oui. Une connectivité réseau virtuelle peut être utilisée en même temps que des VPN multisites.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>À combien de sites locaux et de réseaux virtuels peut se connecter un réseau virtuel ?

Consultez le tableau [Conditions requises de la passerelle](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements).

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Puis-je utiliser une connexion de réseau virtuel à réseau virtuel pour connecter des machines virtuelles ou des services cloud en dehors d’un réseau virtuel ?

Non. La connexion de réseau virtuel à réseau virtuel prend en charge la connexion de réseaux virtuels. Elle ne prend pas en charge la connexion de machines virtuelles ou de services cloud qui ne se trouvent pas dans un réseau virtuel.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Un service cloud ou un point de terminaison d’équilibrage de charge peut-il s’étendre sur différents réseaux virtuels ?

Non. Un service cloud ou un point de terminaison d’équilibrage de charge ne peut pas s’étendre sur différents réseaux virtuels, même si ces derniers sont interconnectés.

### <a name="can-i-used-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Puis-je utiliser un type de réseau VPN basé sur des stratégies pour les connexions de réseau virtuel à réseau virtuel ou multisites ?

Non. Les connexions de réseau virtuel à réseau virtuel et multisites nécessitent des passerelles VPN Azure avec des types de VPN basés sur des itinéraires (dits auparavant de routage dynamique).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Puis-je connecter un réseau virtuel avec un type de réseau VPN basé sur des itinéraires à un autre réseau virtuel avec un type de réseau VPN basé sur des stratégies ?

Non, les deux réseaux virtuels DOIVENT utiliser des réseaux VPN basés sur des itinéraires (dits auparavant de routage dynamique).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Les tunnels VPN se partagent-ils la bande passante ?

Oui. Tous les tunnels VPN du réseau virtuel partagent la bande passante disponible sur la passerelle VPN Azure, ainsi que le même contrat SLA concernant le temps d’activité des passerelles VPN dans Azure.

### <a name="are-redundant-tunnels-supported"></a>Les tunnels redondants sont pris en charge ?

Les tunnels redondants entre deux réseaux virtuels sont pris en charge lorsqu’une passerelle de réseau virtuel est configurée comme active/active.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Des espaces d’adressage peuvent-ils se chevaucher pour les configurations de réseau virtuel à réseau virtuel ?

Non. Il ne peut pas y avoir de chevauchement entre des plages d’adresses IP.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Des espaces d’adressage peuvent-ils se chevaucher entre les réseaux virtuels connectés et les sites locaux ?

Non. Il ne peut pas y avoir de chevauchement entre des plages d’adresses IP.



