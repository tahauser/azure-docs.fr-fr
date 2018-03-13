---
title: "Mise en réseau Azure | Microsoft Docs"
description: "Découvrez les fonctionnalités et les services de mise en réseau Azure."
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: b0de4aa816d7decab1831449f855ab01b101db3a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-networking"></a>Mise en réseau Azure

Azure fournit un éventail de fonctionnalités de mise en réseau qui peuvent être utilisées ensemble ou séparément. Cliquez sur une des fonctionnalités principales suivantes pour en savoir plus à leur sujet :
- [Connectivité entre les ressources Azure](#connectivity) : connectez les ressources Azure ensemble dans un réseau virtuel privé et sécurisé dans le cloud.
- [Connectivité Internet](#internet-connectivity) : communiquez vers et à partir de ressources Azure via Internet.
- [Connectivité locale](#on-premises-connectivity) : connectez un réseau local aux ressources Azure via un réseau privé virtuel (VPN) sur Internet ou via une connexion dédiée à Azure.
- [Équilibrage de charge et direction de trafic](#load-balancing) : équilibrez la charge du trafic sur les serveurs dans le même emplacement et dirigez le trafic vers les serveurs dans différents emplacements.
- [Sécurité](#security) : filtrez le trafic réseau entre les sous-réseaux ou des machines virtuelles du réseau.
- [Routage](#routing) : utilisez le routage par défaut ou contrôlez entièrement le routage entre vos ressources Azure et locales.
- [Facilité de gestion](#manageability) : analysez et gérez vos ressources réseau Azure.
- [Outils de déploiement et de configuration](#tools) : utilisez un portail web ou des outils de ligne de commande multiplateforme pour déployer et configurer les ressources réseau.

## <a name="Connectivity"></a>Connectivité entre les ressources Azure

Les ressources Azure, comme les machines virtuelles, les services de cloud computing, les jeux de mise à l’échelle de machines virtuelles et les environnements Azure App Service peuvent communiquer en privé entre elles via un réseau virtuel Azure. Il s’agit d’un isolement logique du cloud Azure dédié à votre [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Vous pouvez implémenter plusieurs réseaux virtuels au sein de chaque abonnement Azure et de chaque [région](https://azure.microsoft.com/regions) Azure. Chaque réseau virtuel est isolé des autres réseaux virtuels. Pour chaque réseau virtuel, vous pouvez :

- Spécifier un espace d’adressage IP privé personnalisé à l’aide d’adresses (RFC 1918) publiques et privées. Azure attribue aux ressources connectées au réseau virtuel une adresse IP privée à partir de l’espace d’adressage que vous attribuez.
- Segmenter le réseau virtuel en un ou plusieurs sous-réseaux et allouer une partie de l’espace d’adressage du réseau virtuel à chaque sous-réseau.
- Utiliser la résolution de noms fournie par Azure ou spécifier votre propre serveur DNS pour une utilisation par les ressources connectées à un réseau virtuel.

Pour en savoir plus sur le service de réseau virtuel Azure, consultez l’article [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json). Vous pouvez connecter des réseaux virtuels entre eux, permettant aux ressources connectées à un réseau virtuel de communiquer avec eux via des réseaux virtuels. Vous pouvez utiliser une des deux options suivantes pour connecter des réseaux virtuels entre eux :

- **Homologation :** permet à des ressources connectées à différents réseaux virtuels Azure d’une même région Azure de communiquer entre elles. La bande passante et la latence entre les réseaux virtuels est la même que si les ressources étaient connectées au même réseau virtuel. Pour en savoir plus sur l’homologation, lisez l’article [Présentation de l’homologation de réseau virtuel](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Passerelle VPN :** permet à des ressources connectées à différents réseaux virtuels Azure de différentes régions Azure de communiquer entre elles. Le trafic entre les réseaux virtuels transite par une passerelle VPN Azure. La bande passante entre les réseaux virtuels est limitée à la bande passante de la passerelle. Pour en savoir plus sur la connexion de réseaux virtuels avec une passerelle VPN, lisez l’article [Configurer une connexion de réseau virtuel à réseau virtuel à travers les régions](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="internet-connectivity"></a>Connectivité Internet

Toutes les ressources Azure connectées à un réseau virtuel disposent par défaut d’une connectivité sortante vers Internet. L’adresse IP privée de la ressource est traduite via SNAT (source network address translated) en une adresse IP publique par l’infrastructure Azure. Pour en savoir plus sur la connectivité Internet sortante, lisez l’article [Présentation des connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json).

Pour pouvoir communiquer avec les ressources Azure à partir ou depuis Internet sans SNAT, une ressource doit être affectée à une adresse IP publique. Pour en savoir plus sur les adresses IP publiques, consultez l’article [Adresses IP publiques](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="on-premises-connectivity"></a>Connectivité locale

Vous pouvez accéder aux ressources de votre réseau virtuel en toute sécurité via une connexion VPN ou une connexion privée directe. Pour envoyer du trafic réseau entre votre réseau virtuel Azure et votre réseau local, vous devez créer une passerelle de réseau virtuel. Vous configurez les paramètres de la passerelle pour créer le type de connexion que vous souhaitez, VPN ou ExpressRoute.

Vous pouvez connecter votre réseau local à un réseau virtuel à l’aide de n’importe quelle combinaison des options suivantes :

**Point à site (VPN sur SSTP)**

L’illustration suivante montre des connexions point à site distinctes entre plusieurs ordinateurs et un réseau virtuel :

![Point à site](./media/networking-overview/point-to-site.png)

Cette connexion est établie entre un seul ordinateur et un réseau virtuel. Ce type de connexion est utile si vous n’êtes pas familiarisé avec Azure, ou pour les développeurs car elle nécessite peu voire pas de modifications de votre réseau existant. Cela est également pratique lorsque vous vous connectez à partir d’un emplacement distant, comme lors d’une conférence ou à domicile. Les connexions point à site sont souvent associées à une connexion de site à site via la même passerelle de réseau virtuel. La connexion utilise le protocole SSTP pour fournir une communication chiffrée via Internet entre l’ordinateur et le réseau virtuel. La latence d’un réseau VPN de point à site est imprévisible, car le trafic transite par Internet.

**Site à site (tunnel VPN IPsec/IKE)**

![De site à site](./media/networking-overview/site-to-site.png)

Cette connexion est établie entre votre appareil VPN local et une passerelle VPN Azure. Ce type de connexion permet à n’importe quelle ressource locale de votre choix à accéder au réseau virtuel. La connexion s’effectue via un réseau VPN IPSec/IKE qui fournit une communication chiffrée via Internet entre votre appareil local et la passerelle VPN Azure. Vous pouvez connecter plusieurs sites locaux à la même passerelle VPN. L’appareil VPN local sur chaque site doit avoir une adresse IP publique externe qui ne se trouve pas derrière un NAT. La latence d’une connexion de site à site est imprévisible car le trafic transite par Internet.

**ExpressRoute (connexion privée dédiée)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Ce type de connexion est établi entre votre réseau et Azure via un partenaire ExpressRoute. Cette connexion est privée. Le trafic ne transite pas par Internet. La latence d’une connexion ExpressRoute est prévisible, car le trafic ne transite pas par Internet. Vous pouvez associer ExpressRoute à une connexion de site à site.

Pour en savoir plus sur toutes les options de connexion précédentes, lisez l’article [Diagrammes des topologies de connexion](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="load-balancing"></a>Équilibrage de charge et direction de trafic

Microsoft Azure propose plusieurs services destinés à gérer la distribution et l’équilibrage de la charge du trafic réseau. Vous pouvez utiliser une des fonctionnalités suivantes séparément ou ensemble :

**Équilibrage de la charge du DNS**

Le service Azure Traffic Manager assure un équilibrage de charge DNS global. Traffic Manager répond aux clients avec l’adresse IP d’un point de terminaison intègre, sur la base d’une des méthodes de routage suivantes :
- **Géographique** : les utilisateurs sont dirigés vers des points de terminaison spécifiques (Azure, externes ou imbriqués) selon l’emplacement géographique dont leur requête DNS provient. Cette méthode rend possibles des scénarios où connaître la région géographique d’un client, et le routage en conséquence, est importante. Exemples : respect des obligations en matière de souveraineté des données, localisation de contenu et d’expérience utilisateur, mesure du trafic en provenance de différentes régions.
- **Performances :** l’adresse IP retournée au client est la « plus proche » du client. Le point de terminaison « le plus proche » n’est pas nécessairement le plus proche en termes de distance géographique. Au lieu de cela, la méthode détermine le point de terminaison le plus proche en mesurant le temps de réponse (ou latence) du réseau. Traffic Manager tient à jour une Table de latence Internet pour suivre le temps d’aller-retour entre les plages d’adresses IP et chaque centre de données Azure.
- **Priorité :** le trafic est dirigé vers le point de terminaison principal (à priorité la plus élevée). Si le point de terminaison principal n’est pas disponible, Traffic Manager route le trafic vers le deuxième point de terminaison. Si les points de terminaison de type principal et secondaire ne sont pas disponibles, le trafic est envoyé vers le troisième, et ainsi de suite. La disponibilité du point de terminaison est basée sur l’état configuré (activé ou désactivé) et la surveillance du point de terminaison en cours.
- **Tourniquet (round-robin) pondéré :** pour chaque requête, Traffic Manager choisit au hasard un point de terminaison disponible. La probabilité de choisir un point de terminaison dépend des pondérations assignées à tous les points de terminaison disponibles. L’utilisation de la même pondération pour tous les points de terminaison produit une distribution uniforme du trafic. L’utilisation de pondérations supérieures ou inférieures sur certains points de terminaison a pour effet que ceux-ci sont retournés plus ou moins fréquemment dans les réponses DNS.

L’image suivante illustre une demande pour une application web dirigée vers un point de terminaison d’application web. Les points de terminaison peuvent également être d’autres services Azure, tels que des machines virtuelles et services de cloud computing.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Le client se connecte directement à ce point de terminaison. Azure Traffic Manager est capable de détecter un point de terminaison défectueux. Si tel est le cas, il redirige les clients vers un autre point de terminaison intègre. Pour en savoir plus sur Traffic Manager, consultez l’article [Présentation d’Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

**Équilibrage de charge d’application**

Le service Azure Application Gateway fournit Application Deliver Controller (ADC) sous forme de service. Application Gateway offre diverses fonctionnalités d’équilibrage de charge de couche 7 (HTTP/HTTPS) pour vos applications, y compris un pare-feu d’applications web pour protéger vos applications web contre les vulnérabilités et failles. Application Gateway vous permet d’optimiser la productivité de la batterie de serveurs web en déchargeant une terminaison SSL gourmande en ressources du processeur vers la passerelle Application Gateway. 

Parmi les autres fonctionnalités de routage de couche 7, citons la distribution en tourniquet (round robin) du trafic entrant, l’affinité de session basée sur les cookies, le routage basé sur le chemin des URL et la possibilité d’héberger plusieurs sites web derrière une seule passerelle Application Gateway. Cette dernière peut être configurée en tant que passerelle accessible sur Internet, passerelle interne uniquement ou une combinaison des deux. La passerelle Application Gateway est une solution Azure entièrement gérée, très évolutive et hautement disponible. Elle fournit un ensemble complet de fonctionnalités de diagnostics et de journalisation, pour une meilleure gérabilité. Pour en savoir plus sur Application Gateway, lisez l’article [Vue d’ensemble d’Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json).

L’illustration suivante montre le routage d’URL basé sur le chemin avec Application Gateway :

![Application Gateway](./media/networking-overview/application-gateway.png)

**Équilibrage de charge réseau**

Azure Load Balancer fournit un équilibrage de charge de couche 4 hautement performant et à faible latence pour tous les protocoles UDP et TCP. Il gère les connexions entrantes et sortantes. Vous pouvez configurer des points de terminaison avec équilibrage de charge interne et public. Vous pouvez définir des règles de mappage des connexions entrantes aux destinations du pool principal, en utilisant des options d’analyse d’intégrité TCP et HTTP de façon à gérer la disponibilité du service. Pour en savoir plus sur l’équilibrage de charge, lisez l’article [Présentation de l’équilibreur de charge](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

L’illustration suivante montre une application multiniveau sur Internet qui utilise à la fois l’équilibrage de charge interne et externe :

![Équilibrage de charge](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Sécurité

Vous pouvez filtrer le trafic vers et à partir des ressources Azure à l’aide des options suivantes :

- **Réseau :** vous pouvez implémenter des groupes de sécurité réseau Azure (NSG) pour filtrer le trafic entrant et sortant des ressources Azure. Chaque groupe de sécurité réseau contient une ou plusieurs règles de trafic entrant et sortant. Chaque règle spécifie les adresses IP source, les adresses IP de destination, le port et le protocole avec lequel le trafic est filtré. Les groupes de sécurité réseau peuvent être appliqués à des sous-réseaux individuels et des machines virtuelles individuelles. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Présentation des groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Application :** avec une passerelle Application Gateway et le pare-feu d’applications web, vous pouvez protéger vos applications web contre les vulnérabilités et failles. Des exemples courants sont les attaques par injection SQL, les scripts intersites et les en-têtes mal formés. Application Gateway filtre ce trafic et l’empêche d’atteindre vos serveurs web. Vous pouvez configurer les règles que vous souhaitez activer. La possibilité de configurer les stratégies de négociation SSL est fournie pour permettre de désactiver certaines stratégies. Pour en savoir plus sur le pare-feu d’applications web, consultez l’article [Pare-feu d’applications web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

Si vous avez besoin d’une fonctionnalité réseau qu’Azure ne fournit pas, ou que vous souhaitez utiliser des applications réseau que vous utilisez en local, vous pouvez implémenter les produits dans les machines virtuelles et les connecter à votre réseau virtuel. La [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) contient plusieurs machines virtuelles différentes préconfigurées avec des applications réseau que vous utilisez peut-être actuellement. Ces machines virtuelles préconfigurées sont généralement appelées appliances virtuelles réseau (NVA). Les NVA sont disponibles avec des applications comme les pare-feu et l’optimisation du réseau étendu.

## <a name="routing"></a>Routage

Azure crée des tables de routage qui permettent aux ressources connectées à un sous-réseau d’un réseau virtuel de communiquer entre elles, par défaut. Vous pouvez implémenter un des types suivants ou les deux pour remplacer les itinéraires par défaut créés par Azure :
- **Défini par l’utilisateur :** vous pouvez créer des tables de routage personnalisées avec des itinéraires qui contrôlent où le trafic est acheminé pour chaque sous-réseau. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article [Itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Itinéraires BGP (protocole de passerelle frontière) :** si vous connectez votre réseau virtuel à votre réseau local via une connexion ExpressRoute ou la passerelle VPN Azure, vous pouvez propager les itinéraires BGP à vos réseaux virtuels. BGP est le protocole de routage standard couramment utilisé sur Internet pour échanger des informations de routage et d’accessibilité entre plusieurs réseaux. Dans le contexte des réseaux virtuels Azure, le protocole BGP permet aux passerelles VPN Azure et à vos périphériques VPN locaux (appelés voisins ou homologues BGP) d’échanger des « itinéraires » qui informent les deux passerelles de la disponibilité et de l’accessibilité de ces préfixes pour transiter par les passerelles ou routeurs impliqués. Le protocole BGP assure également le routage de transit entre plusieurs réseaux en propageant les itinéraires qu’une passerelle BGP obtient d’un homologue BGP à tous les autres homologues BGP. Pour plus d’informations sur BGP, consultez l’article [Présentation de BGP avec les passerelles VPN Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="manageability"></a>Facilité de gestion

Azure fournit les outils suivants pour surveiller et gérer la mise en réseau :
- **Journaux d’activité :** toutes les ressources Azure ont des journaux d’activité qui fournissent des informations sur les opérations qui ont lieu, l’état des opérations et la personne qui a initié l’opération. Pour en savoir plus sur les journaux d’activité, lisez l’article [Présentation des journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Journaux de diagnostic :** Les événements périodiques et spontanés sont créés par les ressources réseau et journalisés dans les comptes de stockage Azure, puis envoyés à une instance Azure Event Hub ou à Azure Log Analytics. Les journaux de diagnostic fournissent des informations sur l’intégrité d’une ressource. Les journaux de diagnostic sont fournis pour l’équilibrage de charge (sur Internet), les groupes de sécurité réseau, les itinéraires et Application Gateway. Pour en savoir plus sur les journaux de diagnostic, consultez l’article [Présentation des journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Métriques** : Les métriques sont des mesures et des compteurs de performances collectés sur une période donnée. Elles peuvent être utilisées pour déclencher des alertes basées sur des seuils. Les métriques sont actuellement disponibles pour Application Gateway. Pour en savoir plus sur les métriques, consultez l’article [Présentation des métriques](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Résolution de problèmes :** des informations de dépannage sont accessibles directement dans le portail Azure. Les informations vous aident à diagnostiquer les problèmes courants avec ExpressRoute, passerelle VPN, Application Gateway, journaux de sécurité réseau, itinéraires, DNS, équilibrage de charge et Traffic Manager.
- **Contrôle d’accès en fonction du rôle (RBAC) :** contrôlez qui peut créer et gérer les ressources réseau avec le contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez l’article [Prise en main du contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md?toc=%2fazure%2fnetworking%2ftoc.json). 
- **Capture de paquets :** le service Azure Network Watcher offre la possibilité d’exécuter une capture de paquets sur une machine virtuelle via une extension dans la machine virtuelle. Cette fonctionnalité est disponible pour les machines virtuelles Linux et Windows. Pour en savoir plus sur la capture de paquets, consultez l’article [Vue d’ensemble des captures de paquets](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Vérifier les flux IP :** Network Watcher vous permet de vérifier les flux IP entre une machine virtuelle Azure et une ressource distante pour déterminer si les paquets sont autorisés ou non. Cette fonctionnalité offre aux administrateurs la possibilité de diagnostiquer rapidement les problèmes de connectivité. Pour en savoir plus sur la vérification des flux IP, consultez l’article [Vue d’ensemble des flux IP](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Résoudre les problèmes de connectivité VPN :** la fonctionnalité de résolution des problèmes de VPN de Network Watcher offre la possibilité d’interroger une connexion ou passerelle et de vérifier l’intégrité des ressources. Pour en savoir plus sur le dépannage des connexions VPN, consultez l’article [Vue d’ensemble du dépannage de la connectivité VPN](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Afficher la topologie réseau :** afficher une représentation graphique des ressources réseau dans un réseau virtuel avec Network Watcher. Pour en savoir plus sur l’affichage de la topologie réseau, consultez l’article [Vue d’ensemble de la topologie](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="tools"></a>Outils de gestion et de configuration

Vous pouvez déployer et configurer les ressources réseau Azure avec un des outils suivants :

- **Portail Azure :** une interface utilisateur graphique qui s’exécute dans un navigateur. Ouvrez le [portail Azure](http://portal.azure.com).
- **Azure PowerShell :** des outils de ligne de commande pour la gestion d’Azure à partir d’ordinateurs Windows. Apprenez-en davantage sur Azure PowerShell en lisant l’article [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json).
- **Interface de ligne de commande Azure (CLI) :** des outils de ligne de commande pour la gestion d’Azure à partir d’ordinateurs Windows, Mac OS ou Linux. Apprenez-en davantage sur l’interface CLI Azure en lisant l’article [Vue d’ensemble de l’interface CLI Azure](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json).
- **Modèles Azure Resource Manager :** Un fichier (au format JSON) définissant l’infrastructure et la configuration d’une solution Azure. Un modèle vous permet de déployer votre solution à plusieurs reprises tout au long de son cycle de vie pour avoir la garantie que vos ressources présentent un état cohérent lors de leur déploiement. Pour en savoir plus sur la création de modèles, lisez les [Meilleures pratiques pour la création de modèles](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json). Les modèles peuvent être déployés avec le portail Azure, l’interface CLI ou PowerShell. Pour commencer à utiliser des modèles immédiatement, déployez un des nombreux modèles préconfigurés dans la bibliothèque [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?term=network). 

## <a name="pricing"></a>Tarifs

Certains des services de mise en réseau Azure sont facturés, tandis que d’autres sont gratuits. Affichez les pages de facturation pour [Réseau virtuel](https://azure.microsoft.com/pricing/details/virtual-network), [Passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/en-us/pricing/details/application-gateway/), [Équilibrage de charge](https://azure.microsoft.com/pricing/details/load-balancer), [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) et [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

- Créez votre premier réseau virtuel et connectez-y plusieurs machines virtuelles en effectuant les étapes décrites dans l’article [Créer votre premier réseau virtuel](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Connectez votre ordinateur à un réseau virtuel en effectuant les étapes décrites dans l’article [Configurer une connexion de point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Équilibrez la charge de trafic Internet sur les serveurs publics en effectuant les étapes décrites dans l’article [Créer un équilibrage de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
