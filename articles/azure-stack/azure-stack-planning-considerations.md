---
title: "Considérations sur la planification pour les systèmes intégrés Azure Stack | Microsoft Docs"
description: "Découvrez ce que vous pouvez faire pour planifier maintenant et préparer Azure Stack à plusieurs nœuds."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: b39b3b8f73f9deef48173c712d7966eb13c6ecc3
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Considérations sur la planification pour les systèmes intégrés Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Si vous êtes intéressé par un système intégré Azure Stack, vous voudrez comprendre certaines considérations principales sur la planification traitant du déploiement et la façon dont le système s’adapte à votre centre de données. Cette rubrique fournit une vue d’ensemble de ces considérations.

Si vous décidez d’acheter un système intégré, votre fournisseur de matériel OEM vous guide durant une grande partie du processus de planification pour plus en détails. Ils effectueront également le déploiement réel.

## <a name="management-considerations"></a>Considérations relatives à la gestion

Azure Stack est un système fermé, dont l’infrastructure est verrouillée à la fois du point de vue des autorisations et du réseau. Les listes de contrôle d’accès réseau (ACL) sont utilisées pour bloquer tout trafic entrant non autorisé et toute communication inutile entre les composants de l’infrastructure. Cela rend l’accès au système difficile pour les utilisateurs non autorisés.

Pour les opérations et la gestion quotidiennes, il n’existe aucun accès administrateur non restreint à l’infrastructure. Les opérateurs Azure Stack doivent gérer le système via le portail administrateur ou via Azure Resource Manager (via PowerShell ou l’API REST). Il n’existe aucun accès au système par d’autres outils de gestion tels que le gestionnaire Hyper-V ou le gestionnaire du cluster de basculement. Pour protéger le système, les logiciels tiers (par exemple, les agents) ne peuvent pas être installés dans les composants de l’infrastructure Azure Stack. L’interopérabilité avec les logiciels de gestion et de sécurité externes est effectuée via PowerShell ou l’API REST.

Lorsqu’un niveau plus élevé d’accès est nécessaire pour résoudre des problèmes n’ayant pas été résolus à l’aide des étapes de médiation d’alerte, vous devez travailler avec les options de support. Les options de support disposent d’une méthode fournissant un accès administrateur complet temporaire au système pour effectuer des opérations plus avancées. 

## <a name="deploy-connected-or-disconnected"></a>Déploiement connecté ou déconnecté
 
Vous pouvez choisir de déployer Azure Stack en le connectant ou non à internet (et à Azure). Pour tirer le meilleur parti de Azure Stack, y compris les scénarios hybrides entre Azure Stack et Azure, vous souhaiterez le connecter à Azure. Le tableau suivant permet de résumer les principales différences entre les modes de déploiement.

| Domaine | Mode connecté | Mode déconnecté |
| -------- | ------------- | ----------|
| Fournisseur d’identité | Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS) | AD FS uniquement |
| Syndication de Place de marché | Télécharger les éléments directement à partir de Place de marché Azure vers le marketplace dans Azure Stack | Nécessite une gestion manuelle de marketplace dans Azure Stack |
| Modèle de licence | Paiement à l’utilisation ou selon la capacité | Selon la capacité uniquement|
| Correctifs et mises à jour  | Télécharger les packages de mise à jour directement sur Azure Stack | Nécessite un support amovible et un appareil connecté distinct |
| | | |

Vous ne pouvez pas modifier le mode de déploiement plus tard sans effectuer un redéploiement complet du système.

## <a name="identity-considerations"></a>Identité - Éléments à prendre en compte

### <a name="choose-identity-provider"></a>Choisir un fournisseur d’identité

Vous devez prendre en compte le fournisseur d’identité que vous souhaitez utiliser pour le déploiement de Azure Stack, que ce soit Azure AD ou AD FS. Vous ne pouvez pas changer les fournisseurs d’identité après le déploiement sans effectuer un redéploiement complet du système.

Votre choix de fournisseur d’identité n’a aucune incidence sur les machines virtuelles du client, le système d’identité et les comptes qu’ils utilisent, la possibilité de joindre un domaine Active Directory, etc. C’est différent.

**Raisons d’envisager l’utilisation de Azure AD**

