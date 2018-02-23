---
title: Comprendre les connexions sortantes dans Azure | Microsoft Docs
description: Cet article explique comment Azure permet aux machines virtuelles de communiquer avec les services Internet publics.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: e1a2b4c281194ec4c70e4d9fe1bc97c5aa61436e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Comprendre les connexions sortantes dans Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]


Azure assure la connectivité sortante pour les déploiements de clients via différents mécanismes.  Cet article décrit les scénarios, les situations où ils s’appliquent, comment ils fonctionnent et comment les gérer.

Un déploiement dans Azure peut communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public. Quand une instance lance un flux sortant vers une destination dans l’espace d’adressage IP public, Azure mappe dynamiquement l’adresse IP privée à une adresse IP publique.  Une fois ce mappage créé, le trafic de retour pour ce flux d’origine sortant peut également atteindre l’adresse IP privée d’où provient le flux.

Pour exécuter cette fonction, Azure utilise une traduction d’adresses réseau sources.  Quand plusieurs adresses IP privées se dissimulent derrière une adresse IP publique, Azure utilise une [traduction d’adresse de port](#pat) pour masquer les adresses IP privées.  Les ports éphémères utilisés pour la traduction d’adresse de port sont [préaffectés](#preallocatedports) en fonction de la taille du pool.

Il existe plusieurs [scénarios sortants](#scenarios). Il est possible de combiner ces scénarios en fonction des besoins. Examinez-les soigneusement pour comprendre les fonctionnalités, les contraintes et les structures qui s’appliquent à vos modèle de déploiement et scénario d’application.  Prenez connaissance des conseils relatifs à la [gestion de ces scénarios](#snatexhaust).

## <a name="scenarios"></a>Vue d’ensemble des scénarios

Azure offre deux modèles majeurs de déploiement : Azure Resource Manager et Azure Classic. L’équilibreur de charge et les ressources associées sont définis explicitement lors de l’utilisation de [ressources Azure Resource Manager](#arm).  Les déploiements Azure Classic résument le concept d’équilibreur de charge, et expriment une fonction similaire au travers de la définition de points de terminaison d’un [service cloud](#classic). Les [scénarios](#scenarios) applicables à votre déploiement dépendent du déploiement de modèle utilisé.

### <a name="arm"></a>Azure Resource Manager

Actuellement, Azure offre trois méthodes différentes pour obtenir une connexion sortante pour les ressources Azure Resource Manager.  Les déploiements [Classic](#classic) incluent un sous-ensemble de ces scénarios.

| Scénario | Méthode | DESCRIPTION |
| --- | --- | --- |
| [1. Machine virtuelle avec adresse IP publique de niveau d’instance (avec ou sans équilibreur de charge)](#ilpip) | Traduction d’adresses réseau sources, masquage de port non utilisé |Azure utilise l’adresse IP publique affectée à la configuration IP de la carte d’interface réseau de l’instance.  L’instance a tous les ports éphémères disponibles. |
| [2. Équilibreur de charge associé avec une machine virtuelle (aucune adresse IP publique de niveau d’instance sur l’instance)](#lb) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) à l’aide des serveurs frontaux de l’équilibreur de charge |Azure partage l’adresse IP publique des serveurs frontaux de l’équilibreur de charge public avec des adresses IP privées, et utilise les ports éphémères des serveurs frontaux pour la traduction d’adresse de port. |
| [3. Machine virtuelle autonome (sans équilibreur de charge, sans adresse IP publique de niveau d’instance)](#defaultsnat) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) | Azure désigne automatiquement une adresse IP publique pour la traduction d’adresses réseau sources, partage cette adresse IP publique avec plusieurs adresses IP privées du groupe à haute disponibilité, et utilise les ports éphémères de cette adresse IP publique.  Ce scénario est un scénario de secours pour les scénarios 1 et 2 précédents, qui n’est pas recommandé si vous avez besoin de visibilité et de contrôle. |

Si vous ne souhaitez pas qu’une machine virtuelle communique avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public, vous pouvez utiliser des groupes de sécurité réseau pour bloquer l’accès.  L’utilisation de groupes de sécurité réseau est abordée plus en détail dans [Empêchement des connexions sortantes](#preventoutbound).  Cet article n’entre pas dans les détails de la conception et de la gestion d’un réseau virtuel sans accès sortant.

### <a name="classic"></a>Azure Classic (services cloud)

Les scénarios disponibles pour les déploiements Azure Classic sont un sous-ensemble des scénarios disponibles pour les déploiements [Azure Resource Manager](#arm) et l’équilibreur de charge de base.

Une machine virtuelle Azure Classic offre trois scénarios fondamentaux décrits pour les ressources Azure Resource Manager ([1](#ilpip), [2](#lb), [3](#defaultsnat)). Un rôle de travail web Azure Classic n’offre que deux scénarios ([2](#lb), [3](#defaultsnat)).  Les [stratégies de prévention](#snatexhaust) présentent également les mêmes différences.

L’algorithme utilisé pour la [préaffectation de ports éphémères](#ephemeralprots) en lien avec la traduction d’adresse de port pour les déploiements Azure Classic est le même que pour les déploiements de ressources Azure Resource Manager.  

### <a name="ilpip"></a>Scénario 1 : machine virtuelle avec adresse IP publique de niveau d’instance

Dans ce scénario, une adresse IP publique de niveau d’instance (ILPIP) est affectée à la machine virtuelle. En ce qui concerne les connexions sortantes, il importe peu que la machine virtuelle soit à charge équilibrée ou non.  Ce scénario prend le pas sur les autres. En cas d’utilisation d’une adresse IP publique de niveau d’instance, la machine virtuelle utilise celle-ci pour tous les flux sortants.  

Le masquage de port (traduction d’adresse de port) n’est pas utilisé et la machine virtuelle possède tous les ports éphémères disponibles.

Si votre application lance de nombreux flux sortants alors que les ressources de port de traduction d’adresses réseau sources sont épuisées, vous pouvez envisager d’affecter une [adresse IP publique de niveau d’instance afin d’atténuer les contraintes de traduction d’adresses réseau sources](#assignilpip). Lisez l’article [Gestion de l’épuisement des ressources SNAT](#snatexhaust) dans son intégralité.

### <a name="lb"></a>Scénario 2 : machine virtuelle à charge équilibrée sans adresse IP publique de niveau d’instance

Dans ce scénario, la machine virtuelle fait partie d’un pool principal d’équilibreurs de charge public.  La machine virtuelle n’a pas d’adresse IP publique affectée. La ressource d’équilibreur de charge doit être configurée avec une règle d’équilibreur de charge pour créer un lien entre le serveur frontal d’adresse IP publique et le pool backend. Si vous n’effectuez pas cette configuration de règle, le comportement est celui décrit dans le scénario pour [Machine virtuelle autonome sans adresse IP publique de niveau d’instance](#defaultsnat).  La règle n’a pas besoin d’un écouteur opérationnel dans le pool principal ou la sonde d’intégrité pour réussir.

Lorsque la machine virtuelle à charge équilibrée crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique du frontend d’équilibrage de charge public. Azure utilise une traduction d’adresses réseau sources pour exécuter cette fonction, ainsi qu’une [traduction d’adresse de port](#pat) pour masquer plusieurs adresses IP privées derrière une adresse IP publique. Les ports éphémères du serveur frontal d’adresse IP publique de l’équilibreur de charge sont utilisés pour distinguer les flux provenant de la machine virtuelle. La traduction d’adresses réseau utilise dynamiquement des [ports éphémères préaffectés](#preallocatedports) lors de la création de flux sortants. Dans ce contexte, les ports éphémères utilisés pour SNAT sont appelés ports SNAT.

Les ports de traduction d’adresses réseau sources sont préaffectés comme décrit dans la section [Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port](#snat), et constituent une ressource limitée qui peut être épuisée. Il est important de comprendre la manière dont ils sont [consommés](#pat). Pour comprendre comment concevoir et atténuer leur consommation si nécessaire, voir [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

Si [plusieurs adresses IP (publiques) sont associées à un équilibreur de charge de base](load-balancer-multivip-overview.md), chacune d’elles est [candidate pour les flux sortants et une seule est sélectionnée](#multivipsnat).  

Vous pouvez utiliser le service [Log Analytics pour l’équilibreur de charge](load-balancer-monitor-log.md) et les [Journaux des événements d’alerte pour surveiller les messages signalant l’épuisement des ressources de traduction d’adresses réseau sources](load-balancer-monitor-log.md#alert-event-log) pour vérifier l’intégrité des connexions sortantes avec l’équilibreur de charge de base.

### <a name="defaultsnat"></a>Scénario 3 : machine virtuelle autonome sans adresse IP publique de niveau d’instance

Dans ce scénario, la machine virtuelle ne fait pas partie d’un pool Azure Load Balancer et aucune adresse IP publique de niveau d’instance n’y est affectée. Lorsque la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique. L’adresse IP publique utilisée pour ce flux sortant n’est pas configurable et n’entre pas en compte dans la limite de ressource IP publique de l’abonnement. Pour exécuter cette fonction, Azure utilise une traduction d’adresses réseau sources avec masquage de port ([traduction d’adresse de port](#pat)). Ce scénario est similaire au [scénario 2](#lb), sauf qu’il n’existe aucun contrôle de l’adresse IP utilisée.  Il s’agit d’un scénario de secours à défaut des scénarios 1 et 2.  Ce scénario n’est pas recommandé si un contrôle de l’adresse sortante est souhaité.

Les ports de traduction d’adresses réseau sources sont préaffectés comme décrit dans la section [Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port](#snat), et constituent une ressource limitée qui peut être épuisée. Il est important de comprendre la manière dont ils sont [consommés](#pat). Pour comprendre comment concevoir et atténuer leur consommation si nécessaire, voir [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

### <a name="combinations"></a>Plusieurs scénarios combinés

Les scénarios décrits dans les sections qui précèdent peuvent être combinés pour obtenir un résultat particulier.  Lorsque plusieurs scénarios sont présents, un ordre de priorité s’applique : le [scénario 1](#ilpip) est prioritaire sur les [scénarios 2](#lb) et [3](#defaultsnat) (Azure Resource Manager uniquement), et le [scénario 2](#lb) prend le pas sur le [scénario 3](#defaultsnat) (Azure Resource Manager et Azure Classic).

Un exemple est un déploiement Azure Resource Manager où l’application dépend fortement des connexions sortantes à un nombre limité de destinations, mais reçoit également des flux entrants sur un serveur frontal d’équilibreur de charge. Dans ce cas, vous pouvez combiner les scénarios 1 et 2 pour alléger la charge.  Pour d’autres modèles, voir [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

### <a name="multivipsnat"></a> Plusieurs serveurs frontaux pour les flux sortants

L’équilibreur de charge de base choisit un seul serveur frontal pour les flux sortants quand [plusieurs serveurs frontaux d’adresse IP (publics)](load-balancer-multivip-overview.md) sont candidats pour les flux sortants.  Cette sélection n’est pas configurable et l’algorithme de sélection doit être considéré comme aléatoire.  Vous pouvez désigner une adresse IP spécifique pour les flux sortants comme décrit dans [Scénarios combinés](#combinations).

## <a name="snat"></a>Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port

### <a name="pat"></a>Masquage de la traduction d’adresses réseau sources du port (traduction d’adresse de port)

Lorsqu’une ressource Load Balancer publique est associée à des instances de machine virtuelle, la source de chaque connexion sortante est réécrite. La source est réécrite depuis l’espace d’adressage IP privé du réseau virtuel dans l’adresse IP publique frontale de l’équilibreur de charge. Dans l’espace d’adressage IP public, le 5-tuple du flux (adresse IP source, port source, protocole de transport IP, adresse IP de destination, port de destination) doit être unique.  Des ports éphémères sont utilisés à cette fin après réécriture de l’adresse IP privée source, car plusieurs flux proviennent d’une seule adresse IP publique.  Ces ports éphémères sont appelés ports de traduction d’adresses réseau sources. 

Un seul port de traduction d’adresses réseau sources est utilisé par flux vers une adresse IP, un port et un protocole de destination. En cas de flux multiples vers les mêmes adresse IP, port et protocole de destination, chaque flux utilise un seul port de traduction d’adresses réseau sources. Cela garantit que les flux sont uniques quand ils proviennent de la même adresse IP publique et sont dirigés vers les mêmes adresse IP, port et protocole de destination. 

Plusieurs flux, chacun dirigé vers une adresse IP, un port et un protocole de destination distincts, partagent un seul port de traduction d’adresses réseau sources. L’adresse IP, le port et le protocole de destination rendent les flux uniques sans recourir à des ports sources supplémentaires pour distinguer les flux dans l’espace d’adressage IP public.

En cas d’épuisement des ressources de port SNAT, les flux sortants échouent jusqu'à ce que des ports SNAT soient libérés par flux existants. L’équilibreur de charge récupère les ports de traduction d’adresses réseau sources lorsque le flux se ferme, et utilise un [délai d’inactivité de 4 minutes](#idletimeout) pour récupérer les ports de traduction d’adresses réseau sources des flux inactifs.

Pour des modèles d’atténuation des conditions entraînant souvent un épuisement du port de traduction d’adresses réseau sources, voir la section [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

### <a name="preallocatedports"></a>Préaffectation de port éphémère pour la traduction d’adresses réseau sources (traduction d’adresse de port) pour le masquage de port

Azure utilise un algorithme pour déterminer le nombre de ports de traduction d’adresses réseau sources préaffectés disponibles en fonction de la taille du pool principal lorsque vous utilisez une traduction d’adresses réseau sources ([traduction d’adresse de port](#pat)) pour le masquage de port.  Les ports de traduction d’adresses réseau sources sont des ports éphémères disponibles pour une adresse IP publique source donnée.

Azure préaffecte des ports de traduction d’adresses réseau sources à la configuration IP de la carte d’interface réseau de chaque machine virtuelle. Quand une configuration IP est ajoutée au pool, les ports de traduction d’adresses réseau sources sont préaffectés pour cette configuration IP en fonction de la taille du pool principal. Pour les rôles de travail web classique, l’affectation est effectuée par instance de rôle.  Lors de la création de flux sortants, la [traduction d’adresse de port](#pat) consomme (jusqu’à la limite préaffectée) et libère ces ports dynamiquement lorsque le flux se ferme ou en cas de [délais d’inactivité](#ideltimeout).

Le tableau suivant présente les préaffectations de port de traduction d’adresses réseau sources pour les différents niveaux de tailles de pool principal :

| Taille du pool (instances de machine virtuelle) | Ports de traduction d’adresses réseau sources préaffectés par configuration IP|
| --- | --- |
| 1 - 50 | 1 024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

Il est à noter que le nombre de ports SNAT disponibles n’est pas directement lié au nombre de connexions. Un port de traduction d’adresses réseau sources peut être réutilisé pour plusieurs destinations uniques. Les ports sont consommés uniquement si c’est nécessaire pour rendre les flux uniques. Pour des conseils concernant la conception et l’atténuation, voir [comment gérer cette ressource épuisable](#snatexhaust), ainsi que la section décrivant la [traduction d’adresse de port](#pat).

La modification de la taille de votre pool principal peut affecter certains flux établis. Si la taille du pool principal augmente et passe au niveau suivant, la moitié des ports de traduction d’adresses réseau sources préaffectés sont récupérés pendant la transition vers le niveau de pool principal supérieur suivant. Les flux associés à un port de traduction d’adresses réseau sources récupéré expirent et doivent être rétablis. Les nouvelles tentatives de connexion réussissent immédiatement tant que des ports préaffectés sont disponibles.

Si la taille du pool principal diminue et passe à un niveau inférieur, le nombre de ports SNAT disponibles augmente. Dans ce cas, les ports de traduction d’adresses réseau sources affectés existants et leurs flux respectifs ne sont pas concernés.

## <a name="snatexhaust"></a>Gestion de l’épuisement de port de traduction d’adresses réseau sources (traduction d’adresse de port)

Les [ports éphémères](#preallocatedports) utilisés pour la [traduction d’adresse de port](#pat) constituent une ressource épuisable, comme décrit dans les sections [Machine virtuelle autonome sans adresse IP publique de niveau d’instance](#defaultsnat) et [Machine virtuelle à charge équilibrée sans adresse IP publique de niveau d’instance](#lb).

Si vous savez que vous initiez de nombreuses connexions TCP ou UDP sortantes vers les mêmes adresse IP et port de destination, et constatez que des connexions sortantes échouent, ou si l’équipe de support vous signale que le nombre de ports de traduction d’adresses réseau sources ([ports éphémère](#preallocatedports) préaffectés) utilisés par la [traduction d’adresse de port](#pat) arrive à épuisement, plusieurs options d’atténuation générales s’offrent à vous.  Passez en revue ces options et choisissez l’option disponible la plus appropriée pour votre scénario.  Plusieurs options peuvent être adaptées à votre scénario.

Si vous éprouvez des difficultés à comprendre le comportement de la connexion sortante, vous pouvez utiliser les statistiques de pile IP (netstat). Il peut également être utile d’observer les comportements de connexion à l’aide de captures de paquets.  Vous pouvez effectuer ces captures de paquets dans le SE invité de votre instance, ou utiliser le [Network Watcher pour la capture des paquets](../network-watcher/network-watcher-packet-capture-manage-portal.md).

### <a name="connectionreuse"></a>Modifier l’application pour réutiliser des connexions 
Vous pouvez réduire la demande de ports éphémères utilisés pour SNAT en réutilisant des connexions dans votre application.  Cette méthode s'applique tout particulièrement aux protocoles comme HTTP/1.1, où la réutilisation d'une connexion est la règle par défaut.  D'autres protocoles qui utilisent HTTP comme transport (par exemple REST) peuvent également en bénéficier.  La réutilisation est toujours préférable aux connexions TCP atomiques individuelles pour chaque requête, ce qui se traduit par des transactions TCP plus performantes et très efficaces.

### <a name="connection pooling"></a>Modifier l’application pour utiliser un regroupement de connexions
Vous pouvez utiliser un schéma de regroupement de connexions dans votre application, dans lequel les demandes sont distribuées en interne sur un ensemble fixe de connexions (chacune étant réutilisée dans la mesure du possible).  Cela limite le nombre de ports éphémères utilisés et crée un environnement plus prévisible.  Cela peut également augmenter le débit des demandes en autorisant plusieurs opérations simultanées lorsqu'une seule connexion bloque sur la réponse d'une opération.  Le regroupement de connexions existe peut-être déjà dans l'infrastructure que vous utilisez pour développer votre application ou les paramètres de configuration de votre application.  Vous pouvez combiner cette méthode avec la réutilisation des connexions afin que vos multiples demandes consomment un nombre fixe et prévisible de ports vers la même adresse IP et le même port de destination, tout en bénéficiant d'une utilisation très efficace des transactions TCP pour réduire la latence et économiser les ressources.  Cela peut également être bénéfique pour les transactions UDP, car la gestion du nombre de flux UDP peut à son tour éviter des conditions d’épuisement et gérer l’utilisation du port de traduction d’adresses réseau sources.

### <a name="retry logic"></a>Modifier l’application pour utiliser une logique de nouvelle tentative moins agressive
Quand les [ports éphémères préaffectés](#preallocatedports) utilisés pour la [traduction d’adresse de port](#pat) sont épuisés, ou quand des échecs d’application se produisent, les nouvelles tentatives de connexion agressives ou par force brute sans logique de réduction ou d’interruption entraînent la survenance ou la persistance de l’épuisement. Vous pouvez réduire la demande de ports éphémères en utilisant une logique de nouvelle tentative moins agressive.   Les ports éphémères ont un délai d’inactivité de 4 minutes (non modifiable). Si les nouvelles tentatives sont trop agressives, le problème d’épuisement ne pourra pas se résoudre de lui-même.  Par conséquent, il est essentiel de pouvoir évaluer la façon et la fréquence à laquelle votre application relance les transactions.

### <a name="assignilpip"></a>Assigner une adresse IP publique de niveau d’instance à chaque machine virtuelle
Votre scénario consiste alors à [assigner une adresse IP publique au niveau de l’instance à une machine virtuelle](#ilpip).  Tous les ports éphémères de l’adresse IP publique utilisés pour une machine virtuelle sont disponibles pour la machine virtuelle (contrairement aux scénarios dans lesquels les ports éphémères d’une adresse IP publique sont partagés avec toutes les machines virtuelles associées au pool back-end respectif).  Des compromis sont à prendre en compte, notamment le coût supplémentaire des adresses IP publiques et l’impact potentiel de la mise en liste blanche d’un nombre important d’adresses IP individuelles.

>[!NOTE] 
>Cette option n’est pas disponible pour les rôles de travail web.

### <a name="idletimeout"></a>Utilisez des conservations de connexion active pour réinitialiser le délai d’inactivité en sortie

Les connexions sortantes ont un délai d’inactivité de 4 minutes. Ce délai n’est pas modifiable. Toutefois, vous pouvez utiliser un transport (par exemple, des conservations de connexion active TCP) ou des conservations de connexion active de couche Application pour actualiser un flux inactif et réinitialiser ce délai d’inactivité si nécessaire.

## <a name="discoveroutbound"></a>Découverte de l’adresse IP publique utilisée par une machine virtuelle donnée
Il existe de nombreuses manières de déterminer l’adresse IP source publique d’une connexion sortante. OpenDNS fournit un service qui peut vous indiquer l’adresse IP publique votre machine virtuelle. La commande nslookup vous permet d’envoyer une requête DNS pour le myip.opendns.com du nom au programme de résolution OpenDNS. Le service retourne l’adresse IP source qui a été utilisée pour envoyer la requête. Lorsque vous exécutez la requête suivante à partir de votre machine virtuelle, la réponse est l’adresse IP publique utilisée pour cette machine virtuelle.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Prévention de toute connexion sortante
Parfois, il n’est pas souhaitable d’autoriser une machine virtuelle à créer un flux sortant, ou il peut y avoir une exigence visant à définir les destinations pouvant être atteintes avec des flux sortants ou celles pouvant démarrer des flux entrants. Dans ce cas, vous utilisez des [groupes de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md) pour gérer les destinations que la machine virtuelle peut atteindre, ainsi que les destinations publiques pouvant initier des flux entrants. Lorsque vous appliquez un groupe de sécurité réseau à une machine Virtuelle à charge équilibrée, vous devez faire attention aux [balises par défaut](../virtual-network/virtual-networks-nsg.md#default-tags) et aux [règles par défaut](../virtual-network/virtual-networks-nsg.md#default-rules).

Vous devez vous assurer que la machine virtuelle peut recevoir des demandes d’analyse d’intégrité d’Azure Load Balancer. Si un groupe de sécurité réseau bloque les demandes d’analyse d’intégrité depuis la balise par défaut AZURE_LOADBALANCER, votre analyse de l’intégrité de la machine virtuelle échoue et la machine virtuelle est marquée comme défaillante. L’équilibrage de charge arrête l’envoi de nouveaux flux vers cette machine virtuelle.

## <a name="next-steps"></a>étapes suivantes

- En savoir plus sur [la référence De base de Load Balancer](load-balancer-overview.md)
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).
- En savoir plus sur les autres [fonctionnalités de mise en réseau](../networking/networking-overview.md) clés d’Azure.
