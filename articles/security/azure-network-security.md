---
title: Azure Network Security | Microsoft Docs
description: "Découvrez les services informatiques cloud qui incluent une large sélection d’instances de calcul et de services pouvant être mis à l’échelle automatiquement pour répondre aux besoins de votre application ou de votre entreprise."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f0cc1716daa70bf7c860373819568774cf6f95d9
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-network-security"></a>Azure Network Security

Nous savons que la sécurité est la priorité dans le cloud et combien il est important que vous trouviez des informations précises et à jour sur la sécurité Azure. L’une des meilleures raisons d’utiliser Azure pour vos applications et services est de tirer parti de sa large gamme de fonctionnalités et d’outils de sécurité. Ces outils et fonctionnalités permettent de créer des solutions sécurisées sur la plateforme Azure.

Microsoft Azure assure la confidentialité, l’intégrité et la disponibilité des données client, tout en permettant la gestion transparente des responsabilités. L’article « Azure Network Security » vous propose une vue globale des contrôles de sécurité réseau de Microsoft Azure pour vous aider à mieux comprendre l’ensemble des contrôles de sécurité implémentés dans Microsoft Azure, en prenant en compte les perspectives du client.

Ce document vise à vous informer sur la grande variété de contrôles réseau que vous pouvez configurer pour améliorer la sécurité des solutions que vous déployez dans Azure. Si vous êtes intéressé par ce que Microsoft fait pour sécuriser l’infrastructure réseau de la plateforme Azure proprement dite, consultez la section sur la sécurité Azure dans le [Centre de confidentialité Microsoft](https://www.microsoft.com/trustcenter/security/azure-security).

## <a name="azure-platform"></a>Plateforme Azure

Azure est une plateforme de services de cloud public qui prend en charge un large éventail de systèmes d’exploitation, de langages de programmation, d’infrastructures, d’outils, de bases de données et d’appareils.  Elle permet d’exécuter des conteneurs Linux avec l’intégration de Docker, de créer des applications avec JavaScript, Python, .NET, PHP, Java et Node.js, de créer des serveurs principaux pour des appareils iOS, Android et Windows. Les services cloud Azure prennent en charge les technologies auxquelles des millions de développeurs et de professionnels de l’informatique font déjà confiance.

Lorsque vous générez ou faites migrer des ressources informatiques vers un fournisseur de services cloud public, vous comptez sur les capacités de cette organisation à protéger vos applications et données avec les services et les contrôles qu’elle vous fournit pour gérer la sécurité de vos ressources cloud.

L’infrastructure d’Azure est conçue de l’installation vers les applications pour héberger des millions de clients simultanément, et constitue une base fiable permettant de répondre aux besoins des entreprises en matière de sécurité. En outre, Azure vous offre un ensemble complet d’options de sécurité configurables, ainsi que la possibilité de contrôler ces options de façon à personnaliser la sécurité en fonction des exigences uniques de votre organisation en matière de déploiements.

## <a name="abstract"></a>Résumé

Les services de cloud public Microsoft offrent une grande évolutivité des services et de l’infrastructure, des fonctionnalités adaptées aux grandes entreprises et de nombreuses possibilités en matière de connectivité hybride. Vous pouvez choisir d’accéder à ces services via Internet ou Azure ExpressRoute et ainsi bénéficier d’une connectivité réseau privée. La plateforme Microsoft Azure vous permet d’étendre votre infrastructure dans le cloud et de générer des architectures à plusieurs niveaux en toute transparence. Par ailleurs, des tiers peuvent activer des fonctionnalités améliorées en offrant des services de sécurité et des appliances virtuelles.

Par leur conception, les services réseau d’Azure maximisent la flexibilité, la disponibilité, la résilience, la sécurité et l’intégrité. Ce livre blanc décrit en détail les fonctions de mise en réseau d’Azure et explique comment utiliser les fonctionnalités de sécurité natives d’Azure pour protéger les ressources des clients.

Ce livre blanc s’adresse essentiellement aux publics suivants :

- responsables techniques, administrateurs réseau et développeurs à la recherche de solutions de sécurité prises en charge et disponibles dans Azure ;

-   responsables de PME ou de processus d’entreprise désireux d’obtenir une vue d’ensemble globale des technologies et des services de mise en réseau Azure en rapport avec les débats autour de la sécurité réseau dans le cloud public Azure.

## <a name="azure-networking-big-picture"></a>Vision globale de la mise en réseau Azure
Microsoft Azure inclut une infrastructure réseau solide pour prendre en charge les exigences de connectivité de vos applications et services. La connectivité réseau est possible entre les ressources hébergées dans Azure, entre les ressources hébergées sur site et dans Azure, mais aussi vers et à partir d’Internet et d’Azure.

![Vision globale de la mise en réseau Azure](media/azure-network-security/azure-network-security-fig-1.png)

L’[infrastructure réseau Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) vous permet de connecter en toute sécurité les ressources Azure entre elles en utilisant des réseaux virtuels. Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Un réseau virtuel est une isolation logique du réseau cloud Azure dédié à votre abonnement. Vous pouvez connecter des réseaux virtuels à vos réseaux locaux.

Azure prend en charge la connectivité d’une liaison réseau étendu dédiée entre votre réseau local et un réseau virtuel Azure Virtual Network via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). La liaison entre Azure et votre site se fait via une connexion dédiée qui ne passe pas par l’Internet public. Si votre application Azure s’exécute dans plusieurs centres de données, vous pouvez utiliser [Microsoft Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) pour acheminer intelligemment les requêtes des utilisateurs vers différentes instances de l’application. Vous pouvez aussi acheminer le trafic vers des services qui ne s’exécutent pas dans Azure s’ils sont accessibles par Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Composants de mise en réseau Azure destinées aux grandes entreprises
Azure propose de nombreux composants de mise en réseau en rapport avec les débats sur la sécurité réseau. Nous décrivons ces composants de mise en réseau et examinons plus particulièrement les problèmes de sécurité qui leur sont associés.

> [!Note]
> Certains aspects de la mise en réseau Azure ne sont pas abordés : nous nous penchons uniquement sur ceux qui sont considérés comme essentiels dans la planification et la conception d’une infrastructure réseau sécurisée pour les services et les applications que vous déployez dans Azure.

Dans ce document, nous évoquerons les fonctionnalités d’entreprise Azure suivantes :

-   Connectivité réseau de base

-   Connectivité hybride

-   Contrôles de sécurité

-   Validation réseau

### <a name="basic-network-connectivity"></a>Connectivité réseau de base

Le service [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) vous permet de connecter en toute sécurité les ressources Azure entre elles à l’aide de réseaux virtuels. Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Un réseau virtuel est une isolation logique de l’infrastructure réseau Azure dédiée à votre abonnement. Vous pouvez aussi connecter des réseaux virtuels entre eux et à vos réseaux locaux au moyen de réseaux privés virtuels (VPN￼) de site à site et de [liaisons réseau étendu](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) dédiées.