- Vous disposez déjà d’investissements dans Azure AD (par exemple, Azure ou Office 365).
- Vous souhaitez utiliser la même identité dans des clouds Azure et Azure Stack.
- Vous souhaitez prendre en charge les scénarios d’architecture mutualisée, où vous pouvez héberger des organisations différentes sur la même instance de Azure Stack.
- Vous souhaitez utiliser la gestion de répertoire basée sur REST via Azure AD Graph pour approvisionner les utilisateurs, groupes, etc. via des API.

> [!NOTE]
> Un déploiement de Azure Stack ne peut pas se connecter à une instance Azure AD et une instance AD FS existante en même temps. Si vous déployez avec Azure AD, et que vous souhaitez utiliser une instance AD FS existante, vous pouvez fédérer votre instance AD FS locale avec Azure AD.

**Raisons d’envisager l’utilisation de AD FS**

- La connectivité à internet est partielle ou inexistante.
- Il existe des exigences de souveraineté/réglementaires.
- Vous souhaitez utiliser votre propre système d’identité (par exemple, votre Active Directory d’entreprise) pour les comptes d’utilisateurs et d’opérateurs. Pour ce faire, vous pouvez fédérer avec une instance AD FS existante (sur Windows Server 2012, 2012 R2 ou 2016) sauvegardée par Active Directory.
- Vous ne disposez d’aucun investissements Azure, et vous ne souhaitez pas utiliser Azure AD.

> [!NOTE]
> Vous pouvez fédérer Azure Stack uniquement avec une autre instance AD FS sauvegardée par Active Directory. Les autres fournisseurs d’identité ne sont pas pris en charge. Si vous avez d’autres fournisseurs d’identité que vous souhaitez utiliser avec Azure Stack, envisagez l’utilisation du déploiement basé sur Azure AD.

### <a name="ad-fs-and-graph-integration"></a>Intégration de AD FS et de Graph

Si vous choisissez de déployer Azure Stack en utilisant AD FS comme fournisseur d’identité, vous devez intégrer l’instance AD FS à Azure Stack avec une instance AD FS existante via une approbation de fédération. Ainsi, les identités dans une forêt Active Directory existante peuvent s’authentifier auprès des ressources dans Azure Stack.

Vous pouvez également intégrer le service Graph dans Azure Stack avec le Active Directory existant. Cela vous permet de gérer le contrôle d’accès en fonction du rôle (RBAC) dans Azure Stack. Lorsque l’accès à une ressource est délégué, le composant Graph recherche le compte d’utilisateur dans la forêt Active Directory existante à l’aide du protocole LDAP.

