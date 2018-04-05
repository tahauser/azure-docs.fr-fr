---
title: Présentation de Azure Load Balancer Standard | Microsoft Docs
description: Présentation des fonctionnalités Azure Load Balancer Standard
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: d7ee74a19f806faed0bcfcfa5f1c5de3937d9f31
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-load-balancer-standard-overview"></a>Présentation de Azure Load Balancer Standard

Azure Load Balancer vous permet de mettre à l’échelle vos applications et de créer une haute disponibilité pour vos services. Load Balancer peut être utilisé dans des scénarios entrants et sortants et offre une latence faible, un débit élevé et une montée en puissance jusqu’à plusieurs millions de flux pour toutes les applications TCP et UDP. 

Cet article se concentre sur Load Balancer Standard.  Pour obtenir une présentation plus générale d’Azure Load Balancer, consultez [Présentation de Load Balancer](load-balancer-overview.md) également.

## <a name="what-is-standard-load-balancer"></a>Qu’est-ce que Load Balancer Standard ?

Load Balancer Standard est un nouveau produit Load Balancer pour toutes les applications TCP et UDP avec une fonctionnalité mieux développée et plus granulaire définie sur Load Balancer de base.  Bien qu’il existe de nombreuses similitudes, il est important de vous familiariser avec les différences, comme souligné dans cet article.

Vous pouvez utiliser Load Balancer Standard comme un équilibreur de charge public ou interne. Et une machine virtuelle peut être connectée à une ressource Load Balancer publique et interne.

Les fonctions de la ressource Load Balancer sont toujours exprimées en tant que définition de serveur frontal, de règle, de sonde d’intégrité et de pool principal.  Une ressource peut contenir plusieurs règles. Vous pouvez placer des machines virtuelles dans le pool principal en spécifiant le pool principal à partir de la ressource de la carte réseau de la machine virtuelle.  Dans le cas d’un groupe de machines virtuelles identiques, ce paramètre est passé via le profil de réseau et développé.

L’étendue du réseau virtuel pour la ressource constitue un aspect clé.  Bien que Load Balancer de base existe dans l’étendue d’un groupe à haute disponibilité, un Load Balancer Standard est entièrement intégré à l’étendue d’un réseau virtuel et tous les concepts de réseau virtuel s’appliquent.

Les ressources Load Balancer sont des objets dans lesquels vous pouvez exprimer la manière dont Azure doit programmer son infrastructure mutualisée pour obtenir le scénario que vous souhaitez créer.  Il n’existe aucune relation directe entre les ressources Load Balancer et l’infrastructure réelle ; la création d’un Load Balancer ne crée pas d’instance et il y a toujours de la capacité disponible et aucun retard de démarrage ou de mise à l’échelle à prendre en compte. 

