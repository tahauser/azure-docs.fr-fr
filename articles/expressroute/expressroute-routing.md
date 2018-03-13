---
title: Configuration de routage requise pour Azure ExpressRoute | Microsoft Docs
description: "Cette page détaille les conditions nécessaires à la configuration et à la gestion du routage pour les circuits ExpressRoute."
documentationcenter: na
services: expressroute
author: ganesr
manager: ganesr
editor: 
ms.assetid: 5b382e79-fa3f-495a-a764-c5ff86af66a2
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: ganesr
ms.openlocfilehash: 87cf32c23c2b3f50057016a23212c95b706f2910
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="expressroute-routing-requirements"></a>Configuration requise pour le routage ExpressRoute
Pour vous connecter aux services de cloud Microsoft à l’aide d’ExpressRoute, vous devez configurer et gérer le routage. Certains fournisseurs de connectivité proposent la configuration et la gestion du routage comme un service géré. Vérifiez auprès de votre fournisseur de connectivité s’il offre ce service. Si ce n’est pas le cas, vous devez respecter les conditions suivantes :

Pour obtenir une description des sessions de routage qui doivent être configurées afin d’établir la connectivité, reportez-vous à l’article [Circuits et domaines de routage](expressroute-circuit-peerings.md).

> [!NOTE]
> Microsoft ne prend pas en charge les protocoles de redondance de routeur (HSRP, VRRP, par exemple) pour les configurations à haute disponibilité. Nous nous appuyons sur une paire de sessions BGP par homologation pour la haute disponibilité.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Adresses IP utilisées pour les homologations
Vous devez réserver quelques blocs d’adresses IP pour configurer le routage entre votre réseau et les routeurs Microsoft Enterprise Edge (MSEE). Cette section fournit une liste des conditions requises et décrit les règles relatives à la façon dont ces adresses IP doivent être acquises et utilisées.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Adresses IP utilisées pour l’homologation privée Azure
Pour configurer les homologations, vous pouvez utiliser des adresses IP privées ou publiques. La plage d’adresses utilisée pour configurer des routages ne doit pas chevaucher les plages d’adresses utilisées pour créer des réseaux virtuels dans Azure. 

