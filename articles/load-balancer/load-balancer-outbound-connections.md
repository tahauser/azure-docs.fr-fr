---
title: Connexions sortantes dans Azure | Microsoft Docs
description: Cet article explique comment Azure permet aux machines virtuelles de communiquer avec les services Internet publics.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: 32661ad4d647f266273c4c94a5ba177a348c5431
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="outbound-connections-in-azure"></a>Connexions sortantes dans Azure

>[!NOTE]
> La référence SKU Standard de Load Balancer est actuellement en préversion. Le niveau de disponibilité et la fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour vos services de production, utilisez la [référence SKU De base de Load Balancer](load-balancer-overview.md) de la version publique. Pour utiliser la [version préliminaire des zones de disponibilité](https://aka.ms/availabilityzones) avec cette version préliminaire, il vous faut une [inscription différente](https://aka.ms/availabilityzones), en plus de l’inscription à la [version préliminaire standard](#preview-sign-up) de Load Balancer.

Azure assure la connectivité sortante pour les déploiements de clients via différents mécanismes. Cet article décrit les scénarios, les situations où ils s’appliquent, comment ils fonctionnent et comment les gérer.

Un déploiement dans Azure peut communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public. Quand une instance lance un flux sortant vers une destination située dans l’espace d’adressage IP public, Azure mappe dynamiquement l’adresse IP privée à une adresse IP publique. Une fois ce mappage créé, le trafic de retour pour ce flux sortant peut aussi atteindre l’adresse IP privée d’où provient le flux.

Pour exécuter cette fonction, Azure utilise une traduction d’adresses réseau sources. Quand plusieurs adresses IP privées se dissimulent derrière une adresse IP publique, Azure utilise une [traduction d’adresse de port](#pat) pour masquer les adresses IP privées. Les ports éphémères utilisés pour la traduction d’adresse de port sont [préaffectés](#preallocatedports) en fonction de la taille du pool.

Il existe plusieurs [scénarios sortants](#scenarios). Vous pouvez éventuellement combiner ces scénarios. Examinez-les soigneusement pour comprendre les fonctionnalités, les contraintes et les structures qui s’appliquent à vos modèle de déploiement et scénario d’application. Prenez connaissance des conseils relatifs à la [gestion de ces scénarios](#snatexhaust).

>[!IMPORTANT] 
>L’équilibreur de charge standard introduit de nouvelles fonctionnalités et des comportements différents pour la connectivité sortante.   Par exemple, [scénario 3](#defaultsnat) n’existe pas lorsqu’un équilibreur de charge interne standard est présent et différentes étapes doivent être suivies.   Lisez attentivement la totalité de ce document pour comprendre les concepts et les différences généraux entre les références (SKU).

## <a name="scenarios"></a>Vue d’ensemble des scénarios

Azure propose deux modèles de déploiement principaux : Azure Resource Manager et Azure Classic. Azure Load Balancer et les ressources associées sont définis explicitement quand vous utilisez [Azure Resource Manager](#arm). Les déploiements Azure Classic résument le concept d’équilibreur de charge, et expriment une fonction similaire au travers de la définition de points de terminaison d’un [service cloud](#classic). Les [scénarios](#scenarios) applicables à votre déploiement varient en fonction du modèle de déploiement que vous utilisez.

### <a name="arm"></a>Azure Resource Manager

Actuellement, Azure offre trois méthodes différentes pour obtenir une connexion sortante pour les ressources Azure Resource Manager. Les déploiements [Classic](#classic) incluent un sous-ensemble de ces scénarios.

| Scénario | Méthode | Description |
| --- | --- | --- |
| [1. Machine virtuelle avec une adresse IP publique de niveau d’instance (avec ou sans Load Balancer)](#ilpip) | Traduction d’adresses réseau sources, masquage de port non utilisé |Azure utilise l’adresse IP publique affectée à la configuration IP de la carte d’interface réseau de l’instance. L’instance a tous les ports éphémères disponibles. |
| [2. Équilibreur de charge public associé à une machine virtuelle (aucune adresse IP publique de niveau d’instance sur l’instance)](#lb) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) en utilisant des frontends Load Balancer |Azure partage l’adresse IP publique des frontends Load Balancer publics avec plusieurs adresses IP privées. Azure utilise les ports éphémères des frontends pour la traduction d’adresse de port. |
| [3. Machine virtuelle autonome (sans Load Balancer, sans adresse IP publique de niveau d’instance)](#defaultsnat) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) | Azure désigne automatiquement une adresse IP publique pour la traduction d’adresses réseau sources, partage cette adresse IP publique avec plusieurs adresses IP privées du groupe à haute disponibilité, puis utilise les ports éphémères de cette adresse IP publique. Il s’agit d’un scénario de secours pour les scénarios précédents. Nous vous le déconseillons si vous avez besoin de visibilité et de contrôle. |

Si vous voulez empêcher une machine virtuelle de communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public, vous pouvez utiliser des groupes de sécurité réseau (NSG) pour bloquer l’accès comme il se doit. La section [Empêchement des connexions sortantes](#preventoutbound) traite de façon plus détaillée des groupes de sécurité réseau. Les conseils sur la conception, l’implémentation et la gestion d’un réseau virtuel sans accès sortant n’entrent pas dans le cadre de cet article.

### <a name="classic"></a>Azure Classic (services cloud)

Les scénarios disponibles pour les déploiements Azure Classic sont un sous-ensemble des scénarios disponibles pour les déploiements [Azure Resource Manager](#arm) et l’équilibreur de charge de base.

Une machine virtuelle Azure Classic propose les trois scénarios fondamentaux décrits pour les ressources Azure Resource Manager ([1](#ilpip), [2](#lb), [3](#defaultsnat)). Un rôle de travail web Azure Classic ne propose que deux scénarios ([2](#lb), [3](#defaultsnat)). Les [stratégies de prévention](#snatexhaust) présentent également les mêmes différences.

L’algorithme utilisé pour la [préaffectation de ports éphémères](#ephemeralprots) en lien avec la traduction d’adresse de port pour les déploiements Azure Classic est le même que pour les déploiements de ressources Azure Resource Manager.  

### <a name="ilpip"></a>Scénario 1 : machine virtuelle avec adresse IP publique de niveau d’instance

Dans ce scénario, une adresse IP publique de niveau d’instance (ILPIP) est affectée à la machine virtuelle. En ce qui concerne les connexions sortantes, il importe peu que la machine virtuelle soit à charge équilibrée ou non. Ce scénario prend le pas sur les autres. En cas d’utilisation d’une adresse IP publique de niveau d’instance, la machine virtuelle utilise celle-ci pour tous les flux sortants.  

Le masquage de port (traduction d’adresse de port) n’est pas utilisé et la machine virtuelle peut utiliser tous les ports éphémères.

Si votre application lance de nombreux flux sortants et que vous subissez un épuisement des ports de traduction d’adresses réseau sources, envisagez d’affecter une [adresse IP publique de niveau d’instance pour atténuer les contraintes de traduction d’adresses réseau sources](#assignilpip). Lisez [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust) dans son intégralité.

### <a name="lb"></a>Scénario 2 : machine virtuelle à charge équilibrée sans adresse IP publique de niveau d’instance

Dans ce scénario, la machine virtuelle fait partie d’un pool principal d’équilibreurs de charge public. La machine virtuelle n’a pas d’adresse IP publique affectée. La ressource d’équilibreur de charge doit être configurée avec une règle d’équilibreur de charge pour créer un lien entre le serveur frontal d’adresse IP publique et le pool backend.

Si vous n’effectuez pas cette configuration de règle, le comportement est celui décrit dans le scénario pour [Machine virtuelle autonome sans adresse IP publique de niveau d’instance](#defaultsnat). La règle n’a pas besoin d’un écouteur opérationnel dans le pool backend ou la sonde d’intégrité pour réussir.

Lorsque la machine virtuelle à charge équilibrée crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique du frontend d’équilibrage de charge public. Azure utilise la traduction d’adresses réseau sources pour exécuter cette fonction. Azure utilise aussi la [traduction d’adresse de port](#pat) pour masquer plusieurs adresses IP privées derrière une adresse IP publique. 

Les ports éphémères du frontend d’adresse IP publique de l’équilibreur de charge servent à faire la distinction entre les différents flux en provenance de la machine virtuelle. La traduction d’adresses réseau utilise dynamiquement des [ports éphémères préaffectés](#preallocatedports) lors de la création de flux sortants. Dans ce contexte, les ports éphémères utilisés pour la traduction d’adresses réseau sources sont appelés ports SNAT.

Les ports SNAT sont préaffectés comme décrit dans la section [Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port](#snat). Il s’agit d’une ressource limitée sujette à épuisement. Il est important de comprendre comment ils sont [consommés](#pat). Pour savoir comment concevoir en fonction de cette consommation et d’en atténuer éventuellement les effets, consultez [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

Si [plusieurs adresses IP (publiques) sont associées à un équilibreur de charge de base](load-balancer-multivip-overview.md), chacune d’elles est [candidate pour les flux sortants](#multivipsnat) et une seule est sélectionnée.  

Pour surveiller l’intégrité des connexions sortantes avec l’équilibreur de charge de base, vous pouvez utiliser [Log Analytics pour Load Balancer](load-balancer-monitor-log.md) et les [journaux des événements d’alerte](load-balancer-monitor-log.md#alert-event-log) pour surveiller les messages signalant l’épuisement des ports SNAT.

### <a name="defaultsnat"></a>Scénario 3 : machine virtuelle autonome sans adresse IP publique de niveau d’instance

Dans ce scénario, la machine virtuelle ne fait pas partie d’un pool d’équilibreurs de charge public (et non plus d’un pool d’équilibreurs de charge standard interne) et aucune adresse IP publique de niveau d’instance (ILPIP) n’y est assignée. Lorsque la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique. L’adresse IP publique utilisée pour ce flux sortant n’est pas configurable et n’entre pas en compte dans la limite de ressource IP publique de l’abonnement.

>[!IMPORTANT] 
>Ce scénario s’applique également lorsque __seul__ un équilibreur de charge interne de base est joint. Le scénario 3 est __non disponible__ lorsqu’un équilibreur de charge interne standard est joint à une machine virtuelle.  Vous devez créer explicitement [scénario 1](#ilpip) ou [scénario 2](#lb) en plus pour utiliser un équilibreur de charge interne standard.

Pour exécuter cette fonction, Azure utilise la traduction d’adresses réseau sources avec masquage de port ([traduction d’adresse de port](#pat)). Ce scénario est similaire au [scénario 2](#lb), sauf qu’il n’existe aucun contrôle de l’adresse IP utilisée. Il s’agit d’un scénario de secours quand les scénarios 1 et 2 n’existent pas. Nous vous déconseillons ce scénario si vous voulez exercer un contrôle sur l’adresse sortante. Si les connexions sortantes sont un élément essentiel de votre application, il est préférable de choisir un autre scénario.

Les ports SNAT sont préaffectés comme décrit dans la section [Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port](#snat).  Le nombre de machines virtuelles qui partagent un groupe à haute disponibilité détermine quel niveau de pré-allocation s’applique.  Une machine virtuelle autonome sans un groupe à haute disponibilité est effectivement un pool de 1 dans le cadre de la détermination de la pré-allocation (ports SNAT 1024). Les ports SNAT sont une ressource limitée qui peut être épuisée. Il est important de comprendre comment ils sont [consommés](#pat). Pour savoir comment concevoir en fonction de cette consommation et d’en atténuer éventuellement les effets, consultez [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

### <a name="combinations"></a>Plusieurs scénarios combinés

Vous pouvez combiner les scénarios décrits dans les sections précédentes pour obtenir un résultat particulier. En présence de plusieurs scénarios, un ordre de priorité s’applique : le [scénario 1](#ilpip) est prioritaire sur les [scénarios 2](#lb) et [3](#defaultsnat) (Azure Resource Manager uniquement). Le [scénario 2](#lb) se substitue au [scénario 3](#defaultsnat) (Azure Resource Manager et Azure Classic).

Un exemple est un déploiement Azure Resource Manager où l’application dépend fortement des connexions sortantes à un nombre limité de destinations, mais reçoit également des flux entrants sur un serveur frontal d’équilibreur de charge. Dans ce cas, vous pouvez combiner les scénarios 1 et 2 pour alléger la charge. Pour d’autres modèles, consultez [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

### <a name="multife"></a> Plusieurs serveurs frontaux pour les flux sortants

#### <a name="load-balancer-basic"></a>Équilibreur de charge de base

L’équilibreur de charge de base choisit un seul frontend à utiliser pour les flux sortants quand [plusieurs frontends d’adresses IP (publiques)](load-balancer-multivip-overview.md) sont candidats pour les flux sortants. Cette sélection n’est pas configurable, et vous devez considérer l’algorithme de sélection comme étant aléatoire. Vous pouvez désigner une adresse IP spécifique pour les flux sortants comme décrit dans [Scénarios combinés multiples](#combinations).

#### <a name="load-balancer-standard"></a>Équilibreur de charge standard

L’équilibreur de charge standard utilise tous les candidats pour les flux sortants en même temps lorsque [plusieurs serveurs frontaux IP (publics)](load-balancer-multivip-overview.md) sont présents. Chaque serveur frontal multiplie le nombre de ports SNAT pré-alloués disponibles si une règle d’équilibrage de charge est activée pour les connexions sortantes.

Vous pouvez choisir de supprimer l’utilisation d’une adresse IP de serveur frontal pour les connexions sortantes avec une nouvelle option de règle d’équilibrage de charge :

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normalement, cette option par défaut est _false_ et signifie que cette règle programme le SNAT sortant pour les machines virtuelles associées dans le pool principal de la règle d’équilibrage de charge.  Cela peut être remplacé par _true_ pour empêcher l’équilibreur de charge d’utiliser l’adresse IP du serveur frontal associée pour des connexions sortantes pour la machine virtuelle dans le pool principal de cette règle d’équilibrage de charge.  Vous pouvez toutefois toujours désigner une adresse IP spécifique pour les flux sortants comme décrit dans [Scénarios combinés multiples](#combinations).

### <a name="az"></a> Zones de disponibilité

Lorsque vous utilisez [Équilibreur de charge standard avec zones de disponibilité](load-balancer-standard-availability-zones.md), les serveurs frontaux de redondance de zones peuvent fournir des connexions SNAT sortantes de redondance de zones et la programmation du SNAT survit à l’échec de la zone.  Lorsque vous utilisez des serveurs frontaux de zones, les connexions sortantes SNAT connaissent le même sort que la zone à laquelle ils appartiennent.

## <a name="snat"></a>Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port

### <a name="pat"></a>Masquage de la traduction d’adresses réseau sources du port (traduction d’adresse de port)

Lorsqu’une ressource Load Balancer publique est associée à des instances de machine virtuelle, la source de chaque connexion sortante est réécrite. La source est réécrite depuis l’espace d’adressage IP privé du réseau virtuel dans l’adresse IP publique frontend de l’équilibreur de charge. Dans l’espace d’adressage IP public, le 5-tuple du flux (adresse IP source, port source, protocole de transport IP, adresse IP de destination, port de destination) doit être unique.  

Des ports éphémères (ports SNAT) sont utilisés à cette fin après réécriture de l’adresse IP privée source, car plusieurs flux proviennent d’une même adresse IP publique. 

Un seul port de traduction d’adresses réseau sources est utilisé par flux vers une adresse IP, un port et un protocole de destination. En cas de flux multiples vers les mêmes adresse IP, port et protocole de destination, chaque flux utilise un seul port de traduction d’adresses réseau sources. Cela garantit que les flux sont uniques quand ils proviennent de la même adresse IP publique et sont dirigés vers les mêmes adresse IP, port et protocole de destination. 

Plusieurs flux, chacun dirigé vers une adresse IP, un port et un protocole de destination distincts, partagent un même port SNAT. L’adresse IP, le port et le protocole de destination rendent les flux uniques sans qu’il soit nécessaire de recourir à des ports sources supplémentaires pour distinguer les flux dans l’espace d’adressage IP public.

En cas d’épuisement des ressources de port SNAT, les flux sortants échouent tant que les flux existants ne libèrent pas des ports SNAT. L’équilibreur de charge récupère les ports de traduction d’adresses réseau sources lorsque le flux se ferme, et utilise un [délai d’inactivité de 4 minutes](#idletimeout) pour récupérer les ports de traduction d’adresses réseau sources des flux inactifs.

Pour découvrir des modèles permettant d’atténuer les conditions qui aboutissent généralement à un épuisement des ports SNAT, consultez la section [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

### <a name="preallocatedports"></a>Préaffectation de port éphémère pour la traduction d’adresses réseau sources (traduction d’adresse de port) pour le masquage de port

Azure utilise un algorithme pour déterminer le nombre de ports de traduction d’adresses réseau sources préaffectés disponibles en fonction de la taille du pool principal lorsque vous utilisez une traduction d’adresses réseau sources ([traduction d’adresse de port](#pat)) pour le masquage de port. Les ports SNAT sont des ports éphémères disponibles pour une adresse IP publique source donnée.

Azure préaffecte des ports de traduction d’adresses réseau sources à la configuration IP de la carte d’interface réseau de chaque machine virtuelle. Quand une configuration IP est ajoutée au pool, les ports de traduction d’adresses réseau sources sont préaffectés pour cette configuration IP en fonction de la taille du pool principal. Pour les rôles de travail web classique, l’affectation s’effectue par instance de rôle. Au moment où les flux sortants sont créés, la [traduction d’adresse de port](#pat) consomme (jusqu’à la limite préaffectée) et libère dynamiquement ces ports quand le flux se ferme ou en cas de [délais d’inactivité](#ideltimeout).

Le tableau suivant présente les préaffectations de ports SNAT pour les différents niveaux de tailles de pool backend :

| Taille du pool (instances de machine virtuelle) | Ports de traduction d’adresses réseau sources préaffectés par configuration IP|
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1 000 | 32 |

>[!NOTE]
> Lorsque vous utilisez l’équilibreur de charge standard avec [plusieurs serveurs frontaux](load-balancer-multivip-overview.md), [chaque adresse IP de serveur frontal multiplie le nombre de ports SNAT disponibles](#multivipsnat) dans la table précédente. Par exemple, un pool principal de 50 machines virtuelles avec 2 règles d’équilibrage de charge, chacun avec des adresses IP de serveurs frontaux séparées, utilisera les ports SNAT 2048 (2 x 1024) par configuration IP. Affichez les détails pour [plusieurs serveurs frontaux](#multife).

Ne perdez pas de vue que le nombre de ports SNAT disponibles ne se traduit pas directement en nombre de flux. Un port de traduction d’adresses réseau sources peut être réutilisé pour plusieurs destinations uniques. Les ports ne sont consommés que si cela permet de rendre les flux uniques. Pour obtenir des conseils concernant la conception et l’atténuation, consultez la section qui explique [comment gérer cette ressource épuisable](#snatexhaust), ainsi que la section qui décrit la [traduction d’adresse de port](#pat).

La modification de la taille de votre pool backend peut affecter certains de vos flux établis. Si la taille du pool backend augmente et passe au niveau suivant, la moitié des ports SNAT préaffectés sont récupérés pendant la transition vers le niveau de pool backend supérieur suivant. Les flux associés à un port SNAT récupéré expirent et doivent être rétablis. En cas de tentative de lancement d’un nouveau flux, celui-ci réussit immédiatement du moment que des ports préaffectés sont disponibles.

Si la taille du pool backend diminue et passe à un niveau inférieur, le nombre de ports SNAT disponibles augmente. Dans ce cas, les ports de traduction d’adresses réseau sources affectés existants et leurs flux respectifs ne sont pas concernés.

## <a name="problemsolving"></a> Résolution des problèmes 

Cette section vise à atténuer les insuffisances SNAT et d’autres scénarios pouvant se produire avec les connexions sortantes dans Azure.

### <a name="snatexhaust"></a>Gestion de l’insuffisance de ports (PAT) SNAT
Les [ports éphémères](#preallocatedports) utilisés pour la [traduction d’adresse de port](#pat) constituent une ressource épuisable, comme décrit dans [Machine virtuelle autonome sans adresse IP publique de niveau d’instance](#defaultsnat) et [Machine virtuelle à charge équilibrée sans adresse IP publique de niveau d’instance](#lb).

Si vous savez que vous lancez de nombreuses connexions TCP ou UDP sortantes vers les mêmes adresse IP et port de destination et constatez que des connexions sortantes échouent, ou si l’équipe de support vous signale que les ports SNAT ([ports éphémères](#preallocatedports) préaffectés) utilisés par la [PAT](#pat) arrivent à épuisement, plusieurs options d’atténuation générales s’offrent à vous. Passez en revue ces options et choisissez l’option disponible la plus appropriée pour votre scénario. Plusieurs options peuvent être adaptées à votre scénario.

Si vous avez des difficultés à comprendre le comportement des connexions sortantes, vous pouvez utiliser les statistiques de pile IP (netstat). Il peut aussi être utile d’observer les comportements de connexion à travers les captures de paquets. Vous pouvez effectuer ces captures de paquets dans le SE invité de votre instance, ou utiliser le [Network Watcher pour la capture des paquets](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Modifier l’application pour réutiliser des connexions 
Vous pouvez réduire la demande pour les ports éphémères utilisés pour la traduction d’adresses réseau sources en réutilisant des connexions dans votre application. Cette méthode s’applique tout particulièrement aux protocoles comme HTTP/1.1, où la réutilisation des connexions est la règle par défaut. D’autres protocoles qui utilisent HTTP comme transport (par exemple REST) peuvent aussi en bénéficier. 

Il est toujours préférable de réutiliser les connexions que de recourir à des connexions TCP individuelles et atomiques pour chaque demande. La réutilisation produit des transactions TCP plus performantes et très efficaces.

#### <a name="connection pooling"></a>Modifier l’application pour utiliser un regroupement de connexions
Vous pouvez utiliser un schéma de regroupement de connexions dans votre application, dans lequel les demandes sont distribuées en interne sur un ensemble fixe de connexions (chacune étant réutilisée dans la mesure du possible). Ce schéma limite le nombre de ports éphémères utilisés et crée un environnement plus prévisible. Il peut aussi accroître le débit des demandes en autorisant plusieurs opérations simultanées quand une seule connexion se bloque sur la réponse d’une opération.  

Il est possible que le regroupement de connexions existe déjà dans le framework que vous utilisez pour développer votre application ou dans les paramètres de configuration de votre application. Vous pouvez associer le regroupement de connexions à la réutilisation des connexions. Vos diverses demandes consomment alors un nombre de ports fixe et prévisible vers les mêmes adresse IP et port de destination. Les demandes profitent aussi d’une utilisation efficace des transactions TCP, ce qui réduit la latence et l’utilisation de ressources. Cela peut aussi être bénéfique pour les transactions UDP, car la gestion du nombre de flux UDP peut à son tour éviter des conditions d’épuisement et gérer l’utilisation des ports SNAT.

#### <a name="retry logic"></a>Modifier l’application pour utiliser une logique de nouvelle tentative moins agressive
Quand les [ports éphémères préaffectés](#preallocatedports) utilisés pour la [traduction d’adresse de port](#pat) sont épuisés, ou quand des échecs d’application se produisent, les nouvelles tentatives de connexion agressives ou par force brute sans logique de réduction ou d’interruption entraînent la survenance ou la persistance de l’épuisement. Vous pouvez réduire la demande de ports éphémères en utilisant une logique de nouvelle tentative moins agressive. 

Les ports éphémères ont un délai d’inactivité de 4 minutes (non ajustable). Si les nouvelles tentatives sont trop agressives, le problème d’épuisement ne peut pas se résoudre de lui-même. Par conséquent, il est essentiel de pouvoir évaluer comment et selon quelle fréquence votre application relance les transactions.

#### <a name="assignilpip"></a>Assigner une adresse IP publique de niveau d’instance à chaque machine virtuelle
En assignant une adresse IP publique de niveau d’instance, vous passez à un scénario de type [Adresse IP publique au niveau de l’instance affectée à une machine virtuelle](#ilpip). Les ports éphémères de l’adresse IP publique qui sont utilisés pour chaque machine virtuelle sont tous accessibles à la machine virtuelle. (Contrairement aux scénarios où les ports éphémères d’une adresse IP publique sont partagés avec toutes les machines virtuelles associés au pool backend correspondant.) Des compromis sont à prendre en compte, notamment le coût supplémentaire des adresses IP publiques et l’impact possible de la mise sur liste verte d’un nombre important d’adresses IP individuelles.

>[!NOTE] 
>Cette option n’est pas disponible pour les rôles de travail web.

#### <a name="multifesnat"></a>Utiliser plusieurs serveurs frontaux

Lorsque vous utilisez l’équilibreur de charge standard public, vous assignez [plusieurs adresses IP de serveur frontal pour les connexions sortantes](#multife) et [multipliez le nombre de ports SNAT disponibles](#preallocatedports).  Vous devez créer une configuration IP de serveur frontal, la règle et le pool principal pour déclencher la programmation du SNAT à l’IP publique du serveur frontal.  La règle n’a pas besoin de fonctionner et une sonde d’intégrité n’a pas besoin d’aboutir.  Si vous utilisez plusieurs serveurs frontaux pour l’entrant également (plutôt que simplement pour le sortant), vous devez correctement utiliser les sondes d’intégrité personnalisées pour garantir la fiabilité.

>[!NOTE]
>Dans la plupart des cas, l’insuffisance des ports SNAT résulte d’une mauvaise conception.  Assurez-vous que vous comprenez la raison de l’insuffisance de ports avant d’utiliser plus de serveurs frontaux pour ajouter des ports SNAT.  Vous pouvez masquer un problème qui peut provoquer une défaillance ultérieure.

### <a name="idletimeout"></a>Utiliser des conservations de connexion active pour réinitialiser le délai d’inactivité en sortie

Les connexions sortantes ont un délai d’inactivité de 4 minutes. Ce délai d’attente n’est pas ajustable. Cependant, vous pouvez utiliser un transport (par exemple, des conservations de connexion active TCP) ou des conservations de connexion active de couche Application pour actualiser un flux inactif et réinitialiser ce délai d’inactivité, si nécessaire.

## <a name="discoveroutbound"></a>Découverte de l’adresse IP publique utilisée par une machine virtuelle
Il existe de nombreuses manières de déterminer l’adresse IP source publique d’une connexion sortante. OpenDNS fournit un service qui peut vous indiquer l’adresse IP publique votre machine virtuelle. 

La commande nslookup vous permet d’envoyer une requête DNS sur le nom myip.opendns.com au programme de résolution OpenDNS. Le service retourne l’adresse IP source qui a été utilisée pour envoyer la requête. Quand vous exécutez la requête suivante à partir de votre machine virtuelle, la réponse est l’adresse IP publique utilisée pour cette machine virtuelle :

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Prévention de toute connexion sortante
Dans certains cas, il est préférable de ne pas autoriser une machine virtuelle à créer un flux sortant. Il peut aussi exister une condition qui détermine les destinations qui peuvent être atteintes avec des flux sortants ou celles qui peuvent lancer des flux entrants. Dans ce cas, vous pouvez utiliser des [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) pour déterminer les destinations que la machine virtuelle peut atteindre. Vous pouvez aussi utiliser des groupes de sécurité réseau pour déterminer la destination publique qui peut lancer des flux entrants. 

Quand vous appliquez un groupe de sécurité réseau à une machine virtuelle à charge équilibrée, faites attention aux [balises par défaut](../virtual-network/virtual-networks-nsg.md#default-tags) et aux [règles par défaut](../virtual-network/virtual-networks-nsg.md#default-rules). Vous devez vous assurer que la machine virtuelle peut recevoir des demandes d’analyse d’intégrité d’Azure Load Balancer. 

Si un groupe de sécurité réseau bloque les demandes d’analyse d’intégrité depuis la balise par défaut AZURE_LOADBALANCER, votre analyse de l’intégrité de la machine virtuelle échoue et la machine virtuelle est marquée comme défaillante. L’équilibrage de charge arrête l’envoi de nouveaux flux vers cette machine virtuelle.

## <a name="limitations"></a>Limites
- DisableOutboundSnat n’est pas disponible en tant qu’option lors de la configuration d’une règle d’équilibrage de charge dans le portail.  Utilisez les outils REST, modèle ou client à la place.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [la référence De base de Load Balancer](load-balancer-overview.md)
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).
- En savoir plus sur les autres [fonctionnalités de mise en réseau](../networking/networking-overview.md) clés d’Azure.
