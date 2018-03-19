---
title: "Emplacements et fournisseurs de connectivité : Azure ExpressRoute | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et de la façon de se connecter à des régions Azure. Trié par emplacement."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2018
ms.author: pareshmu
ms.openlocfilehash: b626ebae8032902bae168460eeede00dfdc7dfc2
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="expressroute-partners-and-peering-locations"></a>Partenaires ExpressRoute et emplacements d’homologation

> [!div class="op_single_selector"]
> * [Emplacements par fournisseur](expressroute-locations.md)
> * [Fournisseurs par emplacement](expressroute-locations-providers.md)


Les tables de cet article offrent des informations sur les fournisseurs de connectivité ExpressRoute, la couverture géographique ExpressRoute, les services cloud Microsoft pris en charge via ExpressRoute et les intégrateurs système ExpressRoute.

## <a name="partners"></a>Fournisseurs de connectivité ExpressRoute
ExpressRoute est pris en charge dans tous les emplacements et régions Azure. La carte ci-dessous fournit une liste des régions Azure et des emplacements ExpressRoute. Les emplacements ExpressRoute se réfèrent à ceux où Microsoft s’associe à plusieurs fournisseurs de services.

![Carte des emplacements][0]

Vous aurez accès aux services Azure dans toutes les régions au sein d’une région géopolitique si vous êtes connecté à au moins un emplacement ExpressRoute dans la région géopolitique. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique
Le tableau ci-dessous fournit une carte des régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.

| **Région géopolitique** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- |
| **Amérique du Nord** |Est des États-Unis, Ouest des États-Unis, Est des États-Unis 2, Ouest des États-Unis 2, Centre des États-Unis, Centre-Sud des États-Unis, Centre-Nord des États-Unis, Centre Ouest des États-Unis, Centre du Canada, Est du Canada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Washington DC, Montréal, Québec, Toronto |
| **Amérique du Sud** |Sud du Brésil |São Paulo |
| **Europe** |Europe du Nord, Europe de l’Ouest, Ouest du Royaume-Uni, Sud du Royaume-Uni |Amsterdam, Dublin, Londres, Newport (Pays de Galles), Paris |
| **Asie** |Asie orientale, Asie du Sud-Est |Hong Kong, Singapour, Singapour2 |
| **Japon** |Ouest du Japon, Est du Japon |Osaka, Tokyo |
| **Australie** |Sud-est de l’Australie |Est de l’Australie |Melbourne, Sydney |
| **Inde** |Inde-Ouest, Inde-Centre, Inde-Sud |Chennai, Mumbai |
| **Corée du Sud** |Centre de la Corée, Corée du Sud |Busan, Séoul |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Régions et limites géopolitiques pour les clouds nationaux
Le tableau ci-dessous fournit des informations sur les régions et les limites géopolitiques et des clouds nationaux.

| **Région géopolitique** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- |
| **Cloud du gouvernement des États-Unis** |Gouvernement des États-Unis - Iowa, Gouvernement des États-Unis - Virginie, US DoD Centre, US DoD Est  |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **Chine** |Chine du Nord, Chine orientale |Beijing, Shanghai |
| **Allemagne** |Allemagne centrale, Allemagne de l’est |Berlin, Francfort |

La connectivité entre les régions géopolitiques n’est pas prise en charge dans la référence ExpressRoute Standard. Vous devez activer le module complémentaire ExpressRoute Premium pour prendre en charge la connectivité globale. La connectivité à des environnements de cloud nationaux n’est pas prise en charge. En cas de besoin, vous pouvez collaborer avec votre fournisseur de connectivité.

## <a name="locations"></a>Emplacements de fournisseur de connectivité

