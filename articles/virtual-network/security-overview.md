---
title: "Présentation de la sécurité réseau Azure | Microsoft Docs"
description: "En savoir plus sur les options de sécurité qui contrôlent le flux de trafic réseau entre les ressources Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: fbf0556cc47bc08a71fcf050b43c2dbbe5d27184
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="network-security"></a>Sécurité du réseau

Vous pouvez limiter le trafic réseau vers les ressources d’un réseau virtuel à l’aide d’un groupe de sécurité réseau. Un groupe de sécurité réseau contient une liste de règles de sécurité qui autorisent ou refusent le trafic réseau entrant ou sortant en fonction de l’adresse IP source ou de destination, du port et du protocole. 

## <a name="network-security-groups"></a>Groupes de sécurité réseau

Chaque interface réseau est associée à zéro ou un groupe de sécurité réseau. Chaque interface réseau existe dans un sous-réseau de [réseau virtuel](virtual-networks-overview.md). Un sous-réseau peut aussi être associé à zéro ou un groupe de sécurité réseau. 

Quand elles sont appliquées à un sous-réseau, les règles de sécurité sont appliquées à toutes les ressources dans le sous-réseau. En plus des interfaces réseau, vous pouvez avoir des instances d’autres services Azure, tels que HDInsight, des groupes de machines virtuelles identiques et des environnements de service d’application déployés dans le sous-réseau.

L’application des groupes de sécurité réseau lorsqu’ils sont associés à la fois une interface réseau et au sous-réseau hébergeant l’interface réseau fonctionne de la façon suivante :

