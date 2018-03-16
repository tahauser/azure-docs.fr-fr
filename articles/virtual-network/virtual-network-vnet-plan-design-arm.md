---
title: "Guide de planification et de conception de réseau virtuel Azure | Microsoft Docs"
description: "Découvrez comment planifier et concevoir des réseaux virtuels dans Azure selon vos besoins en isolement, connectivité et emplacements."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: ecdc3a847821fd83718f9cfc42308667460feabc
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="plan-and-design-azure-virtual-networks"></a>Planifier et concevoir des réseaux virtuels Azure
La création d’un réseau virtuel à titre d’essai est assez facile, mais vous risquez de déployer plusieurs réseaux virtuels au fil du temps pour prendre en charge les besoins en production de votre organisation. La planification et la conception vous permettent de déployer des réseaux virtuels et de vous connecter aux ressources dont vous avez besoin plus efficacement. Si vous n’êtes pas familiarisé avec les réseaux virtuels, il est recommandé de [découvrir les réseaux virtuels](virtual-networks-overview.md) et d’apprendre [comment en déployer](quick-create-portal.md) un avant de continuer.

## <a name="plan"></a>Planification
Une bonne compréhension des abonnements Azure, des régions et des ressources réseau est essentielle pour réussir. Vous pouvez utiliser la liste des éléments à prendre en compte ci-dessous comme point de départ. Une fois que vous avez compris ces éléments, vous pouvez définir les conditions requises pour la conception de votre réseau.

### <a name="considerations"></a>Considérations
Avant de répondre aux questions de planification ci-dessous, tenez compte des éléments suivants :

