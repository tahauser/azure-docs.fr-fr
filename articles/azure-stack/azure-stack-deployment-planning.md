---
title: "Considérations sur la planification pour les systèmes intégrés Azure Stack | Microsoft Docs"
description: "Découvrez ce que vous pouvez faire pour planifier maintenant et préparer Azure Stack à plusieurs nœuds."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 6ba6bed8321e1ffde8bc8959443682725da36827
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Considérations sur la planification pour les systèmes intégrés Azure Stack
Si vous êtes intéressé par un système intégré Azure Stack, vous devez comprendre certaines considérations principales sur la planification traitant du déploiement et la façon dont le système s’adapte à votre centre de données. Cet article fournit une vue d’ensemble de ces considérations pour vous aider à prendre des décisions d’infrastructure importantes pour votre système Azure Stack à plusieurs nœuds. Comprendre ces considérations est utile pour collaborer avec votre fournisseur de matériel OEM lorsqu’il déploie Azure Stack vers votre centre de données.  

> [!NOTE]
> Les systèmes Azure Stack à plusieurs nœuds ne peuvent être achetés qu’auprès de fournisseurs de matériel autorisés. 

Pour déployer Azure Stack, vous devez prendre un ensemble de décisions pour intégrer correctement Azure Stack à votre environnement. Vous devez fournir ces informations à votre fournisseur de solutions pendant le processus de planification et être préparé vis-à-vis du fournisseur de matériel avant le déploiement pour que le processus soit rapide et simple.

Les informations nécessaires incluent la mise en réseau, la sécurité et les informations d’identité avec de nombreuses décisions importantes qui peuvent nécessiter des connaissances dans différents domaines et de différents décideurs. Par conséquent, vous devrez peut-être faire appel à des personnes de différentes équipes de votre organisation pour vous assurer que toutes les informations requises sont prêtes avant le début du déploiement. Il peut être utile de communiquer avec votre fournisseur de matériel lors de la collecte de ces informations, car il peut vous fournir des conseils utiles à la prise de décisions.

Lors de la recherche et de la collecte des informations nécessaires, vous devrez peut-être apporter des modifications de configuration avant le déploiement à votre environnement réseau. Ceci peut inclure la réservation d’espaces d’adresse IP pour la solution Azure Stack, la configuration de vos routeurs, commutateurs et pare-feu afin de préparer la connectivité aux nouveaux commutateurs de la solution Azure Stack. Assurez-vous que le spécialiste de la zone de l’objet peut vous aider dans votre planification.

## <a name="management-considerations"></a>Considérations relatives à la gestion
Azure Stack est un système fermé, dont l’infrastructure est verrouillée à la fois du point de vue des autorisations et du réseau. Les listes de contrôle d’accès réseau (ACL) sont utilisées pour bloquer tout trafic entrant non autorisé et toute communication inutile entre les composants de l’infrastructure. Cela rend l’accès au système difficile pour les utilisateurs non autorisés.

Pour les opérations et la gestion quotidiennes, il n’existe aucun accès administrateur non restreint à l’infrastructure. Les opérateurs Azure Stack doivent gérer le système via le portail administrateur ou via Azure Resource Manager (via PowerShell ou l’API REST). Il n’existe aucun accès au système par d’autres outils de gestion tels que le gestionnaire Hyper-V ou le gestionnaire du cluster de basculement. Pour protéger le système, les logiciels tiers (par exemple, les agents) ne peuvent pas être installés dans les composants de l’infrastructure Azure Stack. L’interopérabilité avec les logiciels de gestion et de sécurité externes est effectuée via PowerShell ou l’API REST.

Lorsqu’un niveau plus élevé d’accès est nécessaire pour résoudre des problèmes n’ayant pas été résolus à l’aide des étapes de médiation d’alerte, vous devez travailler avec les options de support. Les options de support disposent d’une méthode fournissant un accès administrateur complet temporaire au système pour effectuer des opérations plus avancées. 

## <a name="identity-considerations"></a>Identité - Éléments à prendre en compte

### <a name="choose-identity-provider"></a>Choisir un fournisseur d’identité
Vous devez prendre en compte le fournisseur d’identité que vous souhaitez utiliser pour le déploiement de Azure Stack, que ce soit Azure AD ou AD FS. Vous ne pouvez pas changer les fournisseurs d’identité après le déploiement sans effectuer un redéploiement complet du système.

