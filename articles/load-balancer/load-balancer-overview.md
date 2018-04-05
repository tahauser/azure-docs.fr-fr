---
title: Présentation de l’équilibrage de charge Azure | Microsoft Docs
description: Présentation des fonctionnalités, de l'architecture et de l'implémentation de l'équilibrage de charge Azure. Découvrez comment fonctionne l’équilibrage de charge et l’exploiter dans le cloud.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 3a5d1e897d8ffe063ecf9277bef346c8b7c5092b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-load-balancer-overview"></a>Vue d’ensemble de l’équilibreur de charge Azure

Azure Load Balancer vous permet de mettre à l’échelle vos applications et de créer une haute disponibilité pour vos services. Load Balancer prend en charge les scénarios entrants et sortants, et offre une latence faible, un débit élevé et une montée en puissance jusqu’à plusieurs millions de flux pour toutes les applications TCP et UDP.   

Load Balancer distribue les nouveaux flux entrants arrivant sur le serveur frontal de l’équilibreur de charge sur des instances de pool du serveur principal en fonction des règles et des sondes d’intégrité définies.  

En outre, un équilibreur de charge public peut également fournir des connexions sortantes pour des machines virtuelles à l’intérieur de votre réseau virtuel en traduisant leurs adresses IP privées en adresses IP publiques.

Azure Load Balancer se décline en deux références SKU : De base et Standard.  Celles-ci présentent des différences en termes de mise à l’échelle, de fonctionnalités et de tarification.  N’importe quel scénario possible avec la référence SKU De base de Load Balancer peut également être créé avec la référence SKU Standard, même si l’approche à suivre peut varier légèrement.  Pour bien comprendre le fonctionnement de Load Balancer, il est important de se familiariser avec ses capacités de base et les différences propres aux références SKU.

## <a name="why-use-load-balancer"></a>Pourquoi utiliser Load Balancer ? 

Azure Load Balancer peut être utilisé pour :

