---
title: "Routage de trafic de réseaux virtuels Azure | Microsoft Docs"
description: "Découvrez comment Azure achemine le trafic de réseau virtuel, et comment vous pouvez personnaliser le routage d’Azure."
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
ms.date: 10/26/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: d05492425381649a7893b872c4b1c49e9f241b50
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="virtual-network-traffic-routing"></a>Routage du trafic de réseau virtuel

En savoir plus sur la façon dont Azure achemine le trafic entre Azure, sur site et les ressources Internet. Azure crée automatiquement une table de routage pour chaque sous-réseau au sein d’un réseau virtuel Azure et y ajoute les itinéraires par défaut du système. Pour plus d’informations sur les réseaux virtuels et les sous-réseaux, voir [Présentation du réseau virtuel](virtual-networks-overview.md). Vous pouvez remplacer certains des itinéraires système d’Azure avec des [itinéraires personnalisés](#custom-routes), et ajouter des itinéraires personnalisés supplémentaires aux tables de routage. Azure achemine le trafic sortant à partir d’un sous-réseau selon les itinéraires disponibles dans la table de routage d’un sous-réseau.

## <a name="system-routes"></a>Itinéraires système

Azure crée des itinéraires système automatiquement et assigne les itinéraires pour chaque sous-réseau dans un réseau virtuel. Vous ne pouvez pas créer ou supprimer des itinéraires système, mais vous pouvez remplacer certains itinéraires système avec [des itinéraires personnalisés](#custom-routes). Azure crée des itinéraires système par défaut pour chaque sous-réseau et ajoute [des itinéraires par défaut facultatifs](#optional-default-routes) supplémentaires à des sous-réseaux spécifiques, ou à chaque sous-réseau, lorsque vous utilisez des fonctionnalités Azure spécifiques.

### <a name="default"></a>Default

Chaque itinéraire comporte un préfixe d’adresse et le type de tronçon suivant. Lorsque le trafic sortant d’un sous-réseau est envoyé à une adresse IP dans le préfixe d’adresse d’un itinéraire, l’itinéraire qui contient le préfixe est l’itinéraire utilisé par Azure. En savoir plus sur [la façon dont Azure choisit un itinéraire](#how-azure-selects-a-route) lorsque plusieurs itinéraires contiennent les mêmes préfixes, ou des préfixes qui se chevauchent. Chaque fois qu’un réseau virtuel est créé, Azure crée automatiquement les itinéraires système par défaut suivants pour chaque sous-réseau au sein du réseau virtuel :


|Source |Préfixes d’adresse                                        |Type de tronçon suivant  |
|-------|---------                                               |---------      |
|Default|Unique pour le réseau virtuel                           |Réseau virtuel|
|Default|0.0.0.0/0                                               |Internet       |
|Default|10.0.0.0/8                                              |Aucun           |
|Default|172.16.0.0/12                                           |Aucun           |
|Default|192.168.0.0/16                                          |Aucun           |
|Default|100.64.0.0/10                                           |Aucun           |

Les types de tronçon suivants répertoriés dans le tableau précédent représentent la façon dont Azure achemine le trafic à destination du préfixe d’adresse répertorié. Des explications sont fournies ci-après pour les types de tronçon suivants :

- **Réseau virtuel** : achemine le trafic entre les plages d’adresses au sein de [l’espace d’adressage](virtual-network-manage-network.md#add-address-spaces) d’un réseau virtuel. Azure crée un itinéraire avec un préfixe d’adresse qui correspond à chaque plage d’adresses définie dans l’espace d’adressage d’un réseau virtuel. Si plusieurs plages d’adresses sont définies dans l’espace d’adressage de réseau virtuel, Azure crée un itinéraire individuel pour chaque plage d’adresses. Azure achemine automatiquement le trafic entre les sous-réseaux à l’aide des itinéraires créés pour chaque plage d’adresses. Il n’est pas nécessaire de définir des passerelles pour qu’Azure achemine le trafic entre les sous-réseaux. Bien qu’un réseau virtuel contienne des sous-réseaux, et chaque sous-réseau dispose d’une plage d’adresses définie, Azure *ne crée pas* des itinéraires par défaut pour les plages d’adresses de sous-réseau, car chaque plage d’adresses de sous-réseau fait partie d’une plage d’adresses de l’espace d’adressage d’un réseau virtuel.

- **Internet** : achemine le trafic spécifié par le préfixe d’adresse à Internet. L’itinéraire par défaut du système spécifie le préfixe d’adresse 0.0.0.0/0. Si vous ne substituez pas les itinéraires par défaut d’Azure, Azure achemine le trafic pour n’importe quelle adresse non spécifiée par une plage d’adresses au sein d’un réseau virtuel, à Internet, avec une exception. Si l’adresse de destination correspond à l’un des services d’Azure, Azure achemine le trafic directement au service via le réseau principal d’Azure plutôt que d’acheminer le trafic vers Internet. Le trafic entre les services Azure ne traverse pas le réseau Internet, quelle que soit la région Azure où le réseau virtuel existe ou la région Azure dans laquelle une instance du service Azure est déployée. Vous pouvez remplacer l’itinéraire système par défaut d’Azure pour le préfixe d’adresse 0.0.0.0/0 avec un [itinéraire personnalisé](#custom-routes).

- **Aucun**: le trafic acheminé vers le type de tronçon suivant **Aucun** est supprimé, plutôt que routé à l’extérieur du sous-réseau. Azure crée automatiquement des itinéraires par défaut pour les préfixes d’adresse suivants :
    - **10.0.0.0/8, 172.16.0.0/12 et 192.168.0.0/16** : réservé à un usage privé dans le document RFC 1918.
    - **100.64.0.0/10** : réservé dans RFC 6598.

    Si vous assignez l’une des plages d’adresses précédentes au sein de l’espace d’adressage d’un réseau virtuel, Azure modifie automatiquement le type de tronçon suivant pour l’itinéraire de **Aucun** en **Réseau virtuel**. Si vous affectez une plage d’adresses à l’espace d’adressage d’un réseau virtuel qui comprend, mais n’est pas identique à, un des quatre préfixes d’adresse réservés, Azure supprime l’itinéraire pour le préfixe et ajoute un itinéraire pour le préfixe d’adresse que vous avez ajouté, avec **Réseau virtuel** en tant que type de tronçon suivant.

### <a name="optional-default-routes"></a>Itinéraires par défaut facultatifs

Azure ajoute des itinéraires système par défaut supplémentaires pour les différentes fonctionnalités d’Azure, mais uniquement celles que vous activez. En fonction de la fonctionnalité, Azure ajoute les itinéraires par défaut facultatifs soit à des sous-réseaux spécifiques ou à tous les sous-réseaux au sein d’un réseau virtuel. Les itinéraires système supplémentaires et les types de tronçon suivants qu’Azure peut ajouter lorsque vous activez des fonctionnalités différentes sont :

|Source                 |Préfixes d’adresse                       |Type de tronçon suivant|Sous-réseau au sein d’un réseau virtuel auquel l’itinéraire est ajouté|
|-----                  |----                                   |---------                    |--------|
|Default                |Unique pour le réseau virtuel, par exemple : 10.1.0.0/16|Homologation de réseaux virtuels                 |Tous|
|Passerelle de réseau virtuel|Préfixes publiés à partir du site via le protocole BGP, ou configurés dans la passerelle de réseau local     |Passerelle de réseau virtuel      |Tous|
|Default                |Multiple                               |VirtualNetworkServiceEndpoint|Seul le sous-réseau pour lequel un point de terminaison de service est activé.|

- **Homologation de réseau virtuel (VNet)** : lorsque vous créez une homologation de réseau virtuel entre deux réseaux virtuels, un itinéraire est ajouté pour chaque plage d’adresses dans l’espace d’adressage de chaque réseau virtuel pour laquelle une homologation est créée. En savoir plus sur l’[homologation de réseaux virtuels](virtual-network-peering-overview.md).  
- **Passerelle de réseau virtuel** : un ou plusieurs itinéraires avec *passerelle de réseau virtuel* répertorié comme le type de tronçon suivant sont ajoutés lorsqu’une passerelle de réseau virtuel est ajoutée à un réseau virtuel. La source est également *passerelle de réseau virtuel*, car la passerelle ajoute les itinéraires au sous-réseau. Si votre passerelle de réseau local échange le protocole de passerelle frontière ([BGP](#border-gateway-protocol)) avec une passerelle de réseau virtuel Azure, un itinéraire est ajouté pour chaque itinéraire propagé à partir de la passerelle de réseau local. Nous vous recommandons de résumer les itinéraires locaux aux plages d’adresses les plus vastes possible, afin que le plus petit nombre possible d’itinéraires soit propagé vers une passerelle de réseau virtuel Azure. Le nombre d’itinéraires que vous pouvez propager vers une passerelle de réseau virtuel Azure est limité. Pour plus d’informations, consultez [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).
- **VirtualNetworkServiceEndpoint** : les adresses IP publiques de certains services sont ajoutées à la table de routage par Azure lorsque vous activez un point de terminaison de service pour le service. Les points de terminaison de service sont activés pour des sous-réseaux individuels d’un réseau virtuel, de telle sorte que l’itinéraire est ajouté uniquement à la table de routage d’un sous-réseau pour lequel un point de terminaison de service est activé. Les adresses IP publiques des services Azure changent régulièrement. Azure gère automatiquement les adresses dans la table de routage en cas de changement d’adresses. En savoir plus sur les [points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md), et les services pour lesquels vous pouvez créer des points de terminaison de service. 

> [!NOTE]
> Les types de tronçon suivants de l’**homologation de réseau virtuel** et **VirtualNetworkServiceEndpoint** sont ajoutés uniquement aux tables de routage des sous-réseaux au sein de réseaux virtuels créés par le biais du modèle de déploiement Azure Resource Manager. Les types de tronçon suivants ne sont pas ajoutés aux tables de routage qui sont associées à des sous-réseaux de réseau virtuel créés par le biais du modèle de déploiement classique. En savoir plus sur les [modèles de déploiement](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.

## <a name="custom-routes"></a>Itinéraires personnalisés

Vous pouvez créer des itinéraires soit en créant des itinéraires [définis par l’utilisateur](#user-defined) ou en échangeant les itinéraires du [protocole de passerelle frontière](#border-gateway-protocol) (BGP) entre votre passerelle de réseau local et une passerelle de réseau virtuel Azure. 
 
### <a name="user-defined"></a>Défini par l’utilisateur

Vous pouvez créer des itinéraires personnalisés ou définis par l’utilisateur dans Azure pour remplacer les itinéraires système par défaut d’Azure, ou pour ajouter des itinéraires supplémentaires à la table de routage d’un sous-réseau. Dans Azure, vous créez une table de routage, puis associez cette dernière à zéro ou plusieurs sous-réseaux du réseau virtuel. Chaque sous-réseau peut avoir zéro ou une table de routage associée. Pour en savoir plus sur le nombre maximal d’itinéraires que vous pouvez ajouter à une table de routage et le nombre maximal de tables de routage que vous pouvez créer par abonnement Azure, consultez [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Si vous créez une table de routage et l’associez à un sous-réseau, les itinéraires qu’elle contient sont combinés avec les itinéraires ajoutés par défaut par Azure à un sous-réseau par défaut, ou remplacent ces derniers.

Vous pouvez spécifier les types suivants de tronçon suivants lors de la création d’un itinéraire défini par l’utilisateur :

- **Appliance virtuelle** : une appliance virtuelle est une machine virtuelle qui exécute en général une application réseau telle qu’un pare-feu. Pour en savoir plus sur une panoplie d’appliances virtuelles réseau préconfigurées que vous pouvez déployer dans un réseau virtuel, voir [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Lorsque vous créez un itinéraire avec le **appliance virtuelle** type de tronçon, vous également spécifiez les adresses IP de tronçon suivant. L’adresse IP peut être :

    - L’[adresse IP privée](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) d’une interface réseau attachée à une machine virtuelle. L’option*Activer le transfert IP* doit être activée pour toute interface réseau attachée à une machine virtuelle qui transfère le trafic réseau vers une adresse autre que celle qui lui appartient. Le paramètre désactive la vérification de la source et de destination pour une interface réseau par Azure. En savoir plus sur la façon d’[activer le transfert IP pour une interface réseau](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Bien que le paramètre *Activer le transfert IP* soit un paramètre Azure, il se peut qu’il soit également nécessaire d’activer le transfert IP au sein du système d’exploitation de la machine virtuelle pour que l’appliance transfère le trafic entre les adresses IP privées et les interfaces réseau Azure. Si l’appliance doit acheminer le trafic vers une adresse IP publique, elle doit l’acheminer par proxy ou bien traduire l’adresse réseau de l’adresse IP privée originale vers sa propre adresse IP privée, qu’Azure traduit alors vers une adresse IP publique, avant d’envoyer le trafic sur Internet. Pour déterminer les paramètres requis pour la machine virtuelle, consultez la documentation de votre système d’exploitation ou application réseau. Pour comprendre les connexions sortantes dans Azure, consultez [Comprendre les connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

    > [!NOTE]
    > Déployez une appliance virtuelle dans un sous-réseau autre que celui dans lequel les ressources de routage par l’intermédiaire de l’appliance virtuelle sont déployées. Le déploiement de l’appliance virtuelle dans le même sous-réseau, puis l’application d’une table de routage au sous-réseau qui achemine le trafic via l’appliance virtuelle, peut entraîner des boucles de routage, où le trafic ne quitte jamais le sous-réseau.

    - L’adresse IP privée d’un [équilibreur de charge interne](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure. Un équilibreur de charge est souvent utilisé dans le cadre d’une [stratégie de haute disponibilité pour les appliances virtuelles du réseau](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    Vous pouvez définir un itinéraire avec 0.0.0.0/0 comme le préfixe d’adresse et un type de tronçon suivant de l’appliance virtuelle, ce qui permet à l’appliance d’inspecter le trafic et de déterminer s’il faut le transférer ou le supprimer. Si vous envisagez de créer un itinéraire défini par l’utilisateur contenant le préfixe d’adresse 0.0.0.0/0, lisez d’abord la rubrique [préfixe d’adresse 0.0.0.0/0](#default-route).

- **Passerelle de réseau virtuel** : indiquez quand vous souhaitez que le trafic destiné à des préfixes d’adresse spécifiques soit routé vers une passerelle de réseau virtuel. La passerelle de réseau virtuel doit être créée avec le type **VPN**. Vous ne pouvez pas spécifier une passerelle de réseau virtuel créée comme étant de type **ExpressRoute** dans un itinéraire défini par l’utilisateur, car ExpressRoute nécessite d’utiliser [BGP](#border-gateway-protocol-routes) pour des itinéraires personnalisés. Vous pouvez définir un itinéraire qui dirige le trafic destiné au préfixe d’adresse 0.0.0.0/0 vers une passerelle de réseau virtuel [basée sur un itinéraire](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype). Dans votre environnement local, vous pouvez avoir un dispositif qui inspecte le trafic et détermine s’il faut le transférer ou le supprimer. Si vous envisagez de créer un itinéraire défini par l’utilisateur pour le préfixe d’adresse 0.0.0.0/0, lisez d’abord la rubrique [préfixe d’adresse 0.0.0.0/0](#default-route). Au lieu de configurer un itinéraire défini par l’utilisateur pour le préfixe d’adresse 0.0.0.0/0, vous pouvez publier un itinéraire avec le préfixe 0.0.0.0/0 via BGP, si vous avez [activé BGP pour une passerelle de réseau virtuel VPN](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **Aucun** : indiquez quand vous souhaitez supprimer le trafic vers un préfixe d’adresse, plutôt que de le transférer vers une destination. Si vous n’avez pas encore entièrement configuré une fonctionnalité, Azure peut définir l’option *Aucun* pour certains des itinéraires système facultatifs. Par exemple, si vous voyez que l’option *Aucun* est indiquée comme **Adresse IP de tronçon suivant** avec un **Type de tronçon suivant** défini sur *Passerelle de réseau virtuel* ou *Équipement virtuel*, c’est que l’appareil n’est pas en cours d’exécution ou n’est pas entièrement configuré. Azure crée des [itinéraires système par défaut](#default) pour les préfixes d’adresse réservés avec **Aucun** en tant que type de tronçon suivant.
- **Réseau virtuel** : indiquez quand vous souhaitez remplacer le routage par défaut dans un réseau virtuel. Consultez [Exemple de routage](#routing-example) pour voir un exemple de la raison pour laquelle vous pouvez créer un itinéraire avec le type de tronçon suivant **Réseau virtuel**.
- **Internet** : spécifiez si vous souhaitez explicitement acheminer le trafic destiné à un préfixe d’adresse à Internet, ou si vous souhaitez que le trafic destiné à des services Azure avec des adresses IP publiques reste dans le réseau principal Azure.

Vous ne pouvez pas spécifier **Homologation de réseau virtuel** ou **VirtualNetworkServiceEndpoint** en tant que type de tronçon suivant dans les itinéraires définis par l’utilisateur. Les itinéraires avec les types de tronçon suivants **réseau virtuel d’homologation** ou **VirtualNetworkServiceEndpoint** sont créés uniquement par Azure, lorsque vous configurez une homologation ou un point de terminaison de service de réseau virtuel.

**Types de tronçon suivants dans les outils Azure**

Le nom affiché et référencé pour les types de tronçon suivants diffère entre le portail Azure et les outils en ligne de commande, et entre Azure Resource Manager et les modèles de déploiement classique. Le tableau suivant répertorie les noms utilisés pour faire référence à chaque type de tronçon suivant avec les différents outils et [modèles de déploiement](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Type de tronçon suivant                   |Azure CLI 2.0 et PowerShell (Gestionnaire des ressources) |Azure CLI 1.0 et PowerShell (classique)|
|-------------                   |---------                                       |-----|
|Passerelle de réseau virtuel         |VirtualNetworkGateway                           |VPNGateway|
|Réseau virtuel                 |VnetLocal                                       |VNETLocal (non disponible dans la CLI 1.0 en mode asm)|
|Internet                        |Internet                                        |Internet (non disponible dans la CLI 1.0 en mode asm)|
|Appliance virtuelle               |VirtualAppliance                                |VirtualAppliance|
|Aucun                            |Aucun                                            |Null (non disponible dans la CLI 1.0 en mode asm)|
|Homologation de réseaux virtuels         |Homologation de réseaux virtuels                                    |Non applicable|
|Point de terminaison de service de réseau virtuel|VirtualNetworkServiceEndpoint                   |Non applicable|

### <a name="border-gateway-protocol"></a>Protocole de passerelle frontière

Une passerelle de réseau local peut échanger les itinéraires avec une passerelle de réseau virtuel Azure à l’aide du protocole de passerelle frontière (BGP). L’utilisation du protocole BGP avec une passerelle de réseau virtuel Azure dépend du type que vous avez sélectionné lorsque vous avez créé la passerelle. Si le type que vous avez sélectionné était :

- **ExpressRoute** : vous devez utiliser le protocole BGP pour publier les itinéraires locaux vers le routeur de périphérie Microsoft. Vous ne pouvez pas créer d’itinéraires définis par l’utilisateur pour forcer l’acheminement du trafic vers la passerelle de réseau virtuel ExpressRoute si vous déployez une passerelle de réseau virtuel avec le type ExpressRoute. Vous pouvez utiliser des itinéraires définis par l’utilisateur pour forcer l’acheminement du trafic provenant d’ExpressRoute vers, par exemple, une appliance virtuelle réseau. 
- **VPN** : vous pouvez éventuellement utiliser le protocole BGP. Pour plus d’informations, consultez [BGP avec les connexions VPN de site à site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Lorsque vous échangez des itinéraires avec Azure à l’aide du protocole BGP, un itinéraire distinct est ajouté à la table de routage de tous les sous-réseaux d’un réseau virtuel pour chaque préfixe publié. L’itinéraire est ajouté avec *Passerelle de réseau virtuel* comme source et type de tronçon suivant. 
 
## <a name="how-azure-selects-a-route"></a>Comment Azure choisit un itinéraire

Lorsque le trafic sortant est envoyé à partir d’un sous-réseau, Azure choisit un itinéraire sur la base de l’adresse IP de destination, à l’aide de l’algorithme de correspondance de préfixe le plus long. Par exemple, une table de routage contient deux itinéraires : un itinéraire spécifie le préfixe d’adresse 10.0.0.0/24, tandis que l’autre spécifie le préfixe d’adresse 10.0.0.0/16. Azure achemine le trafic destiné à 10.0.0.5, au type de tronçon suivant spécifié dans l’itinéraire avec le préfixe d’adresse 10.0.0.0/24, car 10.0.0.0/24 est un préfixe plus long que 10.0.0.0/16, même si 10.0.0.5 se trouve dans les deux préfixes d’adresse. Azure achemine le trafic destiné à 10.0.1.5, au type de tronçon suivant spécifié dans l’itinéraire avec le préfixe d’adresse 10.0.0.0/16, car 10.0.1.5 ne fait pas partie du préfixe d’adresse 10.0.0.0/24, et par conséquent, l’itinéraire avec le préfixe d’adresse 10.0.0.0/16 est le préfixe correspondant le plus long.

Si plusieurs itinéraires contiennent le même préfixe d’adresse, Azure choisit le type d’itinéraire en se basant sur l’ordre de priorité suivant :

1. Itinéraire défini par l’utilisateur
2. Itinéraire BGP
3. Itinéraire du système

Par exemple, une table de routage contient les itinéraires suivants :


|Source   |Préfixes d’adresse  |Type de tronçon suivant           |
|---------|---------         |-------                 |
|Default  | 0.0.0.0/0        |Internet                |
|Utilisateur     | 0.0.0.0/0        |Passerelle de réseau virtuel |

Lorsque le trafic est destiné à une adresse IP en dehors des préfixes d’adresses de tous les itinéraires contenus dans la table de routage, Azure choisit l’itinéraire avec la source **Utilisateur**, car les itinéraires définis par l’utilisateur ont une priorité plus élevée que les itinéraires système par défaut.

Consultez [Exemple de routage](#routing-example) pour une table de routage complète avec des explications des itinéraires de la table.

## <a name="default-route"></a>Préfixe d’adresse 0.0.0.0/0

Un itinéraire avec le préfixe d’adresse 0.0.0.0/0 indique à Azure la façon d’acheminer le trafic destiné à une adresse IP qui n’est pas dans le préfixe d’adresse de n’importe quel autre itinéraire de la table de routage d’un sous-réseau. Lors de la création d’un sous-réseau, Azure crée un itinéraire [par défaut](#default) vers le préfixe d’adresse 0.0.0.0/0, avec **Internet** comme type de tronçon suivant. Si vous ne substituez pas cet itinéraire, Azure achemine tout le trafic destiné à des adresses IP non incluses dans le préfixe d’adresse de n’importe quel autre itinéraire vers Internet. L’exception est que le trafic destiné aux adresses IP publiques des services Azure reste sur le réseau principal Azure et qu’il n’est pas routé vers Internet. Si vous substituez cet itinéraire avec un itinéraire [personnalisé](#custom-routes), le trafic destiné à des adresses qui ne sont pas comprises dans les préfixes d’adresse de n’importe quel autre itinéraire dans la table de routage est envoyé à une appliance virtuelle de réseau ou une passerelle de réseau virtuel, en fonction de l’option choisie dans un itinéraire personnalisé.

Lorsque vous substituez le préfixe d’adresse 0.0.0.0/0, en plus du flux du trafic sortant du sous-réseau via la passerelle de réseau virtuel ou l’appliance virtuelle, les modifications suivantes se produisent avec le routage par défaut d’Azure : 

- Azure envoie tout le trafic vers le type de tronçon suivant spécifié dans l’itinéraire y compris le trafic destiné à des adresses IP publiques de services Azure. Lorsque le type de tronçon suivant de l’itinéraire avec le préfixe d’adresse 0.0.0.0/0 est **Internet**, le trafic sortant du sous-réseau et destiné aux adresses IP publiques de services Azure ne quitte jamais le réseau principal d’Azure, quelle que soit la région Azure dans laquelle le réseau virtuel ou la ressource de service Azure existe. Lorsque vous créez un itinéraire défini par l’utilisateur ou BGP avec le type de tronçon suivant **Passerelle de réseau virtuel** ou **Équipement virtuel**, tout le trafic, y compris le trafic envoyé aux adresses IP publiques des services Azure pour lesquels vous n’avez pas activé des [points de terminaison de service](virtual-network-service-endpoints-overview.md), est envoyé au type de tronçon suivant spécifié dans l’itinéraire. Si vous avez activé un point de terminaison de service pour un service, le trafic vers ce service n’est pas routé vers le type de tronçon suivant dans un itinéraire avec le préfixe d’adresse 0.0.0.0/0, car les préfixes d’adresse du service sont spécifiés dans l’itinéraire qu’Azure crée lorsque vous activez le point de terminaison de service point, et les préfixes d’adresse du service sont plus longs que 0.0.0.0/0.
- Vous ne pouvez plus accéder directement aux ressources dans le sous-réseau à partir d’Internet. Vous pouvez indirectement accéder aux ressources dans le sous-réseau à partir d’Internet, si le trafic entrant passe par l’appareil spécifié par le type de tronçon suivant d’un itinéraire avec le préfixe d’adresse 0.0.0.0/0 avant d’atteindre les ressources dans le réseau virtuel. Si l’itinéraire contient les valeurs suivantes comme type de tronçon suivant :
    - **Équipement virtuel** : l’appliance doit :
        - Être accessible à partir d’Internet
        - Avoir une adresse IP publique affectée,
        - Ne doit pas avoir de règle de groupe de sécurité réseau associée qui empêche la communication avec l’appareil
        - Ne doit pas refuser la communication
        - Être en mesure de translater et de transférer une adresse réseau, ou d’acheminer le trafic à travers un serveur proxy vers la ressource de destination dans le sous-réseau, et de retourner le trafic à Internet. 
    - **Passerelle de réseau virtuel** : si la passerelle est une passerelle de réseau virtuel, un appareil local connecté à Internet peut translater et transférer les adresses du réseau ou acheminer le trafic à travers un serveur proxy vers la ressource de destination dans le sous-réseau, via l’[homologation privée](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-private-peering) d’ExpressRoute. 

  Consultez [Zone DMZ entre Azure et votre centre de données local](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Zone DMZ entre Azure et Internet](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2fazure%2fvirtual-network%2ftoc.json) pour les détails de mise en œuvre lors de l’utilisation de passerelles et d’appliances virtuelles entre les Internet et Azure.

## <a name="routing-example"></a>Exemple de routage

Pour illustrer les concepts abordés dans cet article, les sections qui suivent décrivent :
- Un scénario, avec la configuration requise
- Les itinéraires personnalisés nécessaires pour satisfaire les besoins
- La table de routage qui existe pour un sous-réseau qui comprend les itinéraires par défaut et personnalisés nécessaires pour satisfaire aux exigences

> [!NOTE]
> Cet exemple n’est pas destiné à être une mise en œuvre recommandée ou de meilleure pratique. Il est plutôt destiné uniquement à illustrer les concepts de cet article.

### <a name="requirements"></a>Configuration requise

1. Mettre en œuvre deux réseaux virtuels dans la même région Azure et activer les ressources de communication entre les réseaux virtuels.
2. Activer un réseau local pour communiquer en toute sécurité avec les deux réseaux virtuels via un tunnel VPN sur Internet. *Vous pouvez également utiliser une connexion ExpressRoute, mais dans cet exemple, une connexion VPN est utilisée.*
3. Pour un sous-réseau dans un réseau virtuel :
 
    - Forcer tout le trafic sortant du sous-réseau, sauf dans le stockage Azure et le sous-réseau, à circuler via une appliance virtuelle de réseau pour inspection et journalisation.
    - Ne pas inspecter le trafic entre les adresses IP privées dans le sous-réseau ; autoriser le trafic à circuler directement entre toutes les ressources. 
    - Supprimer tout trafic sortant destiné à l’autre réseau virtuel.
    - Permettre au trafic sortant vers le stockage Azure de circuler directement vers le stockage, sans le forcer à traverser une appliance virtuelle de réseau.

4. Autoriser tout le trafic entre tous les autres sous-réseaux et réseaux virtuels.

### <a name="implementation"></a>Implémentation

L’illustration suivante montre une mise en œuvre basée sur le modèle de déploiement Azure Resource Manager qui respecte les exigences précédentes :

![Diagramme du réseau](./media/virtual-networks-udr-overview/routing-example.png)

Flèches indiquent le flux du trafic. 

### <a name="route-tables"></a>Tables de routage

#### <a name="subnet1"></a>Sous-réseau1

La table de routage du *Sous-réseau1* dans l’image contient les itinéraires suivants :

|ID  |Source |État  |Préfixes d’adresse    |Type de tronçon suivant          |Adresse IP de tronçon suivant|Nom d’itinéraire défini par l’utilisateur| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Default|Non valide|10.0.0.0/16         |Réseau virtuel        |                   |              |
|2   |Utilisateur   |Actif |10.0.0.0/16         |Appliance virtuelle      |10.0.100.4         |Au sein de VNet1  |
|3   |Utilisateur   |Actif |10.0.0.0/24         |Réseau virtuel        |                   |Dans le Sous-réseau1|
|4   |Default|Non valide|10.1.0.0/16         |Homologation de réseaux virtuels           |                   |              |
|5.   |Default|Non valide|10.2.0.0/16         |Homologation de réseaux virtuels           |                   |              |
|6.   |Utilisateur   |Actif |10.1.0.0/16         |Aucun                   |                   |ToVNet2-1-Drop|
|7   |Utilisateur   |Actif |10.2.0.0/16         |Aucun                   |                   |ToVNet2-2-Drop|
|8   |Default|Non valide|10.10.0.0/16        |Passerelle de réseau virtuel|[X.X.X.X]          |              |
|9.   |Utilisateur   |Actif |10.10.0.0/16        |Appliance virtuelle      |10.0.100.4         |To-On-Prem    |
|10  |Default|Actif |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Default|Non valide|0.0.0.0/0           |Internet|              |                   |              |
|12  |Utilisateur   |Actif |0.0.0.0/0           |Appliance virtuelle      |10.0.100.4         |Default-NVA   |

Les identificateurs d’itinéraire sont décrits ci-dessous :

1. Azure a automatiquement ajouté cet itinéraire pour tous les sous-réseaux de *Virtual-network-1*, car 10.0.0.0/16 est la seule plage d’adresses définie dans l’espace d’adressage du réseau virtuel. Si l’itinéraire défini par l’utilisateur dans l’itinéraire ID2 n’a pas été créé, le trafic envoyé vers n’importe quelle adresse entre 10.0.0.1 et 10.0.255.254 est acheminé dans le réseau virtuel, car le préfixe est plus long que 0.0.0.0/0 et ne fait pas partie des préfixes d’adresse de tous les autres itinéraires. Azure change automatiquement l’état de *Actif* à *Non valide*, lorsque ID2, un itinéraire défini par l’utilisateur, est ajouté, car il a le même préfixe que l’itinéraire par défaut, et les itinéraires définis par l’utilisateur substituent les itinéraires par défaut. L’état de cet itinéraire est toujours *Actif* pour le *Sous-réseau2*, car la table de routage à laquelle appartient l’itinéraire défini par l’utilisateur, ID2, n’est pas associée au *Sous-réseau2*.
2. Azure a ajouté cet itinéraire lorsqu’un itinéraire défini par l’utilisateur pour le préfixe d’adresse 10.0.0.0/16 a été associé au *Sous-réseau1* dans le réseau virtuel *Virtual-network-1*. L’itinéraire défini par l’utilisateur spécifie 10.0.100.4 comme l’adresse IP de l’appliance virtuelle, car cette adresse est l’adresse IP privée affectée à la machine virtuelle de l’appliance virtuelle. La table de routage à laquelle cet itinéraire appartient n’est pas associée au *Sous-réseau2*, et n’apparaît donc pas dans la table de routage du *Sous-réseau2*. Cet itinéraire remplace l’itinéraire par défaut pour le préfixe 10.0.0.0/16 (ID1), qui a automatiquement acheminé le trafic adressé à 10.0.0.1 et à 10.0.255.254 dans le réseau virtuel via le type de tronçon suivant de réseau virtuel. Cet itinéraire existe pour répondre à l’[exigence](#requirements) 3, afin de forcer tout le trafic sortant à traverser une appliance virtuelle.
3. Azure a ajouté cet itinéraire lorsqu’un itinéraire défini par l’utilisateur pour le préfixe d’adresse 10.0.0.0/24 a été associé au *Sous-réseau1*. Le trafic destiné aux adresses comprises entre 10.0.0.1 et 10.0.0.0.254 reste dans le sous-réseau, au lieu d’être acheminé vers l’appliance virtuelle spécifiée dans la règle précédente (ID2), car il a un préfixe plus long que l’itinéraire ID2. Cet itinéraire n’a pas été associé au *Sous-réseau2*, et n’apparaît donc pas dans la table de routage du *Sous-réseau2*. Cet itinéraire substitue efficacement l’itinéraire ID2 pour le trafic au sein du *Sous-réseau1*. Cet itinéraire existe pour répondre à l’[exigence](#requirements) 3.
4. Azure a ajouté automatiquement les itinéraires dans ID 4 et 5 pour tous les sous-réseaux de *Virtual-network-1*, lorsque le réseau virtuel a été homologué avec *Virtual-network-2.* *Virtual-network-2* a deux plages d’adresses dans son espace d’adressage : 10.1.0.0/16 et 10.2.0.0/16, Azure a donc ajouté un itinéraire pour chaque plage. Si les itinéraires définis par l’utilisateur dans l’itinéraire ID 6 et 7 n’ont pas été créés, le trafic envoyé vers n’importe quelle adresse entre 10.1.0.1-10.1.255.254 et 10.2.0.1-10.2.255.254 serait acheminé vers le réseau virtuel homologué, car le préfixe est plus long que 0.0.0.0/0 et ne fait pas partie des préfixes d’adresse de tous les autres itinéraires. Azure a automatiquement changé l’état de *Actif* à *Non valide* lorsque les itinéraires dans ID 6 et 7 ont été ajoutés, car ils ont les mêmes préfixes que les itinéraires dans ID 4 et 5 et les itinéraires définis par l’utilisateur substituent les itinéraires par défaut. L’état des itinéraires dans ID 4 et 5 est toujours *Actif* pour le *Sous-réseau2*, car la table de routage à laquelle appartiennent les itinéraires définis par l’utilisateur dans ID 4 et 5 n’est pas associée au *Sous-réseau2*. Une homologation de réseau virtuel a été créée pour répondre à [l’exigence](#requirements) 1.
5. Même explication que pour ID4.
6. Azure a ajouté cet itinéraire et l’itinéraire dans ID7 lorsque les itinéraires définis par l’utilisateur pour les préfixes d’adresse 10.1.0.0/16 et 10.2.0.0/16 ont été associés au *Sous-réseau1*. Le trafic destiné aux adresses comprises entre 10.1.0.1 et 10.1.255.254 et entre 10.2.0.1 et 10.2.255.254 est supprimé par Azure au lieu d’être acheminé vers le réseau virtuel homologué, car les itinéraires définis par l’utilisateur remplacent les itinéraires par défaut. Les itinéraires ne sont pas associés au *Sous-réseau2*, et n’apparaissent donc pas dans la table de routage du *Sous-réseau2*. Les itinéraires remplacent les itinéraires ID4 et ID5 pour le trafic sortant du *Sous-réseau1*. Les itinéraires ID6 et ID7 existent pour répondre à [l’exigence](#requirements) 3 afin de supprimer le trafic destiné à l’autre réseau virtuel.
7. Même explication que pour ID6.
8. Azure a ajouté automatiquement cet itinéraire pour tous les sous-réseaux dans *Virtual-network-1* lorsqu’une passerelle de réseau virtuel de type VPN a été créée au sein du réseau virtuel. Azure a ajouté l’adresse IP publique de la passerelle de réseau virtuel à la table de routage. Le trafic envoyé vers n’importe quelle adresse entre 10.10.0.1 et 10.10.255.254 est acheminé vers la passerelle de réseau virtuel. Le préfixe est plus long que 0.0.0.0/0 et ne fait pas partie des préfixes d’adresse des autres itinéraires. Une passerelle de réseau virtuel a été créée pour répondre à l’[exigence](#requirements) 2.
9. Azure a ajouté cet itinéraire lorsqu’un itinéraire défini par l’utilisateur pour le préfixe d’adresse 10.10.0.0/16 a été ajouté à la table de routage associée au *Sous-réseau1*. Cet itinéraire remplace ID8. L’itinéraire envoie tout le trafic destiné au réseau local à une NVA pour inspection, plutôt que d’acheminer le trafic directement sur site. Cet itinéraire a été créé pour répondre à l’[exigence](#requirements) 3.
10. Azure a ajouté automatiquement cet itinéraire au sous-réseau lorsqu’un point de terminaison de service pour un service Azure a été activé pour le sous-réseau. Azure achemine le trafic à partir du sous-réseau vers une adresse IP publique du service, sur le réseau d’infrastructure Azure. Le préfixe est plus long que 0.0.0.0/0 et ne fait pas partie des préfixes d’adresse des autres itinéraires. Un point de terminaison de service a été créé pour répondre à l’[exigence](#requirements) 3, afin de permettre au trafic destiné au stockage Azure de circuler directement vers le stockage Azure.
11. Azure a automatiquement ajouté cet itinéraire à la table de routage de tous les sous-réseaux de *Virtual-network-1* et de *Virtual-network-2.* Le préfixe d’adresse 0.0.0.0/0 est le préfixe le plus court. Tout le trafic envoyé à des adresses avec un plus long préfixe d’adresse est routé en fonction d’autres itinéraires. Par défaut, Azure achemine tout le trafic destiné aux adresses autres que les adresses spécifiées dans l’un des autres itinéraires vers Internet. Azure a automatiquement changé l’état de *Actif* à *Non valide* pour le *Sous-réseaut1* lorsqu’un itinéraire défini par l’utilisateur pour le préfixe d’adresse 0.0.0.0/0 (ID12) a été associé au sous-réseau. L’état de cet itinéraire est toujours *Actif* pour tous les autres sous-réseaux dans les deux réseaux virtuels, car l’itinéraire n’est associé à aucun autre sous-réseau au sein de tout autre réseau virtuel.
12. Azure a ajouté cet itinéraire lorsqu’un itinéraire défini par l’utilisateur pour le préfixe d’adresse 0.0.0.0/0 a été associé au *Sous-réseau1*. L’itinéraire défini par l’utilisateur spécifie 10.0.100.4 comme l’adresse IP de l’appliance virtuelle. Cet itinéraire n’est pas associée au *Sous-réseau2*, et n’apparaît donc pas dans la table de routage du *Sous-réseau2*. Tout le trafic destiné à toute adresse qui n’est pas incluse dans les préfixes d’adresse de tout autre itinéraire est envoyé à l’appliance virtuelle. L’ajout de cet itinéraire a changé l’état de l’itinéraire par défaut pour le préfixe d’adresse 0.0.0.0/0 (ID11) de *Actif* à *Non valide* pour le *Sous-réseau1*, car un itinéraire défini par l’utilisateur remplace un itinéraire par défaut. Cet itinéraire existe pour répondre à l’[exigence](#requirements) 3.

#### <a name="subnet2"></a>Sous-réseau2

La table de routage du *Sous-réseau2* dans l’image contient les itinéraires suivants :

|Source  |État  |Préfixes d’adresse    |Type de tronçon suivant             |Adresse IP de tronçon suivant|
|------- |-------|------              |-------                   |--------           
|Default |Actif |10.0.0.0/16         |Réseau virtuel           |                   |
|Default |Actif |10.1.0.0/16         |Homologation de réseaux virtuels              |                   |
|Default |Actif |10.2.0.0/16         |Homologation de réseaux virtuels              |                   |
|Default |Actif |10.10.0.0/16        |Passerelle de réseau virtuel   |[X.X.X.X]          |
|Default |Actif |0.0.0.0/0           |Internet                  |                   |
|Default |Actif |10.0.0.0/8          |Aucun                      |                   |
|Default |Actif |100.64.0.0/10       |Aucun                      |                   |
|Default |Actif |172.16.0.0/12       |Aucun                      |                   |
|Default |Actif |192.168.0.0/16      |Aucun                      |                   |

La table de routage du *Sous-réseau2* contient tous les itinéraires par défaut créés par Azure et les itinéraires facultatifs d’homologation de réseau virtuel et de passerelle de réseau virtuel. Azure a ajouté les itinéraires facultatifs à tous les sous-réseaux du réseau virtuel lorsque la passerelle et l’homologation ont été ajoutées au réseau virtuel. Azure a supprimé les itinéraires pour les préfixes d’adresse 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 et 100.64.0.0/10 de la table de routage du *Sous-réseau1* lorsque l’itinéraire défini par l’utilisateur pour le préfixe d’adresse 0.0.0.0/0 a été ajouté au *Sous-réseau1*.  

## <a name="next-steps"></a>Étapes suivantes

- [Créer une table de routage définie par l’utilisateur avec des itinéraires et une appliance virtuelle de réseau](create-user-defined-route-portal.md)
- [Configurer BGP pour une passerelle VPN Azure](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Utiliser le protocole BGP avec ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)
- [Afficher tous les itinéraires pour un sous-réseau](virtual-network-routes-troubleshoot-portal.md). Une table de routage définie par l’utilisateur vous montre uniquement les itinéraires définis par l’utilisateur, et n’affiche pas les itinéraires par défaut et BGP pour un sous-réseau. L’affichage de tous les itinéraires affiche les itinéraires par défaut, BGP et définis par l’utilisateur pour le sous-réseau auquel une interface réseau appartient.
- [Déterminer le type de tronçon suivant](../network-watcher/network-watcher-check-next-hop-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) entre une machine virtuelle et une adresse IP de destination. La fonctionnalité de tronçon suivant d’Azure Network Watcher vous permet de déterminer si le trafic a quitté un sous-réseau et s’il est en cours de routage vers la destination escomptée.
