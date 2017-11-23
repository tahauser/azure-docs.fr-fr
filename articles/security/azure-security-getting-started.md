---
title: "Prise en main de la sécurité de Microsoft Azure | Microsoft Docs"
description: "Cet article présente les fonctionnalités de sécurité de Microsoft Azure, ainsi que des considérations générales pour les organisations qui effectuent la migration de leurs ressources vers un fournisseur de cloud."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: e1ee07f2284df925b8bbd9050de7ae40fa66bf65
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="getting-started-with-microsoft-azure-security"></a>Prise en main de la sécurité de Microsoft Azure
Lorsque vous générez ou faites migrer des ressources informatiques vers un fournisseur de cloud, vous comptez sur les capacités de cette organisation à protéger vos applications et données avec les services et les contrôles qu’elle vous fournit pour gérer la sécurité de vos ressources cloud.

L’infrastructure d’Azure est conçue de l’installation vers les applications pour héberger des millions de clients simultanément, et constitue une base fiable permettant de répondre aux besoins des entreprises en matière de sécurité. En outre, Azure vous offre un large éventail d’options de sécurité configurables, ainsi que la possibilité de contrôler ces options pour vous permettre de personnaliser la sécurité et de répondre ainsi aux exigences uniques de vos déploiements.

Dans cet article de présentation sur la sécurité Azure, nous allons examiner les points suivants :

* Services et fonctionnalités Azure que vous pouvez utiliser pour sécuriser vos services et données dans Azure
* Procédure de sécurisation Microsoft de l’infrastructure Azure pour contribuer à protéger vos données et applications

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès
Il est essentiel de contrôler l’accès à l’infrastructure informatique, aux données et aux applications. Microsoft Azure fournit ces fonctionnalités par le biais de services tels qu’Azure Active Directory (Azure AD), Stockage Azure et la prise en charge de nombreuses normes et API.

