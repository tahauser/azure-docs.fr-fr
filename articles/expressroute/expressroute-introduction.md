---
title: 'Présentation d’ExpressRoute : étendre votre réseau local à Azure via une connexion privée | Microsoft Docs'
description: Cette présentation technique d’ExpressRoute détaille le fonctionnement d’une connexion ExpressRoute pour étendre votre réseau local à Azure via une connexion privée.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: 75d69669945d31fbc9876d0c1e709b37f96aee6d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="expressroute-overview"></a>Présentation d’ExpressRoute
Microsoft Azure ExpressRoute vous permet d’étendre vos réseaux locaux au cloud de Microsoft via une connexion privée assurée par un fournisseur de connectivité. Grâce à ExpressRoute, vous pouvez établir des connexions aux services de cloud computing Microsoft, comme Microsoft Azure, Office 365 et Dynamics 365.

La connectivité peut provenir d'un réseau universel (IP VPN), d’un réseau Ethernet point à point ou d’une interconnexion virtuelle via un fournisseur de connectivité dans un centre de colocalisation. Les connexions ExpressRoute ne sont pas établies par le biais de l'Internet public. Elles offrent ainsi de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques sur Internet. Pour plus d’informations sur la connexion de votre réseau à Microsoft à l’aide d’ExpressRoute, consultez [Modèles de connectivité ExpressRoute](expressroute-connectivity-models.md).

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Principaux avantages