![Connectivité réseau de base](media/azure-network-security/azure-network-security-fig-2.png)

Sachant que les machines virtuelles servent à héberger des serveurs dans Azure, la question est de savoir comment ces machines virtuelles se connectent à un réseau. En fait, les machines virtuelles se connectent à un réseau [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Les réseaux Azure Virtual Network sont semblables aux réseaux virtuels que vous utilisez localement avec vos propres solutions de plateforme de virtualisation, telles que Microsoft Hyper-V ou VMware.

#### <a name="intra-vnet-connectivity"></a>Connectivité entre réseaux virtuels

Vous pouvez connecter des réseaux virtuels entre eux, permettant aux ressources connectées à un réseau virtuel de communiquer avec eux via des réseaux virtuels. Vous pouvez utiliser une des deux options suivantes pour connecter des réseaux virtuels entre eux :

- **Homologation :** permet à des ressources connectées à différents réseaux virtuels Azure d’un même emplacement Azure de communiquer entre elles. La bande passante et la latence entre les réseaux virtuels sont les mêmes que si les ressources étaient connectées au même réseau virtuel. Pour en savoir plus sur les homologations, lisez l’article [Homologation de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Homologation](media/azure-network-security/azure-network-security-fig-3.png)

- **Connexion de réseau virtuel à réseau virtuel :** permet à des ressources connectées à un autre réseau virtuel Azure d’un même emplacement Azure ou de différents emplacements. Contrairement à l’homologation, la bande passante est limitée entre les réseaux virtuels car le trafic doit passer par une passerelle VPN Azure.

![Connexion entre réseaux virtuels](media/azure-network-security/azure-network-security-fig-4.png)


Pour en savoir plus sur la connexion de réseaux virtuels avec une connexion de réseau virtuel à réseau virtuel, lisez l’article [Configurer une connexion de réseau virtuel à réseau virtuel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Fonctionnalités de Réseau virtuel Azure :

Comme vous pouvez le voir, un réseau virtuel Azure permet de se connecter au réseau avec des machines virtuelles, ce qui permet à celles-ci de se connecter à d’autres ressources réseau de façon sécurisée. Cependant, la connectivité de base n’est qu’un point de départ. Les fonctionnalités du service Réseau virtuel Azure présentées ci-dessous correspondent aux caractéristiques de sécurité du Réseau virtuel Azure :

-   Isolation

-   Connectivité Internet

-   Connectivité des ressources Azure

-   Connectivité des réseaux virtuels

-   Connectivité locale

-   Filtrage du trafic

-   Routage

**Isolation**

Les réseaux virtuels sont [isolés](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) les uns des autres. Vous pouvez créer des réseaux virtuels distincts à des fins de développement, de test et de production et qui utilisent les mêmes blocs d’adresses [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). À l’inverse, vous pouvez créer plusieurs réseaux virtuels qui utilisent différents blocs d’adresses CIDR et connecter les réseaux entre eux. Vous pouvez segmenter un réseau virtuel en plusieurs sous-réseaux.

Azure assure une résolution de noms interne pour les machines virtuelles et les instances de rôle [Services cloud](https://azure.microsoft.com/services/cloud-services/) connectées à un réseau virtuel. Vous pouvez également configurer un réseau virtuel afin d’utiliser vos propres serveurs DNS au lieu d’utiliser la résolution de noms interne Azure.

Vous pouvez implémenter plusieurs réseaux virtuels au sein de chaque [abonnement](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure et de chaque [région](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure. Chaque réseau virtuel est isolé des autres réseaux virtuels. Pour chaque réseau virtuel, vous pouvez :

-   Spécifier un espace d’adressage IP privé personnalisé à l’aide d’adresses (RFC 1918) publiques et privées. Azure attribue aux ressources connectées au réseau virtuel une adresse IP privée de l’espace d’adressage.

-   Segmenter le réseau virtuel en un ou plusieurs sous-réseaux et allouer une partie de l’espace d’adressage du réseau virtuel à chaque sous-réseau.

-   Utiliser la résolution de noms fournie par Azure ou spécifier votre propre serveur DNS pour une utilisation par les ressources connectées à un réseau virtuel. Pour en savoir plus sur la résolution de noms dans les réseaux virtuels, lisez l’article [Résolution de noms pour les machines virtuelles et les services cloud](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Connectivité Internet**

Toutes les [machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/windows/) et les instances de rôle Services cloud connectées à un réseau virtuel ont accès à Internet, par défaut. Vous pouvez aussi activer l’accès entrant à des ressources spécifiques, en fonction des besoins. Vous pouvez également activer l’accès entrant à des ressources spécifiques, en fonction de vos besoins.

Toutes les ressources connectées à un réseau virtuel disposent par défaut d’une connectivité sortante vers Internet. L’adresse IP privée de la ressource est traduite via SNAT (source network address translated) en une adresse IP publique par l’infrastructure Azure. Vous pouvez modifier la connectivité par défaut en implémentant un routage et un filtrage de trafic personnalisés. Pour en savoir plus sur la connectivité Internet sortante, lisez l’article [Présentation des connexions sortantes dans Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pour pouvoir communiquer avec les ressources Azure à partir ou depuis Internet sans SNAT, une ressource doit être affectée à une adresse IP publique. Pour en savoir plus sur les adresses IP publiques, lisez l’article [Adresses IP publiques](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Connectivité des ressources Azure**

Les [ressources Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), telles que les services cloud et les machines virtuelles, peuvent être connectées à un même réseau virtuel. Les ressources peuvent se connecter entre elles à l’aide d’adresses IP privées, même si elles se trouvent dans des sous-réseaux différents. Azure fournit un routage par défaut entre les sous-réseaux, les réseaux virtuels et les réseaux locaux, sans que vous ayez à configurer et gérer ces itinéraires.

Vous pouvez connecter plusieurs ressources Azure à un réseau virtuel, par exemple des machines virtuelles, des services cloud, des environnements App Service et des groupes de machines virtuelles identiques. Les machines virtuelles se connectent à un sous-réseau d’un réseau virtuel via une interface réseau (NIC). Pour en savoir plus sur les interfaces réseau, lisez l’article [Interfaces réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Connectivité des réseaux virtuels**

Les [réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) peuvent être connectés entre eux, ce qui permet aux ressources connectées à un réseau virtuel de communiquer avec les ressources d’un autre réseau virtuel.

Vous pouvez connecter des réseaux virtuels entre eux, permettant aux ressources connectées à un réseau virtuel de communiquer avec eux via des réseaux virtuels. Vous pouvez utiliser une des deux options suivantes pour connecter des réseaux virtuels entre eux :

- **Homologation :** permet à des ressources connectées à différents réseaux virtuels Azure d’un même emplacement Azure de communiquer entre elles. La bande passante et la latence entre les réseaux virtuels sont les mêmes que si les ressources étaient connectées au même réseau virtuel. Pour en savoir plus sur l’homologation, lisez l’article [Homologation de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Connexion de réseau virtuel à réseau virtuel :** permet à des ressources connectées à un autre réseau virtuel Azure d’un même emplacement Azure ou de différents emplacements. Contrairement à l’homologation, la bande passante est limitée entre les réseaux virtuels car le trafic doit passer par une passerelle VPN Azure. Pour en savoir plus sur la connexion des réseaux virtuels avec une connexion de réseau virtuel à réseau virtuel, lisez l’article [Configurer une connexion de réseau virtuel à réseau virtuel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Connectivité locale**

Les réseaux virtuels peuvent être connectés à des réseaux [locaux](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) via des connexions réseau privées entre votre réseau et Azure, ou via une connexion VPN de site à site sur Internet.

Vous pouvez connecter votre réseau local à un réseau virtuel à l’aide de n’importe quelle combinaison des options suivantes :

- **Réseau privé virtuel (VPN) de point à site :** connexion établie entre un PC unique connecté à votre réseau et le réseau virtuel. Ce type de connexion est utile si vous n’êtes pas familiarisé avec Azure, ou pour les développeurs car elle nécessite peu voire pas de modifications de votre réseau existant. La connexion utilise le protocole SSTP pour fournir une communication chiffrée via Internet entre l’ordinateur et le réseau virtuel. La latence d’un VPN de point à site est imprévisible, car le trafic transite par Internet.

- **VPN de site à site :** connexion établie entre votre appareil VPN et une passerelle VPN Azure. Ce type de connexion permet à n’importe quelle ressource locale de votre choix à accéder à un réseau virtuel. La connexion s’effectue via un réseau virtuel privé IPSec/IKE qui assure le chiffrement des communications sur Internet entre votre appareil local et la passerelle VPN Azure. La latence d’une connexion de site à site est imprévisible, car le trafic transite par Internet.

- **Azure ExpressRoute :** connexion établie entre votre réseau et Azure via un partenaire ExpressRoute. Cette connexion est privée. Le trafic ne transite pas par Internet. La latence d’une connexion ExpressRoute est prévisible, car le trafic ne transite pas par Internet. Pour en savoir plus sur toutes les options de connexion précédentes, lisez l’article [Diagrammes de topologie de connexion](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtrage du trafic**

Le [trafic réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) entrant et sortant des machines virtuelles et des instances de rôle Services cloud peut être filtré par adresse IP source et port, adresse IP de destination et port, et protocole.

Vous pouvez filtrer le trafic réseau entre les sous-réseaux à l’aide d’une des deux options suivantes :

- **Groupes de sécurité réseau (NSG) :** chaque groupe de sécurité réseau peut contenir plusieurs règles de sécurité entrantes et sortantes qui vous permettent de filtrer le trafic par source et adresse IP de destination, port et protocole. Vous pouvez appliquer un groupe de sécurité réseau à chaque carte réseau d’une machine virtuelle. Vous pouvez également appliquer un groupe de sécurité réseau au sous-réseau auquel une carte réseau ou toute autre ressource Azure est connectée. Pour en savoir plus sur les groupes de sécurité réseau, lisez l’article [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Appliances de réseau virtuel :** une appliance de réseau virtuel est une machine virtuelle exécutant un logiciel qui remplit une fonction réseau, telle qu’un pare-feu. Affichez la liste des appliances de réseau virtuel disponibles dans la Place de marché Azure. Il existe également des appliances de réseau virtuel qui fournissent une optimisation du réseau étendu et d’autres fonctions de trafic réseau. Les appliances de réseau virtuel sont généralement utilisées avec des itinéraires définis par l’utilisateur ou des itinéraires BGP. Vous pouvez également utiliser une appliance de réseau virtuel pour filtrer le trafic entre des réseaux virtuels.

**Routage**

Vous pouvez éventuellement remplacer le routage par défaut d’Azure en configurant vos propres itinéraires ou en utilisant des itinéraires BGP via une passerelle réseau.

Azure crée des tables de routage qui permettent aux ressources connectées à un sous-réseau d’un réseau virtuel de communiquer entre elles, par défaut. Vous pouvez implémenter une ou les deux options suivantes pour remplacer les itinéraires par défaut créés par Azure :

- **Itinéraires définis par l’utilisateur :** vous pouvez créer des tables de routage personnalisées avec des itinéraires qui contrôlent où le trafic est acheminé pour chaque sous-réseau. Pour en savoir plus sur les itinéraires définis par l’utilisateur, lisez l’article [Itinéraires définis par l’utilisateur](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Itinéraires BGP :** si vous connectez votre réseau virtuel à votre réseau local via une connexion ExpressRoute ou la passerelle VPN Azure, vous pouvez propager les itinéraires BGP à vos réseaux virtuels.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Connectivité internet hybride : se connecter à un réseau local
Vous pouvez connecter votre réseau local à un réseau virtuel à l’aide de n’importe quelle combinaison des options suivantes :

-   Connectivité Internet

-   VPN de point à site (VPN P2S)

-   VPN de site à site (VPN S2S)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Connectivité Internet

Comme son nom l’indique, la connectivité Internet rend vos charges de travail accessibles via Internet. Elle vous impose d’exposer différents points de terminaison publics aux charges de travail qui résident dans le réseau virtuel. Ces charges de travail peuvent être exposées avec un [équilibreur de charge connecté à Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) ou simplement en affectant une adresse IP publique à la machine virtuelle. De cette manière, il devient possible pour n’importe quel appareil connecté à Internet d’accéder à cette machine virtuelle, pourvu qu’un pare-feu d’hôte, des [groupes de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) et des [itinéraires définis par l’utilisateur ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) le permettent.

Dans ce cas de figure, vous pourriez exposer une application qui doit être publique sur Internet et être en mesure de vous y connecter de n’importe où, ou à partir à de lieux spécifiques, en fonction de la configuration de vos charges de travail.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>VPN de point à site ou VPN de site à site
Les deux appartiennent à la même catégorie. Dans les deux cas, votre réseau virtuel doit disposer d’une passerelle VPN et vous devez pouvoir vous y connecter à l’aide d’un client VPN pour votre station de travail dans le cadre de la [configuration point à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). Sinon, vous devez pouvoir configurer votre [périphérique VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) local de sorte qu’il puisse faire office de terminaison d’un VPN de site à site. De cette manière, les appareils locaux peuvent se connecter aux ressources du réseau virtuel.

Une configuration point à site (P2S) vous permet de connecter de manière sécurisée un ordinateur client individuel à un réseau virtuel. Le P2S est une connexion VPN sur SSTP (Secure Socket Tunneling Protocol).

![VPN de point à site](media/azure-network-security/azure-network-security-fig-5.png)

Les connexions point à site s’avèrent utiles quand vous voulez vous connecter à votre réseau virtuel à partir d’un site distant, par exemple de votre domicile ou d’un centre de conférence, ou quand vous n’avez qu’un nombre limité de clients à connecter à un réseau virtuel.

Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique. Vous établissez la connexion VPN depuis l’ordinateur client. Par conséquent, il n’est pas recommandé d’utiliser le P2S pour se connecter à Azure si une connexion permanente doit être mise en place pour relier un grand nombre d’appareils et d’ordinateurs locaux et votre réseau Azure.

![VPN de site à site](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Pour plus d’informations sur les connexions de point à site, consultez les [questions fréquentes (FAQ) sur les connexions point à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Une connexion de passerelle VPN de site à site permet de connecter votre réseau local à un réseau virtuel Azure via un tunnel VPN IPsec/IKE (IKEv1 ou IKEv2).

Ce type de connexion requiert un périphérique VPN local disposant d’une adresse IP publique exposée en externe. Cette connexion s’effectue via Internet. Elle vous permet de créer un « tunnel » pour le transit des informations d’une liaison chiffrée entre votre réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le chiffrement du tunnel est effectué par le biais du [mode de tunneling IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Le VPN de site à site est fiable, sécurisé et reconnu, mais le trafic transitant par le tunnel passe par Internet. En outre, la bande passante est relativement limitée (environ 200 Mbits/s maximum).

Si, dans le cadre de vos connexions entre locaux, il vous faut un niveau de sécurité hors normes ou des performances exceptionnelles, nous vous recommandons d’utiliser la connectivité entre locaux offerte par Azure ExpressRoute. ExpressRoute représente une liaison réseau étendu dédiée entre le site local et un fournisseur d’hébergement Exchange. Comme il s’agit d’une connexion de télécommunications, vos données ne transitent pas par Internet et, par conséquent, ne sont pas exposées aux risques potentiels inhérents aux communications Internet.

> [!Note]
> Pour plus d’informations sur les passerelles VPN, consultez [À propos de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Liaison réseau étendu dédiée
Microsoft Azure ExpressRoute vous permet d’étendre vos réseaux locaux à Azure par le biais d’une connexion privée dédiée assurée par un fournisseur de connectivité.

Les connexions ExpressRoute ne sont pas établies par le biais de l'Internet public. Elles offrent ainsi de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques sur Internet.

![ Liaison réseau étendu dédiée](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Pour savoir comment connecter votre réseau à Microsoft à l’aide d’ExpressRoute, consultez [Modèles de connectivité ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) et [Présentation technique d’ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Comme les options de VPN de site à site, ExpressRoute vous permet aussi de vous connecter à des ressources qui ne se trouvent pas nécessairement dans un seul réseau virtuel. En fait, selon la référence SKU, vous pouvez vous connecter à 10 réseaux virtuels. Si vous disposez du [module complémentaire Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), il est possible de se connecter à jusqu’à 100 réseaux virtuels, en fonction de la bande passante. Pour en savoir plus sur l’architecture de ces types de connexion, lisez l’article [Diagrammes de topologie de connexion](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Contrôles de sécurité
Avec un réseau virtuel Azure, vous disposez d’un réseau logique sécurisé qui est isolé des autres réseaux virtuels et qui prend en charge la plupart des contrôles de sécurité que vous utilisez sur vos réseaux locaux. Les clients créent leur propre structure en utilisant des sous-réseaux ; ils utilisent leur propre plage d’adresses IP privées et configurent des tables d’itinéraires, des groupes de sécurité réseau, des listes de contrôle d’accès (ACL), des passerelles et des appliances virtuelles pour exécuter leurs charges de travail dans le cloud.

Les contrôles de sécurité que vous pouvez utiliser sur vos réseaux virtuels Azure sont les suivants :

-   Contrôles d’accès réseau

-   Itinéraires définis par l’utilisateur

-   Appliance de sécurité réseau

-   Application Gateway

-   Pare-feu d’applications web Azure

-   Contrôle de disponibilité réseau

#### <a name="network-access-controls"></a>Contrôles d’accès réseau
S’il est vrai que le Réseau virtuel Azure est l’élément central du modèle de mise en réseau Azure et qu’il assure isolation et protection, les [groupes de sécurité réseau (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) constituent le principal outil pour appliquer et contrôler les règles de trafic réseau au niveau du réseau.

![ Contrôles d’accès réseau](media/azure-network-security/azure-network-security-fig-8.png)


Vous pouvez contrôler l’accès en autorisant ou en refusant la communication entre les charges de travail au sein d’un réseau virtuel, à partir des systèmes des réseaux du client via une connectivité des connexions entre différents locaux, ou la communication Internet directe.

Dans le schéma, les réseaux virtuels et les groupes de sécurité réseau résident dans une couche spécifique de la pile de sécurité globale Azure, où les groupes de sécurité réseau, le routage défini par l’utilisateur et les appliances virtuelles réseau peuvent servir à créer des limites de sécurité en vue de protéger les déploiements d’applications dans le réseau protégé.

Les groupes de sécurité réseau s’appuient sur un tuple à 5 éléments pour évaluer le trafic (qui sont utilisés dans les règles que vous configurez pour les groupes de sécurité réseau) :

-   [Adresse IP source et de destination](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Port source et de destination](https://technet.microsoft.com/library/dd197515)

-   Protocole : [TCP (Transmission Control Protocol](https://technet.microsoft.com/library/cc940037.aspx) ou [UDP (User Datagram Protocol)](https://technet.microsoft.com/library/cc940034.aspx)

Cela signifie que vous pouvez contrôler l’accès entre une machine virtuelle unique et un groupe de machines virtuelles, entre une machine virtuelle unique et une autre machine virtuelle unique ou entre des sous-réseaux entiers. Encore une fois, gardez à l’esprit qu’il s’agit d’un filtrage de paquets avec état simple et non d’une inspection de paquets complète. Un groupe de sécurité réseau n’assure aucune validation de protocole et n’offre pas de fonctionnalités IDS ou IPS au niveau du réseau.

Un groupe de sécurité réseau intègre certaines règles vous devez connaître. Ces règles sont les suivantes :

-   **Autoriser l’ensemble du trafic au sein d’un réseau virtuel spécifique :** toutes les machines virtuelles d’un même réseau Azure Virtual Network peuvent communiquer entre elles.

-   **Autoriser l’équilibrage de charge Azure pour le trafic entrant :** cette règle autorise le trafic entre une adresse source et une adresse de destination pour Azure Load Balancer.

-   **Refuser l’ensemble du trafic entrant :** cette règle bloque tout le trafic Internet que vous avez explicitement autorisé.

-   **Autoriser l’ensemble du trafic sortant vers Internet :** cette règle permet aux machines virtuelles de se connecter à Internet. Si vous ne voulez pas autoriser ces connexions, vous devez créer une règle pour bloquer ces connexions ou appliquer le tunneling forcé.

#### <a name="system-routes-and-user-defined-routes"></a>Itinéraires système et itinéraires définis par l’utilisateur

Quand vous ajoutez des machines virtuelles à un réseau virtuel dans Azure, vous remarquez que ces machines peuvent automatiquement communiquer entre elles sur le réseau. Il est inutile de spécifier une passerelle, bien que les machines virtuelles soient hébergées dans des sous-réseaux différents.

Cela vaut également pour la communication des machines virtuelles vers l’Internet public, et même vers votre réseau local en cas de connexion hybride de Microsoft Azure vers votre propre centre de données.

![Itinéraires système](media/azure-network-security/azure-network-security-fig-9.png)

Ce flux de communications est rendu possible par l’utilisation, par Microsoft Azure, d’une série d’itinéraires système régulant le trafic IP. Les itinéraires de système contrôlent le flux de communication dans les scénarios suivants :

-   au sein d’un sous-réseau ;

-   entre deux sous-réseaux d’un réseau virtuel ;

-   entre les machines virtuelles et Internet ;

-   entre deux réseaux virtuels via une passerelle VPN ;

-   entre deux réseaux virtuels via VNet Peering ([chaînage de services](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)) ;

-   entre un réseau virtuel et votre réseau local via une passerelle VPN.

Nombreuses sont les entreprises à avoir des exigences de sécurité et de conformité strictes qui nécessitent d’inspecter localement tous les paquets réseau pour appliquer des stratégies spécifiques. Azure propose un mécanisme appelé [tunneling forcé](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) qui achemine le trafic des machines virtuelles vers le réseau local en créant un itinéraire personnalisé ou au moyen de publications [BGP (Border Gateway Protocol)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) via ExpressRoute ou VPN.

Le tunneling forcé dans Azure est configuré par le biais d’itinéraires définis par l’utilisateur de réseau virtuel. La redirection du trafic vers un site local est exprimée comme un itinéraire par défaut vers la passerelle VPN Azure.

La section suivante répertorie la limite actuelle de la table et des itinéraires de routage pour un réseau virtuel Azure :

-   Chaque sous-réseau du réseau virtuel dispose d’une table de routage système intégrée. La table de routage système comporte les trois groupes d’itinéraires suivants :

 -  **Itinéraire de réseau virtuel local :** directement vers les machines virtuelles de destination dans le même réseau virtuel

 - **Itinéraires locaux :** vers la passerelle VPN Azure

 -  **Itinéraire par défaut :** directement vers Internet. Les paquets destinés aux adresses IP privées non couvertes par les deux itinéraires précédents sont supprimés.

-   Les itinéraires définis par l’utilisateur vous permettent de créer une table de routage pour ajouter un itinéraire par défaut, puis d’associer à cette table à vos sous-réseaux de réseau virtuel pour activer le tunneling forcé sur ces derniers.

-   Vous devez définir un « site par défaut » parmi les sites locaux intersites connectés au réseau virtuel.

-   Le tunneling forcé doit être associé à un réseau virtuel équipé d'une passerelle VPN à routage dynamique (pas de passerelle statique).

- Le tunneling forcé ExpressRoute n'est pas configuré de cette manière, mais il est activé par la publication d’un itinéraire par défaut via les sessions d'homologation BGP ExpressRoute.

> [!Note]
> Pour plus d’informations, consultez la [Documentation ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

#### <a name="network-security-appliances"></a>Appliances de sécurité réseau
Les groupes de sécurité réseau et les itinéraires définis par l’utilisateur peuvent contribuer à renforcer la sécurité réseau dans les couches Réseau et Transport du [modèle OSI](https://en.wikipedia.org/wiki/OSI_model). Cependant, dans certains cas, vous souhaiterez ou serez contraint d’activer la sécurité à des niveaux plus élevés de la pile réseau. Le cas échéant, nous vous recommandons de déployer les appliances de sécurité de réseau virtuel fournies par les partenaires d’Azure.

![Appliances de sécurité réseau](./media/azure-network-security/azure-network-security-fig-10.png)

Les appliances de sécurité réseau Azure améliorent la sécurité des réseaux virtuels et les fonctions réseau. De nombreux éditeurs en proposent via la [Place de marché Azure](https://azuremarketplace.microsoft.com). Ces appliances de sécurité virtuelles peuvent être déployées pour apporter les fonctionnalités suivantes :

-   Pare-feu à haute disponibilité

-   Prévention des intrusions

-   Détection des intrusions

-   Pare-feu d’applications web (WAF)

-   Optimisation de réseau étendu

-   Routage

-   Équilibrage de la charge

-   VPN

-   Gestion de certificats

-   Active Directory

-   Authentification multifacteur

#### <a name="application-gateway"></a>Application Gateway

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) est une appliance virtuelle dédiée qui propose un contrôleur de remise d’applications (ADC) en tant que service.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Application Gateway vous permet d’optimiser les performances et la disponibilité d’une batterie de serveurs web en déchargeant une terminaison SSL gourmande en ressources processeur sur la passerelle Application Gateway (déchargement SSL). Celle-ci offre aussi d’autres fonctionnalités de routage de couche 7, à savoir :

-   Distribution alternée du trafic entrant

-   Affinité de session basée sur les cookies

-   Routage basé sur le chemin de l’URL

-   Possibilité d’héberger plusieurs sites web derrière une seule passerelle Application Gateway


Un [pare-feu d’applications web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) est également intégré à Application Gateway. Il protège les applications web contre les vulnérabilités et le code malveillant exploitant une faille de sécurité les plus courants sur le web. La passerelle Application Gateway peut être configurée en tant que passerelle Internet, passerelle exclusivement interne ou une combinaison des deux.

Le pare-feu WAF Application Gateway peut être exécuté en mode détection ou prévention. Il est fréquent que les administrateurs l’exécutent en mode détection pour repérer le trafic à caractère malveillant. Quand du code potentiellement malveillant est détecté, l’activation du mode prévention a pour effet de bloquer le trafic entrant suspect.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Par ailleurs, le pare-feu WAF Application Gateway vous aide à surveiller les applications par rapport aux attaques via un journal WAF en temps réel intégré à [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) et [Azure Security Center](https://azure.microsoft.com/services/security-center/) qui permet d’assurer le suivi des alertes WAF et d’analyser facilement les tendances.

Ce journal au format JSON est directement intégré au compte de stockage du client. Vous bénéficiez d’un contrôle total sur ces journaux et pouvez appliquer vos propres stratégies de rétention.

Vous pouvez aussi ingérer ces journaux dans votre propre système analytique via l’[intégration des journaux Azure](https://aka.ms/AzLog). Les journaux WAF étant également intégrés à [Operations Management Suite (OMS)](https://www.microsoft.com/cloud-platform/operations-management-suite), vous pouvez utiliser la fonctionnalité Log Analytics d’OMS pour exécuter des requêtes précises et sophistiquées.

#### <a name="azure-web-application-firewall-waf"></a>Pare-feu d’applications web (WAF) Azure

Les applications web sont de plus en plus la cible d’attaques malveillantes qui exploitent des vulnérabilités bien connues, comme les attaques par injection de code SQL, les attaques par exécution de script entre sites et les autres attaques qui figurent dans la liste des [10 principales attaques recensées par la communauté OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). La prévention contre de telles attaques dans l’application passe par une maintenance rigoureuse, des mises à jour correctives et une surveillance dans les différentes couches de la topologie de l’application.

 ![Pare-feu d’applications web (WAF) Azure](./media/azure-network-security/azure-network-security-fig-13.png)

Un [pare-feu d’applications web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) centralisé permet de se prémunir contre les attaques web et de simplifier la gestion de la sécurité sans avoir à modifier l’application.

Une solution WAF peut également réagir plus rapidement à une menace de sécurité en exécutant la mise à jour corrective d’une vulnérabilité connue dans un emplacement central plutôt que de sécuriser individuellement chacune des applications web. Les passerelles d’application existantes peuvent être facilement converties en une passerelle d’application avec un pare-feu d’applications web.

#### <a name="network-availability-controls"></a>Contrôles de disponibilité réseau

Il existe différentes options pour répartir le trafic réseau à l’aide de Microsoft Azure. Ces options fonctionnent différemment, car elles disposent d’un ensemble de fonctionnalités différent et prennent en charge différents scénarios. Elles peuvent être utilisées de manière isolée ou en les combinant.

Les différents contrôles de disponibilité réseau sont :

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure Load Balancer**

Assure une haute disponibilité et des performances réseau optimales pour vos applications. Il s’agit d’un équilibreur de charge Layer-4 (TCP, UDP) qui distribue le trafic entrant parmi des instances saines de services définis dans un jeu à charge équilibrée.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Azure Load Balancer peut être configuré pour :

-   équilibrer la charge du trafic Internet entrant sur les machines virtuelles. Cette configuration est appelée [équilibrage de charge avec accès par Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   équilibrer le trafic entre des machines virtuelles dans un réseau virtuel, entre des machines virtuelles dans les services cloud ou entre des ordinateurs locaux et des machines virtuelles dans un réseau virtuel entre différents locaux. Cette configuration est appelée [équilibrage de charge interne](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   transférer du trafic externe vers une instance spécifique de machine virtuelle.

Toutes les ressources du cloud ont besoin d'une adresse IP publique pour être accessibles depuis Internet. L’infrastructure de cloud dans Azure utilise des adresses IP non routables pour ses ressources. Azure utilise la traduction d’adresses réseau (NAT) avec des adresses IP publiques pour communiquer avec Internet.

 **Application Gateway**

 Application Gateway fonctionne au niveau de la couche Application (couche 7 dans la pile de référence de réseau OSI). Il agit comme un service de proxy inversé, qui met fin à la connexion du client et transfère les requêtes vers les points de terminaison principaux.

 **Traffic Manager**

Microsoft Azure Traffic Manager vous permet de contrôler la répartition du trafic utilisateur pour les points de terminaison de service dans différents centres de données. Les points de terminaison de service pris en charge par Traffic Manager incluent des machines virtuelles Azure, des applications web et des services cloud. Vous pouvez également utiliser Traffic Manager avec des points de terminaison externes non-Azure.

Traffic Manager utilise le DNS (Domain Name System) pour diriger les demandes des clients vers le point de terminaison approprié en fonction de la [méthode de routage du trafic](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) et de l’intégrité des points de terminaison. Traffic Manager fournit un large éventail de méthodes de routage du trafic pour répondre à différents besoins d’application, la [surveillance](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) de l’intégrité des points de terminaison et le basculement automatique. Traffic Manager est résilient aux défaillances, notamment à l’échec d’une région Azure entière.

Azure Traffic Manager vous permet de contrôler la répartition du trafic entre les points de terminaison de votre application. Un point de terminaison est tout service côté Internet hébergé à l’intérieur ou à l’extérieur d’Azure.

Traffic Manager offre deux principaux avantages :

-   Distribution du trafic suivant l’une des [méthodes de routage du trafic](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Surveillance continue de l’intégrité des points de terminaison](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) et basculement automatique en cas d’échec des points de terminaison.

Quand un client tente de se connecter à un service, il doit d’abord résoudre le nom DNS du service à une adresse IP. Le client se connecte ensuite à l’adresse IP pour accéder au service. Traffic Manager utilise DNS pour diriger les clients vers des points de terminaison de service spécifiques en fonction des règles de la méthode de routage du trafic. Les clients se connectent directement au point de terminaison sélectionné. Traffic Manager n’est pas un proxy ou une passerelle. Traffic Manager ne voit pas le trafic entre le client et le service.

### <a name="azure-network-validation"></a>Validation réseau Azure

La validation réseau Azure vise à contrôler que le réseau Azure fonctionne comme prévu et que la validation peut être effectuée à l’aide des services et des fonctionnalités de surveillance réseau disponibles. Avec Azure Network Watcher, vous avez accès à une multitude de fonctionnalités de journalisation et de diagnostic qui vous permettent de tirer des enseignements sur les performances et l’état de votre réseau. Ces fonctionnalités sont accessibles via le portail, PowerShell, l’interface CLI, l’API Rest et le Kit de développement logiciel (SDK).

Azure Operational Security renvoie aux services, contrôles et fonctionnalités auxquels les utilisateurs ont accès pour protéger leurs données, leurs applications et d’autres ressources dans Microsoft Azure. Azure Operational Security repose sur une infrastructure qui intègre les connaissances acquises via différentes fonctionnalités spécifiques de Microsoft, notamment Microsoft Security Development Lifecycle (SDL), le programme Microsoft Security Response Center et une connaissance approfondie des cybermenaces.

-   [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Centre de sécurité Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Les personnes et les processus qui exploitent Microsoft Azure sont peut-être les meilleurs garants de la sécurité de la plateforme. Cette section décrit les fonctionnalités de l’infrastructure globale des centres de données Microsoft qui contribuent à améliorer et à assurer la sécurité, la continuité des activités et la confidentialité.

L’infrastructure sur laquelle s’appuie votre application est généralement constituée de plusieurs composants, par exemple une machine virtuelle, un compte de stockage et un réseau virtuel ou bien une application web, une base de données, un serveur de base de données et des services tiers. Vous ne voyez pas ces composants comme des entités distinctes, mais plutôt comme des parties associées et interdépendantes d’une seule et même entité. Vous avez alors besoin de regrouper le déploiement, la gestion et la surveillance de ces différentes parties. Azure Resource Manager vous permet de travailler avec les ressources de solution sous forme de groupe.

Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre solution dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Le gestionnaire de ressources assure la sécurité, les fonctions d’audit et de balisage pour vous aider à gérer vos ressources après le déploiement.

**Avantages liés à l’utilisation de Resource Manager**

Resource Manager offre plusieurs avantages :

-   Vous pouvez déployer, gérer et surveiller toutes les ressources de votre solution comme un groupe, plutôt que de les gérer individuellement.

-   Vous pouvez déployer votre solution à plusieurs reprises tout au long du cycle de vie de développement et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.

-   Vous pouvez gérer votre infrastructure à l’aide de modèles déclaratifs plutôt que de scripts.

-   Vous pouvez définir les dépendances entre les ressources, de façon à les déployer dans le bon ordre.

-   Vous pouvez appliquer le contrôle d’accès à tous les services dans votre groupe de ressources, car le contrôle d’accès en fonction du rôle (RBAC) est intégré en mode natif à la plateforme de gestion.

-   Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources de votre abonnement.

-   Vous pouvez clarifier la facturation de votre organisation en affichant les coûts d’un groupe de ressources partageant une balise.

> [!Note]
> Resource Manager propose une nouvelle façon de déployer et de gérer vos solutions. Si vous avez utilisé un modèle de déploiement antérieur et souhaitez obtenir des informations sur les modifications, consultez [Présentation du déploiement de Resource Manager et du déploiement classique](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Journalisation et surveillance réseau Azure

Azure propose de nombreux outils destinés à surveiller, prévenir, détecter et répondre aux événements de sécurité réseau. Vous avez ainsi accès aux outils suivants, les plus puissants en la matière :

-   Network Watcher

-   Surveillance au niveau des ressources réseau

-   Log Analytics

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) : les fonctionnalités de Network Watcher permettent de bénéficier d’une surveillance basée sur des scénarios. Ce service inclut la capture de paquets, le tronçon saut suivant, la vérification des flux IP, l’affichage de groupe de sécurité, les journaux de flux de groupe de sécurité réseau. La surveillance basée sur des scénarios fournit une vue de bout en bout des ressources réseau, à l’inverse de la surveillance des ressources réseau qui s’intéresse à chaque ressource spécifique.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un scénario réseau dans, vers et depuis Azure. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des informations sur votre réseau dans Azure.

Network Watcher propose actuellement les fonctionnalités suivantes :

#### <a name="topology"></a>Topologie

La fonctionnalité [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) retourne un graphique des ressources réseau présentes dans un réseau virtuel. Ce graphique illustre l’interconnexion entre les ressources afin de représenter la connectivité réseau de bout en bout. Dans le portail, la fonctionnalité Topologie retourne les objets de ressource par réseau virtuel. Les relations sont représentées par des lignes entre les ressources. Les ressources situées à l’extérieur de la région Network Watcher, même si elles figurent dans le groupe de ressources, ne sont pas affichées. Les ressources renvoyées dans la vue du portail sont un sous-ensemble des composants réseau qui sont représentés graphiquement. Pour afficher la liste complète des ressources réseau, vous pouvez utiliser [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) ou [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

À mesure que les ressources sont retournées, la connexion entre elles est modélisée selon deux relations.

- **Imbrication** : un réseau virtuel contient un sous-ensemble, qui contient lui-même une carte réseau.

- **Associé** : une carte réseau est associée à une machine virtuelle.

#### <a name="variable-packet-capture"></a>Capture de paquets variable

La fonctionnalité de [capture de paquets variable](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) de Network Watcher vous permet de créer des sessions de capture de paquets de façon à suivre le trafic sortant et entrant d’une machine virtuelle. La capture de paquets aide à diagnostiquer les anomalies réseau de manière proactive et réactive. Elle permet aussi de collecter des statistiques réseau, d’obtenir des informations sur les intrusions, de déboguer des communications client-serveur, etc.

La capture de paquets est une extension de machine virtuelle qui est démarrée à distance par le biais de Network Watcher. Cette fonctionnalité allège la tâche d’exécution manuelle d’une capture de paquets sur la machine virtuelle souhaitée et permet ainsi d’économiser un temps précieux. La capture de paquets peut être déclenchée par l’intermédiaire du portail, de PowerShell, de l’interface de ligne de commande ou de l’API REST. Les alertes de machine virtuelle constituent un exemple de mode de déclenchement de la capture de paquets.

#### <a name="ip-flow-verify"></a>Vérification du flux IP

La [vérification du flux IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) vérifie si un paquet à destination ou en provenance d’une machine virtuelle est autorisé ou refusé sur la base des informations d’un tuple à 5 éléments. Ces informations se composent de la direction, du protocole, de l’adresse IP locale, de l’adresse IP distante, du port local et du port distant. Si le paquet est refusé par un groupe de sécurité, le nom de la règle qui a refusé le paquet est renvoyé. Même si toutes les adresses IP source et de destination peuvent être choisies, cette fonctionnalité permet aux administrateurs de diagnostiquer rapidement les problèmes de connectivité en provenance ou à destination d’Internet et en provenance ou à destination de l’environnement local.

La vérification du flux IP cible une interface réseau d’une machine virtuelle. Le flux de trafic est ensuite vérifié en fonction des paramètres configurés vers ou à partir de cette interface réseau. Cette fonctionnalité est utile pour confirmer si une règle d’un groupe de sécurité réseau bloque le trafic entrant ou sortant d’une machine virtuelle.

#### <a name="next-hop"></a>Tronçon suivant

Cette fonctionnalité identifie le [tronçon suivant](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) pour les paquets acheminés dans la structure de réseau Azure, ce qui vous permet de diagnostiquer les itinéraires définis par l’utilisateur mal configurés. Le trafic d’une machine virtuelle est envoyé vers une destination en fonction des itinéraires effectifs associés à une carte réseau. Le tronçon suivant obtient le type de tronçon suivant et l’adresse IP d’un paquet à partir d’une machine virtuelle et d’une carte réseau spécifiques. Cela permet de déterminer si le paquet est dirigé vers la destination ou si le trafic est dirigé vers un trou noir.

Le tronçon suivant renvoie également la table d’itinéraires associée au tronçon suivant. Lorsque vous interrogez un tronçon suivant pour savoir si l’itinéraire est défini comme un itinéraire défini par l’utilisateur, celui-ci est renvoyé. Sinon, le tronçon suivant renvoie « Itinéraire du système ».

#### <a name="security-group-view"></a>Vue du groupe de sécurité

Obtient les règles de sécurité effectives et appliquées à une machine virtuelle. Les groupes de sécurité réseau sont associés à un niveau de sous-réseau ou à un niveau de carte réseau. Lorsqu’il est associé à un niveau de sous-réseau, il s’applique à toutes les instances de machine virtuelle du sous-réseau. La [vue du groupe de sécurité](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) du réseau retourne toutes les règles et tous les groupes de sécurité réseau configurés qui sont associés à un niveau de sous-réseau et de carte réseau pour une machine virtuelle fournissant des informations sur la configuration. En outre, les règles de sécurité effectives sont renvoyées pour chacune des cartes réseau d’une machine virtuelle. L’affichage du groupe de sécurité réseau vous permet de déterminer les vulnérabilités réseau d’une machine virtuelle, telles que les ports ouverts. Vous pouvez également valider si votre groupe de sécurité réseau fonctionne comme prévu en [comparant les règles de sécurité effectives avec celles configurées](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Journal des flux de groupe de sécurité réseau

 Les journaux de flux des groupes de sécurité réseau vous permettent de capturer les journaux relatifs au trafic autorisé ou refusé par les règles de sécurité des groupes. Le flux est défini par des informations de tuple à 5 éléments : adresse IP source, adresse IP de destination, port source, port de destination et protocole.

Les [journaux des flux de groupe de sécurité réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) désignent une fonctionnalité de Network Watcher qui vous permet de visualiser des informations sur le trafic IP d’entrée et de sortie par le biais d’un groupe de sécurité réseau.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Résolution des problèmes de passerelle de réseau virtuel et de connexion

Le service Network Watcher offre de nombreuses fonctionnalités en lien avec la bonne compréhension de vos ressources réseau dans Azure. Il permet notamment de résoudre les problèmes liés aux ressources. La [résolution des problèmes de ressources](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) peut être appelée par PowerShell, l’interface CLI ou l’API REST. Lorsque cette fonctionnalité est appelée, Network Watcher inspecte l’intégrité d’une passerelle de réseau virtuel ou d’une connexion et renvoie ses résultats.

Cette section passe en revue les différentes tâches de gestion actuellement disponibles pour la résolution des problèmes de ressources.

-   [Résoudre les problèmes d’une passerelle de réseau virtuel](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Résoudre les problèmes d’une connexion](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limites d’abonnement réseau

Les [limites d’abonnement réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fournissent des informations sur l’utilisation de chacune des ressources réseau d’un abonnement dans une région par rapport au nombre maximal de ressources disponibles.

#### <a name="configuring-diagnostics-log"></a>Configuration du journal de diagnostic

Network Watcher permet d’afficher les [journaux de diagnostic](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). Cet affichage contient toutes les ressources réseau qui prennent en charge la journalisation des diagnostics. À partir de celui-ci, vous pouvez activer et désactiver les ressources réseau facilement et rapidement.

### <a name="network-resource-level-monitoring"></a>Surveillance au niveau des ressources réseau

Les fonctionnalités suivantes sont disponibles pour la surveillance au niveau des ressources :

#### <a name="audit-log"></a>Journal d’audit

Les opérations réalisées dans le cadre de la configuration des réseaux sont journalisées. Ces journaux d’audit sont essentiels pour établir les différentes conformités. Ces journaux peuvent être affichés dans le portail Azure ou récupérés à l’aide des outils Microsoft tels que Power BI ou des outils tiers. Les journaux d’audit sont disponibles via le portail, PowerShell, l’interface de ligne de commande et l’API REST.

> [!Note]
> Pour plus d’informations sur les journaux d’audit, consultez [Opérations d’audit avec Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Les journaux d’audit sont disponibles pour les opérations effectuées sur toutes les ressources réseau.


#### <a name="metrics"></a>Métriques

Les métriques sont des indicateurs et des compteurs de performances collectés sur une période donnée. Les métriques sont disponibles pour Application Gateway. Elles peuvent être utilisées pour déclencher des alertes basées sur des seuils. Azure Application Gateway analyse par défaut l’intégrité de toutes les ressources de son pool principal et supprime automatiquement du pool les ressources considérées comme défectueuses. Application Gateway continue de surveiller les instances défaillantes et les réintroduit dans le pool principal intègre une fois qu’elles redeviennent disponibles et répondent aux sondes d’intégrité. La passerelle d’application envoie les sondes d’intégrité avec le même port que celui défini dans les paramètres HTTP du serveur principal. Cette configuration garantit que la sonde teste le même port que celui qu’utiliseraient les clients pour se connecter au serveur principal.

> [!Note]
> Consultez [Intégrité du serveur principal, journalisation des diagnostics et métriques pour la passerelle Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) pour afficher la façon dont les métriques peuvent être utilisées pour créer des alertes.

#### <a name="diagnostic-logs"></a>Journaux de diagnostic

Les événements périodiques et spontanés sont créés par les ressources réseau et journalisés dans les comptes de stockage, puis envoyés à un hub d’événements ou à Log Analytics. Ces journaux fournissent des informations sur l’intégrité d’une ressource. Ces journaux peuvent être affichés dans des outils tels que Power BI et Log Analytics. Pour savoir comment afficher les journaux de diagnostic, consultez [Solutions d’analyse réseaux Azure dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Les journaux de diagnostic sont disponibles pour [l’équilibrage de charge](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), les itinéraires, et [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher permet d’afficher les journaux de diagnostic. Cet affichage contient toutes les ressources réseau qui prennent en charge la journalisation des diagnostics. À partir de celui-ci, vous pouvez activer et désactiver les ressources réseau facilement et rapidement.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) est un service d’[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) qui surveille vos environnements cloud et locaux et assure leur disponibilité et leurs performances. Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources.

Log Analytics propose les solutions suivantes pour la surveillance de vos réseaux :

-   Analyseur de performances réseau (NPM)

-   Azure Application Gateway Analytics

-   Azure Network Security Group Analytics

#### <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)
La solution de gestion [Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) est une solution de surveillance réseau qui contrôle l’intégrité, la disponibilité et l’accessibilité des réseaux.

Elle est utilisée pour contrôler la connectivité entre :

-   le cloud public et local ;

-   les centres de données et les emplacements de l’utilisateur (filiales) ;

-   les sous-réseaux hébergeant différents niveaux d’une application multiniveau.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Azure Application Gateway Analytics dans Log Analytics

Les journaux pris en charge pour les passerelles d’application sont les suivants :

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Les métriques prises en charge pour les passerelles d’application sont les suivantes :

-   Débit sur 5 minutes

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Azure Network Security Group Analytics dans Log Analytics

Les journaux suivants sont pris en charge pour les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) :

- **NetworkSecurityGroupEvent :** contient les entrées pour lesquelles des règles NSG sont appliquées aux machines virtuelles et aux rôles d’instance en fonction de l’adresse MAC. L’état de ces règles est collecté toutes les 60 secondes.

- **NetworkSecurityGroupRuleCounter :** contient les entrées correspondant au nombre de fois que chaque règle NSG a été appliquée pour refuser ou autoriser le trafic.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la sécurité, lisez nos rubriques détaillées sur la sécurité :

-   [Analyse de journaux pour les groupes de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Les innovations en matière de réseau qui bouleversent le cloud](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC : le logiciel de commutation réseau qui propulse le cloud mondial Microsoft](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Comment Microsoft bâtit son puissant réseau mondial](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Le pari de l’innovation réseau](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
