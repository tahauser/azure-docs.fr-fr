---
title: FAQ sur les réseaux virtuels Azure | Microsoft Docs
description: Réponses aux questions les plus fréquemment posées sur les réseaux virtuels Microsoft Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.openlocfilehash: a5b4bac9e0d8bc10defaff251557129a70d8a022
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>FAQ sur les réseaux virtuels Azure

## <a name="virtual-network-basics"></a>Concepts de base du réseau virtuel

### <a name="what-is-an-azure-virtual-network-vnet"></a>Qu’est un réseau virtuel (VNet) Azure ?
Un réseau virtuel (VNet) Azure est une représentation de votre propre réseau dans le cloud. Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Vous pouvez utiliser des réseaux virtuels pour configurer et gérer des réseaux privés virtuels (VPN) dans Azure et, éventuellement, lier les réseaux virtuels avec les autres réseaux virtuels dans Azure ou avec votre infrastructure informatique locale pour créer des solutions hybrides ou intersite. Chaque réseau virtuel que vous créez dispose de son propre bloc CIDR et peut être lié à d’autres réseaux virtuels et locaux, du moment que les blocs CIDR ne se chevauchent pas. Vous pouvez également contrôler les paramètres de serveur DNS pour les réseaux virtuels et la segmentation du réseau virtuel en sous-réseaux.

Utilisez les réseaux virtuels pour effectuer les actions suivantes :

* Créer un réseau virtuel cloud uniquement privé dédié Vous n’avez pas toujours besoin d’une configuration intersite pour votre solution. Lorsque vous créez un réseau virtuel, vos services et les machines virtuelles au sein de votre réseau virtuel peuvent communiquer directement et en toute sécurité dans le cloud. Vous pouvez encore configurer des connexions au point de terminaison pour les machines virtuelles et les services qui requièrent une communication Internet dans le cadre de votre solution.
* Étendre en toute sécurité votre centre de données Avec les réseaux virtuels, vous pouvez créer des VPN site à site (S2S) traditionnels pour faire évoluer en toute sécurité la capacité de votre centre de données. Les VPN S2S utilisent le protocole IPSEC pour fournir une connexion sécurisée entre votre passerelle VPN d’entreprise et Azure.
* Activer les scénarios de cloud hybride Les réseaux virtuels vous donnent la possibilité de prendre en charge une variété de scénarios de cloud hybride. Vous pouvez connecter en toute sécurité des applications informatiques à n’importe quel type de système local, comme les ordinateurs centraux et les systèmes Unix.