Le diagramme suivant montre le flux de trafic AD FS et Graph intégré.
![Diagramme montrant le flux de trafic AD FS et de Graph](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modèle de licence

Vous devez choisir le modèle de licence que vous souhaitez utiliser. Pour un déploiement connecté, vous pouvez choisir une licence avec paiement à l’utilisation ou bien selon la capacité. Le paiement à l’utilisation requiert une connexion vers Azure pour rapporter l’utilisation, qui est ensuite facturée via Azure Commerce. Seule la licence selon la capacité est prise en charge si vous effectuez un déploiement déconnecté d’internet. Pour plus d’informations sur les modèles de licence, consultez [Empaquetage et tarification de Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Décisions d’attribution de noms

Vous devez réfléchir à la façon dont vous souhaitez planifier votre espace de noms Azure Stack, notamment le nom de la région et le nom de domaine externe. Le nom de domaine complet (FQDN) de votre déploiement Azure Stack pour les points de terminaison publics est la combinaison de ces deux noms, &lt;*région*&gt;&lt;*nom_de_domaine_externe_complet* &gt;, par exemple, *east.cloud.fabrikam.com*. Dans cet exemple, les portails de Azure Stack sont accessibles aux URL suivantes :

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

Le tableau suivant récapitule ces décisions d’attribution de noms de domaine.

| Nom | Description | 
| -------- | ------------- | 
|Nom de la région | Le nom de votre première région Azure Stack. Ce nom est utilisé comme partie du nom de domaine complet pour les adresses IP virtuelles publiques (VIP) gérées par Azure Stack. En règle générale, le nom de la région est un identificateur d’emplacement physique tel qu’un emplacement de centre de données. | 
| Nom de domaine externe | Le nom de la zone Domain Name System (DNS) pour les points de terminaison avec des adresses IP virtuelles externes. Utilisé dans le nom de domaine complet pour ces adresses IP virtuelles publiques. | 
| Nom de domaine privé (interne) | Le nom du domaine (et de la zone DNS interne) créé sur Azure Stack pour la gestion de l’infrastructure. 
| | |

## <a name="certificate-requirements"></a>Configuration requise des certificats

Pour le déploiement, vous devrez fournir les certificats SSL (Secure Sockets Layer) pour les points de terminaison publics. À un niveau élevé, les certificats présentent les exigences suivantes :

> [!IMPORTANT]
> Les informations du certificat de cet article sont fournies uniquement à titre indicatif. Avant d’acquérir des certificats pour Azure Stack, travaillez avec votre fabricant partenaire de matériel OEM. Ce dernier fournit des conseils et des exigences plus détaillées en matière de certificat.

- Vous pouvez utiliser un certificat à caractères génériques unique, ou vous pouvez utiliser un jeu de certificats dédiés et utiliser des certificats à caractères génériques uniquement pour les points de terminaison tels que le stockage et le coffre de clés.
- Les certificats doivent être émis par une autorité de certification approuvée publique (CA) ou une autorité de certification gérée par le client.
 
Le tableau suivant montre les services et le nombre de points de terminaison publics qui nécessitent des certificats pour le déploiement initial de Azure Stack. 

| Utilisation | Point de terminaison 
| -------- | ------------- | 
| Azure Resource Manager (administrateur) | adminmanagment.[region].[external_domain] |
| Azure Resource Manager (utilisateur) | management.[region].[external_domain] |
| Portail (administrateur) | adminportal.[region].[external_domain] |
| Portail (utilisateur) | portal. [region].[external_domain] |
| Key Vault (utilisateur) | &#42;.vault.[region].[external_domain] |
| Key Vault (administrateur) | &#42;.adminvault.[region].[external_domain] |
| Storage | &#42;.blob.[region].[external_domain]<br>&#42;.table.[region].[external_domain]<br>&#42;.queue.[region].[external_domain]  |
| Graph** | graph.[region].[external_domain] |
| AD FS** | adfs.[region].[external_domain] |
| | |

** Les certificats pour les points de terminaison Graph et AD FS sont nécessaires uniquement pour les déploiements AD FS.

Si vous souhaitez utiliser un certificat à caractères génériques unique, vous avez besoin de six autres noms de l’objet (SAN) pour le déploiement initial de Azure Stack. Ces autres noms de l’objet sont : 

- &#42;.[region].[external_domain]
- &#42;.vault.[region].[external_domain]
- &#42;.adminvault.[region].[external_domain]
- &#42;.blob.[region].[external_domain]
- &#42;.table.[region].[external_domain] 
- &#42;.queue.[region].[external_domain]

## <a name="time-synchronization"></a>Synchronisation temporelle

Vous devez synchroniser le serveur de temps de Azure Stack avec un serveur de temps externe qui peut être résolu via l’adresse IP. Un serveur de temps sur le réseau d’entreprise est requis pour un déploiement déconnecté.

## <a name="network-connectivity"></a>Connectivité réseau

### <a name="dns-integration"></a>Intégration DNS

Afin de résoudre les noms DNS externes de Azure Stack (par exemple, www.bing.com), vous devrez fournir des serveurs DNS que Azure Stack peut utiliser pour transférer les requêtes DNS pour lesquelles Azure Stack ne fait pas autorité. En tant qu’entrées du déploiement, vous devez fournir au moins deux serveurs à utiliser comme redirecteurs DNS pour la tolérance de panne.

Afin de résoudre les noms DNS des points de terminaison Azure Stack en dehors de Azure Stack (par exemple, à partir de la forêt d’entreprise), vous devez intégrer les serveurs DNS qui hébergent la zone DNS externe pour Azure Stack aux serveurs DNS qui hébergent la zone parent que vous souhaitez utiliser. Cela requiert une résolution de noms DNS entre Azure Stack et des zones DNS existantes dans le centre de données. Pour ce faire, vous allez utiliser des méthodes telles que la délégation de zone ou le transfert conditionnel. Nous vous recommandons cette approche conditionnelle si vous contrôlez directement les serveurs DNS qui hébergent la zone parente pour votre espace de noms DNS externe Azure Stack. (Si votre zone DNS Azure Stack externe apparaît comme un domaine enfant de votre nom de domaine d’entreprise (par exemple, azurestack.contoso.com et contoso.com), vous devez utiliser la délégation de zone à la place.

### <a name="network-infrastructure"></a>Infrastructure réseau

L’infrastructure réseau pour Azure Stack se compose de plusieurs réseaux logiques configurés sur les commutateurs. Le diagramme suivant illustre ces réseaux logiques, et la façon dont ils s’intègrent avec les commutateurs Top-of-Rack (TOR), Baseboard Management Controller (BMC) et de bordure (réseau clients).

![Diagramme de réseau logique et connexions de commutateurs](media/azure-stack-planning-considerations/NetworkDiagram.png)

Le tableau suivant montre les réseaux logiques et les plages de sous-réseau IPv4 associées que vous devez planifier.

| Réseau logique | Description | Taille | 
| -------- | ------------- | ------------ | 
| Adresse IP virtuelle publique | Les adresses IP publiques pour un petit ensemble de services Azure Stack, avec le reste utilisé par les machines virtuelles du client. L’infrastructure de Azure Stack utilise 32 adresses à partir de ce réseau. Si vous envisagez d’utiliser App Service et les fournisseurs de ressources SQL, 7 adresses supplémentaires sont utilisées. | / 26 (62 hôtes) - /22 (1022 hôtes)<br><br>Recommandé = /24 (254 hôtes) | 
| Infrastructure du commutateur | Adresses IP de point à point pour le routage, les interfaces de gestion de commutateur dédiées et les adresses de bouclage attribuées au commutateur. | /26 | 
| Infrastructure | Utilisé pour les composants internes de Azure Stack pour communiquer. | /24 |
| Privé | Utilisé pour le réseau de stockage et les adresses IP virtuelles privées. | /24 | 
| BMC | Utilisé pour communiquer avec les contrôleurs BMC sur les hôtes physiques. | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>Liaison montante vers des commutateurs de bordure

Vous aurez besoin d’une liaison montante configurée pour les commutateurs de bordure dans votre centre de données. Vous pouvez router ce trafic de couche 3 à partir des commutateurs top-of-rack (TOR) faisant parties d’un système intégré Azure Stack à l’aide de l’une des méthodes suivantes :

- Protocole de passerelle frontière (BGP) 
- routage statique

Nous vous recommandons BGP, car il permet la mise à jour automatique des itinéraires publiés par le multiplexeur d’équilibrage de charge de logiciel (SLB MUX) vers des réseaux externes. Ceci est important si vous ajoutez des plages d’adresses IP publiques après le déploiement. Si vous effectuez une homologation BGP à partir des commutateurs TOR vers le commutateur d’agrégation auquel les commutateurs TOR sont connectés, les plages d’adresses IP publiques que vous ajoutez après le déploiement sont automatiquement publiées sur le commutateur d’agrégation sans intervention manuelle. Si vous utilisez le routage statique, vous devez mettre à jour manuellement les itinéraires pour les nouvelles plages chaque fois que vous ajoutez un bloc d’adresses IP publiques.

### <a name="proxy-server"></a>Serveur proxy

Azure Stack prend en charge uniquement les serveurs proxy transparents. Un proxy transparent intercepte les demandes au niveau de la couche réseau sans demander une configuration client spéciale.

### <a name="publish-azure-stack-services"></a>Publier des services de Azure Stack

Vous devrez rendre les services de Azure Stack disponibles aux utilisateurs en dehors de Azure Stack. Azure Stack définit différents points de terminaison pour ses rôles d’infrastructure. Ces points de terminaison se voient assigner des adresses IP virtuelles du pool d’adresses IP publiques. Une entrée DNS est créée pour chaque point de terminaison dans la zone DNS externe spécifiée au moment du déploiement. Par exemple, le portail utilisateur se voit attribué l’entrée d’hôte DNS de « portal. <*region*>. <*external_FQDN*>. » 

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

![Diagramme montrant un scénario ExpressRoute à un client](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

Le diagramme suivant montre ExpressRoute pour un scénario mutualisé.

![Diagramme montrant un scénario ExpressRoute mutualisé](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

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

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les cas d’usage, l’achat, les partenaires et les fabricants de matériel OEM, consultez la page produit [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Pour plus d’informations sur la feuille de route et la disponibilité géographique des systèmes intégrés Azure Stack, consultez le livre blanc : [Azure Stack : une extension de Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
