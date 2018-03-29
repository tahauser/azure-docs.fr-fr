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
ms.openlocfilehash: f9b597427c3731b28ec68a7bc518955eb5f829ce
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>L’authentification RADIUS est-elle prise en charge sur toutes les références SKU de passerelle VPN Azure ?

L’authentification RADIUS est prise en charge pour les références SKU VpnGw1, VpnGw2 et VpnGw3. Si vous utilisez des références SKU héritées, l’authentification RADIUS est prise en charge sur les références SKU standard et de hautes performances. Elle n’est pas prise en charge sur la référence SKU de passerelle de base. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>L’authentification RADIUS est-elle prise en charge pour le modèle de déploiement classique ?
 
Non. L’authentification RADIUS n’est pas prise en charge pour le modèle de déploiement classique.
 
### <a name="are-3rd-party-radius-servers-supported"></a>Les serveurs RADIUS tiers sont-ils pris en charge ?

Oui, les serveurs RADIUS tiers sont pris en charge.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Quels sont les besoins en connectivité pour s’assurer que la passerelle Azure est en mesure d’atteindre un serveur RADIUS local ?

Une connexion site à site au site local est nécessaire, avec les itinéraires appropriés configurés.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>Le trafic vers un serveur RADIUS local (à partir de la passerelle VPN Azure) peut-il être routé via une connexion ExpressRoute ?

Non. Il ne peut être routé que via une connexion site à site.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>Y a-t-il une modification du nombre de connexions SSTP prises en charge avec l’authentification RADIUS ? Quel est le nombre maximal de connexions SSTP et IKEv2 prises en charge ?

Il n’y a pas de modification quant au nombre maximal de connexions SSTP prises en charge sur une passerelle avec l’authentification RADIUS. Il reste à 128. Le nombre maximal de connexions prises en charge est de 128, que la passerelle soit configurée pour SSTP, IKEv2 ou pour les deux.
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trusted-certificate-to-azure"></a>Quelle est la différence entre l’authentification par certificat à l’aide d’un serveur RADIUS et à l’aide de l’authentification par certificat Azure native (en chargeant un certificat approuvé dans Azure).

Dans l’authentification par certificat RADIUS, la demande d’authentification est transférée à un serveur RADIUS qui gère la validation du certificat réel. Cette option est utile si vous souhaitez intégrer une infrastructure d’authentification par certificat que vous possédez déjà via RADIUS.
  
Lorsque vous utilisez Azure pour l’authentification par certificat, la passerelle VPN Azure effectue la validation du certificat. Vous devez charger la clé publique de votre certificat sur la passerelle. Vous pouvez également spécifier la liste des certificats révoqués qui ne doivent pas être autorisés à se connecter.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>L’authentification RADIUS fonctionne-t-elle avec les réseaux VPN IKEv2 et SSTP ?

Oui, l’authentification RADIUS est prise en charge pour les réseaux VPN IKEv2 et SSTP.  