* Connectivité de couche 3 entre votre réseau local et le cloud de Microsoft via un fournisseur de connectivité. La connectivité peut provenir d'un réseau universel (IPVPN), d’une connexion Ethernet point à point, ou d’une interconnexion virtuelle via un échange Ethernet.
* Connectivité aux services de cloud de Microsoft dans toutes les régions de la zone géopolitique.
* Connectivité globale aux services de Microsoft dans toutes les régions grâce au module complémentaire ExpressRoute premium.
* Routage dynamique entre votre réseau et Microsoft via des protocoles standard (BGP).
* Redondance intégrée dans chaque emplacement d'homologation pour une plus grande fiabilité.
* [SLA](https://azure.microsoft.com/support/legal/sla/)de disponibilité de la connexion.
* Support de la qualité de service pour Skype Entreprise.

Pour plus d’informations, consultez le [Forum Aux Questions ExpressRoute](expressroute-faqs.md).

## <a name="features"></a>Caractéristiques

### <a name="layer-3-connectivity"></a>Connectivité de couche 3
Microsoft utilise le protocole de routage dynamique standard (BGP) pour échanger des routages entre votre réseau local, vos instances dans Azure et les adresses publiques Microsoft.  Nous établissons plusieurs sessions BGP avec votre réseau pour tester différents profils de trafic. Vous trouverez plus de détails à ce sujet dans l’article [Circuit ExpressRoute et domaines de routage](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Redondance
Chaque circuit ExpressRoute se compose de deux connexions à deux routeurs Microsoft Enterprise Edge (MSEE) entre le fournisseur de connectivité et le périmètre de votre réseau. Microsoft nécessite une double connexion BGP entre le fournisseur de connectivité et vous, une pour chaque routeur MSEE. Vous pouvez choisir de ne pas déployer d’appareils redondants/de circuits Ethernet de votre côté. Cependant, les fournisseurs de connectivité utilisent des appareils redondants pour garantir que vos connexions sont transmises à Microsoft de manière redondante. Une configuration de connectivité de couche 3 redondante est nécessaire à la validité de notre [SLA](https://azure.microsoft.com/support/legal/sla/) .

### <a name="connectivity-to-microsoft-cloud-services"></a>Connectivité aux services de cloud Microsoft

Les connexions ExpressRoute permettent d’accéder aux services suivants :

* Services Microsoft Azure
* Services Microsoft Office 365
* Microsoft Dynamics 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Pour consulter une liste détaillée des services pris en charge via ExpressRoute, visitez la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md) 

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Connectivité à toutes les zones d’une région géopolitique
Vous pouvez vous connecter à Microsoft dans un de nos [emplacements d’homologation](expressroute-locations.md) et ainsi accéder à toutes les zones de la région géopolitique. 

Par exemple, si vous êtes connecté via ExpressRoute à Microsoft, à Amsterdam, vous aurez accès à tous les services cloud Microsoft hébergés en Europe du Nord et en Europe occidentale. Pour obtenir une vue d’ensemble des régions géopolitiques, des zones de cloud Microsoft associées et des emplacements d’homologation ExpressRoute correspondants, consultez l’article [Partenaires ExpressRoute et emplacements d’homologation](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Connectivité globale avec le module complémentaire ExpressRoute premium
Vous pouvez activer la fonctionnalité du module complémentaire ExpressRoute premium pour étendre la connectivité au-delà des limites géopolitiques. Par exemple, si vous êtes connecté à Microsoft à Amsterdam via ExpressRoute, vous aurez accès à tous les services de cloud Microsoft hébergés dans toutes les régions du monde (à l’exception des clouds nationaux). Vous pouvez accéder aux services déployés en Amérique du Sud ou en Australie de la même façon que vous accédez aux régions d’Europe du Nord et d’Europe occidentales.

### <a name="rich-connectivity-partner-ecosystem"></a>Riche écosystème de partenaires de connectivité
ExpressRoute offre un écosystème sans cesse croissant de fournisseurs de connectivité et de partenaires intégrateurs de systèmes. Vous pouvez consulter l’article [Partenaires ExpressRoute et emplacements d’homologation](expressroute-locations.md) pour obtenir les dernières informations.

### <a name="connectivity-to-national-clouds"></a>Connectivité aux clouds nationaux
Microsoft gère des environnements de cloud isolés dans des régions géopolitiques et des segments de clientèle spécifiques. Reportez-vous à la page [Fournisseurs et emplacements ExpressRoute](expressroute-locations.md) pour obtenir la liste des clouds nationaux et des fournisseurs.

### <a name="bandwidth-options"></a>Options de bande passante
Vous pouvez acheter des circuits ExpressRoute pour un large éventail de bandes passantes. Les bandes passantes prises en charge sont répertoriées ci-dessous. Veillez à vérifier auprès de votre fournisseur de connectivité la liste des bandes passantes prises en charge qu'il propose.

* 50 Mbits/s
* 100 Mbits/s
* 200 Mbits/s
* 500 Mbits/s
* 1 Gbit/s
* 2 Gbit/s
* 5 Gbit/s
* 10 Gbits/s

### <a name="dynamic-scaling-of-bandwidth"></a>Mise à l'échelle dynamique de la bande passante
Vous pouvez augmenter la bande passante du circuit ExpressRoute (dans la mesure du possible) sans ralentir vos connexions. 

### <a name="flexible-billing-models"></a>Modèles de facturation flexibles
Vous pouvez choisir le modèle de facturation qui vous convient le mieux. Choisissez parmi les modèles de facturation ci-dessous. Pour plus d’informations, consultez le [Forum Aux Questions ExpressRoute](expressroute-faqs.md).

* **Données illimitées**. Le circuit ExpressRoute est facturé sur une base mensuelle, et tous les transferts de données entrants et sortants sont inclus gratuitement. 
* **Données limitées**. Le circuit ExpressRoute est facturé sur une base mensuelle. Tous les transferts de données entrants sont gratuits. Chaque transfert de données sortant est facturé par Go de données transférées. Les taux de transfert de données varient selon la région.
* **Module complémentaire ExpressRoute premium**. Le module complémentaire ExpressRoute premium est un module qui s’ajoute au circuit ExpressRoute Le module complémentaire ExpressRoute premium offre les fonctionnalités suivantes : 
  * Augmentation de 4 000 à 10 000 routages des limites de routage pour les homologations publiques et privées Azure.
  * Connectivité globale des services. Un circuit ExpressRoute créé dans une région quelconque (à l'exclusion des clouds nationaux) a accès aux ressources de n'importe quelle autre région dans le monde. Par exemple, un réseau virtuel créé en l'Europe occidentale est accessible via un circuit ExpressRoute configuré dans la Silicon Valley.
  * Augmentation de 10 à une limite supérieure du nombre de liens de réseaux virtuels par circuit ExpressRoute, selon la bande passante du circuit.

## <a name="faq"></a>Forum Aux Questions

Pour les questions fréquemment posées sur ExpressRoute, consultez le [Forum aux questions ExpressRoute](expressroute-faqs.md).

## <a name="next-steps"></a>Étapes suivantes

* Consultez des informations supplémentaires sur les [modèles de connectivité ExpressRoute](expressroute-connectivity-models.md).
* Découvrez en détail les connexions ExpressRoute et les domaines de routage. Consultez la page [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Recherchez un fournisseur de services. Consultez la page [Partenaires ExpressRoute et emplacements d’homologation](expressroute-locations.md).
* Vérifiez que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).
* Reportez-vous aux conditions requises pour le [routage](expressroute-routing.md), la [traduction d’adresses réseau](expressroute-nat.md) et la [qualité de service](expressroute-qos.md).
* Configurez votre connexion ExpressRoute.
  * [Création d’un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configuration de l’homologation pour un circuit ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
  * [Connexion d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* En savoir plus sur les autres [fonctionnalités de mise en réseau](../networking/networking-overview.md) clés d’Azure.