[Azure AD](../active-directory/active-directory-whatis.md) est un référentiel d’identités et un moteur qui fournit l’authentification, l’autorisation et le contrôle d’accès pour les utilisateurs, groupes et objets d’une organisation. En outre, Azure AD offre aux développeurs une méthode efficace pour intégrer la gestion des identités à leurs applications. La prise en charge de protocoles standard comme [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx) et [OpenID Connect](http://openid.net/connect/) permet l’identification sur différentes plateformes telles que .NET, Java, Node.js et PHP.

L’API Graph REST permet aux développeurs d’accéder en lecture et en écriture au répertoire depuis toute plateforme. Grâce à la prise en charge [d’OAuth 2.0](http://oauth.net/2/), les développeurs peuvent concevoir des applications web et mobiles qui s’intègrent aux API web Microsoft et tierces, et créer leurs propres API web sécurisées. Des bibliothèques clientes open source sont disponibles pour .Net, le Windows Store, iOS et Android, et des bibliothèques supplémentaires sont en cours de développement.

### <a name="how-azure-enables-identity-and-access-management"></a>Comment Azure permet la gestion de l’identité et de l’accès
Azure AD peut servir de répertoire cloud autonome pour votre organisation, ou de solution intégrée avec votre Active Directory local existant. Certaines fonctionnalités d’intégration incluent la synchronisation de répertoire et l’authentification unique (SSO). Celles-ci étendent la portée de vos identités locales existantes dans le cloud et améliorent l’expérience administrateur et utilisateur.

Parmi les autres fonctionnalités pour la gestion de l’identité et de l’accès :

* Azure AD permet [l’authentification unique](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) sur les applications SaaS, quel que soit l’endroit où elles sont hébergées. Certaines applications sont fédérées avec Azure AD, d’autres utilisent le mot de passe de l’authentification unique. Les applications fédérées peuvent également prendre en charge l’approvisionnement d’utilisateurs et la mise au coffre des mots de passe.
* L’accès aux données dans [Azure Storage](https://azure.microsoft.com/services/storage/) est contrôlé via l’authentification. Chaque compte de stockage possède une clé primaire ([clé de compte de stockage](https://msdn.microsoft.com/library/azure/ee460785.aspx) ou SAK) et une clé secrète secondaire (signature d’accès partagé ou SAP).
* Azure AD fournit l’identité en tant que service par le biais de la fédération en utilisant les [services de fédération Active Directory (AD FS)](../active-directory/fundamentals-identity.md), la synchronisation et la réplication avec les annuaires locaux.
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) est le service d’authentification multifacteur qui demande aux utilisateurs de vérifier les connexions à l’aide d’une application mobile, d’un appel téléphonique ou d’un message texte. Ce service est utilisable avec Azure AD afin de sécuriser les ressources locales avec le serveur Azure Multi-Factor Authentication, ainsi qu’avec des applications et annuaires personnalisés à l’aide du Kit de développement logiciel (SDK).
* Les [services de domaine Azure AD](https://azure.microsoft.com/services/active-directory-ds/) vous permettent de joindre des machines virtuelles Azure à un domaine sans déployer de contrôleurs de domaine. Vous pouvez vous connecter à ces machines virtuelles avec vos informations d’identification Active Directory d’entreprise, et administrer les machines virtuelles jointes au domaine en utilisant une stratégie de groupe pour appliquer des lignes de base de sécurité sur toutes vos machines virtuelles Azure.
* Pour les applications accessibles aux consommateurs, [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) fournit un service de gestion des identités global et hautement disponible, qui s’adapte à des centaines de millions d’identités. Le service peut être intégré sur l’ensemble des plateformes web et mobiles. Vos consommateurs peuvent se connecter à toutes vos applications par le biais d’expériences personnalisables en utilisant leurs comptes de réseaux sociaux existants ou en créant des comptes avec de nouvelles informations d’identification.

## <a name="data-access-control-and-encryption"></a>Contrôle d’accès aux données et chiffrement
Microsoft utilise les principes de séparation des tâches et du [moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) pour l’ensemble des opérations Azure. L’accès aux données par le personnel de support Azure requiert une autorisation explicite de votre part et est accordé sur la base du « juste-à-temps ». Cette autorisation est consignée et auditée, puis révoquée après l’achèvement de la mission.

En outre, Azure fournit plusieurs fonctionnalités de protection des données en transit et au repos, notamment des fonctions de chiffrement pour les données, fichiers, applications, services, communications et lecteurs. Vous pouvez chiffrer les informations avant de les placer dans Azure, et également stocker des clés dans vos centres de données locaux.

![Microsoft Antimalware dans Azure](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Technologies de chiffrement Azure
Vous pouvez collecter des informations sur l’accès administratif à votre environnement d’abonnement à l’aide de la [Création de rapports Azure AD](../active-directory/active-directory-reporting-audit-events.md). Vous pouvez configurer la fonction [Chiffrement de lecteur BitLocker](https://technet.microsoft.com/library/cc732774.aspx) sur les disques durs virtuels contenant des informations sensibles dans Azure.

Parmi les autres fonctionnalités dans Azure qui vous aideront à sécuriser vos données :

* Les développeurs d’applications peuvent créer le chiffrement dans les applications qu’ils déploient dans Azure à l’aide de Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) et de .NET Framework.
* Contrôlez totalement les clés grâce au chiffrement côté client pour le service Stockage Blob Azure. Le service de stockage ne voit jamais les clés et est incapable de déchiffrer les données.
* [Azure Rights Management (Azure RMS)](https://technet.microsoft.com/library/jj585026.aspx) (avec le [kit de développement logiciel (SDK) RMS](https://msdn.microsoft.com/library/dn758244.aspx)) fournit un chiffrement au niveau des fichiers et des données et empêche la fuite des données grâce à la gestion des accès basée sur des stratégies.
* Azure prend en charge le [chiffrement au niveau table et au niveau colonne](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) dans les machines virtuelles SQL Server, ainsi que les serveurs gestionnaires de clés locaux tiers dans les centres de données.
* Les clés de compte de stockage, signatures d’accès partagé, certificats de gestion et autres clés sont uniques pour chaque client Azure.
* Le stockage hybride Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) chiffre les données à l’aide d’une paire de clés publique/privée 128 bits avant de les charger dans le service Stockage Azure.
* Azure prend en charge et utilise de nombreux mécanismes de chiffrement, y compris SSL/TLS, IPsec et AES, selon les types de données, les conteneurs et les transports.

## <a name="virtualization"></a>Virtualisation
La plateforme Azure utilise un environnement virtualisé. Les instances utilisateur fonctionnent comme des machines virtuelles autonomes qui n’ont pas accès à un serveur hôte physique, et cette isolation est appliquée à l’aide des [niveaux de privilège (Anneau 0/Anneau 3) de processeur physique](https://en.wikipedia.org/wiki/Protection_ring).

L’anneau 0 est le plus privilégié, et l’anneau 3 le moins privilégié. Le système d’exploitation invité s’exécute dans un anneau 1 moins privilégié, tandis que les applications s’exécutent dans l’anneau 3, le moins privilégié. Cette virtualisation des ressources physiques entraîne une séparation claire entre le système d’exploitation invité et l’hyperviseur, ce qui donne lieu à une séparation de sécurité supplémentaire entre les deux.

L’hyperviseur d’Azure agit comme un micronoyau et transfère toutes les demandes d’accès matériel des machines virtuelles invitées vers l’hôte à des fins de traitement au moyen d’une interface de mémoire partagée appelée VMBus. Cela empêche les utilisateurs d’obtenir un accès brut en lecture/écriture/exécution au système, et réduit le risque de partage des ressources système.

![Microsoft Antimalware dans Azure](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Comment Azure implémente la virtualisation
Azure utilise un pare-feu hyperviseur (filtre de paquets) qui est implémenté dans l’hyperviseur et configuré par un agent contrôleur de structure. Cela contribue à protéger les clients contre les accès non autorisés. Par défaut, tout le trafic est bloqué lorsqu’une machine virtuelle est créée. Ensuite, l’agent contrôleur de structure configure le filtre de paquets pour ajouter des *règles et exceptions* afin de permettre le trafic autorisé.

Il existe deux catégories de règles qui sont programmées ici :

* **Règles de configuration des machines ou d’infrastructure** : par défaut, toutes les communications sont bloquées. Il existe des exceptions qui permettent d’autoriser une machine virtuelle à envoyer et recevoir le trafic DHCP et DNS. Les machines virtuelles peuvent également envoyer le trafic vers l’Internet « public » et vers d’autres machines virtuelles dans le cluster et le serveur d’activation du système d’exploitation. La liste de destinations sortantes autorisées des machines virtuelles n’inclut pas les sous-réseaux de routeur Azure, les serveurs principaux de gestion Azure, ni d’autres propriétés Microsoft.
* **Fichier de configuration de rôle** : ceci définit les listes de contrôle d’accès (ACL) entrant en fonction du modèle de service du client. Par exemple, si un client possède un serveur web frontal sur le port 80 sur une machine virtuelle spécifique, Azure ouvre le port TCP 80 à toutes les adresses IP si vous configurez un point de terminaison dans le [modèle de déploiement Azure Classic](../azure-resource-manager/resource-manager-deployment-model.md). Si la machine virtuelle comporte un rôle de serveur principal ou de travail en cours d’exécution, Azure n’ouvre le rôle de travail qu’à la machine virtuelle dans le même client.

## <a name="isolation"></a>Isolement
Une autre exigence importante en matière de sécurité dans le cloud consiste à procéder à une séparation pour empêcher le transfert non autorisé et accidentel d’informations entre les déploiements dans une architecture mutualisée partagée .

Azure implémente le [contrôle d’accès réseau](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) et la répartition par le biais de l’isolation des réseaux locaux virtuels (VLAN), des ACL, des équilibreurs de charge et des filtres IP. Azure limite le trafic externe entrant aux ports et protocoles de vos machines virtuelles que vous définissez. Azure implémente le filtrage réseau pour empêcher le trafic falsifié et limiter le trafic entrant et sortant aux composants de plateforme sécurisés. Les stratégies de flux de trafic sont implémentées sur des dispositifs de protection des limites refusant le trafic par défaut.

![Microsoft Antimalware dans Azure](./media/azure-security-getting-started/sec-azgsfig3.PNG)

La traduction d’adresses réseau (NAT) est utilisée pour séparer le trafic réseau interne du trafic externe. Le trafic interne n’est pas routable en externe. Les [adresses IP virtuelles](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) qui sont routables en externe sont traduites en adresses [IP dynamiques internes](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) qui sont uniquement routables dans Azure.

Le trafic externe vers les machines virtuelles Azure est protégé par un pare-feu par l’intermédiaire des ACL sur les routeurs, équilibreurs de charge et commutateurs de couche 3. Seuls les protocoles connus spécifiques sont autorisés. Les ACL sont en place pour limiter le trafic en provenance des machines virtuelles invitées vers d’autres VLAN utilisés pour la gestion. En outre, le trafic filtré par le biais des filtres IP sur le système d’exploitation hôte limite davantage le trafic à la fois sur la liaison de données et sur les couches réseau.

### <a name="how-azure-implements-isolation"></a>Comment Azure implémente l’isolation
Le contrôleur de structure Azure est chargé d’allouer des ressources d’infrastructure aux charges de travail des clients, et gère les communications unidirectionnelles de l’hôte vers les machines virtuelles. L’hyperviseur Azure applique la séparation de mémoire et de processus entre les machines virtuelles, et achemine en toute sécurité le trafic réseau vers les clients du système d’exploitation invité. Azure implémente également l’isolation pour les clients, le stockage et les réseaux virtuels.

* Chaque client Azure AD est isolé logiquement à l’aide des limites de sécurité.
* Chaque abonnement dispose de son propre compte de stockage Azure. Pour y accéder, vous devez vous authentifier à l’aide d’une clé de compte de stockage.
* Les réseaux virtuels sont isolés logiquement grâce à une combinaison d’adresses IP privées uniques, de pare-feu et d’ACL IP. Les équilibreurs de charge acheminent le trafic vers les clients appropriés en fonction des définitions de point de terminaison.

## <a name="virtual-networks-and-firewalls"></a>Réseaux virtuels et pare-feu
Les [réseaux virtuels et distribués](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) dans Azure contribuent à assurer que votre trafic réseau privé est isolé logiquement du trafic sur les autres réseaux virtuels Azure.

![Microsoft Antimalware dans Azure](./media/azure-security-getting-started/sec-azgsfig4.PNG)

Votre abonnement peut contenir plusieurs réseaux privés isolés (et inclure des pare-feu, un équilibrage de charge et une traduction d’adresses réseau).

Azure fournit trois niveaux principaux de répartition réseau dans chaque cluster Azure pour séparer logiquement le trafic. Les [réseaux locaux virtuels](https://azure.microsoft.com/services/virtual-network/) (VLAN) sont utilisés pour séparer le trafic client du reste du réseau Azure. L’accès au réseau Azure depuis l’extérieur du cluster est limité via les équilibreurs de charge.

Le trafic réseau à destination et en provenance des machines virtuelles doit passer par le commutateur virtuel de l’hyperviseur. Le composant de filtre IP dans le système d’exploitation racine isole la machine virtuelle racine des machines virtuelles invitées, ainsi que les machines virtuelles invitées les unes des autres. Il effectue un filtrage du trafic afin de restreindre la communication entre les nœuds du client et l’Internet public (selon la configuration du service du consommateur), séparant ainsi ces derniers des autres clients.

Le filtre IP contribue à empêcher les machines virtuelles invitées d’effectuer les opérations suivantes :

* Générer un trafic falsifié
* Recevoir du trafic qui ne leur est pas adressé
* Diriger le trafic vers des points de terminaison d’infrastructure protégés
* Envoyer ou recevoir du trafic de diffusion inapproprié

Vous pouvez placer vos machines virtuelles sur les [réseaux virtuels Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Ces réseaux virtuels sont similaires aux réseaux que vous configurez dans les environnements locaux, où ils sont généralement associés à un commutateur virtuel. Les machines virtuelles connectées au même réseau virtuel peuvent communiquer sans configuration supplémentaire. Vous pouvez également configurer différents sous-réseaux au sein de votre réseau virtuel.

Vous pouvez utiliser les technologies de réseau virtuel Azure ci-après pour contribuer à sécuriser les communications sur votre réseau virtuel :

* [**Groupes de sécurité réseau (NSG)**](../virtual-network/virtual-networks-nsg.md). Vous pouvez utiliser un NSG pour contrôler le trafic vers une ou plusieurs instances de machine virtuelle dans votre réseau virtuel. Un groupe de sécurité réseau contient les règles de contrôle d’accès qui autorisent ou refusent le trafic en fonction de la direction du trafic, du protocole, de l’adresse et du port source ainsi que de l’adresse et du port de destination.
* [**Routage défini par l’utilisateur**](../virtual-network/virtual-networks-udr-overview.md). Vous pouvez contrôler le routage des paquets par le biais d’une appliance virtuelle en créant des itinéraires définis par l’utilisateur qui redirigent vers une appliance de sécurité de réseau virtuel le prochain tronçon de paquets circulant vers un sous-réseau spécifique.
* [**Transfert IP**](../virtual-network/virtual-networks-udr-overview.md). Une appliance de sécurité de réseau virtuel doit être capable de recevoir le trafic entrant qui ne lui est pas adressé. Pour permettre à une machine virtuelle de recevoir le trafic adressé à d’autres destinations, vous activez le transfert IP pour cette machine virtuelle.
* [**Tunneling forcé**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). Le tunneling forcé vous permet de rediriger ou de « forcer » tout le trafic Internet généré par vos machines virtuelles dans un réseau virtuel vers votre emplacement local par le biais d’un tunnel VPN site à site à des fins d’inspection et d’audit.
* [**ACL de point de terminaison**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Vous pouvez contrôler les machines pour lesquelles les connexions entrantes sont autorisées à partir d’Internet vers une machine virtuelle sur votre réseau virtuel Azure en définissant des ACL de point de terminaison.
* [**Solutions de sécurité réseau de partenaires**](https://azure.microsoft.com/marketplace/). Il existe un certain nombre de solutions de sécurité réseau de partenaires auxquelles vous pouvez accéder à partir de la plateforme Place de marché Azure.

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Mode d’implémentation par Azure des réseaux virtuels et des pare-feu
Azure implémente des pare-feu de filtrage de paquets sur l’ensemble des machines virtuelles hôtes et invitées par défaut. Le Pare-feu Windows est également activé par défaut pour les images du système d’exploitation Windows de la Place de marché Azure. Les équilibreurs de charge au niveau du périmètre des réseaux Azure publics contrôlent les communications en fonction des ACL IP gérées par les administrateurs de clients.

Si Azure déplace des données d’un client dans le cadre d’opérations normales ou lors d’une urgence, il le fait sur des canaux de communication privés et chiffrés. Voici d’autres fonctionnalités employées par Azure qu’il convient d’utiliser dans les réseaux virtuels et les pare-feu :

* **Pare-feu d’hôte natif** : la plateforme Azure Service Fabric et le service Stockage Azure s’exécutent sur un système d’exploitation natif dépourvu d’hyperviseur. Par conséquent, le Pare-feu Windows est configuré avec les deux ensembles de règles ci-dessus. Le stockage s’exécute de manière native pour optimiser les performances.
* **Pare-feu d’hôte** : le pare-feu d’hôte sert à protéger le système d’exploitation hôte qui exécute l’hyperviseur. Les règles sont programmées de façon que seuls le contrôleur Service Fabric et les « jump boxes » soient autorisés à communiquer avec le système d’exploitation hôte sur un port spécifique. Les autres exceptions permettent d’autoriser la réponse DHCP et les réponses DNS. Azure utilise un fichier de configuration des machines qui contient le modèle de règles de pare-feu pour le système d’exploitation hôte. L’hôte lui-même est protégé des attaques externes par un Pare-feu Windows configuré de façon à n’autoriser la communication qu’à partir de sources connues et authentifiées.
* **Pare-feu d’invité** : ce pare-feu réplique les règles figurant dans le filtre de paquets de commutateur de machine virtuelle, mais est programmé dans un logiciel différent (tel que l’élément Pare-feu Windows du système d’exploitation invité). Le pare-feu de machine virtuelle invitée peut être configuré pour restreindre les communications à destination ou en provenance de la machine virtuelle invitée, même si la communication est autorisée par les configurations du filtre IP de l’hôte. Par exemple, vous pouvez choisir d’utiliser le pare-feu de machine virtuelle invitée pour restreindre la communication entre deux de vos réseaux virtuels qui ont été configurés pour se connecter l’un à l’autre.
* **Pare-feu de stockage** : le pare-feu sur le serveur de stockage frontal filtre le trafic afin qu’il s’effectue uniquement sur les ports 80/443 et sur d’autres ports utilitaires nécessaires. Le pare-feu sur le serveur de stockage principal restreint les communications afin qu’elles proviennent uniquement des serveurs de stockage frontaux.
* **Passerelle de réseau virtuel** : la [passerelle de réseau virtuel Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) sert de passerelle entre différents locaux, en connectant vos charges de travail dans le service Réseau virtuel Azure à vos sites locaux. Il est nécessaire de se connecter aux sites locaux par le biais de [tunnels VPN site à site IPsec](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) ou de circuits [ExpressRoute](../expressroute/expressroute-introduction.md). Pour les tunnels VPN IPsec/IKE, les passerelles établissent des liaisons IKE, ainsi que des tunnels VPN S2S IPsec entre les réseaux virtuels et les sites locaux. Les passerelles de réseau virtuel terminent également les [VPN de point à site](../vpn-gateway/vpn-gateway-point-to-site-create.md).

## <a name="secure-remote-access"></a>Accès à distance sécurisé
Les données stockées dans le cloud doivent disposer de protections suffisantes activées pour empêcher les attaques et maintenir la confidentialité et l’intégrité pendant le transit. Cela inclut les contrôles réseau associés aux mécanismes de gestion de l’identité et de l’accès, vérifiables et basés sur la stratégie d’une organisation.

La technologie de chiffrement intégrée vous permet de chiffrer les communications au sein de et entre les déploiements, entre les régions Azure et depuis Azure vers les centres de données locaux. Les administrateurs accèdent aux machines virtuelles par le biais des [sessions Bureau à distance](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) et de [Windows PowerShell à distance](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx), et le Portail Azure est toujours chiffré.

Pour étendre en toute sécurité votre centre de données local dans le cloud, Azure fournit un [VPN de site à site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) et un [VPN de point à site](../vpn-gateway/vpn-gateway-point-to-site-create.md), ainsi que des liaisons dédiées avec [ExpressRoute](../expressroute/expressroute-introduction.md) (les connexions aux réseaux virtuels Azure par le biais d’un VPN sont chiffrées).

### <a name="how-azure-implements-secure-remote-access"></a>Comment Azure implémente un accès distant sécurisé
Les connexions au Portail Azure doivent toujours être authentifiées et nécessitent les protocoles SSL/TLS. Vous pouvez configurer les certificats de gestion pour activer la gestion sécurisée. Les protocoles de sécurité standard tels que [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) et [IPsec](https://en.wikipedia.org/wiki/IPsec) sont entièrement pris en charge.

[Azure ExpressRoute](../expressroute/expressroute-introduction.md) vous permet de créer des connexions privées entre les centres de données Azure et une infrastructure locale ou dans un environnement de colocalisation. Les connexions ExpressRoute ne sont pas établies par le biais de l'Internet public. Elles offrent davantage de fiabilité, des vitesses supérieures, des latences inférieures et une sécurité renforcée par rapport aux liaisons Internet classiques. Dans certains cas, le transfert de données entre des emplacements locaux et Azure à l’aide de connexions ExpressRoute peut également générer des économies substantielles.

## <a name="logging-and-monitoring"></a>Enregistrement et surveillance
Azure assure une journalisation authentifiée des événements liés à la sécurité qui génèrent une piste d’audit, et est conçu pour résister aux falsifications. Cela inclut les informations système, notamment les journaux des événements de sécurité dans les machines virtuelles d’infrastructure Azure et Azure AD. La surveillance des événements de sécurité comprend la collecte d’événements, notamment : modifications d’adresses IP de serveurs DHCP ou DNS, tentatives d’accès aux ports, protocoles ou adresses IP bloqués par conception, modifications de stratégies de sécurité ou de paramètres de pare-feu, création de comptes ou de groupes, installation de pilotes ou processus inattendus.

![Microsoft Antimalware dans Azure](./media/azure-security-getting-started/sec-azgsfig5.PNG)

Des journaux d’audit enregistrant les accès et activités utilisateur privilégiés, les tentatives d’accès autorisé et non autorisé, les exceptions de système et les événements de sécurité sont conservés pour un laps de temps défini. La conservation de vos journaux est à votre entière discrétion parce que vous configurez la collecte et la conservation selon vos propres exigences.

### <a name="how-azure-implements-logging-and-monitoring"></a>Comment Azure implémente l’enregistrement et la surveillance
Azure déploie des agents de gestion (MA) et moniteurs de sécurité Azure (ASM) pour chaque calcul, stockage ou nœud de structure gérés, qu’ils soient natifs ou virtuels. Chaque agent de gestion est configuré pour s’authentifier sur un compte de stockage d’équipe de services avec un certificat obtenu à partir du magasin de certificats Azure, et pour transférer les données de diagnostics et d’événements préconfigurées vers le compte de stockage. Ces agents ne sont pas déployés sur les machines virtuelles des clients.

Les administrateurs Azure accèdent aux journaux via un portail web de manière authentifiée et contrôlée. Un administrateur peut analyser, filtrer, mettre en corrélation et analyser les journaux. Les comptes de stockage de l’équipe de services Azure pour les journaux sont protégés de l’accès administrateur direct pour éviter la falsification de journaux.

Microsoft collecte des journaux à partir de périphériques réseau, à l’aide du protocole Syslog, et à partir de serveurs hôtes, à l’aide des services ACS Microsoft. Ces journaux sont placés dans une base de données de journaux à partir de laquelle des alertes sont générées pour les événements suspects. L’administrateur peut accéder à ces journaux et les analyser.

Les [diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) sont une fonctionnalité d’Azure vous permettant de collecter des données de diagnostic à partir d’une application exécutée dans Azure. Ces données de diagnostic peuvent servir au débogage et à la résolution des problèmes, à la mesure des performances, au suivi de l’utilisation des ressources, à l’analyse de trafic, à la planification de capacité et à l’audit. Une fois les données de diagnostic collectées, elles peuvent être transférées vers un compte de stockage Azure pour être conservées. Les transferts peuvent être planifiés ou effectués à la demande.

## <a name="threat-mitigation"></a>Atténuation des menaces
Outre l’isolation, le chiffrement et le filtrage, Azure utilise un certain nombre de mécanismes et de processus d’atténuation des menaces afin de protéger l’infrastructure et les services. Ceux-ci incluent des contrôles et des technologies internes utilisés pour détecter et corriger les menaces avancées, telles que le déni de service distribué (DDoS), la réaffectation de privilèges et les risques [OWASP Top-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Les contrôles de sécurité et processus de gestion des risques mis en place par Microsoft pour sécuriser son infrastructure cloud réduisent le risque d’incidents de sécurité. Toutefois, en cas d’incident, l’équipe SIM (gestion des incidents de sécurité) au sein de l’équipe Microsoft OSSC (services de sécurité et conformité en ligne) se tient prête à répondre à tout moment.

### <a name="how-azure-implements-threat-mitigation"></a>Comment Azure implémente l’atténuation des menaces
Azure dispose de contrôles de sécurité en place pour implémenter l’atténuation des menaces, ainsi que pour aider les clients à minimiser les menaces potentielles dans leurs environnements. La liste ci-après récapitule les fonctionnalités d’atténuation des menaces proposées par Azure :

* [Azure Antimalware](azure-security-antimalware.md) est activé par défaut sur tous les serveurs d’infrastructure. Vous avez la possibilité de l’activer dans vos propres machines virtuelles.
* Microsoft gère la surveillance continue des serveurs, réseaux et applications afin de détecter les menaces et d’empêcher les attaques. Les alertes automatisées notifient les administrateurs des comportements anormaux, ce qui leur permet de prendre des mesures correctives sur les menaces internes et externes.
* Vous pouvez déployer des solutions de sécurité tierces au sein de vos abonnements, comme les pare-feu d’applications web de [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).
* L’approche de test de pénétration de Microsoft inclut [Red Teaming](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), qui implique des attaques des systèmes de production réels (non clients) dans Azure par des professionnels de la sécurité Microsoft, afin de tester les défenses contre des menaces persistantes, réelles et avancées.
* Des systèmes de déploiement intégrés gèrent la distribution et l’installation de correctifs de sécurité sur la plateforme Azure.

## <a name="next-steps"></a>Étapes suivantes
[Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/)

[Blog de l’équipe de sécurité Azure](http://blogs.msdn.com/b/azuresecurity/)

[Centre de réponse aux problèmes de sécurité Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

[Blog Active Directory](http://blogs.technet.com/b/ad/)
