---
title: "Bonnes pratiques en matière de sécurité réseau | Microsoft Docs"
description: "Cet article détaille les meilleures pratiques en matière de sécurité réseau, sur la base de capacités Azure intégrées."
services: security
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3dee3411dadbca5e88951dec2ed1836d440423c4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="azure-network-security-best-practices"></a>Meilleures pratiques en matière de sécurité réseau - Azure
Microsoft Azure vous permet de connecter des machines virtuelles et des appliances à d’autres appareils en réseau, en les plaçant sur des réseaux virtuels Microsoft Azure. Un réseau virtuel Azure est une construction qui vous permet de connecter des cartes d’interface réseau virtuel à un réseau virtuel, afin de permettre des communications via TCP/IP entre les appareils fonctionnant sur le réseau. Les machines Microsoft Azure Virtual Machines reliées à un réseau virtuel Azure peuvent se connecter à des appareils se trouvant sur le même réseau virtuel Azure, ou des réseaux virtuels Azure différents, sur Internet ou sur vos réseaux locaux.

Dans cet article, nous aborderons différentes meilleures pratiques en matière de sécurité réseau sur Azure. Ces meilleures pratiques sont issues de notre expérience dans le domaine de la mise en réseau Azure, mais également de celle des clients, comme vous.

Pour chaque meilleure pratique, nous allons détailler les éléments suivants :

* Nature de la meilleure pratique
* Raison pour laquelle activer cette bonne pratique
* Conséquence possible en cas de non-utilisation de la meilleure pratique
* Alternatives possibles à la meilleure pratique
* Comment apprendre à utiliser la meilleure pratique

Cet article repose sur un consensus, ainsi que sur les fonctionnalités et ensembles de fonctions de la plate-forme Azure disponibles lors de l’écriture. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

Les meilleures pratiques en matière de sécurité réseau sur Azure qui sont abordées dans le cadre de cet article sont les suivantes :

* Segmentation logique des sous-réseaux
* Contrôle du comportement de routage
* Activation du tunneling forcé
* Utilisation d’appliances de réseau virtuel
* Déploiement de zones DMZ pour la segmentation de sécurité
* Suppression de toute exposition à Internet grâce à des liaisons réseau étendu dédiées
* Optimisation de la durée active et des performances
* Utilisation de l’équilibrage de charge global
* Désactivation de l’accès RDP à Microsoft Azure Virtual Machines
* Activation d’Azure Security Center
* Extension de votre centre de données dans Azure