>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios.  Si vous recherchez une terminaison TLS (« déchargement SSL ») ou un traitement de couche d’application par requête HTTP/HTTPS, consultez [Application Gateway](../application-gateway/application-gateway-introduction.md).  Si vous recherchez un équilibrage de charge DNS global, consultez [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions en fonction de vos besoins.

## <a name="why-use-standard-load-balancer"></a>Pourquoi utiliser Load Balancer Standard ?

Utilisez Load Balancer Standard pour l’intégralité de la plage de centres de données virtuels, allant des déploiements à petite échelle à des architectures multizones étendues et complexes.

Consultez la table ci-dessous pour obtenir une vue d’ensemble des différences entre Load Balancer Standard et Load Balancer de base :

>[!NOTE]
> De nouvelles conceptions doivent être envisagées à l’aide de Load Balancer Standard. 

| | Référence SKU standard | Référence SKU de base |
| --- | --- | --- |
| Taille de pool de serveur principal | jusqu'à 1 000 instances | jusqu'à 100 instances |
| Points de terminaison de pool principal | toute machine virtuelle dans un seul réseau virtuel, y compris la fusion de machines virtuelles, les groupes à haute disponibilité, groupes de machines identiques. | machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques unique |
| Zones de disponibilité | serveurs frontaux redondants dans une zone et zonaux pour trafic entrant et sortant, les mappages de flux sortants survivent aux échecs de zone, l’équilibrage de charge inter-zone | / |
| Diagnostics | Azure Monitor, métriques à plusieurs dimensions, notamment les compteurs d’octets et de paquets, état de la sonde d’intégrité, tentatives de connexion (TCP SYN), intégrité de la connexion sortante (flux SNAT réussies et échouées), mesures de plan de données actives | Azure Log Analytics pour le Load Balancer public seulement, alerte d’insuffisance SNAT, compte d’intégrité du pool principal |
| Ports HA | Load Balancer interne | / |
| Sécuriser par défaut | par défaut fermé pour les points de terminaison IP et Load Balancer publics et un groupe de sécurité réseau doit être utilisé pour mettre explicitement sur liste verte le flux du trafic | ouvert par défaut, groupe de sécurité réseau facultatif |
| Connexions sortantes | Plusieurs serveurs frontaux avec règle de retrait. Un scénario sortant _doit_ être explicitement créé pour que la machine virtuelle puisse utiliser une connectivité sortante.  [Les points de terminaison de service du réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) peuvent être atteints sans connectivité sortante et ne sont pas comptabilisés dans les données traitées.  Toutes les adresses IP publiques, y compris les services PaaS Azure non disponibles comme les points de terminaison de service réseau virtuel doivent être atteints via la connectivité sortante et comptabilisés dans les données traitées. Lorsque seul un Load Balancer interne est utilisé par une machine virtuelle, les connexions sortantes via la SNAT par défaut ne sont pas disponibles. La programmation de SNAT sortante est un protocole de transport spécifique basé sur le protocole de la règle d’équilibrage de charge entrant. | Serveur frontal unique, sélectionné de manière aléatoire lorsque plusieurs serveurs frontaux sont présents.  Lorsque seul un Load Balancer interne est utilisé par une machine virtuelle, la valeur par défaut SNAT est utilisée. |
| Plusieurs serveurs frontaux | Trafic entrant et sortant | Entrant uniquement |
| Opérations de gestion | La plupart des opérations < 30 secondes | généralement 60 à 90 secondes et plus par défaut |
| Contrat SLA | 99,99 % pour le chemin de données avec les deux machines virtuelles intègres | Implicite dans le contrat de niveau de service de la machine virtuelle | 
| Tarifs | Facturé en fonction du nombre de règles, des données traitées entrantes ou sortantes associées aux ressources  | Aucun frais |

Consultez les [limites de service pour Load Balancer](https://aka.ms/lblimits), ainsi que la [tarification](https://aka.ms/lbpricing) et le [contrat de niveau de service](https://aka.ms/lbsla).


### <a name="backend"></a>Pool principal

Les pools principaux Load Balancer Standard se développent sur n’importe quelle ressource de machine virtuelle dans un réseau virtuel.  Il peut contenir jusqu'à 1 000 instances de serveur principal.  Une instance de serveur principal est une configuration IP, qui est une propriété de ressource de la carte réseau.

Le pool principal peut contenir des machines virtuelles autonomes, des groupes à haute disponibilité ou des groupes de machines virtuelles identiques.  Vous pouvez fusionner les ressources dans le pool principal et il peut contenir n’importe quelle combinaison de ces ressources, jusqu'à 150 au total.

Lorsque vous envisagez la conception de votre pool principal, vous pouvez concevoir le moins de ressources de pool principal individuelles pour optimiser davantage la durée des opérations de gestion.  Il n’existe aucune différence de performances ou de mise à l’échelle du plan de données.

## <a name="az"></a> Zones de disponibilité

>[!NOTE]
> Pour utiliser [Préversion des Zones de disponibilité](https://aka.ms/availabilityzones) avec Load Balancer Standard une [inscription aux Zones de disponibilité](https://aka.ms/availabilityzones) est requise.

Load Balancer Standard prend en charge des fonctionnalités supplémentaires dans les régions où les Zones de disponibilité sont disponibles.  Ces fonctionnalités sont incrémentielles pour tous les Load Balancer Standard fournis.  Les configurations de Zones de disponibilité sont disponibles pour Load Balancer Standard public et interne.

Des serveurs frontaux non-zonaux deviennent redondants dans une zone par défaut lors du déploiement dans une région avec des Zones de disponibilité.   Un serveur frontal redondant dans une zone survit à des échecs de zone et est pris en charge par une infrastructure dédiée dans toutes les zones simultanément. 

En outre, vous pouvez garantir un serveur frontal dans une zone spécifique. Un serveur frontal zonal partage le même devenir que la zone concernée et est pris en charge uniquement par une infrastructure dédiée dans une seule zone.

L’équilibrage de charge entre les zones est disponible pour le pool principal et n’importe quelle ressource de machine virtuelle dans un réseau virtuel peut faire partie d’un pool principal.

Consultez la [discussion détaillée sur les fonctionnalités relatives aux Zones de disponibilité](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnostics

Load Balancer Standard fournit des métriques multidimensionnelles via Azure Monitor.  Ces métriques peuvent être filtrées, regroupées et fournissent des analyses en cours et historiques sur les performances et l’intégrité de votre service.  Resource Health est également pris en charge.  Voici une brève vue d’ensemble des diagnostics pris en charge :

| Métrique | Description |
| --- | --- |
| Disponibilité VIP | La référence Standard de Load Balancer teste en continu le chemin de données d’une région vers le serveur frontal Load Balancer, jusqu’à la pile SDN qui prend en charge votre machine virtuelle. Tant que les instances saines restent, la mesure suit le même chemin que le trafic à charge équilibrée de vos applications. Le chemin de données utilisé par vos clients est également validé. La mesure est invisible pour votre application et n’interfère pas avec les autres opérations.|
| Disponibilité DIP | La référence Standard de Load Balancer utilise un service de détection d’intégrité distribué qui surveille l’intégrité du point de terminaison de votre application en fonction de vos paramètres de configuration. Cette métrique fournit un agrégat ou une vue filtrée par point de terminaison de chaque point de terminaison d’instance dans le pool Load Balancer.  Vous pouvez observer comment Load Balancer voit l’intégrité de votre application comme indiqué par votre configuration de sonde d’intégrité.
| Paquets SYN | La référence Standard de Load Balancer ne termine pas les connexions TCP et n’interagit pas avec les flux de paquets UDP et TCP. Les flux et leurs établissements de liaisons sont toujours entre la source et l’instance de machine virtuelle. Pour mieux résoudre les problèmes posés par vos scénarios de protocole TCP, vous pouvez utiliser les compteurs de paquets SYN pour comprendre le nombre de tentatives de connexion TCP effectuées. La métrique indique le nombre de paquets SYN TCP reçus.|
| Connexions SNAT | La référence Standard de Load Balancer indique le nombre de flux sortants usurpés sur le serveur frontal d’adresse IP publique. Les ports SNAT constituent une ressource qui n’est pas inépuisable. Cette métrique peut donner une idée de l’importance du rôle joué par SNAT dans votre application pour les flux sortants.  Les compteurs relatifs aux flux SNAT sortants réussis et mis en échec sont indiqués et peuvent être utilisés pour comprendre l’intégrité de vos flux sortants et résoudre les problèmes associés.|
| Compteurs d’octets | La référence Standard de Load Balancer indique les données traitées par serveur frontal.|
| Compteurs de paquets | La référence Standard de Load Balancer indique les paquets traités par serveur frontal.|

Consultez la [discussion détaillée sur les diagnostics de Load Balancer Standard](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Ports HA

Load Balancer Standard prend en charge un nouveau type de règle.  

Vous pouvez configurer des règles d’équilibrage de charge pour mettre à l’échelle votre application et qu’elle soit très fiable. Lorsque vous utilisez une règle d’équilibrage de charge des ports HA, Load Balancer Standard fournit un équilibrage de charge par flux sur tous les ports éphémères d’adresse IP de serveur frontal de Load Balancer Standard interne.  Cette fonctionnalité s’avère également utile pour d’autres scénarios, par exemple lorsqu’il est difficile ou qu’il n’est pas souhaitable de spécifier les ports individuels.

Une règle d’équilibrage de charge de ports HA vous permet de créer des scénarios actif-passif ou actif-actif n+1 pour des appliances virtuelles et toute application qui nécessite de grandes plages de ports entrants.  Une sonde d’intégrité peut être utilisée pour déterminer quels serveurs principaux devraient recevoir de nouveau flux.  Vous pouvez utiliser un groupe de sécurité réseau pour émuler un scénario de plage de ports.

>[!IMPORTANT]
> Si vous envisagez d’utiliser une appliance virtuelle de réseau, consultez votre fournisseur pour obtenir des conseils quant aux tests effectués sur leur produit avec les ports HA et suivre leurs instructions spécifiques pour l’implémentation. 

Consultez une [discussion détaillée sur les ports HA](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Sécuriser par défaut

Load Balancer Standard est entièrement intégré au réseau virtuel.  Le réseau virtuel est un réseau privé et fermé.  Étant donné que les équilibreurs de charge Standard et des adresses IP publiques Standard sont conçus pour permettre à ce réseau virtuel d’être accessible depuis l’extérieur du réseau virtuel, ces ressources sont désormais fermées par défaut, sauf si vous les ouvrez. Cela signifie que les Groupes de sécurité réseau (NSG) sont désormais utilisés pour autoriser explicitement et mettre sur liste verte le trafic autorisé.  Vous pouvez créer votre centre de données virtuel complet et décider via le Groupe de sécurité réseau de ce qui doit être disponible et quand.  Si vous n’avez pas de Groupe de sécurité réseau sur un sous-réseau ou une carte réseau de votre ressource de machine virtuelle, nous n’autoriserons pas au trafic d’atteindre cette ressource.

Pour plus d’informations sur les Groupes de sécurité réseau et la façon de les appliquer à votre scénario, consultez [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

### <a name="outbound"></a> Connexions sortantes

Load Balancer prend en charge les scénarios entrants et sortants.  Load Balancer Standard est très différent de Load Balancer de base en ce qui concerne les connexions sortantes.

La traduction d’adresses réseau sources (SNAT) est utilisée pour mapper les adresses IP internes et privées sur votre réseau virtuel vers les adresses IP publiques sur les serveurs frontaux Load Balancer.

Load Balancer Standard introduit un nouvel algorithme pour un [algorithme SNAT plus robuste, évolutif et prévisible](load-balancer-outbound-connections.md#snat) et active de nouvelles fonctionnalités, supprime l’ambiguïté et force les configurations explicites plutôt que les effets secondaires. Ces modifications sont nécessaires pour autoriser l’émergence de nouvelles fonctionnalités. 

Voici les principes clés à retenir lors de l’utilisation de Load Balancer Standard :

- la réalisation d’une règle dirige la ressource du Load Balancer  toute programmation Azure dérive de sa configuration
- lorsque plusieurs serveurs frontaux sont disponibles, tous les serveurs frontaux sont utilisés et chacun multiplie le nombre de ports SNAT disponibles
- vous pouvez choisir et contrôler si vous ne souhaitez pas utiliser un serveur frontal particulier pour des connexions sortantes
- les scénarios sortants sont explicites et la connectivité sortante n’existe pas jusqu'à ce qu’elle ait été spécifiée.
- les règles de l’équilibrage de charge infèrent la programmation de la SNAT. Les règles de l’équilibrage de charge sont spécifiques au protocole. La SNAT est spécifique au protocole et la configuration doit refléter cela plutôt que créer un effet secondaire.

#### <a name="multiple-frontends"></a>Plusieurs serveurs frontaux
Si vous souhaitez davantage de ports SNAT, car vous attendez ou vous rencontrez déjà une demande élevée pour les connexions sortantes, vous pouvez également ajouter l’inventaire de ports SNAT incrémentiels en configurant d’autres serveurs frontaux, des règles et des pools principaux aux ressources de la même machine virtuelle.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Contrôler quel serveur frontal utiliser pour la sortie
Si vous souhaitez limiter la provenance des connexions sortantes à une seule adresse IP de serveur frontal spécifique, vous pouvez éventuellement désactiver la SNAT sortante sur la règle qui exprime le mappage sortant.

#### <a name="control-outbound-connectivity"></a>Contrôler la connectivité sortante
Load Balancer Standard existe dans le contexte du réseau virtuel.  Un réseau virtuel est un réseau isolé et privé.  Sauf si une association avec une adresse IP publique existe, la connectivité n’est pas autorisée.  Vous pouvez atteindre des [points de terminaison de service du réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) car ils se trouvent à l’intérieur et sont locaux à votre réseau virtuel.  Si vous souhaitez établir une connectivité sortante vers une destination en dehors de votre réseau virtuel, vous avez deux options :
- assigner une adresse IP publique de référence SKU Standard comme adresse IP publique de niveau de l’Instance à la ressource de la machine virtuelle ou
- placer la ressource de la machine virtuelle dans le pool principal d’un Load Balancer Standard public.

Les deux permettent une connectivité sortante à partir du réseau virtuel vers l’extérieur du réseau virtuel. 

Si vous disposez _uniquement_ d’un Load Balancer Standard interne associé au pool principal dans lequel se trouve votre ressource de machine virtuelle, votre machine virtuelle ne peut atteindre que des ressources de réseau virtuel et les [points de terminaison de service du réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).  Vous pouvez suivre les étapes décrites dans le paragraphe précédent pour créer une connectivité sortante.

La connectivité sortante d’une ressource de machine virtuelle non associée aux références SKU Standard reste comme avant.

Consultez la [discussion détaillée sur les connexions sortantes](load-balancer-outbound-connections.md).

### <a name="multife"></a>Plusieurs serveurs frontaux
Load Balancer prend en charge plusieurs règles avec plusieurs serveurs frontaux.  Load Balancer Standard s’étend aux scénarios sortants.  Les scénarios sortants sont essentiellement l’inverse d’une règle d’équilibrage de charge entrante.  La règle d’équilibrage de charge entrante crée également une règle associée aux connexions sortantes. Load Balancer Standard utilise tous les serveurs frontaux associés à une ressource de machine virtuelle via une règle d’équilibrage de charge.  En outre, un paramètre sur la règle de l’équilibrage de charge vous permet de supprimer une règle d’équilibrage de charge pour les besoins de la connectivité sortante, ce qui permet de sélectionner les serveurs frontaux spécifiques, y compris aucun.

Pour la comparaison, Load Balancer de base sélectionne un seul serveur frontal de manière aléatoire et il n’existe aucune possibilité de contrôler quel serveur est sélectionné.

Consultez la [discussion détaillée sur les connexions sortantes](load-balancer-outbound-connections.md).

### <a name="operations"></a> Opérations de gestion

Les ressources de Load Balancer Standard existent sur une plateforme d’infrastructure entièrement nouvelle.  Cela permet des opérations de gestion beaucoup plus rapides pour les références SKU Standard et les durées d’exécution sont généralement inférieures à 30 secondes par ressource de référence SKU Standard.  Notez qu’étant donné que la taille des pools principaux augmente, la durée requise pour la modification du pool principal augmente également.

Vous pouvez modifier des ressources du Load Balancer Standard et déplacer une adresse IP publique Standard d’une machine virtuelle à l’autre beaucoup plus rapidement.

## <a name="migration-between-skus"></a>Migration entre les références SKU

Les références SKU ne sont pas mutables. Suivez les étapes décrites dans cette section pour passer d’une référence SKU de ressource à une autre.

### <a name="migrate-from-basic-to-standard-sku"></a>Migration de la référence SKU De base à la référence SKU Standard

1. Créez une ressource Standard (Load Balancer et adresses IP publiques si nécessaire). Recréez vos règles et définitions de sonde.

2. Créez ou mettez à jour un groupe de sécurité réseau (NSG) sur la carte réseau ou le sous-réseau pour mettre en liste blanche le trafic à charge équilibrée, la sonde, ainsi que tout autre trafic que vous souhaitez autoriser.

3. Supprimez les ressources de la référence SKU De base (Load Balancer et adresses IP publiques, le cas échéant) de toutes les instances de machine virtuelle. Veillez également à supprimer toutes les instances de machine virtuelle d’un groupe à haute disponibilité.

4. Associez toutes les instances de machine virtuelle aux nouvelles ressources de référence SKU Standard.

### <a name="migrate-from-standard-to-basic-sku"></a>Migration de la référence SKU Standard à la référence SKU De base

1. Créez une ressource De base (Load Balancer et adresses IP publiques si nécessaire). Recréez vos règles et définitions de sonde. 

2. Supprimez les ressources de la référence SKU Standard (Load Balancer et adresses IP publiques, le cas échéant) de toutes les instances de machine virtuelle. Veillez également à supprimer toutes les instances de machine virtuelle d’un groupe à haute disponibilité.

3. Associez toutes les instances de machine virtuelle aux nouvelles ressources de référence SKU De base.

>[!IMPORTANT]
>
>L’utilisation des références SKU De base et Standard impose certaines limitations.
>
>Les ports HA et les diagnostics de la référence SKU Standard sont uniquement disponibles dans la référence SKU Standard. Vous ne pouvez pas migrer de la référence SKU Standard à la référence SKU De base et conserver ces fonctionnalités.
>
>Les références SKU de base et Standard présentent plusieurs différences comme décrit dans cet article.  Assurez-vous avoir compris et être bien préparé.
>
>Les références SKU qui correspondent doivent être utilisées pour les ressources de Load Balancer et d’adresse IP publique. Vous ne pouvez pas avoir à la fois des ressources de référence SKU De base et de référence SKU Standard. Vous ne pouvez pas joindre des machines virtuelles autonomes, des machines virtuelles dans une ressource de groupe à haute disponibilité ou des ressources de groupe de machines virtuelles identiques aux deux références SKU simultanément.

## <a name="region-availability"></a>Disponibilité des régions

La référence Standard de Load Balancer est actuellement disponible dans toutes les régions cloud publiques.

## <a name="sla"></a>Contrat SLA

Les Load Balancer Standard sont disponibles avec un Contrat de niveau de service de 99,99 %.  Examinez le [Contrat de niveau de service Load Balancer Standard](https://aka.ms/lbsla) pour plus d’informations.

## <a name="pricing"></a>Tarifs

Load Balancer Standard est un produit facturé en fonction du nombre de règles d’équilibrage de charge configurées et du volume total de données entrantes et sortantes traitées. Pour plus d’informations sur la tarification de Load Balancer Standard, consultez la page [Tarification de Load Balancer](https://aka.ms/lbpricing).

## <a name="limitations"></a>Limites

- Actuellement, les instances de serveur principal Load Balancer ne peuvent pas être placées dans des réseaux virtuels homologués. Toutes les instances de serveur principal doivent être dans la même région.
- Les références SKU ne sont pas mutables. Vous ne pouvez pas modifier la référence SKU d’une ressource existante.
- Une ressource de machine virtuelle autonome, une ressource de groupe à haute disponibilité ou une ressource de groupe de machines virtuelles identiques peut faire référence à une seule référence SKU, jamais aux deux.
- Les [alertes Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-alerts.md) ne sont pas prises en charge pour l’instant.
- Les [opérations de déplacement d’abonnement](../azure-resource-manager/resource-group-move-resources.md) ne sont pas prises en charge pour les ressources LB et PIP de SKU standard.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser [Load Balancer Standard et les zones de disponibilité](load-balancer-standard-availability-zones.md)
- En savoir plus sur les [zones de disponibilité](../availability-zones/az-overview.md)
- En savoir plus sur les [Diagnostics Load Balancer Standard](load-balancer-standard-diagnostics.md).
- En savoir plus sur les [métriques multidimensionnelles prises en charge](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) pour obtenir des diagnostics dans [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Découvrez comment utiliser [Load Balancer pour les connexions sortantes](load-balancer-outbound-connections.md)
- En savoir plus sur [Load Balancer Standard avec les règles d’équilibrage de charge des ports HA](load-balancer-ha-ports-overview.md)
- Découvrez comment utiliser [Load Balancer avec plusieurs serveurs frontaux](load-balancer-multivip-overview.md)
- En savoir plus sur les [Réseaux virtuels](../virtual-network/virtual-networks-overview.md).
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).
- En savoir plus sur les [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md)
- En savoir plus sur les autres [fonctionnalités de mise en réseau](../networking/networking-overview.md) clés d’Azure.
- En savoir plus sur [Load Balancer](load-balancer-overview.md).