* équilibrer la charge du trafic Internet entrant sur les machines virtuelles. Cette configuration est appelée [équilibreur de charge public](#publicloadbalancer).
* Équilibrer la charge du trafic entre des machines virtuelles à l’intérieur d’un réseau virtuel. Vous pouvez également communiquer avec un serveur frontal d’équilibreur de charge à partir d’un réseau local dans le cadre d’un scénario hybride. Ces deux scénarios s’appuient sur une configuration appelée [équilibreur de charge interne](#internalloadbalancer).
* Réacheminer le trafic d’un port vers un port spécifique sur des machines virtuelles données avec des règles NAT entrantes.
* Fournir une [connectivité sortante](load-balancer-outbound-connections.md) pour des machines virtuelles à l’intérieur de votre réseau virtuel à l’aide d’un équilibreur de charge public.


>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios.  Si vous recherchez une terminaison TLS (« déchargement SSL ») ou un traitement de couche Application par requête HTTP/HTTPS, consultez [Vue d’ensemble de la passerelle Application Gateway](../application-gateway/application-gateway-introduction.md).  Si vous recherchez un équilibrage de charge DNS global, consultez [Vue d’ensemble de Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions en fonction de vos besoins.

## <a name="what-is-load-balancer"></a>Qu’est-ce que l’équilibrage de charge ?

Une ressource Load Balancer peut exister sous la forme d’un équilibreur de charge public ou d’un équilibreur de charge interne. Les fonctions de la ressource Load Balancer sont exprimées en tant que définition de serveur frontal, de règle, de sonde d’intégrité et de pool de serveur principal.  Les machines virtuelles sont placées dans le pool de serveur principal en spécifiant le pool de serveur principal de l’ordinateur virtuel.

Les ressources Load Balancer sont des objets dans lesquels vous pouvez exprimer la manière dont Azure doit programmer son infrastructure mutualisée pour obtenir le scénario que vous souhaitez créer.  Il n’existe aucune relation directe entre les ressources Load Balancer et l’infrastructure réelle ; la création d’un équilibreur de charge ne crée pas d’instance et il y a toujours de la capacité disponible. 

## <a name="fundamental-load-balancer-features"></a>Fonctionnalités de base de Load Balancer

Load Balancer offre les fonctionnalités de base suivantes pour les applications TCP et UDP :

* **Équilibrage de charge**

    Azure Load Balancer permet de créer une règle pour distribuer le trafic arrivant sur un serveur frontal sur des instances de pool de serveur principal.  Il utilise un algorithme de hachage pour la distribution des flux entrants et réécrit les en-têtes des flux sur les instances du pool du serveur principal en conséquence. Un serveur est disponible pour recevoir les nouveaux flux quand la sonde d’intégrité indique un point de terminaison de serveur principal sain.
    
    Par défaut, il utilise un hachage à 5 tuples composé de l’adresse IP source, du port source, de l’adresse IP de destination, du port de destination et du numéro de protocole IP pour mapper les flux vers les serveurs disponibles.  Vous pouvez choisir de créer une affinité avec une adresse IP source spécifique en utilisant un hachage à 2 ou 3 tuples pour une règle donnée.  Tous les paquets d’un même flux arrivent sur la même instance derrière le serveur frontal soumis à l’équilibrage de charge.  Quand le client démarre un nouveau flux à partir de la même adresse IP source, le port source est modifié. La distribution à 5 tuples qui en résulte peut provoquer l’acheminement du trafic vers un autre point de terminaison de serveur principal.

    Pour plus d’informations, consultez [Mode de distribution de l’équilibrage de charge](load-balancer-distribution-mode.md). Le graphique suivant montre la distribution basée sur le hachage :

    ![Distribution basée sur le hachage](./media/load-balancer-overview/load-balancer-distribution.png)

    *Figure : distribution basée sur le hachage*

* **Réacheminement de port**

    Azure Load Balancer permet de créer une règle NAT entrante pour réacheminer le trafic d’un port spécifique d’une adresse IP donnée du serveur frontal vers un port spécifique d’une instance donnée du serveur principal à l’intérieur du réseau virtuel. Cette opération est accomplie à l’aide de la même distribution basée sur le hachage que l’équilibrage de charge.  Les scénarios courants pour cette fonctionnalité incluent les sessions de protocole RDP (Remote Desktop Protocol) ou SSH (Secure Shell) avec des instances de machines virtuelles individuelles à l’intérieur du réseau virtuel.  Vous pouvez mapper plusieurs points de terminaison internes à des ports différents sur la même adresse IP de serveur frontal. Vous pouvez les utiliser pour administrer à distance vos machines virtuelles via Internet sans avoir besoin de serveur de rebond supplémentaire.

* **Indépendance vis-à-vis des applications et transparence**

    Load Balancer n’interagit pas directement avec les protocoles TCP et UDP ou la couche Application et n’importe quel scénario d’application TCP ou UDP peut être pris en charge.  Par exemple, même si Load Balancer n’assure pas lui-même la terminaison TLS, vous pouvez créer des applications TLS et augmenter la taille de leurs instances à l’aide de Load Balancer et terminer la connexion TLS sur la machine virtuelle. Load Balancer ne termine pas un flux et les établissements de liaisons de protocoles se font toujours directement entre le client et l’instance de pool du serveur principal sélectionnée par le hachage. Par exemple, l’établissement d’une liaison TCP se fait toujours entre le client et la machine virtuelle du serveur principal sélectionnée.  Et une réponse à une requête envoyée à un serveur frontal est une réponse générée à partir de la machine virtuelle du serveur principal.  Les performances réseau sortantes de Load Balancer sont limitées uniquement par la référence SKU de machine virtuelle choisie et les flux resteront actifs pendant de longues périodes si le délai d’inactivité n’est jamais atteint.

* **Reconfiguration automatique**

    L'équilibrage de charge Azure se reconfigure instantanément lors de la mise à l'échelle d'instances vers le haut ou vers le bas. Quand vous ajoutez ou supprimez des machines virtuelles du pool du serveur principal, l’équilibreur de charge est reconfiguré sans opérations supplémentaires sur la ressource Load Balancer.

* **Sondes d’intégrité**

    Azure Load Balancer fait appel à des sondes d’intégrité définies par vos soins pour déterminer l’intégrité des instances du pool du serveur principal. Lorsqu’une sonde ne répond pas, l’équilibrage de charge n’envoie plus de nouvelles connexions aux instances défaillantes. Les connexions existantes ne sont pas affectées et restent actives jusqu'à ce que l’application termine le flux, qu’un délai d’inactivité soit atteint ou que la machine virtuelle soit arrêtée.

    Trois types de sondes sont pris en charge :

    - **Sonde HTTP personnalisée :** vous pouvez l'utiliser pour créer votre propre logique personnalisée afin de déterminer l'intégrité d'une instance du pool du serveur principal. L’équilibrage de charge sonde régulièrement votre point de terminaison (toutes les 15 secondes, par défaut). L’instance est considérée comme saine si elle répond avec un code d’état HTTP 200 dans le délai imparti (31 secondes par défaut). Tout code d’état autre que HTTP 200 provoque l’échec de cette sonde.  Ce type de sonde s’avère également utile pour implémenter votre propre logique afin de supprimer des instances de l’équilibreur de charge. Vous pouvez, par exemple, configurer l'instance pour qu'elle renvoie un état autre que 200 si l'instance a une utilisation supérieure à 90 % de l'UC.   Cette sonde remplace la sonde d’agent invité par défaut.

    - **Sonde personnalisée TCP :** cette sonde s’appuie sur l’établissement réussi d’une session TCP sur un port de sonde défini.  Tant que l’écouteur spécifié sur la machine virtuelle existe, cette sonde réussit. Si la connexion est refusée, la sonde échoue. Cette sonde remplace la sonde d’agent invité par défaut.

    - **Sonde d’agent invité (sur machines virtuelles de plateforme en tant que service uniquement)** : l’équilibreur de charge peut également exploiter l’agent invité dans la machine virtuelle. L’agent invité écoute et répond HTTP 200 OK uniquement quand l’instance est prête. Si l’agent ne répond pas HTTP 200 OK, l’équilibreur de charge marque l’instance comme ne répondant pas et arrête d’envoyer du trafic vers cette instance. L’équilibreur de charge continue d’essayer d’atteindre l’instance. Si l’agent invité répond avec un HTTP 200, l’équilibrage de charge envoie à nouveau du trafic vers cette instance.  Ne faites appel aux sondes d’agent invité qu’en dernier recours. Ne les utilisez pas si des configurations de sondes HTTP ou TCP personnalisées sont possibles. 
    
* **Connexions sortantes (Source NAT)**

    Tous les flux sortants circulant d’adresses IP privées à l’intérieur de votre réseau virtuel vers des adresses IP publiques sur Internet peuvent être traduites en une adresse IP de serveur frontal de l’équilibreur de charge. Quand un serveur frontal public est lié à une machine virtuelle de serveur principal par le biais d’une règle d’équilibrage de charge, Azure programme la traduction automatique des connexions sortantes en adresse IP du serveur frontal public. Cette fonctionnalité est également appelée mode Source NAT (SNAT). SNAT offre des avantages importants :

    + Il permet une mise à niveau et une récupération d'urgence simples des services, étant donné que le serveur frontal peut être dynamiquement mappé à une autre instance du service.
    + Il facilite la gestion des listes de contrôle d’accès (ACL). Les ACL exprimées sous forme d’adresses IP de serveur frontal ne changent pas quand les services montent en puissance, descendent en puissance ou sont redéployés.

    Pour une présentation détaillée de cette fonctionnalité, consultez l’article [Connexions sortantes](load-balancer-outbound-connections.md).

La référence SKU Standard de Load Balancer offre des fonctionnalités spécifiques en plus de ces capacités de base.  Pour plus d’informations, poursuivez la lecture de cet article.

## <a name="skus"></a> Comparaison des références SKU de Load Balancer

Azure Load Balancer prend en charge deux références SKU : De base et Standard.  Celles-ci présentent des différences en termes d’échelle de scénario, de fonctionnalités et de tarification.  N’importe quel scénario possible avec la référence SKU De base de Load Balancer peut également être créé avec la référence SKU Standard.  En fait, les API sont identiques pour ces deux références SKU et sont appelées en spécifiant une référence SKU.  Les références SKU pour Load Balancer et les adresses IP publiques sont prises en charge à partir de la version d’API 2017-08-01.  Les deux références SKU présentent les mêmes API et structure générales.

Toutefois, selon la référence SKU choisie, les détails de configuration du scénario complet peuvent varier légèrement. Dans la documentation relative à Load Balancer, les articles applicables uniquement à une référence SKU spécifique sont signalés. Pour comparer les références SKU et comprendre leurs différences, consultez le tableau ci-dessous.  Pour plus d’informations, consultez [Présentation de la référence Standard de Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Pour les nouvelles conceptions, il est recommandé d’utiliser la référence SKU Standard de Load Balancer. 

Les machines virtuelles autonomes, les groupes à haute disponibilité et les groupes de machines virtuelles identiques peuvent uniquement être connectés à une référence SKU, jamais aux deux. Quand des adresses IP publiques sont utilisées, les références SKU de Load Balancer et des adresses IP publiques doivent correspondre. Les références SKU de Load Balancer et des adresses IP publiques ne sont pas mutables.

_Il est recommandé de spécifier les références SKU de manière explicite, même si cela n’est pas encore obligatoire._  Pour l’heure, les modifications requises sont réduites au minimum. Si aucune référence SKU n’est spécifiée, cela est interprété comme l’intention d’utiliser la référence SKU De base dans la version d’API 2017-08-01.

>[!IMPORTANT]
>La référence SKU Standard de Load Balancer est un nouveau produit qui constitue essentiellement une version plus avancée de la référence De base de Load Balancer.  Il existe des différences importantes et délibérées entre ces deux produits.  N’importe quel scénario complet possible avec la référence SKU De base de Load Balancer peut être créé avec la référence SKU Standard.  Si vous êtes déjà habitué à la référence SKU De base de Load Balancer, vous devez vous familiariser avec la référence SKU Standard de Load Balancer pour comprendre les changements cassants en termes de comportement entre ces deux références et leur impact. Lisez attentivement cette section.

| | [Référence Standard](load-balancer-standard-overview.md) | Référence SKU De base |
| --- | --- | --- |
| Taille de pool de serveur principal | Jusqu'à 1 000 instances | Jusqu'à 100 instances |
| Points de terminaison du pool du serveur principal | Toute machine virtuelle dans un réseau virtuel, y compris la combinaison de machines virtuelles, de groupes à haute disponibilité et de groupes de machines virtuelles identiques | Machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques unique |
| Zones de disponibilité | Serveurs frontaux redondants dans une zone et zonaux pour le trafic entrant et sortant, mappages de flux sortants protégés contre les défaillances de zone, équilibrage de charge interzones | / |
| Diagnostics | Azure Monitor, métriques multidimensionnelles incluant les compteurs d’octets et de paquets, état des sondes d’intégrité, tentatives de connexion (TCP SYN), intégrité des connexions sortantes (flux SNAT ayant réussi et échoué), mesures du plan de données actif | Azure Log Analytics pour l’équilibreur de charge public uniquement, alerte d’épuisement des ports SNAT, mesure de l’intégrité du pool du serveur principal |
| Ports HA | Équilibreur de charge interne | / |
| Sécurisé par défaut | Fermé par défaut pour les points de terminaison IP et Load Balancer publics. Un groupe de sécurité réseau doit être utilisé pour mettre explicitement sur liste verte les flux de trafic. | Ouvert par défaut, groupe de sécurité réseau facultatif |
| Connexions sortantes | Plusieurs serveurs frontaux avec retrait par règle. Un scénario sortant _doit_ être explicitement créé pour que la machine virtuelle puisse utiliser une connectivité sortante.  Les [points de terminaison de service du réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) peuvent être atteints sans connectivité sortante et ne sont pas comptabilisés dans les données traitées.  Toutes les adresses IP publiques, y compris les services PaaS Azure non disponibles en tant que points de terminaison de service du réseau virtuel, doivent être atteintes via la connectivité sortante et sont comptabilisées dans les données traitées. Quand seul un équilibreur de charge interne gère une machine virtuelle, les connexions sortantes via le mode SNAT par défaut ne sont pas disponibles. La programmation du SNAT pour les connexions sortantes est basée sur le protocole de transport de la règle d’équilibrage de charge du trafic entrant. | Serveur frontal unique, sélectionné de manière aléatoire quand plusieurs serveurs frontaux sont présents.  Quand seul un équilibreur de charge interne gère une machine virtuelle, le mode SNAT par défaut est utilisé. |
| Plusieurs serveurs frontaux | Trafic entrant et sortant | Entrant uniquement |
| Opérations de gestion | La plupart des opérations < 30 secondes | Généralement 60 à 90 secondes et plus |
| Contrat SLA | 99,99 % pour le chemin de données avec deux machines virtuelles saines | Implicite dans le SLA de la machine virtuelle | 
| Tarifs | Facturation en fonction du nombre de règles configurées et des données associées aux ressources traitées en entrée ou en sortie  | Aucuns frais |

Consultez les [limites de service pour Load Balancer](https://aka.ms/lblimits).  Pour la référence SKU Standard de Load Balancer, consultez également la [présentation plus détaillée](load-balancer-standard-overview.md), la page relative à la [tarification](https://aka.ms/lbpricing) et la page relative au [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Concepts

### <a name = "publicloadbalancer"></a>Équilibreur de charge public

L’équilibreur de charge public mappe l’adresse IP publique et le numéro de port du trafic entrant à l’adresse IP privée et au numéro de port de la machine virtuelle et inversement pour le trafic provenant de la machine virtuelle. Les règles d’équilibrage de charge vous permettent de distribuer des types spécifiques de trafic entre plusieurs machines virtuelles ou services. Par exemple, vous pouvez répartir la charge du trafic des requêtes web entre plusieurs serveurs web.

La figure suivante présente un point de terminaison à charge équilibrée pour le trafic Web partagé entre trois machines virtuelles pour les ports TCP public et privé 80. Celles-ci sont incluses dans un jeu d’équilibrage de la charge.

![exemple d’équilibrage de charge public](./media/load-balancer-overview/IC727496.png)

**Figure 1 : Équilibrage du trafic web à l’aide d’un équilibreur de charge public**

Quand les clients Internet envoient des requêtes de pages web à l’adresse IP publique d’une application web sur le port TCP 80, Azure Load Balancer distribue les requêtes entre les trois machines virtuelles du groupe soumis à l’équilibrage de charge. Des informations supplémentaires sur l’algorithme de l’équilibreur de charge sont disponibles sur la [page de présentation de l’équilibreur de charge](load-balancer-overview.md#load-balancer-features).

Par défaut, Azure Load Balancer répartit le trafic réseau équitablement sur plusieurs instances de machine virtuelle. Vous pouvez également configurer l’affinité de session. Pour plus d’informations, consultez [Mode de distribution de l’équilibrage de charge](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Équilibreur de charge interne

Un équilibreur de charge interne dirige le trafic uniquement vers les ressources qui se trouvent à l’intérieur d’un réseau virtuel ou qui utilisent un VPN pour accéder à l’infrastructure Azure. Un équilibreur de charge interne est en cela différent d’un équilibreur de charge public. L’infrastructure Azure limite l’accès aux adresses IP du serveur frontal soumis à l’équilibrage de charge au sein d’un réseau virtuel. Les adresses IP du serveur frontal et les réseaux virtuels ne sont jamais directement exposés à un point de terminaison Internet. Les applications métier internes s’exécutent dans Azure et sont accessibles à partir d’Azure ou à partir des ressources locales.

Un équilibreur de charge interne permet d’effectuer les types d'équilibrage de charge suivants :

* Dans un réseau virtuel : équilibrage de charge des machines virtuelles dans le réseau virtuel à un ensemble de machines virtuelles qui résident au sein du même réseau virtuel.
* Pour un réseau virtuel intersites : équilibrage de charge des ordinateurs locaux à un ensemble de machines virtuelles qui résident au sein du même réseau virtuel. 
* Pour les applications à plusieurs niveaux : équilibrage de charge pour les applications multiniveau accessibles sur Internet dans lesquelles les niveaux principaux ne sont pas accessibles sur Internet. Les niveaux principaux nécessitent un équilibrage de la charge du trafic du niveau accessible sur Internet (voir Figure 2).
* Pour les applications métier : équilibrage de charge pour des applications métier hébergées dans Azure, sans matériel ou logiciel d’équilibrage de charge supplémentaire. Ce scénario inclut des serveurs locaux dans l’ensemble d’ordinateurs dont la charge du trafic est équilibrée.

![Exemple d’équilibreur de charge interne](./media/load-balancer-overview/IC744147.png)

**Figure 2 : Applications multiniveaux utilisant à la fois un équilibreur de charge public et un équilibreur de charge interne**

## <a name="pricing"></a>Tarifs
La référence SKU Standard est facturée en fonction du nombre de règles configurées et du volume total de données entrantes et sortantes traitées. Pour plus d’informations sur la tarification de la référence SKU Standard, consultez la page [Tarification Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

La référence SKU De base de Load Balancer est proposée gratuitement.

## <a name="sla"></a>Contrat SLA

Pour plus d’informations sur le contrat de niveau de service (SLA) de la référence SKU Standard de Load Balancer, consultez la page [SLA pour Load Balancer](https://aka.ms/lbsla). 

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [présentation détaillée de la référence SKU Standard de Load Balancer](load-balancer-standard-overview.md)
- Découvrez comment utiliser [la référence SKU Standard de Load Balancer et les zones de disponibilité](load-balancer-standard-availability-zones.md)
- Découvrez comment utiliser [Load Balancer pour les connexions sortantes](load-balancer-outbound-connections.md)
- Consultez la [présentation des ports haute disponibilité de Load Balancer](load-balancer-ha-ports-overview.md)
- Découvrez comment utiliser [Load Balancer avec plusieurs serveurs frontaux](load-balancer-multivip-overview.md)
- Consultez la [présentation des points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md)
- Découvrez comment créer un [équilibreur de charge public de base](load-balancer-get-started-internet-portal.md)