Votre choix de fournisseur d’identité n’a aucune incidence sur les machines virtuelles du client, le système d’identité et les comptes qu’ils utilisent, la possibilité de joindre un domaine Active Directory, etc. C’est différent.

Vous pouvez en savoir plus sur le choix d’un fournisseur d’identité dans l’[article relatif aux décisions de déploiement pour systèmes intégrés Azure Stack](.\azure-stack-deployment-decisions.md).

### <a name="ad-fs-and-graph-integration"></a>Intégration de AD FS et de Graph
Si vous choisissez de déployer Azure Stack en utilisant AD FS comme fournisseur d’identité, vous devez intégrer l’instance AD FS à Azure Stack avec une instance AD FS existante via une approbation de fédération. Ainsi, les identités dans une forêt Active Directory existante peuvent s’authentifier auprès des ressources dans Azure Stack.

Vous pouvez également intégrer le service Graph dans Azure Stack avec le Active Directory existant. Cela vous permet de gérer le contrôle d’accès en fonction du rôle (RBAC) dans Azure Stack. Lorsque l’accès à une ressource est délégué, le composant Graph recherche le compte d’utilisateur dans la forêt Active Directory existante à l’aide du protocole LDAP.

Le diagramme suivant montre le flux de trafic AD FS et Graph intégré.
![Diagramme montrant le flux de trafic AD FS et de Graph](media/azure-stack-deployment-planning/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modèle de licence

Vous devez choisir le modèle de licence que vous souhaitez utiliser. Pour un déploiement connecté, vous pouvez choisir une licence avec paiement à l’utilisation ou bien selon la capacité. Le paiement à l’utilisation requiert une connexion vers Azure pour rapporter l’utilisation, qui est ensuite facturée via Azure Commerce. Seule la licence selon la capacité est prise en charge si vous effectuez un déploiement déconnecté d’internet. Pour plus d’informations sur les modèles de licence, consultez [Empaquetage et tarification de Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Décisions d’attribution de noms

Vous devez réfléchir à la façon dont vous souhaitez planifier votre espace de noms Azure Stack, notamment le nom de la région et le nom de domaine externe. Le nom de domaine complet (FQDN) de votre déploiement Azure Stack pour les points de terminaison publics est la combinaison de ces deux noms, &lt;*région*&gt;&lt;*nom_de_domaine_externe_complet* &gt;, par exemple, *east.cloud.fabrikam.com*. Dans cet exemple, les portails de Azure Stack sont accessibles aux URL suivantes :

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

Le tableau suivant récapitule ces décisions d’attribution de noms de domaine.

| NOM | DESCRIPTION | 
| -------- | ------------- | 
|Nom de la région | Le nom de votre première région Azure Stack. Ce nom est utilisé comme partie du nom de domaine complet pour les adresses IP virtuelles publiques (VIP) gérées par Azure Stack. En règle générale, le nom de la région est un identificateur d’emplacement physique tel qu’un emplacement de centre de données. | 
| Nom de domaine externe | Le nom de la zone Domain Name System (DNS) pour les points de terminaison avec des adresses IP virtuelles externes. Utilisé dans le nom de domaine complet pour ces adresses IP virtuelles publiques. | 
| Nom de domaine privé (interne) | Le nom du domaine (et de la zone DNS interne) créé sur Azure Stack pour la gestion de l’infrastructure. 
| | |

## <a name="certificate-requirements"></a>Configuration requise des certificats

Pour le déploiement, vous devrez fournir les certificats SSL (Secure Sockets Layer) pour les points de terminaison publics. À un niveau élevé, les certificats présentent les exigences suivantes :

- Vous pouvez utiliser un certificat à caractères génériques unique, ou vous pouvez utiliser un jeu de certificats dédiés et utiliser des certificats à caractères génériques uniquement pour les points de terminaison tels que le stockage et le coffre de clés.
- Les certificats peuvent être émis par une autorité de certification (AC) approuvée publique ou une autorité de certification gérée par le client.

Pour plus d’informations sur les certificats pour infrastructure à clé publique requis pour déployer Azure Stack et comment les obtenir, consultez [Exigences de certificat pour infrastructure à clé publique Azure Stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Les informations de certificat pour infrastructure à clé publique fournies doivent être utilisées comme des conseils d’ordre général. Avant d’acquérir des certificats pour infrastructure à clé publique pour Azure Stack, travaillez avec votre fabricant partenaire de matériel OEM. Ce dernier fournit des conseils et des exigences plus détaillées en matière de certificat.



## <a name="time-synchronization"></a>Synchronisation temporelle
Vous devez choisir un serveur de temps spécifique, qui est utilisé pour synchroniser Azure Stack.  La synchronisation du temps est essentielle pour Azure Stack et ses rôles d’infrastructure, car elle est utilisée pour générer les tickets Kerberos utilisés pour authentifier les services internes avec eux.

Vous devez spécifier une adresse IP pour le serveur de synchronisation du temps. Bien que la plupart des composants de l’infrastructure soit en mesure de résoudre une URL, certains prennent uniquement en charge les adresses IP. Si vous utilisez l’option de déploiement déconnecté, vous devez spécifier un serveur de temps sur votre réseau d’entreprise accessible à partir du réseau d’infrastructure dans Azure Stack.


## <a name="network-connectivity"></a>Connectivité réseau
Cette section fournit des informations d’infrastructure réseau Azure Stack qui vous aideront à prendre des décisions importantes sur la meilleure intégration possible d’Azure Stack dans votre environnement réseau existant. 

> [!NOTE]
> Afin de résoudre les noms DNS externes de Azure Stack (par exemple, www.bing.com), vous devrez fournir des serveurs DNS que Azure Stack peut utiliser pour transférer les requêtes DNS pour lesquelles Azure Stack ne fait pas autorité. Pour plus d’informations sur les exigences DNS d’Azure Stack, consultez [Intégration au centre de données Azure Stack - DNS](azure-stack-integrate-dns.md).

### <a name="physical-network-design"></a>Conception du réseau physique
La solution Azure Stack requiert une infrastructure physique robuste et hautement disponible pour prendre en charge ses opérations et services. Vous trouverez ci-dessous un diagramme de notre conception recommandée :

![Conception de réseau Azure Stack recommandée](media/azure-stack-deployment-planning/recommended-design.png)


### <a name="logical-networks"></a>Réseaux logiques
Les réseaux logiques représentent une abstraction de l’infrastructure réseau physique sous-jacente. Ils sont utilisés pour organiser et simplifier les affectations réseau pour les hôtes, les machines virtuelles et les services. Lors de la création d’un réseau logique, des sites réseau sont créés pour définir les réseaux locaux virtuels (VLAN), les sous-réseaux IP et les paires sous-réseau IP/VLAN qui sont associés au réseau logique dans chaque emplacement physique.
L’infrastructure réseau pour Azure Stack utilise les réseaux logiques suivants qui sont configurés sur les commutateurs :

### <a name="network-infrastructure"></a>Infrastructure réseau
L’infrastructure réseau pour Azure Stack se compose de plusieurs réseaux logiques configurés sur les commutateurs. Le diagramme suivant illustre ces réseaux logiques, et la façon dont ils s’intègrent avec les commutateurs Top-of-Rack (TOR), Baseboard Management Controller (BMC) et de bordure (réseau clients).

![Diagramme de réseau logique et connexions de commutateurs](media/azure-stack-deployment-planning/NetworkDiagram.png)

#### <a name="bmc-network"></a>Réseau BMC
Ce réseau est conçu pour connecter tous les contrôleurs de gestion de carte de base (également appelés processeurs de service ; par exemple, iDRAC, iLO, iBMC, etc.) au réseau de gestion. Le cas échéant, l’hôte Hardware Lifecycle Host (HLH) se trouve sur ce réseau et peut fournir des logiciels spécifiques OEM pour la maintenance et ou la surveillance du matériel. 

#### <a name="private-network"></a>Réseau privé
Ce réseau /24 (adresse IP hôte 254) est privé pour la région Azure Stack (ne développez pas au-delà de appareils de commutation limite de la région Azure Stack) et est divisé en deux sous-réseaux :

- **Réseau de stockage**. Réseau /25 (adresse IP hôte 126) utilisé pour prendre en charge l’utilisation du trafic de stockage Spaces Direct et Server Message Block (SMB) et la migration dynamique de machine virtuelle. 
- **Réseau IP virtuel interne**. Réseau /25 dédié aux adresses IP virtuelles internes uniquement pour l’équilibrage de charge logicielle.

#### <a name="azure-stack-infrastructure-network"></a>Réseau d’infrastructure Azure Stack
Ce réseau /24 est dédié aux composants Azure Stack internes afin qu’ils puissent communiquer et échanger des données entre eux. Ce sous-réseau requiert des adresses IP routables, mais reste privée pour la solution à l’aide de listes de contrôle d’accès (ACL). Il n’est pas censé être acheminé au-delà des commutateurs limites à l’exception d’une très petite plage équivalant à un réseau /27 utilisé par certains de ces services lorsqu’ils requièrent un accès à des ressources externes et/ou à Internet. 

#### <a name="public-infrastructure-network"></a>Réseau d’infrastructure publique
Ce réseau /27 correspond à la très petite plage du sous-réseau d’infrastructure Azure Stack mentionné précédemment. Il ne requiert pas d’adresses IP publiques, mais requiert un accès à Internet via NAT ou un proxy transparent. Ce réseau sera alloué pour le système de console de récupération d’urgence (ERCS). La machine virtuelle ERCS requiert un accès à Internet pendant l’inscription auprès d’Azure et doit être routable vers votre réseau de gestion à des fins de dépannage.

#### <a name="public-vip-network"></a>Réseau d’adresse IP virtuelle publique
Le réseau d’adresse IP virtuelle publique est affecté au contrôleur réseau dans Azure Stack. Il ne s’agit pas d’un réseau logique sur le commutateur. Le SLB utilise le pool d’adresses et assigne des réseaux /32 pour les charges de travail clientes. Dans la table de routage du commutateur, ces adresses IP 32 sont publiées en tant qu’itinéraire disponible via BGP. Ce réseau contient les adresses IP accessibles en externe ou publiques. L’infrastructure Azure Stack utilise au moins 8 adresses de ce réseau d’adresse IP virtuelle publique tandis que les autres sont utilisées par les machines virtuelles clientes. La taille réseau sur ce sous-réseau peut aller d’un minimum de /26 (64 hôtes) à un maximum de /22 (1022 hôtes). Nous vous recommandons de prévoir pour un réseau /24.

#### <a name="switch-infrastructure-network"></a>Réseau d’infrastructure du commutateur
Ce réseau /26 est le sous-réseau contenant des sous-réseaux IP point à point routables /30 (2 adresses IP d’hôte) et les bouclages dédiés aux sous-réseaux /32 pour la gestion du commutateur intrabande et l’ID de routeur BGP. Cette plage d’adresses IP doit être routable en externe de la solution Azure Stack pour votre centre de données. Il peut s’agit d’adresses IP privées ou publiques.

#### <a name="switch-management-network"></a>Réseau de gestion du commutateur
Ce réseau /29 (6 adresses IP d’hôte) est dédié à la connexion des ports de gestion des commutateurs. Il autorise un accès hors bande pour le déploiement, la gestion et la résolution des problèmes. Il est calculé à partir du réseau d’infrastructure du commutateur mentionné ci-dessus.

### <a name="transparent-proxy"></a>PROXY TRANSPARENT
La solution Azure Stack ne prend pas en charge les proxies web normaux. Si le centre de données exige que l’ensemble du trafic utilise un proxy, vous devez configurer un proxy transparent pour traiter l’ensemble du trafic du rack afin de le gérer conformément à la stratégie, en séparant le trafic entre les zones de votre réseau. Un proxy transparent (également appelé proxy d’interception, en ligne ou forcé) intercepte une communication normale sur la couche réseau sans nécessiter une configuration spéciale du client. Les clients ne doivent pas nécessairement être informés de l’existence du proxy.

### <a name="publish-azure-stack-services"></a>Publier des services de Azure Stack

Vous devrez rendre les services de Azure Stack disponibles aux utilisateurs en dehors de Azure Stack. Azure Stack définit différents points de terminaison pour ses rôles d’infrastructure. Ces points de terminaison se voient assigner des adresses IP virtuelles du pool d’adresses IP publiques. Une entrée DNS est créée pour chaque point de terminaison dans la zone DNS externe spécifiée au moment du déploiement. Par exemple, le portail utilisateur se voit attribué l’entrée d’hôte DNS portal.*&lt;region>.&lt;fqdn>*.

#### <a name="ports-and-urls"></a>Ports et URL

Pour rendre les services de Azure Stack (tels que les portails, Azure Resource Manager, DNS, etc.) disponibles pour les réseaux externes, vous devez autoriser le trafic entrant vers ces points de terminaison pour les URL, les ports et les protocoles spécifiques.
 
Dans un déploiement où un proxy transparent transfère les données vers un serveur proxy traditionnel, vous devez autoriser des URL et des ports spécifiques pour les communications sortantes. Cela comprend les ports et les URL pour l’identité, la syndication de Place de marché, les correctifs et les mises à jour, l’inscription et les données d’utilisation.

Pour plus d'informations, consultez les pages suivantes :
- [Ports et protocoles entrants](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [Ports et URL sortants](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Connecter Azure Stack à Azure

Pour les scénarios de cloud hybrides, vous devrez planifier la façon dont vous souhaitez connecter Azure Stack à Azure. Deux méthodes vous permettent de connecter des réseaux virtuels Azure Stack à des réseaux virtuels Azure : 
- **Site à site**. Une connexion de réseau privé virtuel (VPN) sur IPsec (IKE v1 et IKE v2). Ce type de connexion requiert un périphérique VPN ou le service de routage et d’accès à distance (RRAS). Pour plus d’informations sur les passerelles VPN dans Azure, consultez l’article [À propos de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). La communication via ce tunnel est chiffrée et sécurisée. Toutefois, la bande passante est limitée par le débit maximal du tunnel (100-200 Mbps).
- **NAT de trafic sortant**. Par défaut, toutes les machines virtuelles dans Azure Stack ont une connectivité aux réseaux externes via le NAT de trafic sortant. Chaque réseau virtuel créé dans Azure Stack se voit attribuer une adresse IP publique. Si la machine virtuelle se voit directement attribuée une adresse IP publique, ou se trouve derrière un équilibreur de charge avec une adresse IP publique, il aura un accès sortant via le NAT de trafic sortant à l’aide de l’adresse IP virtuelle du réseau virtuel. Cela fonctionne uniquement pour une communication initiée par la machine virtuelle et destinée à des réseaux externes (internet ou intranet). Il ne peut pas être utilisé pour communiquer avec la machine virtuelle à partir de l’extérieur.

#### <a name="hybrid-connectivity-options"></a>Options de connectivité hybride

Pour une connectivité hybride, il est important de savoir quel type de déploiement vous souhaitez proposer et où il sera déployé. Vous devrez prendre en compte le fait de devoir isoler le trafic par client, et le fait d’effectuer un déploiement intranet ou internet.

- **Azure Stack à client unique**. Un déploiement de Azure Stack qui semble, au moins du point de vue de la mise en réseau, être un seul client. Il peut y avoir beaucoup d’abonnements clients, mais comme tout service intranet, tout le trafic transite sur les mêmes réseaux. Le trafic provenant d’un abonnement transite sur la même connexion réseau comme un autre abonnement et ne doit être pas être isolé via un tunnel chiffré.

- **Azure Stack mutualisé**. Un déploiement de Azure Stack où le trafic de l’abonnement de chaque client lié pour les réseaux externes à Azure Stack doit être isolé du trafic des autres clients.
 
- **Déploiement intranet**. Un déploiement de Azure Stack qui se trouve sur un intranet d’entreprise, en général sur l’espace d’adresse IP privée et derrière un ou plusieurs pare-feu. Les adresses IP publiques ne sont pas réellement publiques, car elles ne peuvent pas être routées directement via l’internet public.

- **Déploiement internet**. Un déploiement de Azure Stack qui est connecté à l’internet public et utilise des adresses IP publiques routables sur internet pour la plage d’adresses IP virtuelles publiques. Le déploiement peut toujours se placer derrière un pare-feu, mais la plage d’adresses IP virtuelles publiques est directement accessible depuis l’internet public et Azure.
 
Le tableau suivant résume les scénarios de connectivité hybride, avec les avantages, les inconvénients et les cas d’usage.

| Scénario | Méthode de connectivité | Avantages | Inconvénients | Bien pour |
| -- | -- | --| -- | --|
| Azure Stack avec un seul client, déploiement en intranet | NAT de trafic sortant | Meilleure bande passante pour des transferts plus rapides. Simple à implémenter ; aucune passerelle requise. | Trafic non chiffré ; aucun chiffrement ou isolation au-delà de TOR. | Déploiements d’entreprise où tous les clients sont fiables.<br><br>Entreprises qui ont un circuit Azure ExpressRoute vers Azure. |
| Azure Stack mutualisé, déploiement en intranet | VPN de site à site | Le trafic du réseau virtuel du client vers la destination est sécurisé. | La bande passante est limitée par le tunnel VPN de site à site.<br><br>Requiert une passerelle dans le réseau virtuel et un périphérique VPN sur le réseau de destination. | Les déploiements d’entreprise où certains trafics de client doivent être sécurisés par rapport aux autres clients. |
| Azure Stack avec un seul client, déploiement par internet | NAT de trafic sortant | Meilleure bande passante pour des transferts plus rapides. | Trafic non chiffré ; aucun chiffrement ou isolation au-delà de TOR. | Hébergement des scénarios où le client obtient son propre déploiement de Azure Stack et un circuit dédié à l’environnement Azure Stack. Par exemple, ExpressRoute et MPLS (Multiprotocol Label Switching).
| Azure Stack mutualisé, déploiement par internet | VPN de site à site | Le trafic du réseau virtuel du client vers la destination est sécurisé. | La bande passante est limitée par le tunnel VPN de site à site.<br><br>Requiert une passerelle dans le réseau virtuel et un périphérique VPN sur le réseau de destination. | Hébergement des scénarios où le fournisseur souhaite offrir un cloud mutualisé, où les clients ne se font pas confiance et où le trafic doit être chiffré.
|  |  |  |  |  |

#### <a name="using-expressroute"></a>Utilisation de ExpressRoute

Vous pouvez connecter Azure Stack à Azure via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pour un intranet à un client et des scénarios mutualisés. Vous aurez besoin d’un circuit ExpressRoute approvisionné via un [fournisseur de connectivité](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Le diagramme suivant montre ExpressRoute pour un scénario à un client (où « Connexion du client » est le circuit ExpressRoute).

![Diagramme montrant un scénario ExpressRoute à un client](media/azure-stack-deployment-planning/ExpressRouteSingleTenant.PNG)

Le diagramme suivant montre ExpressRoute pour un scénario mutualisé.

![Diagramme montrant un scénario ExpressRoute mutualisé](media/azure-stack-deployment-planning/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Surveillance externe
Pour obtenir une vue unique de toutes les alertes à partir de vos appareils et du déploiement de Azure Stack, et d’intégrer des alertes dans les flux de travail existants de gestion du service informatique pour la création de tickets, vous pouvez intégrer Azure Stack aux solutions de surveillance du centre de données externe.

Inclus dans la solution Azure Stack, l’hôte de cycle de vie du matériel est un ordinateur extérieur à Azure Stack qui exécute les outils d’administration fournie par le fabricant OEM pour le matériel. Vous pouvez utiliser ces outils ou d’autres solutions s’intégrant directement avec les solutions de surveillance existantes dans votre centre de données.

Le tableau suivant récapitule la liste des options actuellement disponibles.

| Domaine | Solution de surveillance externe |
| -- | -- |
| Logiciel Azure Stack | - [Pack d’administration Azure Stack pour Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Plug-in Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>- Appels d’API basés sur REST | 
| Serveurs physiques (BMC via IPMI) | Pack d’administration de fournisseur Operations Manager<br>- Solution fournie par le fabricant de matériel OEM<br>- Plu-gins Nagios du fabricant de matériel | Solution de surveillance prise en charge par le partenaire OEM (inclus) | 
| Périphériques réseau (SNMP) | - Découverte des périphériques réseau Operations Manager<br>- Solution fournie par le fabricant de matériel OEM<br>- Plug-in de commutateur Nagios |
| Surveillance de l’intégrité de l’abonnement client | - [Pack d’administration System Center pour Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Notez les exigences suivantes :
- La solution que vous utilisez doit être sans agent. Vous ne pouvez pas installer d’agents tiers à l’intérieur des composants de Azure Stack. 
- Si vous souhaitez utiliser System Center Operations Manager, cela requiert Operations Manager 2012 R2 ou Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Sauvegarde et récupération d'urgence

La planification de la sauvegarde et de la récupération d’urgence implique une planification pour l’infrastructure Azure Stack sous-jacente hébergeant les machines virtuelles IaaS et les services PaaS, et pour les applications et les données du client. Vous devez faire ces planifications séparément.

### <a name="protect-infrastructure-components"></a>Protéger les composants d’infrastructure

Azure Stack sauvegarde les composants d’infrastructure sur un partage que vous spécifiez.

- Vous aurez besoin d’un partage de fichiers SMB externe sur un serveur de fichiers Windows existant ou un périphérique tiers.
- Vous devriez utiliser ce même partage pour la sauvegarde des commutateurs réseau et l’hôte de cycle de vie du matériel. Votre fabricant de matériel OEM aidera à fournir des conseils pour la sauvegarde et la restauration de ces composants étant donné qu’ils ne font pas partie de Azure Stack. Vous êtes responsable d’exécuter les flux de travail de sauvegarde basés sur la recommandation du fabricant OEM.

En cas de perte catastrophique de données, vous pouvez utiliser la sauvegarde de l’infrastructure pour réimplanter les données de déploiement telles que les entrées et les identificateurs de déploiement, les comptes de service, le certificat racine d’autorité de certification, les ressources fédérés (dans les déploiements déconnectés), les plans, les offres, les abonnements, les quotas, les attributions de rôle et de stratégie RBAC et les secrets de coffre de clés.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Protéger les applications du client sur des machines virtuelles IaaS

Azure Stack ne sauvegarde pas les applications et les données du client. Vous devez planifier la sauvegarde et la protection de récupération d’urgence vers une cible externe au Azure Stack. La protection du client est une activité qu’il conduit lui-même. Pour les machines virtuelles IaaS, les clients peuvent utiliser les technologies intégrées pour protéger des dossiers de fichiers, des données d’application et l’état du système. Toutefois, en tant qu’entreprise ou fournisseur de service, vous voudrez peut-être offrir une solution de sauvegarde et de restauration dans le même centre de données ou à l’extérieur, dans un cloud.

Pour sauvegarder des machines virtuelles IaaS Windows ou Linux, vous devez utiliser des produits de sauvegarde avec un accès au système d’exploitation invité pour protéger les fichiers, les dossiers, l’état du système d’exploitation et les données d’application. Vous pouvez utiliser Azure Backup, System Center Data Protection Center Manager, ou les produits tiers pris en charge.

Pour répliquer des données vers un emplacement secondaire et orchestrer le basculement d’application si un incident se produit, vous pouvez utiliser Azure Site Recovery ou des produits tiers pris en charge. (Lors de la mise en production initiale des systèmes intégrés, Azure Site Recovery ne prendra pas en charge la restauration automatique. Toutefois, vous pouvez effectuer une restauration automatique via un processus manuel.) En outre, les applications qui prennent en charge la réplication native (par exemple, Microsoft SQL Server) peuvent répliquer des données vers un autre emplacement dans lequel l’application est en cours d’exécution.

> [!IMPORTANT]
> Lors de la mise en production initiale des systèmes intégrés, nous prendrons en charge les technologies de protection qui fonctionnent au niveau invité d’une machine virtuelle IaaS. Vous ne pouvez pas installer des agents sur les serveurs d’infrastructure sous-jacente.

## <a name="next-steps"></a>étapes suivantes

- Pour plus d’informations sur les cas d’usage, l’achat, les partenaires et les fabricants de matériel OEM, consultez la page produit [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Pour plus d’informations sur la feuille de route et la disponibilité géographique des systèmes intégrés Azure Stack, consultez le livre blanc : [Azure Stack : une extension de Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