* Vous devez réserver un sous-réseau /29 ou deux sous-réseaux /30 pour les interfaces de routage.
* Les sous-réseaux utilisés pour le routage peuvent être des adresses IP privées ou publiques.
* Les sous-réseaux ne doivent pas entrer en conflit avec la plage réservée par le client pour une utilisation dans le cloud Microsoft.
* Si un sous-réseau /29 est utilisé, il est subdivisé en deux sous-réseaux /30. 
  * Le premier sous-réseau /30 est utilisé pour le lien principal, et le second sous-réseau /30 est utilisé pour le lien secondaire.
  * Pour chacun des sous-réseaux /30, vous devez utiliser la première adresse IP du sous-réseau /30 sur votre routeur. Microsoft utilise la deuxième adresse IP du sous-réseau /30 pour configurer une session BGP.
  * Vous devez configurer les deux sessions BGP pour que notre [contrat SLA de disponibilité](https://azure.microsoft.com/support/legal/sla/) soit valide.  

#### <a name="example-for-private-peering"></a>Exemple pour l’homologation privée
Si vous choisissez d’utiliser un sous-réseau a.b.c.d/29 pour configurer l’homologation, il est scindé en deux sous-réseaux /30. Dans l’exemple ci-dessous, nous examinons l’utilisation du sous-réseau a.b.c.d/29. 

a.b.c.d/29 est scindé en a.b.c.d/30 et a.b.c.d+4/30 puis transmis à Microsoft via les API d’approvisionnement. Vous utilisez a.b.c.d+1 comme l’IP VRF pour le PE principal et Microsoft utilisera a.b.c.d+2 comme IP VRF pour le MSEE principal. Vous utilisez a.b.c.d+5 comme l’IP VRF pour le PE secondaire et Microsoft utilisera a.b.c.d+6 IP VRF pour le MSEE secondaire.

Imaginons que vous sélectionnez 192.168.100.128/29 pour configurer l’homologation privée. 192.168.100.128/29 inclut les adresses de 192.168.100.128 à 192.168.100.135, parmi lesquelles :

* 192.168.100.128/30 sera attribuée à link1, et le fournisseur utilisera 192.168.100.129 tandis que Microsoft utilisera 192.168.100.130.
* 192.168.100.132/30 sera attribuée à link2, et le fournisseur utilisera 192.168.100.133 tandis que Microsoft utilisera 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-peering"></a>Adresses IP utilisées pour l’homologation publique Azure
Vous devez utiliser des adresses IP publiques que vous possédez pour configurer les sessions BGP. Microsoft doit être en mesure de vérifier la propriété des adresses IP via les Registres Internet de routage régional (RIR) et les Registres Internet de routage (IRR). 

* Vous devez utiliser un sous-réseau unique /29 ou deux sous-réseaux /30 afin de configurer l’homologation BGP pour chaque homologation par circuit ExpressRoute (si vous en avez plusieurs). 
* Si un sous-réseau /29 est utilisé, il est subdivisé en deux sous-réseaux /30. 
  * Le premier sous-réseau /30 est utilisé pour le lien principal, et le second sous-réseau /30 est utilisé pour le lien secondaire.
  * Pour chacun des sous-réseaux /30, vous devez utiliser la première adresse IP du sous-réseau /30 sur votre routeur. Microsoft utilise la deuxième adresse IP du sous-réseau /30 pour configurer une session BGP.
  * Vous devez configurer les deux sessions BGP pour que notre [contrat SLA de disponibilité](https://azure.microsoft.com/support/legal/sla/) soit valide.

### <a name="ip-addresses-used-for-microsoft-peering"></a>Adresses IP utilisées pour l’homologation Microsoft
Vous devez utiliser des adresses IP publiques que vous possédez pour configurer les sessions BGP. Microsoft doit être en mesure de vérifier la propriété des adresses IP via les Registres Internet de routage régional (RIR) et les Registres Internet de routage (IRR).

* Vous devez utiliser un sous-réseau unique /29 (IPv4) ou /125 (IPv6) ou bien deux sous-réseaux /30 (IPv4) ou /126 (IPv6) afin de configurer l’homologation BGP pour chaque homologation par circuit ExpressRoute (si vous en avez plusieurs).
* Si un sous-réseau /29 est utilisé, il est subdivisé en deux sous-réseaux /30.
* Le premier sous-réseau /30 est utilisé pour le lien principal, et le second sous-réseau /30 sera utilisé pour le lien secondaire.
* Pour chacun des sous-réseaux /30, vous devez utiliser la première adresse IP du sous-réseau /30 sur votre routeur. Microsoft utilise la deuxième adresse IP du sous-réseau /30 pour configurer une session BGP.
* Si un sous-réseau /125 est utilisé, il est subdivisé en deux sous-réseaux /126.
* Le premier sous-réseau /126 est utilisé pour le lien principal, et le second sous-réseau /126 sera utilisé pour le lien secondaire.
* Pour chacun des sous-réseaux /126, vous devez utiliser la première adresse IP du sous-réseau /126 sur votre routeur. Microsoft utilise la deuxième adresse IP du sous-réseau /126 pour configurer une session BGP.
* Vous devez configurer les deux sessions BGP pour que notre [contrat SLA de disponibilité](https://azure.microsoft.com/support/legal/sla/) soit valide.

## <a name="public-ip-address-requirement"></a>Spécification d’adresse IP publique

### <a name="private-peering"></a>Homologation privée
Vous pouvez choisir d’utiliser des adresses IPv4 publiques ou privées pour l’homologation privée. Nous fournissons une isolation de bout en bout du trafic. Ainsi, le chevauchement des adresses avec d’autres clients n’est pas possible dans le cas d’une homologation privée. Ces adresses ne sont pas publiées sur Internet. 


### <a name="public-peering"></a>Homologation publique
Le chemin d'homologation publique Azure vous permet de vous connecter à tous les services hébergés dans Azure en utilisant leurs adresses IP publiques. Cela inclut les services répertoriés dans le [FAQ sur ExpressRoute](expressroute-faqs.md) et tous les services hébergés par les éditeurs de logiciels sur Microsoft Azure. La connectivité aux services Microsoft Azure sur l’homologation publique est toujours lancée de votre réseau vers le réseau Microsoft. Vous devez utiliser des adresses IP publiques pour le trafic destiné au réseau Microsoft.

> [!IMPORTANT]
> Tous les services PaaS Azure sont également accessibles via l’homologation Microsoft. Nous vous recommandons de créer l’homologation Microsoft et de connecter les services PaaS Azure via cette dernière.  
>   


Un numéro AS privé est autorisé avec l’homologation publique.

### <a name="microsoft-peering"></a>Homologation Microsoft
Le chemin d’homologation Microsoft vous permet de vous connecter aux services de cloud Microsoft non pris en charge via le chemin d'homologation publique Azure. La liste des services inclut les services Office 365, notamment Exchange Online, SharePoint Online, Skype Entreprise et Dynamics 365. Microsoft prend en charge la connectivité bidirectionnelle sur l’homologation Microsoft. Le trafic destiné aux services de cloud Microsoft doit utiliser des adresses IPv4 publiques valides avant leur entrée sur le réseau Microsoft.

Assurez-vous que votre adresse IP et votre numéro AS sont enregistrés à votre nom dans l’un des registres ci-dessous :


* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

Si vos préfixes et le numéro ASN ne vous sont pas affectés dans les registres précédents, vous devez ouvrir un dossier de support pour valider manuellement vos préfixes et votre numéro ASN. La prise en charge requiert un document, tel qu’une lettre d’autorisation, qui prouve que vous êtes autorisé à utiliser les ressources.

Un numéro ASN privé est autorisé avec l’homologation Microsoft, mais nécessite également une validation manuelle.

> [!IMPORTANT]
> Les adresses IP publiques proposées à Microsoft via ExpressRoute ne doivent pas être publiées sur Internet. Cela pourrait interrompre la connectivité avec d’autres services Microsoft. Toutefois, les adresses IP publiques utilisées par les serveurs de votre réseau qui communiquent avec les points de terminaison O365 au sein de Microsoft peuvent être publiées via ExpressRoute. 
> 
> 

## <a name="dynamic-route-exchange"></a>Échange de routage dynamique
L’échange de routage s’effectuera via le protocole eBGP. Des sessions EBGP sont établies entre les MSEE et les routeurs. L’authentification des sessions BGP n’est pas obligatoire. Si nécessaire, un hachage MD5 peut être configuré. Pour plus d’informations sur la configuration des sessions BGP, consultez [Configuration du routage](expressroute-howto-routing-classic.md) et [Workflows d’approvisionnement du circuit et états du circuit](expressroute-workflows.md).

## <a name="autonomous-system-numbers"></a>Numéros système autonomes
Microsoft utilise le numéro AS 12076 pour les homologations publiques Azure, privées Azure et Microsoft. Nous avons réservé les numéros AS 65515 à 65520 pour un usage interne. Les numéros AS 16 bits et 32 bits sont pris en charge. Nous avons besoin d’un ASN enregistré publiquement pour l’homologation Microsoft uniquement. Les homologations privées et publiques peuvent utiliser des ASN privés.

Il n’existe aucune exigence concernant une symétrie de transfert des données. Les chemins d’envoi et de réception peuvent transiter par différentes paires de routeurs. Les routages identiques doivent être publiés des deux côtés sur plusieurs paires de circuits vous appartenant. Les métriques de routage n’ont pas besoin d’être identiques.

## <a name="route-aggregation-and-prefix-limits"></a>Agrégation de routages et limites de préfixes
Nous prenons en charge jusqu’à 4 000 préfixes qui nous sont proposés via l’homologation privée Azure. Ce chiffre peut être augmenté jusqu’à 10 000 préfixes si le module complémentaire ExpressRoute premium est activé. Nous acceptons jusqu’à 200 préfixes par session BGP pour les homologations publiques Azure et Microsoft. 

La session BGP s’arrête si le nombre de préfixes dépasse la limite. Nous accepterons les routages par défaut uniquement sur le lien d’homologation privée. Le fournisseur doit filtrer les adresses IP de routage et privées par défaut (RFC 1918) des chemins d’homologation publique Azure et Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Routage de transit et routage entre régions
ExpressRoute ne peut pas être configuré comme des routeurs de transit. Vous devez vous appuyer sur votre fournisseur de connectivité pour les services de routage de transit.

## <a name="advertising-default-routes"></a>Publication des routages par défaut
Les routages par défaut sont autorisés uniquement sur les sessions d’homologation privées Azure. Dans ce cas, nous acheminerons tout le trafic des réseaux virtuels associés vers votre réseau. La publication de routages par défaut dans l’homologation privée entraîne le blocage du chemin Internet à partir d’Azure. Vous devez compter sur votre matériel edge d’entreprise afin d’acheminer le trafic depuis et vers Internet pour les services hébergés dans Azure. 

 Pour activer la connectivité avec d’autres services Azure et services d’infrastructure, vous devez vous assurer qu’un des éléments suivants est en place :

* L’homologation publique Azure est activée pour acheminer le trafic vers les points de terminaison publics
* Vous utilisez le routage défini par l’utilisateur pour permettre la connectivité Internet pour chaque sous-réseau nécessitant une connectivité Internet définie par l’utilisateur.

> [!NOTE]
> La publication des routages par défaut arrête Windows et toute autre activation de licence de machine virtuelle. Suivez les instructions [ici](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) pour contourner ce problème.
> 
> 

## <a name="bgp"></a>Prise en charge des communautés BGP
Cette section fournit une vue d'ensemble de l'utilisation des communautés BGP avec ExpressRoute. Microsoft publiera des routages sur les chemins d’homologation publiques et Microsoft avec des routages marqués à l’aide des valeurs de communauté appropriées. La logique de cette procédure et les détails concernant les valeurs de la communauté sont décrits ci-dessous. Cependant, Microsoft ignorera toutes les valeurs de communauté marquées pour des itinéraires qui lui sont proposés.

Si vous vous connectez à Microsoft via ExpressRoute dans n’importe quel emplacement d’homologation d’une région géopolitique, vous aurez accès à tous les services de cloud Microsoft de toutes les régions situées dans les limites géopolitiques. 

Par exemple, si vous êtes connecté à Microsoft à Amsterdam via ExpressRoute, vous aurez accès à tous les services de cloud Microsoft hébergés en Europe du Nord et en Europe occidentale. 

Reportez-vous à la page [Partenaires ExpressRoute et emplacements d’homologation](expressroute-locations.md) pour obtenir une liste détaillée des régions géopolitiques, des régions Azure associées et des emplacements d’homologation ExpressRoute correspondants.

Vous pouvez acheter plusieurs circuits ExpressRoute par région géopolitique. Le fait de disposer de plusieurs connexions vous offre des avantages significatifs en termes de haute disponibilité en raison de la redondance géographique. Si vous avez plusieurs circuits ExpressRoute, vous recevrez le même jeu de préfixes publiés par Microsoft sur les chemins d'homologation publiques et Microsoft. Cela signifie que vous disposez de plusieurs chemins de votre réseau vers Microsoft. Vous risquez ainsi de prendre des décisions de routage non optimales au sein de votre réseau. Et par conséquent, vous risquez de rencontrer des problèmes de connectivité non optimale avec différents services. Vous pouvez compter sur les valeurs fournies par la communauté pour prendre les bonnes décisions en matière de routage et offrir un [routage optimal aux utilisateurs](expressroute-optimize-routing.md).

| **Région Microsoft Azure** | **Valeur de communauté BGP** |
| --- | --- |
| **Amérique du Nord** | |
| Est des États-Unis | 12076:51004 |
| Est des États-Unis 2 | 12076:51005 |
| États-Unis de l’Ouest | 12076:51006 |
| Ouest des États-Unis 2 | 12076:51026 |
| Centre-Ouest des États-Unis | 12076:51027 |
| Centre-Nord des États-Unis | 12076:51007 |
| États-Unis - partie centrale méridionale | 12076:51008 |
| Centre des États-Unis | 12076:51009 |
| Centre du Canada | 12076:51020 |
| Est du Canada | 12076:51021 |
| **Amérique du Sud** | |
| Sud du Brésil | 12076:51014 |
| **Europe** | |
| Europe du Nord | 12076:51003 |
| Europe de l’Ouest | 12076:51002 |
| Sud du Royaume-Uni | 12076:51024 |
| Ouest du Royaume-Uni | 12076:51025 |
| France-Centre | 12076:51030 |
| France-Sud | 12076:51031 |
| **Asie-Pacifique** | |
| Est de l'Asie | 12076:51010 |
| Asie du Sud-Est | 12076:51011 |
| **Japon** | |
| Est du Japon | 12076:51012 |
| Ouest du Japon | 12076:51013 |
| **Australie** | |
| Est de l’Australie | 12076:51015 |
| Sud-est de l’Australie | 12076:51016 |
| **Inde** | |
| Sud de l’Inde | 12076:51019 |
| Inde-Ouest | 12076:51018 |
| Inde-Centre | 12076:51017 |
| **Corée** | |
| Corée du Sud | 12076:51028 |
| Centre de la Corée | 12076:51029 |


Tous les routages publiés par Microsoft seront marqués avec la valeur de communauté appropriée. 

> [!IMPORTANT]
> Les préfixes globaux sont marqués avec une valeur de communauté appropriée et seront publiés uniquement lorsque le module complémentaire Premium ExpressRoute est activé.
> 
> 

Par ailleurs, Microsoft marquera également des préfixes basés sur le service auquel ils appartiennent. Cela s'applique uniquement à l'homologation Microsoft. Le tableau ci-dessous fournit un mappage d’un service à la valeur de communauté BGP.

| **Service** | **Valeur de communauté BGP** |
| --- | --- |
| Exchange Online | 12076:5010 |
| SharePoint Online | 12076:5020 |
| Skype Entreprise Online | 12076:5030 |
| Dynamics 365 | 12076:5040 |
| Autres services Office 365 en ligne | 12076:5100 |

> [!NOTE]
> Microsoft ignore les valeurs de communauté BGP définies sur les itinéraires proposés à Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds-preview"></a>Support de la communauté BGP dans les clouds nationaux (version préliminaire)

| **Région Azure pour les clouds nationaux**| **Valeur de communauté BGP** |
| --- | --- |
| **Gouvernement américain** |  |
| Gouvernement des États-Unis – Arizona | 12076:51106 |
| US Gov Iowa | 12076:51109 |
| Gouvernement américain - Virginie | 12076:51105 |
| Gouvernement des États-Unis – Texas | 12076:51108 |
| Centre des États-Unis – US DoD | 12076:51209 |
| Est des États-Unis – US DoD | 12076:51205 |


| **Service dans les clouds nationaux** | **Valeur de communauté BGP** |
| --- | --- |
| **Gouvernement américain** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype Entreprise Online |12076:5130 |
| Dynamics 365 |12076:5140 |
| Autres services Office 365 en ligne |12076:5200 |

## <a name="next-steps"></a>Étapes suivantes
* Configurez votre connexion ExpressRoute.
  
  * [Créer et modifier un circuit](expressroute-howto-circuit-arm.md)
  * [Créer et modifier une configuration de l’homologation](expressroute-howto-routing-arm.md)
  * [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
