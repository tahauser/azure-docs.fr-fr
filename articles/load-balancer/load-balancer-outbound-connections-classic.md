---
title: Connexions sortantes dans Azure (Classic) | Microsoft Docs
description: Cet article explique comment Azure permet aux services cloud de communiquer avec les services Internet publics.
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
ms.date: 03/14/2018
ms.author: kumud
ms.openlocfilehash: 7a307a598bd71369615b30476d387c06f473c397
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="outbound-connections-classic"></a>Connexions sortantes (Classic)

Azure assure la connectivité sortante pour les déploiements de clients via différents mécanismes. Cet article décrit les scénarios, les situations où ils s’appliquent, comment ils fonctionnent et comment les gérer.

>[!NOTE]
>Cet article traite uniquement des déploiements Classic.  Consultez [Connexions sortantes](load-balancer-outbound-connections.md) pour découvrir tous les scénarios de déploiement Resource Manager dans Azure.

Un déploiement dans Azure peut communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public. Quand une instance lance un flux sortant vers une destination située dans l’espace d’adressage IP public, Azure mappe dynamiquement l’adresse IP privée à une adresse IP publique. Une fois ce mappage créé, le trafic de retour pour ce flux sortant peut aussi atteindre l’adresse IP privée d’où provient le flux.