* Tout ce que vous créez dans Azure se compose d’une ou de plusieurs ressources. Une machine virtuelle est une ressource, l’interface de carte réseau utilisée par une machine virtuelle est une ressource, l’adresse IP publique utilisée par une interface de carte réseau est une ressource, le réseau virtuel auquel l’interface de carte réseau est connectée est une ressource.
* Vous créez des ressources au sein d’une [région Azure](https://azure.microsoft.com/regions/#services) et d’un abonnement. Les ressources peuvent être connectées uniquement à un réseau virtuel qui existe dans les mêmes région et abonnement.
* Vous pouvez connecter des réseaux virtuels entre eux comme suit :
    * **[Homologation de réseau virtuel](virtual-network-peering-overview.md)** : les réseaux virtuels doivent se trouver dans la même région Azure. La bande passante entre des ressources figurant dans des réseaux virtuels homologués est la même que si les ressources qui étaient connectées au même réseau virtuel.
    * **Passerelle [VPN Azure](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)** : les réseaux virtuels peuvent exister dans une même région Azure ou dans des régions différentes. La bande passante entre des ressources figurant dans des réseaux virtuels connectés via une passerelle VPN est limitée par la bande passante de la passerelle VPN.
* Vous pouvez connecter des réseaux virtuels à votre réseau local à l’aide de l’une des [options de connectivité](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) disponibles dans Azure.
* Différentes ressources peuvent être regroupées dans des [groupes de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), ce qui facilite la gestion de la ressource en tant qu’unité. Un groupe de ressources peut contenir des ressources provenant de plusieurs régions, tant que les ressources appartiennent au même abonnement.

### <a name="define-requirements"></a>Définir les conditions requises
Utilisez les questions ci-dessous comme point de départ pour la conception de votre réseau Azure.    

1. Quels emplacements Azure allez-vous utiliser pour héberger des réseaux virtuels ?
2. Devez-vous assurer la communication entre ces emplacements Azure ?
3. Devez-vous assurer la communication entre vos réseaux virtuels Azure et vos centres de données locaux ?
4. Combien de machines virtuelles IaaS (Infrastructure as a Service), de rôles de services cloud et d’applications web sont nécessaires pour votre solution ?
5. Devez-vous isoler le trafic en fonction des groupes de machines virtuelles (autrement dit, serveurs web frontaux et serveurs de base de données principaux) ?
6. Devez-vous contrôler le flux du trafic à l’aide d’équipements virtuels ?
7. Est-ce que les utilisateurs ont besoin de différents jeux d’autorisations pour différentes ressources Azure ?

### <a name="understand-vnet-and-subnet-properties"></a>Découvrir les propriétés des réseaux virtuels et sous-réseaux
Les ressources de réseaux virtuels et de sous-réseaux permettent de définir une limite de sécurité pour les charges de travail s’exécutant dans Azure. Un réseau virtuel est caractérisé par une collection d’espaces d’adressage, appelés blocs CIDR.

> [!NOTE]
> Les administrateurs réseau sont familiarisés avec la notation CIDR. Si vous n’êtes pas familiarisé avec CIDR, [obtenez davantage d’informations](http://whatismyipaddress.com/cidr).
>
>

Les réseaux virtuels contiennent les propriétés suivantes.

| Propriété | Description | Contraintes |
| --- | --- | --- |
| **name** |Nom du réseau virtuel |Chaîne de 80 caractères au maximum. Peut contenir des lettres, des chiffres, un trait de soulignement, des points ou des traits d’union. Doit commencer par une lettre ou un chiffre. Doit se terminer par une lettre, un chiffre ou un trait de soulignement. Peut contenir des majuscules ou minuscules. |
| **location** |Emplacement Azure (également appelé région). |Doit être l’un des emplacements Azure valides. |
| **addressSpace** |Collection de préfixes d’adresses qui composent le réseau virtuel dans la notation CIDR. |Doit être un tableau de blocs d’adresses CIDR valides, y compris des plages d’adresses IP publiques. |
| **Sous-réseaux** |Collection des sous-réseaux qui composent le réseau virtuel |consultez le tableau de propriétés des sous-réseaux ci-dessous. |
| **dhcpOptions** |Objet qui contient une seule propriété obligatoire nommée **dnsServers**. | |
| **dnsServers** |Tableau des serveurs DNS utilisés par le réseau virtuel. Si aucun serveur n’est spécifié, la résolution de noms interne Azure est utilisée. |Doit être un tableau de 10 serveurs DNS au maximum, par adresse IP. |

Un sous-réseau est une ressource enfant d’un réseau virtuel, et permet de définir des segments d’espaces d’adressage dans un bloc CIDR, à l’aide de préfixes d’adresses IP. Les cartes d’interface réseau (NIC) peuvent être ajoutées aux sous-réseaux et connectées aux machines virtuelles, ce qui fournit une connectivité pour différentes charges de travail.

Les sous-réseaux contiennent les propriétés suivantes.

| Propriété | Description | Contraintes |
| --- | --- | --- |
| **name** |Nom du sous-réseau |Chaîne de 80 caractères au maximum. Peut contenir des lettres, des chiffres, un trait de soulignement, des points ou des traits d’union. Doit commencer par une lettre ou un chiffre. Doit se terminer par une lettre, un chiffre ou un trait de soulignement. Peut contenir des majuscules ou minuscules. |
| **location** |Emplacement Azure (également appelé région). |Doit être l’un des emplacements Azure valides. |
| **addressPrefix** |Préfixe d’adresse unique qui constitue le sous-réseau dans la notation CIDR |Doit être un bloc CIDR unique qui fait partie de l’un des espaces d’adressage  du réseau virtuel. |
| **networkSecurityGroup** |NSG appliquée au sous-réseau | |
| **routeTable** |Table de routage appliquée au sous-réseau | |
| **ipConfigurations** |Collection d’objets de configuration IP utilisée par la carte réseau connectée au sous-réseau | |

### <a name="name-resolution"></a>Résolution de noms
Par défaut, votre réseau virtuel utilise la [résolution de noms fournie par Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md) pour résoudre les noms à l’intérieur du réseau virtuel et sur l’Internet public. Toutefois, si vous connectez vos réseaux virtuels à vos centres de données locaux, vous devez fournir [votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) pour résoudre les noms entre les réseaux.  

### <a name="limits"></a>limites
Passez en revue les limites de mise en réseau dans l’article sur les [limites Azure](../azure-subscription-service-limits.md#networking-limits) pour vérifier que votre conception n’entre en conflit avec aucune limite. Il est possible d’augmenter certaines limites par le biais d’un ticket d’assistance.

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle
Vous pouvez utiliser le [contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md) pour contrôler le niveau d'accès de différents utilisateurs à différentes ressources dans Azure. De cette façon, vous pouvez isoler le travail effectué par votre équipe en fonction de leurs besoins.

En ce qui concerne les réseaux virtuels, les utilisateurs dans le rôle **Collaborateur de réseau** ont un contrôle total sur les ressources du réseau virtuel Azure Resource Manager. De même, les utilisateurs dans le rôle **Collaborateur de réseau classique** ont un contrôle total sur les ressources du réseau virtuel classique.

> [!NOTE]
> Vous pouvez également [créer vos propres rôles](../active-directory/role-based-access-control-configure.md) pour séparer les besoins administratifs.
>
>

## <a name="design"></a>Conception
Une fois que vous connaissez les réponses aux questions dans la section [Planifier](#Plan) , consultez les rubriques suivantes avant de définir vos réseaux virtuels.

### <a name="number-of-subscriptions-and-vnets"></a>Nombre d’abonnements et de réseaux virtuels
Vous devez envisager de créer plusieurs réseaux virtuels dans les scénarios suivants :

* **Machines virtuelles qui doivent être placées à différents emplacements Azure**. Les réseaux virtuels dans Azure sont régionaux. Ils ne peuvent pas couvrir des emplacements. Par conséquent, vous avez besoin d’au moins un réseau virtuel pour chaque emplacement Azure dans lequel vous voulez héberger des machines virtuelles.
* **Charges de travail qui doivent être totalement isolées les unes des autres**. Vous pouvez créer des réseaux virtuels distincts, qui utilisent même des espaces d’adressage IP identiques, pour isoler différentes charges de travail les unes des autres.

Gardez à l’esprit que les limites affichées ci-dessus sont définies par région, par abonnement. Cela signifie que vous pouvez utiliser plusieurs abonnements pour augmenter la limite des ressources que vous pouvez gérer dans Azure. Vous pouvez utiliser un réseau VPN de site à site, ou un circuit ExpressRoute, pour connecter les réseaux virtuels dans différents abonnements.

### <a name="subscription-and-vnet-design-patterns"></a>Modèles de conception des abonnements et réseaux virtuels
Le tableau ci-dessous présente quelques modèles de conception courants pour l’utilisation des abonnements et réseaux virtuels.

| Scénario | Diagramme | Avantages | Inconvénients |
| --- | --- | --- | --- |
| Abonnement unique, deux réseaux virtuels par application |![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Un seul abonnement à gérer. |Nombre maximal de réseaux virtuels par région Azure. Vous avez besoin d’abonnements supplémentaires après cela. Pour plus d’informations, consultez l’article sur les [limites Azure](../azure-subscription-service-limits.md#networking-limits). |
| Un abonnement par application, deux réseaux virtuels par application |![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Utilise uniquement deux réseaux virtuels par abonnement. |Gestion plus difficile quand il existe un trop grand nombre d’applications. |
| Un abonnement par division, deux réseaux virtuels par application. |![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Équilibre entre le nombre d’abonnements et de réseaux virtuels. |Nombre maximal de réseaux virtuel par division (abonnement). Pour plus d’informations, consultez l’article sur les [limites Azure](../azure-subscription-service-limits.md#networking-limits). |
| Un abonnement par division, deux réseaux virtuels par groupe d’applications. |![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Équilibre entre le nombre d’abonnements et de réseaux virtuels. |Les applications doivent être isolées à l’aide de sous-réseaux et de groupes de sécurité réseau. |

### <a name="number-of-subnets"></a>Nombre de sous-réseaux
Vous devez envisager d’utiliser plusieurs sous-réseaux dans un réseau virtuel dans les scénarios suivants :

* **Pas suffisamment d’adresses IP privées pour toutes les cartes réseau dans un sous-réseau**. Si l’espace d’adressage de votre sous-réseau ne contient pas suffisamment d’adresses IP pour le nombre de cartes réseau dans le sous-réseau, vous devez créer plusieurs sous-réseaux. Gardez à l’esprit qu’Azure réserve 5 adresses IP privées de chaque sous-réseau qui ne peuvent pas être utilisées : les première et dernière adresses de l’espace d’adressage (pour l’adresse de sous-réseau et la multidiffusion) et 3 adresses à utiliser en interne (pour DHCP et DNS).
* **Sécurité**. Vous pouvez utiliser des sous-réseaux pour séparer les groupes de machines virtuelles les uns des autres pour les charges de travail qui ont une structure multicouche, et appliquer différents [groupes de sécurité réseau](virtual-networks-nsg.md#subnets) pour ces sous-réseaux.
* **Connectivité hybride**. Vous pouvez utiliser des passerelles VPN et circuits ExpressRoute pour [connecter](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) vos réseaux virtuels entre eux et à vos centres de données locaux. Les passerelles VPN et les circuits ExpressRoute nécessitent la création d’un sous-réseau qui leur est propre.
* **Équipements virtuels**. Vous pouvez utiliser un équipement virtuel, comme un pare-feu, un accélérateur WAN ou une passerelle VPN, dans un réseau virtuel Azure. Quand vous procédez ainsi, vous devez [acheminer le trafic](virtual-networks-udr-overview.md) vers ces équipements et les isoler dans leur propre sous-réseau.

### <a name="subnet-and-nsg-design-patterns"></a>Modèles de conception des sous-réseaux et groupes de sécurité réseau
Le tableau ci-dessous présente quelques modèles de conception courants pour l’utilisation des sous-réseaux.

| Scénario | Diagramme | Avantages | Inconvénients |
| --- | --- | --- | --- |
| Sous-réseau unique, groupes de sécurité réseau par couche d’application, par application |![Sous-réseau unique](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Un seul sous-réseau à gérer. |Plusieurs groupes de sécurité réseau nécessaires pour isoler chaque application. |
| Un sous-réseau par application, groupes de sécurité réseau par couche d’application |![Sous-réseau par application](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Moins de groupes de sécurité réseau à gérer. |Plusieurs sous-réseaux à gérer. |
| Un sous-réseau par couche d’application, groupes de sécurité réseau par application |![Sous-réseau par couche](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Équilibre entre le nombre de sous-réseaux et de groupes de sécurité réseau. |Nombre maximal de groupes de sécurité réseau par abonnement. Pour plus d’informations, consultez l’article sur les [limites Azure](../azure-subscription-service-limits.md#networking-limits). |
| Un sous-réseau par couche d’application, par application, groupes de sécurité réseau par sous-réseau |![Sous-réseau par couche et par application](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Nombre éventuellement inférieur de groupes de sécurité réseau. |Plusieurs sous-réseaux à gérer. |

## <a name="sample-design"></a>Exemple de conception
Pour illustrer l’application des informations de cet article, envisagez le scénario suivant.

Vous travaillez pour une société qui possède 2 centres de données en Amérique du Nord et deux centres de données en Europe. Vous avez identifié 6 applications clientes différentes gérées par 2 divisions différentes que vous souhaitez migrer vers Azure comme projet pilote. L’architecture de base pour les applications est la suivante :

* App1, App2, App3 et App4 sont des applications web hébergées sur des serveurs Linux exécutant Ubuntu. Chaque application se connecte à un serveur d’applications distinct qui héberge les services RESTful sur des serveurs Linux. Les services RESTful se connectent à une base de données MySQL principale.
* App5 et App6 sont des applications web hébergées sur des serveurs Windows exécutant Windows Server 2012 R2. Chaque application se connecte à une base de données SQL Server principale.
* Toutes les applications sont actuellement hébergées dans l’un des centres de données de la société en Amérique du Nord.
* Les centres de données locaux utilisent l’espace d’adressage 10.0.0.0/8.

Vous devez concevoir une solution de réseau virtuel qui remplit les conditions suivantes :

* Aucune division ne doit être affectée par la consommation de ressources d’autres divisions.
* Vous devez réduire la quantité de réseaux virtuels et sous-réseaux pour faciliter la gestion.
* Pour chaque division, un seul réseau virtuel de test/développement doit être utilisé pour toutes les applications.
* Chaque application est hébergée dans 2 centres de données Azure différents par continent (Amérique du Nord et Europe).
* Chaque application est totalement isolée des autres.
* Chaque application est accessible aux clients sur Internet via HTTP.
* Chaque application est accessible aux utilisateurs connectés aux centres de données locaux à l’aide d’un tunnel chiffré.
* La connexion aux centres de données locaux doit utiliser des appareils VPN existants.
* Le groupe réseau de la société doit avoir un contrôle total sur la configuration de réseau virtuel.
* Les développeurs dans chaque division doivent uniquement pouvoir déployer des machines virtuelles vers des sous-réseaux existants.
* Toutes les applications sont migrées telles quelles vers Azure (modèle « lift-and-shift »).
* Les bases de données dans chaque emplacement doivent être répliquées vers d’autres emplacements Azure une fois par jour.
* Chaque application doit utiliser 5 serveurs web frontaux, 2 serveurs d’applications (si nécessaire) et 2 serveurs de base de données.

### <a name="plan"></a>Planification
Vous devez commencer la planification de votre conception en répondant à la question dans la section [Définir les conditions requises](#Define-requirements) comme indiqué ci-dessous.

1. Quels emplacements Azure allez-vous utiliser pour héberger des réseaux virtuels ?

    2 emplacements en Amérique du Nord et 2 emplacements en Europe. Vous devez les choisir selon l’emplacement physique de vos centres de données locaux existants. Votre connexion depuis vos emplacements physiques vers Azure aura ainsi une meilleure latence.
2. Devez-vous assurer la communication entre ces emplacements Azure ?

    Oui. Étant donné que les bases de données doivent être répliquées sur tous les emplacements.
3. Devez-vous assurer la communication entre vos réseaux virtuels Azure et vos centres de données locaux ?

    Oui. Étant donné que les utilisateurs connectés aux centres de données locaux doivent être en mesure d’accéder aux applications via un tunnel chiffré.
4. Combien de machines virtuelles IaaS sont nécessaires pour votre solution ?

    200 machines virtuelles IaaS. App1, App2, App3 et App4 nécessitent 5 serveurs web chacun, 2 serveurs d’applications chacun et 2 serveurs de base de données chacun. Ce qui fait un total de 9 machines virtuelles IaaS par application ou 36 machines virtuelles IaaS. App5 et App6 nécessitent 5 serveurs web et 2 serveurs de base de données chacun. Ce qui fait un total de 7 machines virtuelles IaaS par application ou 14 machines virtuelles IaaS. Par conséquent, vous avez besoin de 50 machines virtuelles IaaS pour toutes les applications dans chaque région Azure. Étant donné que nous devons utiliser 4 régions, 200 machines virtuelles IaaS sont nécessaires.

    Vous devez également fournir des serveurs DNS dans chaque réseau virtuel ou dans vos centres de données locaux pour résoudre le nom entre les machines virtuelles IaaS Azure et le réseau local.
5. Devez-vous isoler le trafic en fonction des groupes de machines virtuelles (autrement dit, serveurs web frontaux et serveurs de base de données principaux) ?

    Oui. Chaque application doit être totalement isolée des autres, et chaque couche d’application doit également être isolée.
6. Devez-vous contrôler le flux du trafic à l’aide d’équipements virtuels ?

    Non. Les équipements virtuels permettent de mieux contrôler le flux du trafic, notamment une consignation de plan de données plus détaillée.
7. Est-ce que les utilisateurs ont besoin de différents jeux d’autorisations pour différentes ressources Azure ?

    Oui. L’équipe réseau a besoin d’un contrôle total sur les paramètres de réseau virtuel, tandis que les développeurs doivent uniquement pouvoir déployer des machines virtuelles vers des sous-réseaux préexistants.

### <a name="design"></a>Conception
Vous devez suivre la conception spécifiant les abonnements, les réseaux virtuels, les sous-réseaux et les groupes de sécurité réseau. Nous abordons le sujet ici, mais vous devez en savoir plus sur les [groupes de sécurité réseau](virtual-networks-nsg.md) avant de terminer votre conception.

**Nombre d’abonnements et de réseaux virtuels**

Les conditions requises suivantes sont associées aux abonnements et réseaux virtuels :

* Aucune division ne doit être affectée par la consommation de ressources d’autres divisions.
* Vous devez réduire la quantité de réseaux virtuels et sous-réseaux.
* Pour chaque division, un seul réseau virtuel de test/développement doit être utilisé pour toutes les applications.
* Chaque application est hébergée dans 2 centres de données Azure différents par continent (Amérique du Nord et Europe).

En fonction de ces conditions requises, vous avez besoin d’un abonnement pour chaque division. De cette façon, la consommation des ressources d’une division n’est pas prise en compte pour déterminer les limites d’autres divisions. En outre, comme vous souhaitez réduire le nombre de réseaux virtuels, vous devez envisager d’utiliser le modèle **un abonnement par division, deux réseaux virtuels par groupe d’applications** comme indiqué ci-dessous.

![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Vous devez également spécifier l’espace d’adressage pour chaque réseau virtuel. Étant donné que vous avez besoin de connectivité entre les centres de données locaux et les régions Azure, l’espace d’adressage utilisé pour les réseaux virtuels Azure ne peut pas entrer en conflit avec le réseau local, et l’espace d’adressage utilisé par chaque réseau virtuel ne doit pas entrer en conflit avec d’autres réseaux virtuels existants. Vous pouvez utiliser les espaces d’adressage dans le tableau ci-dessous pour remplir ces conditions requises.  

| **Abonnement** | **Réseau virtuel** | **Région Azure** | **Espace d’adressage** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |États-Unis de l’Ouest |172.16.0.0/16 |
| BU1 |ProdBU1US2 |Est des États-Unis |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Europe du Nord |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Europe de l'Ouest |172.19.0.0/16 |
| BU1 |TestDevBU1 |États-Unis de l’Ouest |172.20.0.0/16 |
| BU2 |TestDevBU2 |États-Unis de l’Ouest |172.21.0.0/16 |
| BU2 |ProdBU2US1 |États-Unis de l’Ouest |172.22.0.0/16 |
| BU2 |ProdBU2US2 |Est des États-Unis |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Europe du Nord |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Europe de l'Ouest |172.25.0.0/16 |

**Nombre de sous-réseaux et de groupes de sécurité réseau**

Les conditions requises suivantes sont associées aux sous-réseaux et groupes de sécurité réseau :

* Vous devez réduire la quantité de réseaux virtuels et sous-réseaux.
* Chaque application est totalement isolée des autres.
* Chaque application est accessible aux clients sur Internet via HTTP.
* Chaque application est accessible aux utilisateurs connectés aux centres de données locaux à l’aide d’un tunnel chiffré.
* La connexion aux centres de données locaux doit utiliser des appareils VPN existants.
* Les bases de données dans chaque emplacement doivent être répliquées vers d’autres emplacements Azure une fois par jour.

En fonction de ces conditions requises, vous pouvez utiliser un seul sous-réseau par couche d’application et utiliser des groupes de sécurité réseau pour filtrer le trafic par application. De cette façon, vous avez uniquement 3 sous-réseaux dans chaque réseau virtuel (frontal, couche d’application et couche de données) et un groupe de sécurité réseau par application et par sous-réseau. Dans ce cas, vous devez envisager d’utiliser le modèle de conception **un sous-réseau par couche d’application, groupes de sécurité réseau par application** . La figure ci-dessous illustre l’utilisation du modèle de conception représentant le réseau virtuel **ProdBU1US1** .

![Un sous-réseau par couche, un groupe de sécurité réseau par application et par couche](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Toutefois, vous devez également créer un sous-réseau supplémentaire pour la connectivité VPN entre les réseaux virtuels et centres de données locaux. Vous devez également spécifier l’espace d’adressage pour chaque sous-réseau. La figure ci-dessous illustre un exemple de solution pour le réseau virtuel **ProdBU1US1** . Vous pouvez répliquer ce scénario pour chaque réseau virtuel. Chaque couleur représente une application différente.

![Exemple de réseau virtuel](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Contrôle d’accès**

Les conditions requises suivantes sont associées au contrôle d’accès :

* Le groupe réseau de la société doit avoir un contrôle total sur la configuration de réseau virtuel.
* Les développeurs dans chaque division doivent uniquement pouvoir déployer des machines virtuelles vers des sous-réseaux existants.

En fonction de ces conditions requises, vous pouvez ajouter des utilisateurs de l’équipe réseau au rôle intégré **Collaborateur de réseau** dans chaque abonnement et créer un rôle personnalisé pour les développeurs d’applications dans chaque abonnement en leur donnant les droits d’ajouter des machines virtuelles aux sous-réseaux existants.

## <a name="next-steps"></a>Étapes suivantes
* [Déployer un réseau virtuel](virtual-networks-create-vnet-arm-template-click.md) selon un scénario.
* Découvrir comment [équilibrer la charge](../load-balancer/load-balancer-overview.md) des machines virtuelles IaaS et [gérer le routage entre plusieurs régions Azure](../traffic-manager/traffic-manager-overview.md).
* En savoir plus sur les [groupes de sécurité réseau et comment planifier et concevoir](virtual-networks-nsg.md) une solution de groupe de sécurité réseau.
* En savoir plus sur vos [options de connectivité de réseau virtuel et entre locaux](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).