Le tableau suivant présente les emplacements de connectivité et les fournisseurs de services pour chaque emplacement. Si vous souhaitez afficher les fournisseurs de service et les lieux pour lesquels ils peuvent fournir le service, consultez la page [Lieux par fournisseur de services](expressroute-locations.md#locations). 


### <a name="production-azure"></a>Production Azure
| **Lieu** | **Fournisseurs de services** |
| --- | --- |
| **Amsterdam** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions - Cloud Connect, Interxion, KPN, Level 3 Communications, Megaport, NTT Communications Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Verizon, Zayo |
| **Atlanta** |Equinix |
| **Busan** |LG CNS |
| **Chennai** | Airtel+, Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chicago** |AT&T NetBond, Comcast, Coresite, Equinix, PacketFabric, Level 3 Communications, Megaport, Verizon, Zayo |
| **Dallas** |Aryaka Networks, AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport, Verizon, Zayo|
| **Denver** |CoreSite, Megaport |
| **Dublin** |Colt, eir, Interxion, Megaport, Telecity Group |
| **Hong Kong** |Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Las Vegas** |Level 3 Communications, Megaport |
| **Londres** |AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telehouse - KDDI, Telenor, Verizon, Vodafone, Zayo |
| **Los Angeles** |CoreSite, Equinix, Megaport, NTT, Zayo |
| **Melbourne** |AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Miami** |C3ntro+, Megaport, Neutrona Networks |
| **Montréal** |Bell Canada, Cologix, Telus, Zayo |
| **Mumbai** |Airtel+, Global CloudXchange (GCX), Sify, Tata Communications |
| **New York** |Coresite, Equinix, Level 3 Communications, Megaport, Zayo |
| **Newport (Nouvelle-Galles du Sud)** |Communications de niveau 3, données de dernière génération |
| **Osaka** |Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paris** |Colt, Intercloud, Interxion Equinix, Orange |
| **Québec** | Bell Canada, Megaport |
| **San Antonio** |Level 3 Communications, Megaport |
| **São Paulo** |Ascenty Data Centers+, Equinix, Level 3 Communications, Neutrona Networks, Telefonica, UOLDIVEO |
| **Seattle** |Equinix, Level 3 Communications, Megaport |
| **Séoul** |KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** |Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Coresite, Equinix, PacketFabric, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |
| **Singapour** |Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Verizon |
| **Singapore2** |Megaport, SingTel |
| **Sydney** |AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, NTT Communications, Orange, Telstra Corporation, Verizon |
| **Tokyo** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. (IIJ), Level 3 Communications, NTT Communications, Softbank, Verizon |
| **Toronto** |AT&T NetBond, Bell Canada, Cologix, Console, Equinix, Megaport, Telus, Zayo |
| **Washington DC** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, Sprint, Tata Communications, Verizon, Zayo |

 **+** = bientôt disponible

### <a name="national-cloud-environments"></a>Environnements de cloud national

### <a name="us-government-cloud"></a>Cloud du gouvernement des États-Unis
| **Lieu** | **Fournisseurs de services** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, Level 3 Communications+, Verizon |
| **Silicon Valley** | Equinix, Level 3 Communications |
| **Seattle** | Equinix |
| **Washington DC** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |

### <a name="china"></a>Chine
| **Lieu** | **Fournisseurs de services** |
| --- | --- |
| **Beijing** |China Telecom |
| **Shanghai** |China Telecom |

Pour plus d’informations, consultez [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Allemagne
| **Emplacement** | **Fournisseurs de services** |
| --- | --- |
| **Berlin** |Colt+, e-shelter, Megaport+, T-Systems |
| **Francfort** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Connectivité via des fournisseurs Exchange
Si votre fournisseur de connectivité ne se trouve pas dans la liste des sections précédentes, vous pouvez quand même créer une connexion.

* Vérifiez auprès de votre fournisseur de connectivité s’il est connecté à l’un des échanges dans le tableau ci-dessous. Vous pouvez consulter les liens ci-dessous pour recueillir des informations supplémentaires sur les services proposés par les fournisseurs d’échange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](http://www.interxion.com/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Demandez à votre fournisseur de connectivité d’étendre votre réseau à l’emplacement d’homologation de votre choix.
  * Vérifiez que votre fournisseur de connectivité étend votre connectivité avec une haute disponibilité pour éviter tout point de défaillance unique.
* Commandez un circuit ExpressRoute avec échange en tant que fournisseur de connectivité pour se connecter à Microsoft.
  * Pour définir la connectivité, procédez de la manière décrite dans [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="c1partners"></a>Connectivité via d’autres fournisseurs de services
| **Lieu** | **Microsoft Exchange** | **Fournisseurs de connectivité** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Telecity | Eurofiber, Fastweb S.p.A, MainOne, Nianet, Telia |
| **Chicago** | Equinix | Lightower, Windstream |
| **Dallas** | Equinix, Megaport | C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **Francfort** | Telecity | Nianet, QSC AG |
| **Hong Kong** | Equinix | Macroview Telecom |
| **Londres** | Equinix, euNetworks, Telecity | Bezeq International Ltd., Epsilon, Exponential E, HSO, NexGen Networks, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montréal** | Cologix, Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Equinix | Cox Business, Windstream |
| **Singapour** |Equinix |1CLOUDSTAR, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, Gtt Communications Inc, Epsilon, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Intégrateurs système ExpressRoute
L’activation de la connectivité privée pour l’adapter à vos besoins peut s’avérer difficile selon l’échelle de votre réseau. Vous pouvez faire appel à l’un des intégrateurs système figurant dans le tableau ci-dessous pour vous aider à intégrer ExpressRoute.

| **Continent** | **Intégrateurs système** |
| --- | --- |
| **Asie** |Avanade Inc., OneAs1a |
| **Australie** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europe** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Amérique du Nord** |Avanade Inc., Equinix Professional Services, FlexManage, Perficient, Presidio |
| **Amérique du Sud** |Avanade Inc. |
## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
* Assurez-vous que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte géographique"