- **Trafic entrant** : le groupe de sécurité réseau associé au sous-réseau de l’interface réseau est évalué en premier. Tout trafic autorisé via le groupe de sécurité réseau associé au sous-réseau est ensuite évalué par le groupe de sécurité réseau associé à l’interface réseau. Par exemple, vous pouvez avoir besoin d’un accès entrant sur une machine virtuelle via le port 80 à partir d’Internet. Si vous associez un groupe de sécurité réseau à l’interface réseau et au sous-réseau hébergeant l’interface réseau, le groupe de sécurité réseau associé au sous-réseau et à l’interface réseau doit autoriser le port 80. Si vous avez uniquement autorisé le port 80 via le groupe de sécurité réseau associé au sous-réseau ou à l’interface réseau, la communication échoue en raison des règles de sécurité par défaut. Pour plus de détails, voir les [règles de sécurité par défaut](#default-security-rules). Si vous avez uniquement appliqué un groupe de sécurité réseau au sous-réseau ou à l’interface réseau, que le groupe de sécurité réseau contient une règle qui autorise le trafic entrant sur le port 80 par exemple, la communication réussit. 
- **Trafic sortant** : le groupe de sécurité réseau associé à l’interface réseau est évalué en premier. Tout trafic autorisé via le groupe de sécurité réseau associé à l’interface réseau est ensuite évalué par le groupe de sécurité réseau associé au sous-réseau.

Vous ne pouvez pas toujours savoir si les groupes de sécurité réseau sont appliquées à la fois à une interface réseau et à un sous-réseau. Vous pouvez facilement afficher des règles d’agrégation appliquées à une interface réseau en consultant les [règles de sécurité efficaces](virtual-network-manage-nsg-arm-portal.md) relatives à une interface réseau. Vous pouvez également utiliser la fonctionnalité de [vérification du flux IP](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dans Azure Network Watcher pour déterminer si la communication est autorisée vers ou à partir d’une interface réseau. L’outil vous indique si la communication est autorisée, ainsi que la règle de sécurité réseau qui autorise ou refuse le trafic.
 
> [!NOTE]
> Les groupes de sécurité réseau sont associés à des sous-réseaux ou à des machines virtuelles et services cloud déployés dans le modèle de déploiement classique, plutôt qu’à des interfaces réseau dans le modèle de déploiement du Gestionnaire de ressources. Pour en savoir plus sur les modèles de déploiement Azure, consultez l’article [Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le même groupe de sécurité réseau est applicable à toutes les interfaces réseau individuelles et à tous les sous-réseaux que vous souhaitez.

## <a name="security-rules"></a>Règles de sécurité

Un groupe de sécurité réseau contient le nombre des règles souhaité (ou aucune), dans les [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) de l’abonnement Azure. Chaque règle spécifie les propriétés suivantes :

|Propriété  |Explication  |
|---------|---------|
|NOM|Nom unique au sein du groupe de sécurité réseau.|
|Priorité | Nombre compris entre 100 et 4096. Les règles sont traitées dans l’ordre croissant, car les nombres les plus faibles sont prioritaires. Une fois que le trafic correspond à une règle, le traitement s’arrête. Par conséquent, les règles avec des priorités plus faibles (des nombres plus élevés) et ayant les mêmes attributs que les règles de priorité supérieure ne sont pas traitées.|
|Source ou destination| Tout, ou adresse IP individuelle, bloc CIDR (10.0.0.0/24, par exemple), balise de service ou groupe de sécurité d’application. En savoir plus sur les [balises de service](#service-tags) et les [groupes de sécurité d’application](#application-security-groups). En spécifiant une plage, une balise de service ou un groupe de sécurité d’application, vous pouvez créer moins des règles de sécurité. La possibilité de spécifier plusieurs adresses IP individuelles et plages (vous ne pouvez pas spécifier plusieurs balises de service ou groupes d’applications) dans une règle est désignée sous le nom de règles de sécurité augmentée. Pour plus d’informations sur les règles de sécurité augmentée, consultez [cet article](#augmented-security-rules). Les règles de sécurité augmentée peuvent uniquement être créées dans des groupes de sécurité réseau créés par le biais du modèle de déploiement du Gestionnaire de ressources. Vous ne pouvez pas spécifier plusieurs adresses IP et plages d’adresses IP dans les groupes de sécurité réseau créés par le biais du modèle de déploiement classique.|
|Protocole     | TCP, UDP ou Tout, ce qui inclut TCP, UDP et ICMP. Vous ne pouvez pas spécifier ICMP seul. Si vous avez besoin d’ICMP, vous devez donc utiliser Tout. |
|Direction| Indique si la règle s’applique au trafic entrant ou sortant.|
|Plage de ports     |Vous pouvez spécifier un port individuel ou une plage de ports. Par exemple, indiquez 80 ou 10000-10005. La spécification de plages vous permet de créer moins de règles de sécurité. Les règles de sécurité augmentée peuvent uniquement être créées dans des groupes de sécurité réseau créés par le biais du modèle de déploiement du Gestionnaire de ressources. Vous ne pouvez pas spécifier plusieurs ports ou plages de ports dans les groupes de sécurité réseau créés par le biais du modèle de déploiement classique.   |
|Action     | Autoriser ou refuser        |

Les règles de sécurité sont avec état. Si vous spécifiez une règle de sécurité sortante vers n’importe quelle adresse sur le port 80, par exemple, il n’est pas nécessaire d’indiquer une règle de sécurité entrante pour la réponse au trafic sortant. Vous devez uniquement spécifier une règle de sécurité entrante si la communication est établie en externe. Le contraire est également vrai. Si le trafic entrant est autorisé sur un port, il n’est pas nécessaire de spécifier une règle de sécurité sortante pour répondre au trafic sur ce port. Pour en savoir plus sur les limites lors de la création de règles de sécurité, vérifiez les [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Règles de sécurité augmentée

Les règles de sécurité augmentée simplifient la définition de la sécurité pour les réseaux virtuels, ce qui vous permet de définir des stratégies de sécurité réseau plus vastes et plus complexes, avec moins de règles. Vous pouvez combiner plusieurs ports, adresses IP explicites, balises de service et groupes de sécurité d’application dans une seule règle de sécurité facilement compréhensible. Utiliser des règles de sécurité augmentée dans les champs de la source, de la destination et du port d’une règle. Lorsque vous créez une règle, vous pouvez spécifier plusieurs adresses IP explicites, plages CIDR et ports. Pour simplifier la maintenance de votre définition de règle de sécurité, combinez des règles de sécurité augmentée avec des balises de service ou des groupes de sécurité d’application. 

Pour en savoir plus sur les limites lors de la création de règles de sécurité augmentée, vérifiez les [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="default-security-rules"></a>Règles de sécurité par défaut

Si un groupe de sécurité réseau n’est pas associé à une interface réseau ou à un sous-réseau, tout le trafic entrant ou sortant est autorisé vers et à partir du sous-réseau ou de l’interface réseau. Dès qu’un groupe de sécurité réseau est créé, Azure crée les règles par défaut suivantes dans celui-ci :

### <a name="inbound"></a>Trafic entrant

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorité|Source|Ports source|Destination|Ports de destination|Protocole|Access|
|---|---|---|---|---|---|---|
|65 000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Tous|AUTORISER|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorité|Source|Ports source|Destination|Ports de destination|Protocole|Access|
|---|---|---|---|---|---|---|
|65 001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Tous|AUTORISER|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priorité|Source|Ports source|Destination|Ports de destination|Protocole|Access|
|---|---|---|---|---|---|---|
|65 500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Tous|Deny|

### <a name="outbound"></a>Règle de trafic sortant

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 65 000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Tous | AUTORISER |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 65 001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Tous | AUTORISER |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 65 500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Tous | Deny |

Dans les colonnes **Source** et **Destination**, *VirtualNetwork*, *AzureLoadBalancer* et *Internet* sont des [balises de service](#tags), non des adresses IP. Dans la colonne de protocole, **Tout** englobe TCP, UDP et ICMP. Lorsque vous créez une règle, vous pouvez spécifier TCP, UDP ou Tout, mais vous ne pouvez pas indiquer uniquement ICMP. Par conséquent, si votre règle requiert ICMP, vous devez sélectionner *Tout* comme protocole. *0.0.0.0/0* dans les colonnes **Source** et **Destination** représente toutes les adresses.
 
Vous ne pouvez pas supprimer les règles par défaut, mais vous pouvez les remplacer par des règles de priorité plus élevée.

## <a name="service-tags"></a>Balises de service

 Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité. Vous ne peut pas créer votre propre balise de service, ni spécifier les adresses IP incluses dans une balise. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. Vous pouvez utiliser les balises de service suivantes dans la définition de règle de sécurité. Leurs noms varient légèrement selon les [modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (Gestionnaire des ressources) (**VIRTUAL_NETWORK** pour le mode Classic) : cette balise inclut l’espace d’adressage du réseau virtuel (toutes les plages CIDR définies pour le réseau virtuel), tous les espaces d’adressage locaux connectés, ainsi que les réseaux virtuels [appairés](virtual-network-peering-overview.md) ou réseaux virtuels connectés à une [passerelle de réseau virtuel](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** pour Classic) : cette balise désigne l’équilibreur de charge de l’infrastructure Azure. Elle est convertie en l’[adresse IP de centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) de l’emplacement d’où proviennent les sondes d’intégrité d’Azure. Vous pouvez remplacer cette règle si vous n’utilisez pas l’équilibreur de charge Azure.
* **Internet** (Resource Manager) (**INTERNET** pour Classic) : cette balise indique l’espace d’adressage IP qui se trouve en dehors du réseau virtuel et est accessible par l’Internet public. La plage d’adresse inclut l’[espace de l’adresse IP public d’Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (Resource Manager uniquement) : cette balise désigne l’espace d’adressage IP pour les adresses IP de sonde Microsoft Azure Traffic Manager. Vous trouverez plus d’informations sur les adresses IP de sonde Traffic Manager dans le [Forum Aux Questions (FAQ) relatif à Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-faqs).
* **Storage** (Resource Manager uniquement) : cette balise désigne l’espace d’adressage IP pour le service Azure Storage. Si vous spécifiez *Storage* pour la valeur, le trafic est autorisé ou refusé vers le stockage. Si vous souhaitez uniquement autoriser l’accès au stockage dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez préciser la région. Par exemple, si vous souhaitez autoriser l’accès à Azure Storage uniquement dans la région Est des États-Unis, vous pouvez spécifier *Storage.EastUS* comme balise de service. La balise représente le service, mais pas des instances du service. Par exemple, la balise représente le service Azure Storage, mais pas un compte Azure Storage spécifique.
* **Sql** (Resource Manager uniquement) : cette balise désigne les préfixes d’adresse des services Azure SQL Database et Azure SQL Data Warehouse. Si vous spécifiez *Sql* pour la valeur, le trafic vers Sql est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à Sql dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez préciser la région. Par exemple, si vous souhaitez autoriser l’accès à Azure SQL Database uniquement dans la région Est des États-Unis, vous pouvez spécifier *Sql.EastUS* comme balise de service. La balise représente le service, mais pas des instances du service. Par exemple, la balise représente le service Azure SQL Database, mais pas une base de données ou un serveur SQL spécifique.

> [!NOTE]
> Si vous implémentez un [point de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md) pour un service, par exemple Stockage Azure ou Azure SQL Database, Azure ajoute un itinéraire vers un sous-réseau de réseau virtuel pour le service. Les préfixes d’adresse de l’itinéraire sont les mêmes préfixes d’adresse ou plages CIDR que pour la balise de service correspondante.

## <a name="application-security-groups"></a>Groupes de sécurité d’application

Les groupes de sécurité d’application permettent de configurer la sécurité réseau comme un prolongement naturel de la structure de l’application, et donc de regrouper les machines virtuelles et définir des stratégies de sécurité réseau basés sur ces groupes. Cette fonctionnalité vous permet de réutiliser votre stratégie de sécurité à grande échelle sans maintenance manuelle d’adresses IP explicites. La plateforme gère la complexité des adresses IP explicites et plusieurs ensembles de règles, ce qui vous permet de vous concentrer sur la logique métier.

Vous pouvez spécifier un groupe de sécurité d’application en tant que source et destination dans une règle de sécurité. Une fois que votre stratégie de sécurité est définie, vous pouvez créer des machines virtuelles et attribuer les interfaces réseau sur la machine virtuelle à un groupe de sécurité d’application. La stratégie est appliquée en fonction de l’appartenance de groupe de sécurité d’application de chaque interface réseau sur une machine virtuelle. L’exemple suivant illustre comment vous pouvez utiliser un groupe de sécurité d’application pour tous les serveurs web de votre abonnement :

1. Créez un groupe de sécurité d’application nommé *WebServers*.
2. Créez un groupe de sécurité réseau nommé *MyNSG*.
3. Créez une règle de sécurité entrante dans le groupe de sécurité réseau, en spécifiant la balise de service *Internet* pour l’adresse de la source et le groupe de sécurité d’application *WebServers* en tant qu’adresse de destination. Autorisez les ports 80 et 443.
4. Déployer une machine virtuelle exécutant une application de serveur web. Spécifiez que l’interface réseau sur la machine virtuelle est membre du groupe de sécurité d’application *WebServers*. Les ports 80 et 443 sont alors autorisés pour la machine virtuelle. Ces ports sont également autorisés pour tous les serveurs web vous créerez par la suite comme membres du groupe de sécurité d’application *WebServers*. 

Si vous créez d’autres règles, en spécifiant d’autres groupes de sécurité d’application comme destination, ces règles ne sont pas appliquées aux serveurs web, dans l’exemple précédent. Les règles spécifiant un groupe de sécurité d’application sont appliquées uniquement à des interfaces réseau qui sont membres du groupe de sécurité d’application. Les groupes de sécurité d’application, combinés avec les règles de sécurité augmentée et les balises de service, permettent de créer un nombre minimal de groupes de sécurité réseau pour gérer la sécurité du réseau au sein de votre abonnement.
 
Pour en savoir plus sur les limites lors de la création de groupes de sécurité d’application et lors de leur spécification dans les règles de sécurité, consultez les [limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Les groupes de sécurité d’application sont disponibles dans la version préliminaire. Les fonctionnalités de la version préliminaire n’offrent pas le même niveau de disponibilité et de fiabilité que les fonctionnalités de la version générale. Avant d’utiliser des groupes de sécurité d’application, vous devez vous inscrire pour les utiliser en suivant les étapes 1 à 5 de la section Azure ou PowerShell dans le didacticiel de [création d’un groupe de sécurité réseau avec des groupes de sécurité d’application](create-network-security-group-preview.md). Les groupes de sécurité d’application ont les contraintes suivantes :

-   Toutes les interfaces réseau au sein d’un groupe de sécurité d’application doivent exister dans le même réseau virtuel. Vous ne pouvez pas ajouter d’interfaces réseau à partir de différents réseaux virtuels au même groupe de sécurité d’application. Le réseau virtuel dans lequel la première interface réseau assignée au groupe de sécurité d’application se trouve définit le réseau virtuel dans lequel toutes les interfaces réseau assignées par la suite doivent se trouver.
- Si vous spécifiez des groupes de sécurité d’application en tant que source et destination dans une règle de sécurité, les interfaces réseau dans les deux groupes de sécurité d’application doivent se trouver dans le même réseau virtuel. Par exemple, si ASG1 contient des interfaces réseau de VNet1 et si ASG2 contient des interfaces réseau de VNet2, vous ne pouvez pas assigner ASG1 en tant que source et ASG2 en tant que destination dans une règle. Toutes les interfaces réseaux doivent se trouver dans VNet1.

## <a name="azure-platform-considerations"></a>Considérations relatives à la plateforme Azure

- **Adresse IP virtuelle du nœud hôte :** des services d’infrastructure de base tels que DHCP, DNS et l’analyse du fonctionnement sont fournis par le biais des adresses IP d’hôte virtualisé 168.63.129.16 et 169.254.169.254. Ces adresses IP publiques appartiennent à Microsoft et sont les seules adresses IP virtualisées utilisées dans toutes les régions à cet effet. Ces adresses mappent vers l’adresse IP physique de l’ordinateur (nœud hôte) du serveur qui héberge la machine virtuelle. Le nœud hôte agit en tant que relais DHCP, le programme de résolution récursif DNS et la sonde source de la sonde d’intégrité de l’équilibreur de charge et de la sonde d’intégrité de la machine. Les communications vers ces adresses IP ne constituent pas une attaque. Si vous bloquez le trafic vers ou à partir de ces adresses IP, une machine virtuelle ne peut pas fonctionner correctement.
- **Gestion des licences (service de gestion de clés)** : les images Windows en cours d’exécution sur les machines virtuelles doivent être acquises sous licence. Pour assurer la gestion des licences, une requête est envoyée aux serveurs hôtes du Service de gestion de clés qui gèrent les requêtes de ce type. La requête est effectuée en sortie par le biais du port 1688. Cette règle de plateforme est désactivée pour les déploiements utilisant la configuration [itinéraire par défaut 0.0.0.0/0](virtual-networks-udr-overview.md#default-route).
- **Machines virtuelles dans des pools d’équilibrage de charge** : les plages de ports et d’adresses appliquées proviennent de l’ordinateur d’origine, pas l’équilibreur de charge. La plage de ports et d’adresses de destination sont ceux de l’ordinateur de destination, et non de l’équilibreur de charge.
- **Instances de service Azure** : les instances de plusieurs services Azure, tels que HDInsight, les environnements de service d’application et les groupes de machines virtuelles identiques, sont déployées dans des sous-réseaux du réseau virtuel. Pour obtenir la liste complète des services que vous pouvez déployer sur des réseaux virtuels, consultez [Réseau virtuel pour les services Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Familiarisez-vous avec les exigences de port pour chaque service avant d’appliquer un groupe de sécurité réseau au sous-réseau dans lequel la ressource est déployée. Si vous refusez les ports requis par le service, ce dernier ne fonctionnera pas correctement.
- **Envoi de courrier sortant** : Microsoft vous recommande l’utilisation de services de relais authentifiés SMTP (généralement connectés via le port TCP 587, mais parfois via d’autres ports) pour envoyer des courriers électroniques depuis des machines virtuelles Azure. Les services de relais SMTP se spécialisent dans la réputation des expéditeurs, afin de minimiser les risques de renvoi de messages de la part de fournisseurs de messagerie tiers. Ce type de services de relais SMTP incluent, sans s’y limiter, Exchange Online Protection et SendGrid. L’utilisation de services de relais SMTP n’est en aucun cas limitée dans Azure et ne tient pas compte de votre type d’abonnement. 

  Si vous avez créé votre abonnement Azure avant le 15 novembre 2017, vous pouvez, en plus d’utiliser des services de relais SMTP, envoyer des messages électroniques via le port TCP 25 directement. Si vous avez créé votre abonnement après le 15 novembre 2017, vous ne serez peut-être pas en mesure d’envoyer des messages électroniques via le port TCP 25 directement. Le comportement des communications sortantes via le port 25 dépend de votre type d’abonnement :

     - **Contrat Entreprise** : Les communications sortantes via le port 25 sont autorisées. Vous êtes en mesure d’envoyer du courrier sortant directement depuis les machines virtuelles vers des fournisseurs de messagerie électronique externes, sans limitations de la plateforme Azure. 
     - **Paiement à l’utilisation** : Les communications sortantes via le port 25 sont bloquées vers toutes les ressources. Si vous devez envoyer des courriers électroniques directement depuis une machine virtuelle vers des fournisseurs de messagerie électronique externes (sans utiliser des relais SMTP authentifiés), vous pouvez effectuer une requête pour retirer la restriction. Les demandes sont étudiées et acceptées par Microsoft. Elles ne sont accordées qu’après des vérifications antifraude. Pour effectuer une requête, ouvrez un cas d’assistance avec pour type de problème *Technique*, *Connectivité du réseau virtuel*, *Envoi de messages électroniques impossible (SMTP/Port 25)*. Dans votre cas d’assistance, indiquez les raisons pour lesquelles votre abonnement doit être en mesure d’envoyer des courriers électroniques directement aux fournisseurs, sans passer par un relais authentifié SMTP. Si votre abonnement est exempté, seules les machines virtuelles créées après la date d’exemption sont en mesure d’utiliser des communications sortantes via le port 25.
     - **Fournisseur de services cloud (CSP), MSDN, Pass Azure, Azure dans Open, Éducation, BizSpark et essai gratuit** : les communications sortantes via le port 25 sont bloquées pour toutes les ressources. Aucune demande pour retirer la restriction ne peut être faite. Elles ne sont pas autorisées. Si vous devez envoyer des courriers électroniques depuis votre machine virtuelle, vous devez utiliser un service de relais SMTP.

  Si Azure vous permet d’envoyer des courriers électroniques via le port 25, Microsoft ne peut vous garantir que les fournisseurs de messagerie électronique accepteront le message entrant en provenance de votre machine virtuelle. Si un fournisseur spécifique rejette les messages en provenance de votre machine virtuelle, vous devez vous arranger directement avec le fournisseur pour résoudre tout problème de livraison de messages ou de filtrage de spam, ou bien utiliser un service de relais SMTP authentifié. 


## <a name="next-steps"></a>Étapes suivantes

* Suivre le didacticiel [Créer un groupe de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md)
* Suivre le didacticiel [Créer un groupe de sécurité réseau avec des groupes de sécurité d’application](create-network-security-group-preview.md)
