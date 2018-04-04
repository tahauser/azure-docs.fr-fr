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
ms.openlocfilehash: 072c16a0e50a4922d44dd354b632f39b33d23cdd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
### <a name="supportedclientos"></a>Quels systèmes d’exploitation client puis-je utiliser avec une connexion point à site ?

Les systèmes d’exploitation clients pris en charge sont les suivants :

* Windows 7 (32 bits et 64 bits)
* Windows Server 2008 R2 (64 bits uniquement)
* Windows 8 (32 bits et 64 bits)
* Windows 8.1 (32 bits et 64 bits)
* Windows Server 2012 (64 bits uniquement)
* Windows Server 2012 R2 (64 bits uniquement)
* Windows Server 2016 (64 bits uniquement)
* Windows 10
* Mac OS X version 10.11 (El Capitan)
* Mac OS X version 10.12 (Sierra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Combien de points de terminaison clients VPN puis-je avoir dans ma configuration point à site ?

Nous prenons en charge jusqu'à 128 clients VPN pouvant se connecter à un réseau virtuel en même temps.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Puis-je parcourir les serveurs proxy et les pare-feu à l’aide de la fonctionnalité point à site ?

Azure prend en charge deux types d’options de VPN point à site :

* Protocole SSTP (Secure Socket Tunneling Protocol). SSTP est une solution SSL propriétaire de Microsoft qui peut pénétrer les pare-feux, car la plupart des pare-feux ouvrent le port TCP 443 utilisé par SSL.

* VPN IKEv2. Le VPN IKEv2 est une solution VPN IPsec basée sur des normes qui utilise les ports UDP 500 et 4500 ainsi que le protocole IP no. 50. Les pare-feux n’ouvrent pas toujours ces ports. Il est donc possible que le VPN IKEv2 ne soit pas en mesure de parcourir les proxies et pare-feux.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Si je redémarre un ordinateur client avec une configuration point à site, le réseau VPN va-t-il se reconnecter automatiquement ?

Par défaut, l'ordinateur client ne rétablit pas automatiquement la connexion VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>La configuration point à site prend-elle en charge la reconnexion automatique et DDNS sur les clients VPN ?

La reconnexion automatique et DDNS ne sont actuellement pas pris en charge dans les configurations VPN point à site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Puis-je avoir des configurations coexistantes site à site et point à site pour un même réseau virtuel ?

Oui. Pour le modèle de déploiement Resource Manager, vous devez disposer d’un type de VPN basé sur le routage pour votre passerelle. Pour le modèle de déploiement Classic, vous avez besoin d’une passerelle dynamique. Nous ne prenons pas en charge la configuration point à site pour les passerelles VPN à routage statique ou les passerelles VPN basée sur une stratégie.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Puis-je configurer un client point à site pour me connecter à plusieurs réseaux virtuels en même temps ?

Non. Un client point à site ne peut se connecter qu’aux ressources dans le réseau virtuel dans lequel réside la passerelle de réseau virtuel.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quel débit puis-je attendre des connexions site à site ou point à site ?

Il est difficile de maintenir le débit exact des tunnels VPN. IPsec et SSTP sont des protocoles VPN de chiffrement lourd. Le débit est également limité par la latence et la bande passante entre vos locaux et Internet. Pour une passerelle VPN ne disposant que des connexions VPN point à site IKEv2, le débit total que vous obtiendrez dépend de la référence SKU de passerelle. Pour plus d’informations sur le débit, consultez [Références SKU de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Puis-je utiliser un client VPN logiciel pour une connexion point à site prenant en charge SSTP et/ou IKEv2 ?

Non. Vous ne pouvez utiliser le client VPN natif sur Windows que pour SSTP et pour le client VPN natif sur Mac pour IKEv2. Reportez-vous à la liste des systèmes d’exploitation client pris en charge.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Azure prend-elle en charge le VPN IKEv2 avec Windows ?

Le protocole IKEv2 est pris en charge sur Windows 10 et Server 2016. Toutefois, pour pouvoir utiliser le protocole IKEv2, vous devez installer les mises à jour et définir une valeur de clé de Registre localement. Les versions du système d’exploitation antérieures à Windows 10 ne sont pas prises en charge et ne peuvent utiliser que SSTP.

Pour préparer Windows 10 ou Server 2016 pour IKEv2 :

1. Installez la mise à jour.

  | Version du SE | Date | Nombre/lien |
  |---|---|---|---|
  | Windows Server 2016<br>Windows 10 version 1607 | 17 janvier 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
  | Windows 10 version 1703 | 17 janvier 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
  |  |  |  |  |

2. Définissez la valeur de clé de Registre. Créer ou de définir la clé REG_DWORD « HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload » sur 1 dans le Registre.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Que se passe-t-il lorsque je configure SSTP et IKEv2 pour les connexions VPN P2S ?

Lorsque vous configurez SSTP et IKEv2 dans un environnement mixte (composé d’appareils Windows et Mac), le client VPN Windows essaiera toujours le tunnel IKEv2 d’abord, mais il reviendra à SSTP si la connexion IKEv2 n’a pas abouti. MacOSX se connecte uniquement via le protocole IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>À part Windows et Mac, quelles autres plateformes sont prises en charge par Azure pour le réseau VPN P2S ?

Azure prend en charge uniquement Windows et Mac pour le réseau VPN P2S.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>J’ai déjà une passerelle VPN Azure déployée. Puis-je activer RADIUS et/ou le réseau VPN IKEv2 sur celle-ci ?

Oui, vous pouvez activer ces nouvelles fonctionnalités sur les passerelles déjà déployées à l’aide de Powershell ou du portail Azure, pourvu que la référence SKU de passerelle utilisée prenne en charge RADIUS et/ou IKEv2. Par exemple, la référence SKU de base de passerelle VPN ne prend pas en charge RADIUS ou IKEv2.