## <a name="logically-segment-subnets"></a>Segmentation logique des sous-réseaux
Les [réseaux virtuels Azure](https://azure.microsoft.com/documentation/services/virtual-network/) sont similaires à des LAN au sein de votre réseau local. Un réseau virtuel Azure repose sur un concept : la création d’un réseau basé sur l’espace et une adresse IP privée unique, au sein duquel vous pouvez placer toutes vos machines virtuelles [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/). Les espaces d’adressage IP privés disponibles se trouvent dans les plages des classes A (10.0.0.0/8), B (172.16.0.0/12) et C (192.168.0.0/16).

Comme vous le faites en local, vous devez segmenter l’espace d’adressage plus volumineux en sous-réseaux. Pour créer ces derniers, vous pouvez utiliser les principes de création de sous-réseau reposant sur [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Le routage entre les sous-réseaux est effectué automatiquement ; vous n’avez pas besoin de configurer manuellement des tables de routage. Toutefois, par défaut, il n’y a aucun contrôle d’accès réseau entre les sous-réseaux que vous créez sur le réseau virtuel Azure. Pour créer des contrôles d’accès réseau entre les sous-réseaux, vous devrez placer un élément entre ces derniers.

Pour ce faire, vous pouvez utiliser un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG, Network Security Group). Un groupe de sécurité réseau est un simple appareil d’inspection des paquets, avec état, qui applique la méthode basée sur les 5 tuples (adresse IP source, port source, adresse IP de destination, port de destination et protocole de couche 4) pour créer des règles visant à autoriser ou refuser le trafic réseau. Vous pouvez autoriser ou refuser le trafic vers et depuis une ou plusieurs adresses IP, voire entre des sous-réseaux entiers, dans les deux directions.

En gérant le contrôle d’accès réseau via des groupes de sécurité réseau, vous pouvez placer des ressources appartenant au même rôle ou à la même zone de sécurité dans leurs propres sous-réseaux. Prenons l’exemple d’une application simple à 3 couches : une couche web, une couche de logique d’application et une couche de base de données. Vous placez les machines virtuelles qui appartiennent à chacune de ces couches dans leurs propres sous-réseaux. Ensuite, vous utilisez les groupes de sécurité réseau pour contrôler le trafic entre les sous-réseaux :

* Les machines virtuelles de la couche web peuvent uniquement initier des connexions vers les machines de la couche de logique d’application et acceptent les connexions depuis Internet seulement.
* Les machines virtuelles de la couche de logique d’application peuvent uniquement initier des connexions avec la couche de base de données et acceptent les connexions depuis la couche web seulement.
* Quant aux machines virtuelles de la couche de base de données, elles ne peuvent pas initier de connexion avec des systèmes placés hors de leurs sous-réseaux et acceptent les connexions depuis la couche de logique d’application seulement.

Pour en savoir plus sur les groupes de sécurité réseau et leur utilisation pour la segmentation logique de vos réseaux virtuels Azure, consultez l’article [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

## <a name="control-routing-behavior"></a>Contrôle du comportement de routage
Lorsque vous placez une machine virtuelle sur un réseau virtuel Azure, vous pouvez constater qu’elle peut se connecter à n’importe quelle autre machine virtuelle placée sur le même réseau virtuel Azure, même si les autres machines virtuelles se trouvent dans des sous-réseaux différents. En effet, il existe une collection d’itinéraires de système activés par défaut, qui permettent ce type de communication. Grâce à ces itinéraires par défaut, les machines virtuelles placées sur le même réseau virtuel Azure peuvent initier des connexions les unes avec les autres, ainsi qu’avec Internet (pour les communications sortantes vers Internet uniquement).

Les itinéraires de système par défaut sont utiles dans de nombreux scénarios de déploiement, mais il se peut que vous souhaitiez personnaliser la configuration du routage pour vos déploiements. Ces personnalisations vous permettent de configurer l’adresse du saut suivant, afin d’atteindre des destinations spécifiques.

Nous vous recommandons de configurer des itinéraires définis par l’utilisateur lorsque vous déployez une appliance de sécurité de réseau virtuel (nous en parlerons dans le cadre d’une meilleure pratique à venir).

> [!NOTE]
> Les itinéraires définis par l’utilisateur ne sont pas obligatoires ; les itinéraires de système par défaut sont applicables dans la plupart des cas.
>
>

Pour en savoir plus sur les itinéraires définis par l’utilisateur et leur configuration, consultez l’article [Présentation des itinéraires définis par l’utilisateur et du transfert IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Activation du tunneling forcé
Pour mieux appréhender le concept de « tunneling forcé », il peut être utile de comprendre le fonctionnement du « tunneling fractionné ».
L’exemple le plus courant de tunneling fractionné est associé aux connexions VPN. Prenons un exemple : vous établissez une connexion VPN entre votre chambre d’hôtel et votre réseau d’entreprise. Cette connexion vous permet d’accéder aux ressources d’entreprise ; toutes les communications vers votre réseau d’entreprise transitent via le tunnel VPN.

Que se passe-t-il lorsque vous souhaitez vous connecter à des ressources sur Internet ? Lorsque le tunneling fractionné est activé, ces connexions accèdent directement à Internet, sans passer par le tunnel VPN. Certains experts en matière de sécurité considèrent que cette pratique peut constituer un risque. Pour cette raison, ils recommandent de désactiver le tunneling fractionné et de faire en sorte que toutes les connexions (celles qui sont dirigées vers Internet comme celles qui ont pour destination les ressources d’entreprise) passent par le tunnel VPN. Cela présente un avantage : les connexions à Internet doivent transiter via les appareils de gestion de la sécurité réseau de l’entreprise. Si le client VPN était connecté à Internet en dehors du tunnel VPN, ce ne serait pas le cas.

Revenons à présent aux machines virtuelles du réseau virtuel Azure. Les itinéraires par défaut d’un réseau virtuel Azure permettent aux machines virtuelles d’initier tout trafic vers Internet. Cela peut également constituer un risque de sécurité, car ces connexions sortantes sont susceptibles d’augmenter la surface d’attaque d’une machine virtuelle et d’être exploitées de manière frauduleuse.
Pour cette raison, nous vous recommandons d’activer le tunneling forcé sur vos machines virtuelles lorsque la connectivité entre locaux est établie entre votre réseau virtuel Azure et votre réseau local. Nous parlerons un peu plus tard de la connectivité entre locaux.

Si aucune connectivité entre locaux n’est établie, tirez parti des groupes de sécurité réseau (voir ci-dessus) ou des appliances de sécurité du réseau virtuel Azure (voir ci-dessous) pour empêcher toute connexion sortante vers Internet depuis vos machines virtuelles Azure.

Pour en savoir plus sur le tunneling forcé et son activation, consultez l’article [Configurer le tunneling forcé à l’aide de PowerShell et d’Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Utilisation d’appliances de réseau virtuel
Les groupes de sécurité réseau et les itinéraires définis par l’utilisateur peuvent contribuer à renforcer la sécurité au niveau des couches transport et réseau du [modèle OSI](https://en.wikipedia.org/wiki/OSI_model), dans une certaine mesure. Toutefois, dans certains cas, vous souhaiterez ou serez contraint d’activer la sécurité à des niveaux élevés de la pile. Le cas échéant, nous vous recommandons de déployer les appliances de sécurité de réseau virtuel fournies par les partenaires d’Azure.

Ces appliances peuvent offrir des niveaux de sécurité nettement plus élevés que ceux des contrôles appliqués au niveau du réseau. Voici quelques-unes des fonctionnalités de sécurité réseau fournies par les appliances de sécurité de réseau virtuel :

* Installation de pare-feu
* Détection et prévention des intrusions
* Gestion des vulnérabilités
* Contrôle des applications
* Détection des anomalies basée sur le réseau
* Filtrage web
* Antivirus
* Protection contre les botnets

S’il vous faut un niveau plus élevé de sécurité réseau que celui qu’offrent les contrôles d’accès au niveau du réseau, nous vous invitons à envisager le déploiement d’appliances de sécurité du réseau virtuel Azure.

Pour connaître les appliances de sécurité de réseau virtuel Azure disponibles et en savoir plus sur leurs capacités, accédez au site [Microsoft Azure Marketplace](https://azure.microsoft.com/marketplace/) et recherchez les termes « sécurité » et « sécurité réseau ».

## <a name="deploy-dmzs-for-security-zoning"></a>Déploiement de zones DMZ pour la segmentation de sécurité
Une zone DMZ, ou « réseau de périmètre », est un segment de réseau logique ou physique conçu pour fournir une couche de sécurité supplémentaire entre vos ressources et Internet. L’objectif de la zone DMZ est de placer les appareils de contrôle d’accès réseau spécialisés sur le périmètre du réseau DMZ, afin que seul le trafic souhaité puisse transiter via l’appareil de gestion de la sécurité réseau et accéder au réseau virtuel Azure.

Les zones DMZ sont utiles, car vous pouvez concentrer la gestion des contrôles d’accès réseau, la surveillance, la journalisation et la création de rapports sur les appareils situés à la périphérie de votre réseau virtuel Azure. Ici, vous devez généralement activer la prévention des DDoS, les systèmes de détection et de prévention des intrusions (IDS/IPS), les règles et stratégies de pare-feu, le filtrage web, les fonctions anti-programme malveillant du réseau, etc. Les appareils dédiés à la sécurité réseau se trouvent entre Internet et le réseau virtuel Azure et disposent d’une interface sur les deux réseaux.

Il s’agit là de la conception de base d’une zone DMZ. Cependant, il existe plusieurs autres conceptions (configurations « dos à dos », à triple hébergement, multirésidentes, etc.).

Pour tous les déploiements haute sécurité, nous vous recommandons d’envisager le déploiement d’une zone DMZ, afin d’améliorer la sécurité réseau de vos ressources Azure.

Pour en savoir plus sur les zones DMZ et leur déploiement dans Azure, consultez l’article [Services de cloud computing et sécurité réseau Microsoft](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Suppression de toute exposition à Internet grâce à des liaisons réseau étendu dédiées
De nombreuses organisations ont opté pour l’informatique hybride. Dans un environnement informatique hybride, certaines ressources informatiques de l’entreprise se trouvent sur Azure, alors que d’autres restent en local. Dans de nombreux cas, certains composants d’un service seront exécutés dans Azure, tandis que d’autres le seront localement.

Un scénario hybride propose généralement un certain type de connectivité entre locaux. Cette connectivité permet à l’entreprise de relier ses réseaux locaux aux réseaux virtuels Azure. Deux solutions de connectivité entre locaux sont disponibles :

* VPN de site à site
* ExpressRoute

[Un VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) assure une connexion privée virtuelle entre le réseau local et un réseau virtuel Azure. Cette connexion s’effectue via Internet. Elle vous permet de créer un « tunnel » pour le transit des informations d’une liaison chiffrée entre votre réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le chiffrement du tunnel est effectué par le biais du [mode de tunneling IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Le VPN de site à site est fiable, sécurisé et reconnu, mais le trafic transitant par le tunnel passe par Internet. En outre, la bande passante est relativement limitée (200 Mbits/s maximum, environ).

Si, dans le cadre de vos connexions entre locaux, il vous faut un niveau de sécurité hors normes ou des performances exceptionnelles, nous vous recommandons d’utiliser la connectivité entre locaux offerte par Azure ExpressRoute. ExpressRoute représente une liaison réseau étendu dédiée entre le site local et un fournisseur d’hébergement Exchange. Comme il s’agit d’une connexion de télécommunications, vos données ne transitent pas par Internet et, par conséquent, ne sont pas exposées aux risques potentiels inhérents aux communications Internet.

Pour en savoir plus sur le fonctionnement d’Azure ExpressRoute et son déploiement, consultez l’article [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimisation de la durée active et des performances
La confidentialité, l’intégrité et la disponibilité sont les trois facteurs principaux du modèle de sécurité le plus en vogue actuellement. La vérification de la confidentialité repose sur le chiffrement et la protection de la vie privée ; la vérification de l’intégrité vise à garantir que les données ne sont pas modifiées par des utilisateurs non autorisés. Enfin, la vérification de la disponibilité a pour objectif de garantir que les utilisateurs autorisés peuvent accéder aux informations prévues. Un manquement dans l’un ou l’autre de ces domaines peut constituer une faille de sécurité potentielle.

La notion de disponibilité peut être rapprochée de la durée active et des performances. Si un service est défaillant, les informations sont inaccessibles. Si les performances sont tellement médiocres que les données deviennent inutilisables, nous pouvons considérer que ces dernières sont inaccessibles. Par conséquent, dans une perspective de sécurité, nous devons faire tout ce qui est en notre pouvoir pour garantir des performances et une durée active optimales pour nos services.
Pour optimiser cette durée active et ces performances, une méthode bien connue pour son efficacité est l’équilibrage de charge. L’équilibrage de charge est une méthode de répartition du trafic réseau entre les serveurs qui font partie d’un service. Ainsi, si votre service inclut plusieurs serveurs web frontaux, vous pouvez utiliser l’équilibrage de charge pour répartir le trafic entre ces derniers.

La répartition du trafic permet d’augmenter la disponibilité. En effet, si l’un des serveurs web est indisponible, l’équilibreur de charge arrête d’envoyer des données à ce serveur et redirige le trafic vers les serveurs actifs. L’équilibrage de charge améliore également les performances, car la surcharge du processeur, du réseau et de la mémoire associée au traitement des requêtes est répartie sur les différents serveurs avec équilibrage de charge.

Nous vous recommandons de tirer parti aussi souvent que possible de l’équilibrage de charge, selon les besoins de vos services. Nous aborderons la pertinence au cours des sections suivantes : Au niveau du réseau virtuel Azure, Azure vous propose trois options principales en matière d’équilibrage de charge :

* Équilibrage de charge basé sur HTTP
* Équilibrage de charge externe
* Équilibrage de charge interne

## <a name="http-based-load-balancing"></a>Équilibrage de charge basé sur HTTP
L’équilibrage de charge basé sur HTTP détermine à quel serveur envoyer des connexions en fonction des caractéristiques du protocole HTTP. Azure propose un équilibreur de charge HTTP appelé Application Gateway.

Nous vous recommandons de tirer parti d’Azure Application Gateway dans les cas suivants :

* Les applications pour lesquelles les requêtes provenant d’une même session utilisateur/client doivent atteindre la même machine virtuelle back-end. Exemples : applications de panier d’achat et serveurs de messagerie.
* Les applications qui veulent supprimer la surcharge liée aux arrêts SSL sur les batteries de serveurs web, en tirant parti de la fonctionnalité de [déchargement SSL](https://f5.com/glossary/ssl-offloading) d’Application Gateway.
* Les applications, telles qu’un réseau de distribution de contenu, qui exigent le routage ou l’équilibrage de charge sur différents serveurs principaux des multiples requêtes HTTP sur une même connexion TCP de longue durée.

Pour en savoir plus sur le fonctionnement d’Azure Application Gateway et son utilisation dans le cadre de vos déploiements, consultez l’article [Vue d’ensemble d’Application Gateway](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Équilibrage de charge externe
L’équilibrage de charge externe intervient lorsque la charge des connexions entrantes en provenance d’Internet est équilibrée entre vos serveurs situés au sein d’un réseau virtuel Azure. L’équilibreur de charge externe Azure peut vous fournir cette fonctionnalité. Nous vous recommandons de l’utiliser lorsque vous n’avez pas besoin de sessions temporaires ou du déchargement SSL.

Contrairement à l’équilibrage de charge basé sur HTTP, l’équilibrage de charge externe utilise les informations fournies par les couches réseau et transport du modèle de mise en réseau OSI pour choisir le serveur dont la charge de connexion doit être équilibrée.

Nous vous invitons à recourir à l’équilibrage de charge externe quand vous avez des [applications sans état](http://whatis.techtarget.com/definition/stateless-app) qui acceptent les requêtes entrantes en provenance d’Internet.

Pour en savoir plus sur le fonctionnement de l’équilibreur de charge externe Azure et son déploiement, consultez l’article [Création d’un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Équilibrage de charge interne
Similaire à l’équilibrage de charge externe, il utilise le même mécanisme pour équilibrer la charge des connexions avec les serveurs qui se trouvent derrière elles. Seule différence : l’équilibreur de charge accepte dans ce cas les connexions provenant des machines virtuelles qui ne sont pas sur Internet. Dans la plupart des situations, les connexions qui sont acceptées à des fins d’équilibrage de charge sont initiées par des appareils se trouvant sur un réseau virtuel Azure.

Nous vous recommandons d’utiliser l’équilibrage de charge interne pour les scénarios qui bénéficient de cette fonctionnalité, par exemple lorsque vous devez équilibrer la charge des connexions aux instances SQL Server ou aux serveurs web internes.

Pour en savoir plus sur le fonctionnement de l’équilibreur de charge interne Azure et son déploiement, consultez l’article [Création d’un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Utilisation de l’équilibrage de charge global
Le cloud computing public permet de déployer des applications réparties au niveau global et dotées de composants situés sur des centres de données aux quatre coins du monde. Cette opération est possible sur Microsoft Azure, grâce à la présence de centres de données Azure au niveau mondial. Contrairement aux technologies d’équilibrage de charge détaillées précédemment, l’équilibrage de charge global permet d’assurer la disponibilité des services et ce, même lorsque des centres de données dans leur ensemble ne sont plus disponibles.

[Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/) propose ce type d’équilibrage de charge dans Azure. Grâce à ce logiciel, vous pouvez équilibrer la charge des connexions vers vos services en fonction de l’emplacement de l’utilisateur.

Par exemple, si l’utilisateur qui envoie une requête à votre service se trouve dans un pays de l’Union européenne, la connexion est redirigée vers vos services qui se trouvent au sein d’un centre de données de cette zone. Cette étape de l’équilibrage de charge global assuré par Traffic Manager permet d’optimiser les performances, car la connexion au centre de données le plus proche est plus rapide que dans le cas de centres de données éloignés.

Du point de vue de la disponibilité, ce type d’équilibrage de charge permet de garantir que votre service reste disponible, même si tout un centre de données devient indisponible.

Ainsi, si un centre de données Azure est indisponible en raison d’une catastrophe naturelle ou d’une panne (défaillance de réseau, par exemple), les connexions à votre service sont redirigées vers le centre de données actif le plus proche. Cet équilibrage de charge global s’effectue sur la base de stratégies DNS, que vous pouvez créer dans Traffic Manager.

Nous vous recommandons d’utiliser Traffic Manager pour toute solution cloud que vous développez, dont la portée touche plusieurs régions et qui requiert la durée active la plus longue possible.

Pour en savoir plus sur Azure Traffic Manager et son déploiement, consultez l’article [Qu’est-ce que Traffic Manager ?](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Désactivation de l’accès RDP/SSH à Microsoft Azure Virtual Machines
Il est possible d’atteindre les machines Azure Virtual Machines à l’aide des protocoles [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) et [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Ces protocoles permettent de gérer des machines virtuelles à partir d’emplacements distants. Ils sont souvent utilisés par les centres de données informatiques.

Cependant, ils peuvent être sources de problèmes de sécurité quand ils sont utilisés sur Internet. En effet, les utilisateurs malveillants peuvent recourir à des attaques par [force brute](https://en.wikipedia.org/wiki/Brute-force_attack) pour accéder aux machines virtuelles Azure. Lorsqu’ils y parviennent, ils peuvent utiliser votre machine virtuelle comme point de départ pour le piratage d’autres machines au sein de votre réseau virtuel Azure, voire attaquer des appareils mis en réseau en dehors d’Azure.

Pour cette raison, nous vous recommandons de désactiver l’accès direct des protocoles RDP et SSH à vos machines Azure Virtual Machines depuis Internet. Cela fait, vous disposez d’autres options vous permettant d’accéder à ces machines virtuelles à des fins de gestion à distance :

* VPN de point à site
* VPN de site à site
* ExpressRoute

L’expression [VPN de point à site](../vpn-gateway/vpn-gateway-point-to-site-create.md) est synonyme de connexion du client/serveur VPN pour un accès à distance. Un VPN de point à site permet à un utilisateur unique de se connecter à un réseau virtuel Azure via Internet. Une fois la connexion de point à site établie, l’utilisateur est en mesure d’utiliser le protocole RDP ou SSH pour se connecter à toutes les machines virtuelles situées sur le réseau virtuel Azure auquel cet utilisateur est connecté, via le VPN de point à site. Cela suppose que l’utilisateur dispose des autorisations requises pour atteindre ces machines virtuelles.

Le VPN de point à site est davantage sécurisé qu’une connexion RDP ou SSH directe, car l’utilisateur doit s’authentifier deux fois pour pouvoir se connecter à une machine virtuelle. Tout d’abord, il doit s’authentifier et être autorisé à établir la connexion VPN de point à site. Ensuite, il doit s’authentifier et être autorisé à établir la session RDP ou SSH.

Un [VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md) connecte un réseau dans son ensemble à un autre réseau, par le biais d’Internet. Vous pouvez utiliser un VPN de site à site pour connecter votre réseau local à un réseau virtuel Azure. Si vous déployez un VPN de site à site, les utilisateurs recourant à votre réseau local pourront se connecter à des machines virtuelles situées sur le réseau virtuel Azure, en tirant parti de la connexion VPN de site à site via RDP ou SSH. Vous n’aurez pas besoin d’autoriser un accès direct d’un de ces deux protocoles via Internet.

Pour proposer une fonctionnalité similaire à la connexion VPN de site à site, vous pouvez également utiliser une liaison réseau étendu dédiée. Les principales différences sont 1. La liaison WAN dédiée ne traverse pas Internet et 2. Les liaisons WAN dédiées sont généralement plus stables et performantes. Azure fournit une solution de liaison réseau étendu dédiée sous la forme de son logiciel [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Activation d’Azure Security Center
Azure Security Center vous aide à prévenir, détecter et résoudre les menaces, en vous apportant une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

Azure Security Center vous permet d’optimiser et de surveiller la sécurité réseau grâce aux opérations suivantes :

* Mise à disposition de recommandations relatives à la sécurité réseau
* Surveillance de l’état de votre configuration de la sécurité réseau
* Envoi d’alertes destinées à vous informer de toute menace touchant le réseau, au niveau des points de terminaison comme à celui du réseau

Nous vous recommandons vivement d’activer Azure Security Center pour tous vos déploiements Azure.

Pour en savoir plus sur Azure Security Center et son activation pour vos déploiements, consultez l’article [Présentation du Centre de sécurité Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Extension sécurisée de votre centre de données dans Azure
De nombreux départements informatiques cherchent à étendre leur infrastructure dans le cloud plutôt que de développer leurs centres de données locaux. Ce faisant, ils étendent l’infrastructure informatique existante dans le cloud public. En tirant parti de différentes options de connectivité entre locaux, il est possible de considérer vos réseaux virtuels Azure comme de simples sous-réseaux au sein de votre infrastructure de réseau local.

Toutefois, cela implique le traitement préalable de divers problèmes liés à la planification et à la conception. Ce traitement est particulièrement important dès lors qu’on parle de sécurité réseau. L’un des meilleurs moyens de comprendre comment aborder ce type de conception consiste à étudier de près un exemple spécifique.

Microsoft a créé le [diagramme d’architecture de référence des extensions de centre de données](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) et la documentation associée pour vous aider à comprendre à quoi ressemble une extension de centre de données de ce type. Il fournit un exemple d’implémentation de référence, que vous pouvez utiliser pour planifier et concevoir une extension de centre de données d’entreprise sécurisée dans le cloud. Nous vous recommandons de consulter ce document pour vous familiariser avec les composants clés d’une solution sécurisée.

Pour en savoir plus sur l’extension sécurisée de votre centre de données dans Azure, consultez notre vidéo portant sur [l’extension de votre centre de données dans Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