Pour exécuter cette fonction, Azure utilise une traduction d’adresses réseau sources. Quand plusieurs adresses IP privées se dissimulent derrière une adresse IP publique, Azure utilise une [traduction d’adresse de port](#pat) pour masquer les adresses IP privées. Les ports éphémères utilisés pour la traduction d’adresse de port sont [préaffectés](#preallocatedports) en fonction de la taille du pool.

Il existe plusieurs [scénarios sortants](#scenarios). Vous pouvez éventuellement combiner ces scénarios. Examinez-les soigneusement pour comprendre les fonctionnalités, les contraintes et les structures qui s’appliquent à vos modèle de déploiement et scénario d’application. Prenez connaissance des conseils relatifs à la [gestion de ces scénarios](#snatexhaust).

## <a name="scenarios"></a>Vue d’ensemble des scénarios

Azure propose trois méthodes différentes pour réaliser des déploiements Classic de connectivité sortante.  Les trois scénarios ne sont pas disponibles pour tous les déploiements Classic :

| Scénario | Méthode | Description | Rôle de travail web | IaaS | 
| --- | --- | --- | --- | --- |
| [1. Machine virtuelle avec adresse IP publique de niveau d’instance](#ilpip) | Traduction d’adresses réseau sources, masquage de port non utilisé |Azure utilise l’adresse IP publique affectée à la machine virtuelle. L’instance a tous les ports éphémères disponibles. | Non  | OUI |
| [2. Point de terminaison à charge équilibrée](#publiclbendpoint) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) sur le point de terminaison public |Azure partage le point de terminaison de l’adresse IP publique avec plusieurs points de terminaison privés. Azure utilise des ports éphémères du point de terminaison public pour la traduction d’adresse de port. | OUI | OUI |
| [3. Machine virtuelle autonome ](#defaultsnat) | Traduction d’adresses réseau sources avec masquage de port (traduction d’adresse de port) | Azure désigne automatiquement une adresse IP publique pour la traduction d’adresses réseau sources, partage cette adresse IP publique avec tout le déploiement et utilise des ports éphémères de l’adresse IP du point de terminaison public pour la traduction d’adresse de port. Il s’agit d’un scénario de secours pour les scénarios précédents. Nous vous le déconseillons si vous avez besoin de visibilité et de contrôle. | OUI | OUI|

Il s’agit d’un sous-ensemble des fonctionnalités de connexion sortante disponibles pour les déploiements Resource Manager dans Azure.  

Les différents déploiements dans Classic offrent des fonctionnalités différentes :

| Déploiement classique | Fonctionnalités disponibles | 
| --- | --- |
| Machine virtuelle | Scénario [1](#ilpip), [2](#publiclbendpoint) ou [3](#defaultsnat) |
| Rôle de travail web | Seul scénario [2](#publiclbendpoint), [3](#defaultsnat) | 

Les [stratégies de prévention](#snatexhaust) présentent également les mêmes différences.

L’[algorithme utilisé pour la préaffectation de ports éphémères](#ephemeralports) en lien avec la traduction d’adresse de port pour les déploiements Azure Classic est le même que pour les déploiements de ressources Azure Resource Manager.

### <a name="ilpip"></a>Scénario 1 : machine virtuelle avec adresse IP publique de niveau d’instance

Dans ce scénario, une adresse IP publique de niveau d’instance (ILPIP) est affectée à la machine virtuelle. En ce qui concerne les connexions sortantes, il importe peu que la machine virtuelle ait un point de terminaison à charge équilibrée ou non. Ce scénario prend le pas sur les autres. En cas d’utilisation d’une adresse IP publique de niveau d’instance, la machine virtuelle utilise celle-ci pour tous les flux sortants.  

Le masquage de port (traduction d’adresse de port) n’est pas utilisé et la machine virtuelle peut utiliser tous les ports éphémères.

Si votre application lance de nombreux flux sortants et que vous subissez un épuisement des ports de traduction d’adresses réseau sources, envisagez d’affecter une [adresse IP publique de niveau d’instance pour atténuer les contraintes de traduction d’adresses réseau sources](#assignilpip). Lisez [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust) dans son intégralité.

### <a name="publiclbendpoint"></a>Scénario 2 : point de terminaison à charge équilibrée public

Dans ce scénario, la machine virtuelle ou le rôle de travail web est associé à une adresse IP publique via le point de terminaison à charge équilibrée. La machine virtuelle n’a pas d’adresse IP publique affectée. 

Lorsque la machine virtuelle à charge équilibrée crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en adresse IP source publique du point de terminaison à charge équilibrée public. Azure utilise la traduction d’adresses réseau sources pour exécuter cette fonction. Azure utilise aussi la [traduction d’adresse de port](#pat) pour masquer plusieurs adresses IP privées derrière une adresse IP publique. 

Les ports éphémères du frontend d’adresse IP publique de l’équilibreur de charge servent à faire la distinction entre les différents flux en provenance de la machine virtuelle. La traduction d’adresses réseau utilise dynamiquement des [ports éphémères préaffectés](#preallocatedports) lors de la création de flux sortants. Dans ce contexte, les ports éphémères utilisés pour la traduction d’adresses réseau sources sont appelés ports SNAT.

Les ports SNAT sont préaffectés comme décrit dans la section [Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port](#snat). Il s’agit d’une ressource limitée sujette à épuisement. Il est important de comprendre comment ils sont [consommés](#pat). Pour savoir comment concevoir en fonction de cette consommation et d’en atténuer éventuellement les effets, consultez [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

Quand il existe [plusieurs points de terminaison à charge équilibrée publics](load-balancer-multivip.md), l’une de ces adresses IP publiques est [candidate aux flux sortants](#multivipsnat) et sélectionnée au hasard.  

### <a name="defaultsnat"></a>Scénario 3 : aucune adresse IP publique associée

Dans ce scénario, la machine virtuelle ou le rôle de travail web ne fait pas partie d’un point de terminaison à charge équilibrée public.  Et, dans le cas d’une machine virtuelle, aucune adresse ILPIP ne lui est affectée. Lorsque la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique. L’adresse IP publique utilisée pour ce flux sortant n’est pas configurable et n’entre pas en compte dans la limite de ressource IP publique de l’abonnement.  Azure alloue automatiquement cette adresse.

Pour exécuter cette fonction, Azure utilise la traduction d’adresses réseau sources avec masquage de port ([traduction d’adresse de port](#pat)). Ce scénario est similaire au [scénario 2](#lb), sauf qu’il n’existe aucun contrôle de l’adresse IP utilisée. Il s’agit d’un scénario de secours quand les scénarios 1 et 2 n’existent pas. Nous vous déconseillons ce scénario si vous voulez exercer un contrôle sur l’adresse sortante. Si les connexions sortantes sont un élément essentiel de votre application, il est préférable de choisir un autre scénario.

Les ports SNAT sont préaffectés comme décrit dans la section [Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port](#snat).  Le nombre de machines virtuelles ou de rôles de travail web qui partage l’adresse IP publique détermine le nombre de ports éphémères préalloués.   Il est important de comprendre comment ils sont [consommés](#pat). Pour savoir comment concevoir en fonction de cette consommation et d’en atténuer éventuellement les effets, consultez [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

## <a name="snat"></a>Présentation de la traduction d’adresses réseau sources et de la traduction d’adresse de port

### <a name="pat"></a>Masquage de la traduction d’adresses réseau sources du port (traduction d’adresse de port)

Quand un déploiement établit une connexion sortante, chaque source de connexion sortante est réécrite. La source est réécrite à partir de l’espace d’adressage IP privé vers l’adresse IP publique associée au déploiement (en fonction des scénarios décrits ci-dessus). Dans l’espace d’adressage IP public, le 5-tuple du flux (adresse IP source, port source, protocole de transport IP, adresse IP de destination, port de destination) doit être unique.  

Des ports éphémères (ports SNAT) sont utilisés à cette fin après réécriture de l’adresse IP privée source, car plusieurs flux proviennent d’une même adresse IP publique. 

Un seul port de traduction d’adresses réseau sources est utilisé par flux vers une adresse IP, un port et un protocole de destination. En cas de flux multiples vers les mêmes adresse IP, port et protocole de destination, chaque flux utilise un seul port de traduction d’adresses réseau sources. Cela garantit que les flux sont uniques quand ils proviennent de la même adresse IP publique et sont dirigés vers les mêmes adresse IP, port et protocole de destination. 

Plusieurs flux, chacun dirigé vers une adresse IP, un port et un protocole de destination distincts, partagent un même port SNAT. L’adresse IP, le port et le protocole de destination rendent les flux uniques sans qu’il soit nécessaire de recourir à des ports sources supplémentaires pour distinguer les flux dans l’espace d’adressage IP public.

En cas d’épuisement des ressources de port SNAT, les flux sortants échouent tant que les flux existants ne libèrent pas des ports SNAT. L’équilibreur de charge récupère les ports de traduction d’adresses réseau sources lorsque le flux se ferme, et utilise un [délai d’inactivité de 4 minutes](#idletimeout) pour récupérer les ports de traduction d’adresses réseau sources des flux inactifs.

Pour découvrir des modèles permettant d’atténuer les conditions qui aboutissent généralement à un épuisement des ports SNAT, consultez la section [Gestion de l’épuisement de la traduction d’adresses réseau sources](#snatexhaust).

### <a name="preallocatedports"></a>Préaffectation de port éphémère pour la traduction d’adresses réseau sources (traduction d’adresse de port) pour le masquage de port

Azure utilise un algorithme pour déterminer le nombre de ports de traduction d’adresses réseau sources préaffectés disponibles en fonction de la taille du pool principal lorsque vous utilisez une traduction d’adresses réseau sources ([traduction d’adresse de port](#pat)) pour le masquage de port. Les ports SNAT sont des ports éphémères disponibles pour une adresse IP publique source donnée.

Azure préalloue les ports SNAT lors du déploiement d’une instance en fonction du nombre d’instances de machine virtuelle ou de rôle de travail web qui partagent une adresse IP publique donnée.  Au moment où les flux sortants sont créés, la [traduction d’adresse de port](#pat) consomme (jusqu’à la limite préaffectée) et libère dynamiquement ces ports quand le flux se ferme ou en cas de [délais d’inactivité](#ideltimeout).

Le tableau suivant présente les préaffectations de ports SNAT pour les différents niveaux de tailles de pool backend :

| Instances | Ports SNAT préallouées par instance |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1 000 | 32 |

Ne perdez pas de vue que le nombre de ports SNAT disponibles ne se traduit pas directement en nombre de flux. Un port de traduction d’adresses réseau sources peut être réutilisé pour plusieurs destinations uniques. Les ports ne sont consommés que si cela permet de rendre les flux uniques. Pour obtenir des conseils concernant la conception et l’atténuation, consultez la section qui explique [comment gérer cette ressource épuisable](#snatexhaust), ainsi que la section qui décrit la [traduction d’adresse de port](#pat).

La modification de la taille de votre déploiement peut affecter certains de vos flux établis. Si la taille du pool backend augmente et passe au niveau suivant, la moitié des ports SNAT préaffectés sont récupérés pendant la transition vers le niveau de pool backend supérieur suivant. Les flux associés à un port SNAT récupéré expirent et doivent être rétablis. En cas de tentative de lancement d’un nouveau flux, celui-ci réussit immédiatement du moment que des ports préaffectés sont disponibles.

Si la taille du déploiement diminue et passe à un niveau inférieur, le nombre de ports SNAT disponibles augmente. Dans ce cas, les ports de traduction d’adresses réseau sources affectés existants et leurs flux respectifs ne sont pas concernés.

## <a name="problemsolving"></a> Résolution des problèmes 

Cette section vise à atténuer l’épuisement de la traduction d’adresses réseau sources et d’autres scénarios pouvant se produire avec les connexions sortantes dans Azure.

### <a name="snatexhaust"></a> Gestion de l’épuisement des ports de traduction d’adresses réseau sources (traduction d’adresse de port)
Les [ports éphémères](#preallocatedports) utilisés pour la [traduction d’adresse de port](#pat) sont une ressource épuisable, comme décrit dans les scénarios [aucune adresse IP publique associée](#defaultsnat) et [point de terminaison à charge équilibrée public](#publiclbendpoint).

Si vous savez que vous lancez de nombreuses connexions TCP ou UDP sortantes vers les mêmes adresse IP et port de destination et constatez que des connexions sortantes échouent, ou si l’équipe de support vous signale que les ports SNAT ([ports éphémères](#preallocatedports) préaffectés) utilisés par la [PAT](#pat) arrivent à épuisement, plusieurs options d’atténuation générales s’offrent à vous. Passez en revue ces options et choisissez l’option disponible la plus appropriée pour votre scénario. Plusieurs options peuvent être adaptées à votre scénario.

Si vous avez des difficultés à comprendre le comportement des connexions sortantes, vous pouvez utiliser les statistiques de pile IP (netstat). Il peut aussi être utile d’observer les comportements de connexion à travers les captures de paquets.

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
En assignant une adresse IP publique de niveau d’instance, vous passez à un scénario de type [Adresse IP publique au niveau de l’instance affectée à une machine virtuelle](#ilpip). Les ports éphémères de l’adresse IP publique qui sont utilisés pour chaque machine virtuelle sont tous accessibles à la machine virtuelle. (Contrairement aux scénarios où les ports éphémères d’une adresse IP publique sont partagés avec toutes les machines virtuelles associés au déploiement correspondant.) Des compromis sont à prendre en compte, notamment l’impact potentiel de la mise en liste blanche d’un grand nombre d’adresses IP individuelles.

>[!NOTE] 
>Cette option n’est pas disponible pour les rôles de travail web.

### <a name="idletimeout"></a>Utiliser des conservations de connexion active pour réinitialiser le délai d’inactivité en sortie

Les connexions sortantes ont un délai d’inactivité de 4 minutes. Ce délai d’attente n’est pas ajustable. Cependant, vous pouvez utiliser un transport (par exemple, des conservations de connexion active TCP) ou des conservations de connexion active de couche Application pour actualiser un flux inactif et réinitialiser ce délai d’inactivité, si nécessaire.  Vérifiez auprès du fournisseur des logiciels empaquetés si cela est pris en charge ou comment l’activer.  En général, un seul côté doit générer conservations de connexion active pour réinitialiser le délai d’inactivité. 

## <a name="discoveroutbound"></a>Découverte de l’adresse IP publique utilisée par une machine virtuelle
Il existe de nombreuses manières de déterminer l’adresse IP source publique d’une connexion sortante. OpenDNS fournit un service qui peut vous indiquer l’adresse IP publique votre machine virtuelle. 

La commande nslookup vous permet d’envoyer une requête DNS sur le nom myip.opendns.com au programme de résolution OpenDNS. Le service retourne l’adresse IP source qui a été utilisée pour envoyer la requête. Quand vous exécutez la requête suivante à partir de votre machine virtuelle, la réponse est l’adresse IP publique utilisée pour cette machine virtuelle :

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur l’[équilibrage de charge](load-balancer-overview.md) utilisé dans les déploiements Resource Manager.