### <a name="how-do-i-get-started"></a>Comment faire pour démarrer ?
Consultez la [Documentation Réseau virtuel](https://docs.microsoft.com/azure/virtual-network/) pour commencer. Ce document fournit des informations de présentation et de déploiement pour toutes les fonctionnalités du réseau virtuel.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Puis-je utiliser des réseaux virtuels sans connectivité intersite ?
Oui. Vous pouvez utiliser un réseau virtuel sans connexion à votre site. Par exemple, vous pouvez exécuter des contrôleurs de domaine Microsoft Windows Server Active Directory et des batteries de serveurs SharePoint dans un réseau virtuel Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Puis-je exécuter une optimisation WAN entre des réseaux virtuels ou entre un réseau virtuel et mon centre de données local ?

Oui. Vous pouvez déployer une [appliance virtuelle réseau d’optimisation WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) à partir de plusieurs fournisseurs via Azure Marketplace.

## <a name="configuration"></a>Configuration

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quels outils utiliser pour créer un réseau virtuel ?
Vous pouvez utiliser les outils suivants pour créer ou configurer un réseau virtuel :

* Portail Azure
* PowerShell
* Azure CLI
* Fichier de configuration réseau (netcfg - pour les réseaux virtuels classiques uniquement). Consultez l’article [Configurer un réseau virtuel à l’aide d’un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quelles plages d’adresses puis-je utiliser dans mes réseaux virtuels ?
Toute plage d’adresses IP définie dans [RFC 1918](http://tools.ietf.org/html/rfc1918). Par exemple, 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Puis-je avoir des adresses IP publiques dans mes réseaux virtuels ?
Oui. Pour plus d’informations sur les plages d’adresses IP publiques, consultez [Create a virtual network](manage-virtual-network.md#create-a-virtual-network) (Créer un réseau virtuel). Les adresses IP publiques ne sont pas directement accessibles à partir d’Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Y a-t-il une limite au nombre de sous-réseaux dans mon réseau virtuel ?
Oui. Pour plus d’informations, consultez [Limites de mise en réseau](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Les espaces d’adressage de sous-réseau ne peuvent pas se chevaucher.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?
Oui. Azure réserve des adresses IP dans chaque sous-réseau. Les première et dernière adresses IP de chaque sous-réseau sont réservées à la conformité du protocole, et les adresses x.x.x.1-x.x.x.3 de chaque sous-réseau sont utilisées pour les services Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Quelle taille peuvent avoir les réseaux virtuels et les sous-réseaux ?
Le plus petit sous-réseau pris en charge est /29 et le plus grand est /8 (à l’aide de définitions de sous-réseau CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Puis-je ajouter mes VLAN à Azure à l’aide de réseaux virtuels ?
Non. Les réseaux virtuels sont des superpositions de couche 3. Azure ne prend en charge aucune sémantique de couche 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Puis-je spécifier des stratégies de routage personnalisées sur des réseaux virtuels et des sous-réseaux ?
Oui. Vous pouvez créer une table de routage et l’associer à un sous-réseau. Pour plus d’informations sur le routage dans Azure, consultez [Routage du trafic de réseau virtuel](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Les réseaux virtuels prennent-ils en charge la multidiffusion ou la diffusion ?
Non. La multidiffusion et la diffusion ne sont pas prises en charge.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quels protocoles puis-je utiliser au sein de réseaux virtuels ?
Vous pouvez utiliser les protocoles TCP, UDP et ICMP TCP/IP au sein des réseaux virtuels. La monodiffusion est prise en charge dans les réseaux virtuels, à l’exception du protocole DHCP (Dynamic Host Configuration Protocol) via la monodiffusion (port source UDP/68 / port de destination UDP/67). La multidiffusion, la diffusion, les paquets encapsulés IP dans IP et les paquets Encapsulation générique de routage (GRE, Generic Routing Encapsulation) sont bloqués dans les réseaux virtuels. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Puis-je effectuer un test Ping sur mes routeurs par défaut au sein d’un réseau virtuel ?
Non.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Puis-je utiliser l’application tracert pour diagnostiquer la connectivité ?
Non.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Puis-je ajouter des sous-réseaux après avoir créé le réseau virtuel ?
Oui. Des sous-réseaux peuvent être ajoutés à des réseaux virtuels à tout moment, tant que la plage d’adresses de sous-réseau ne fait pas partie d’un autre sous-réseau et qu’il reste de l’espace dans la plage d’adresses du réseau virtuel.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Puis-je modifier la taille de mon sous-réseau après sa création ?
Oui. Vous pouvez ajouter, supprimer, développer ou réduire un sous-réseau si aucune machine virtuelle ou aucun service n’y est déployé.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Puis-je modifier des sous-réseaux après leur création ?
Oui. Vous pouvez ajouter, supprimer et modifier les blocs CIDR utilisés par un réseau virtuel.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Si j’exécute mes services dans un réseau virtuel, puis-je me connecter à Internet ?
Oui. Tous les services déployés au sein d’un réseau virtuel peuvent être connectés en sortie à Internet. Pour en savoir plus sur les connexions Internet sortantes dans Azure, consultez [Connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Si vous souhaitez vous connecter en entrée à une ressource déployée par le biais de Resource Manager, la ressource doit avoir une adresse IP publique qui lui est affectée. Pour en savoir plus sur les adresses IP publiques, consultez [Créer, modifier ou supprimer une adresse IP publique](virtual-network-public-ip-address.md). Chaque service cloud Azure déployé dans Azure dispose d’une adresse IP virtuelle publiquement adressable qui lui est assignée. Vous définissez des points de terminaison d’entrée pour les points de terminaison et les rôles PaaS des machines virtuelles afin de permettre à ces services d’accepter les connexions à partir d’Internet.

### <a name="do-vnets-support-ipv6"></a>Les réseaux virtuels prennent-ils en charge IPv6 ?
Non. Vous ne pouvez pas utiliser IPv6 avec les réseaux virtuels pour l’instant. Vous pouvez toutefois attribuer des adresses IPv6 aux équilibreurs de charge Azure pour équilibrer la charge des machines virtuelles. Pour plus d’informations, consultez [Vue d’ensemble du protocole IPv6 pour Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Un réseau virtuel peut-il couvrir plusieurs régions ?
Non. Un réseau virtuel est limité à une seule région. Un réseau virtuel peut toutefois couvrir des zones de disponibilité. Pour en savoir plus sur les zones de disponibilité, consultez [Vue d’ensemble de zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous pouvez connecter des réseaux virtuels dans différentes régions à l’aide d’une homologation de réseaux virtuels. Pour plus d’informations, consultez [Homologation de réseaux virtuels](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Puis-je connecter un réseau virtuel à un autre réseau virtuel dans Azure ?
Oui. Vous pouvez connecter un réseau virtuel à un autre réseau virtuel à l’aide des éléments suivants :
- **Homologation de réseaux virtuels** : pour plus d’informations, consultez [Homologation de réseaux virtuels](virtual-network-peering-overview.md)
- **Passerelle VPN Azure** : pour plus d’informations, consultez [Configurer une connexion de passerelle VPN de réseau virtuel à réseau virtuel à l’aide du portail Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Résolution de noms pour les machines virtuelles et les instances de rôle

### <a name="what-are-my-dns-options-for-vnets"></a>Quelles sont les options DNS pour les réseaux virtuels ?
Utilisez la table des décisions sur la page [Résolution de noms pour les machines virtuelles et les instances de rôle](virtual-networks-name-resolution-for-vms-and-role-instances.md) pour plus informations sur les options DNS disponibles.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Puis-je spécifier des serveurs DNS pour un réseau virtuel ?
Oui. Vous pouvez spécifier des adresses IP de serveur DNS dans les paramètres de réseau virtuel. Le paramètre est appliqué en tant que serveur DNS par défaut pour toutes les machines virtuelles du réseau virtuel.

### <a name="how-many-dns-servers-can-i-specify"></a>Combien de serveurs DNS puis-je spécifier ?
Reportez-vous aux [limites de mise en réseau](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Puis-je modifier mes serveurs DNS une fois que le réseau créé ?
Oui. Vous pouvez modifier la liste des serveurs DNS de votre réseau virtuel à tout moment. Si vous modifiez la liste des serveurs DNS, vous devez redémarrer chacune des machines virtuelles dans votre réseau virtuel de façon à sélectionner le nouveau serveur DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Qu’est ce qu’un serveur DNS fourni par Azure et fonctionne-t-il avec les réseaux virtuels ?
Le serveur DNS fourni par Azure est un serveur DNS mutualisé proposé par Microsoft. Azure enregistre toutes vos machines virtuelles et instances de rôle de service cloud dans ce service. Ce service fournit la résolution de noms par nom d’hôte pour les machines virtuelles et les instances de rôles contenues dans le même service cloud et par nom de domaine complet pour les machines virtuelles et les instances de rôle du même réseau virtuel. Pour en savoir plus sur DNS, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Il existe une limitation aux 100 premiers services cloud du réseau virtuel pour la résolution de nom inter-clients à l’aide du serveur DNS fourni par Azure. Si vous utilisez votre propre serveur DNS, cette restriction ne s’applique pas.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Puis-je remplacer mes paramètres DNS sur une base machine virtuelle/service cloud ?
Oui. Vous pouvez configurer des serveurs DNS par machine virtuelle ou service cloud pour remplacer les paramètres réseau par défaut. Toutefois, il est recommandé d’utiliser autant que possible le DNS à l’échelle du réseau.

### <a name="can-i-bring-my-own-dns-suffix"></a>Puis-je afficher mon propre suffixe DNS ?
Non. Vous ne pouvez pas spécifier un suffixe DNS personnalisé pour vos réseaux virtuels.

## <a name="connecting-virtual-machines"></a>Connexion aux machines virtuelles

### <a name="can-i-deploy-vms-to-a-vnet"></a>Puis-je déployer des machines virtuelles sur un réseau virtuel ?
Oui. Toutes les interfaces réseau (NIC) attachées à une machine virtuelle déployée via le modèle de déploiement Resource Manager doivent être connectées à un réseau virtuel. Les machines virtuelles déployées via le modèle de déploiement classique peuvent éventuellement être connectées à un réseau virtuel.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quels sont les différents types d’adresses IP que je peux attribuer aux machines virtuelles ?
* **Privé :** attribué à chaque carte réseau sur chaque machine virtuelle. L’adresse est attribuée à l’aide de la méthode statique ou dynamique. Les adresses IP privées sont affectées à partir de la plage que vous avez spécifiée dans les paramètres de sous-réseau de votre réseau virtuel. Les ressources déployées par le biais du modèle de déploiement classique se voient attribuer des adresses IP privées, même si elles ne sont pas connectées à un réseau virtuel. Le comportement de la méthode d’allocation est différent selon qu’une ressource a été déployée avec le modèle de déploiement classique ou Resource Manager : 

  - **Resource Manager** : une adresse IP privée attribuée avec la méthode statique ou dynamique reste associée à une machine virtuelle (Resource Manager) jusqu’à ce que la ressource soit supprimée. La différence est que vous sélectionnez l’adresse à attribuer lors de l’utilisation d’une méthode statique, et Azure choisit quand utiliser la méthode dynamique. 
  - **Classique** : une adresse IP privée attribuée avec la méthode dynamique peut changer lorsqu’une machine virtuelle (classique) est redémarrée après avoir été arrêtée (désallouée). Si vous devez vous assurer que l’adresse IP privée d’une ressource déployée via le modèle de déploiement classique ne change jamais, attribuez une adresse IP privée avec la méthode statique.

* **Public :** attribué (facultatif) aux cartes réseau attachées aux machines virtuelles déployées via le modèle de déploiement Azure Resource Manager. L’adresse peut être attribuée à l’aide de la méthode d’allocation statique ou dynamique. Toutes les machines virtuelles et instances de rôle de services cloud déployées via le modèle de déploiement classique existent au sein d’un service cloud, qui se voit attribuer une adresse IP virtuelle publique *dynamique*. Une adresse IP *statique* publique, appelée [Adresse IP réservée](virtual-networks-reserved-public-ip.md), peut éventuellement être attribuée en tant qu’adresse IP virtuelle. Vous pouvez attribuer des adresses IP publiques à des machines virtuelles ou instances de rôle de services cloud individuelles déployées via le modèle de déploiement classique. Ces adresses sont appelées [Adresses IP publiques de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md) et elles peuvent être attribuées de manière dynamique.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Puis-je réserver une adresse IP privée pour une machine virtuelle que je créerai ultérieurement ?
Non. Vous ne pouvez pas réserver d’adresse IP privée. Si une adresse IP privée est disponible, elle est affectée à une machine virtuelle ou à une instance de rôle par le serveur DHCP. La machine virtuelle peut être celle à laquelle vous souhaitez attribuer l’adresse IP privée. Vous pouvez toutefois modifier l’adresse IP privée d’une machine virtuelle déjà créée et utiliser n’importe quelle adresse IP privée disponible.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Les adresses IP privées peuvent-elles changer pour les machines virtuelles d’un réseau virtuel ?
Cela dépend. Si la machine virtuelle a été déployée via Resource Manager, non, et ce, que l’adresse IP ait été attribuée avec la méthode d’allocation statique ou dynamique. Si la machine virtuelle a été déployée par le biais du modèle de déploiement classique, les adresses IP dynamiques peuvent changer quand une machine virtuelle est démarrée après avoir été arrêtée (désallouée). L’adresse est libérée d’une machine virtuelle déployée via un modèle de déploiement lorsque la machine virtuelle est supprimée.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Puis-je attribuer manuellement des adresses IP aux cartes réseau au sein du système d’exploitation de la machine virtuelle ?
Oui, mais cela n’est pas recommandé, sauf si nécessaire, par exemple lors de l’affectation de plusieurs adresses IP à une machine virtuelle. Pour plus d’informations, consultez [Ajouter des adresses IP à une machine virtuelle](virtual-network-multiple-ip-addresses-portal.md#os-config). Si l’adresse IP attribuée à une carte réseau Azure jointe à une machine virtuelle fait l’objet de modifications et si l’adresse IP au sein du système d’exploitation de la machine virtuelle est différente, vous perdez la connectivité à la machine virtuelle.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Si j’arrête un emplacement de déploiement de service cloud ou si j’arrête une machine virtuelle à partir du système d’exploitation, que se passe-t-il pour mes adresses IP ?
Rien. Les adresses IP (adresse IP virtuelle publique, publique et privée) restent affectées à la machine virtuelle ou à l’emplacement de déploiement de services cloud.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Puis-je déplacer des machines virtuelles d’un sous-réseau à un autre dans un réseau virtuel sans redéploiement ?
Oui. Vous trouverez plus d’informations dans l’article [Déplacement d’une machine virtuelle ou d’une instance de rôle vers un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Puis-je configurer une adresse MAC statique pour ma machine virtuelle ?
Non. Une adresse MAC ne peut pas être configurée de manière statique.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Une fois créée, l’adresse MAC restera-t-elle la même pour ma machine virtuelle ?
Oui, l’adresse MAC reste la même pour une machine virtuelle déployée via les modèles de déploiement Resource Manager et classique jusqu’à sa suppression. Auparavant, l’adresse MAC était libérée si la machine virtuelle était arrêtée (libérée), mais désormais l’adresse MAC est conservée même lorsque la machine virtuelle est dans l’état libéré.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Puis-je me connecter à Internet à partir d’une machine virtuelle dans un réseau virtuel ?
Oui. Toutes les machines virtuelles et instances de rôle de services cloud déployés au sein d’un réseau virtuel peuvent se connecter à Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Services Azure qui se connectent à des réseaux virtuels

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Puis-je utiliser Azure App Service Web Apps avec un réseau virtuel ?
Oui. Vous pouvez déployer des applications web à l’intérieur d’un réseau virtuel à l’aide d’ASE (App Service Environment). Si vous avez configuré une connexion point à site pour votre réseau virtuel, toutes les applications web peuvent se connecter en toute sécurité et accéder aux ressources dans le réseau virtuel. Pour plus d’informations, consultez les articles suivants :

* [Création d'applications web dans un environnement App Service](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Intégrer une application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Utilisation de l’intégration au réseau virtuel et des connexions hybrides avec les applications web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Puis-je déployer des services cloud avec les rôles web et de travail (PaaS) dans un réseau virtuel ?
Oui. Vous pouvez déployer (facultatif) des instances de rôle de services cloud dans des réseaux virtuels. Pour cela, vous spécifiez le nom de réseau virtuel et les mappages rôle/sous-réseau dans la section de configuration réseau de votre configuration de service. Il est inutile de mettre à jour vos fichiers binaires.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Puis-je connecter un groupe de machines virtuelles identiques à un réseau virtuel ?
Oui. Vous devez connecter un groupe de machines virtuelles identiques à un réseau virtuel.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Existe-t-il une liste complète des services Azure à partir desquels je peux déployer des ressources dans un réseau virtuel ?

Oui. Pour plus d’informations, consultez [Intégration d’un réseau virtuel pour les services Azure](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Pour quelles ressources PaaS Azure puis-je restreindre l’accès à partir d’un réseau virtuel ?

Les ressources déployées par le biais de certains services PaaS Azure (par exemple, Stockage Azure et Azure SQL Database) peuvent restreindre l’accès réseau aux ressources d’un réseau virtuel uniquement via l’utilisation de points de terminaison de service de réseau virtuel. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Puis-je faire entrer ou sortir mes services dans les réseaux virtuels ?
Non. Impossible de faire entrer ou de faire sortir des services dans les réseaux virtuels. Pour déplacer une ressource vers un autre réseau virtuel, vous devez supprimer et redéployer la ressource.

## <a name="security"></a>Sécurité

### <a name="what-is-the-security-model-for-vnets"></a>Quel est le modèle de sécurité pour les réseaux virtuels ?
Les réseaux virtuels sont isolés les uns des autres, ainsi que des autres services hébergés dans l’infrastructure Azure. Un réseau virtuel est une délimitation d’approbation.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Puis-je limiter le flux de trafic entrant ou sortant aux ressources connectées au réseau virtuel ?
Oui. Vous pouvez appliquer des [groupes de sécurité réseau](security-overview.md) à des sous-réseaux individuels d’un réseau virtuel, à des cartes réseau attachées à un réseau virtuel, ou les deux.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Puis-je mettre en place un pare-feu entre les ressources connectées au réseau virtuel ?
Oui. Vous pouvez déployer une [appliance virtuelle réseau de pare-feu](https://azure.microsoft.com/marketplace/?term=firewall) à partir de plusieurs fournisseurs via Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Existe-t-il des informations sur la sécurisation des réseaux virtuels ?
Oui. Pour plus d’informations, consultez [Présentation de la sécurité réseau Azure](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API, schémas et outils

### <a name="can-i-manage-vnets-from-code"></a>Puis-je gérer les réseaux virtuels à partir du code ?
Oui. Vous pouvez utiliser des API REST pour les réseaux virtuels dans les modèles de déploiement [Azure Resource Manager](/rest/api/virtual-network) et [classique (Service Management)](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>Existe-t-il une prise en charge des outils pour les réseaux virtuels ?
Oui. En savoir plus sur l’utilisation des éléments suivants :
- Le portail Azure pour déployer des réseaux virtuels via les modèles de déploiement [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) et [classique](virtual-networks-create-vnet-classic-pportal.md).
- PowerShell pour gérer les réseaux virtuels déployés via les modèles de déploiement [Resource Manager](/powershell/module/azurerm.network) et [classique](/powershell/module/azure/?view=azuresmps-3.7.0).
- L’interface de ligne de commande Azure pour déployer et gérer les réseaux virtuels déployés via les modèles de déploiement [Resource Manager](/cli/azure/network/vnet) et [classique](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources).  
