---
title: Comprendre les connexions sortantes dans Azure | Microsoft Docs
description: Cet article explique comment Azure permet aux machines virtuelles de communiquer avec les services Internet publics.
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: d02960017b8793eccc2990a17e3d854991e877b6
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="understanding-outbound-connections-in-azure"></a>Comprendre les connexions sortantes dans Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Une machine virtuelle (VM) dans Azure peut communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public. Lorsqu’une machine virtuelle démarre un flux sortant vers une destination dans l’espace d’adressage IP public, Azure mappe l’adresse IP privée de la machine virtuelle sur une adresse IP publique et autorise le trafic de retour vers la machine virtuelle.

Azure propose trois méthodes différentes pour réaliser la connectivité sortante. Chaque méthode possède ses propres fonctionnalités et contraintes. Passez en revue chaque méthode soigneusement pour choisir celle qui correspond à vos besoins.

| Scénario | Méthode | Remarque |
| --- | --- | --- |
| Machine virtuelle autonome (aucun équilibrage de charge, aucune adresse IP publique de niveau d’instance) |SNAT par défaut |Azure associe une adresse IP publique pour SNAT |
| Machine virtuelle à charge équilibrée (aucune adresse IP publique de niveau d’instance sur la machine virtuelle) |SNAT utilisant l’équilibrage de charge |Azure utilise une adresse IP publique de l’équilibrage de charge pour SNAT |
| Machine virtuelle avec l’adresse IP publique de niveau d’instance (avec ou sans équilibrage de charge) |SNAT n’est pas utilisé |Azure utilise l’adresse IP publique affectée à la machine virtuelle |

Si vous ne souhaitez pas qu’une machine virtuelle communique avec les points de terminaison en dehors d’Azure dans l’espace d’adressage IP public, vous pouvez utiliser des groupes de sécurité réseau (NSG) pour bloquer l’accès. L’utilisation de groupes de sécurité réseau est abordée plus en détail dans [Empêchement de la connectivité publique](#preventing-public-connectivity).

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>Machine virtuelle autonome sans adresse IP publique de niveau d’instance

Dans ce scénario, la machine virtuelle ne fait pas partie d’un pool Azure Load Balancer et aucune adresse IP publique de niveau d’instance (ILPIP) n’y est affectée. Lorsque la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique. L’adresse IP publique utilisée pour ce flux sortant n’est pas configurable et n’entre pas en compte dans la limite de ressource IP publique de l’abonnement. Azure utilise le mode SNAT (Source Network Address Translation) pour exécuter cette fonction. Les ports éphémères de l’adresse IP publique sont utilisés pour distinguer chaque flux provenant de la machine virtuelle. SNAT alloue dynamiquement des ports éphémères lors de la création de flux. Dans ce contexte, les ports éphémères utilisés pour SNAT sont appelés ports SNAT.

Les ports SNAT sont une ressource limitée qui peut être épuisée. Il est important de comprendre leur utilisation. Un seul port SNAT est utilisé par flux vers une adresse IP de destination unique. En cas de flux multiples vers la même adresse IP et le même port de destination, chaque flux utilise un seul port SNAT. Cela garantit que les flux sont uniques s’ils proviennent de la même adresse IP publique et sont dirigés vers la même adresse IP et le même port de destination. Plusieurs flux, chacun dirigé vers une adresse IP et un port de destination différents, partagent un même port SNAT. L’adresse IP et le port de destination rendent les flux uniques.

Vous pouvez utiliser la fonctionnalité [Analytique des journaux pour l’équilibreur de charge](load-balancer-monitor-log.md) et le [Journal des événements d’alerte pour surveiller la présence de messages signalant l’épuisement des ressources de port SNAT](load-balancer-monitor-log.md#alert-event-log) et vérifier l’intégrité des connexions sortantes. En cas d’épuisement des ressources de port SNAT, les flux sortants échouent jusqu'à ce que des ports SNAT soient libérés par flux existants. L’équilibrage de charge utilise un délai d’inactivité de 4 minutes pour la récupération des ports SNAT.  Consultez [VM with an Instance Level Public IP address (with or without Load Balancer](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer) (Machine virtuelle avec une adresse IP publique au niveau de l’instance (avec ou sans équilibrage de charge), la section suivante, ainsi que l’article [Managing SNAT exhaustion](#snatexhaust) (Gestion de l’épuisement des ressources SNAT).

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>Machine virtuelle à charge équilibrée sans adresse IP publique de niveau d’instance

Dans ce scénario, la machine virtuelle fait partie d’un pool Azure Load Balancer.  La machine virtuelle n’a pas d’adresse IP publique affectée. La ressource d’équilibreur de charge doit être configurée avec une règle pour lier le serveur frontal d’adresse IP publique au pool principal.  Si vous n’effectuez pas cette configuration, le comportement est celui qui est décrit dans la section précédente pour [Machine virtuelle autonome sans adresse IP publique de niveau d’instance](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address).

Lorsque la machine virtuelle à charge équilibrée crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique du frontend d’équilibrage de charge public. Azure utilise le mode SNAT (Source Network Address Translation) pour exécuter cette fonction. Les ports éphémères de l’adresse IP publique de l’équilibrage de charge sont utilisés pour distinguer chaque flux provenant de la machine virtuelle. SNAT alloue dynamiquement des ports éphémères lors de la création de flux sortants. Dans ce contexte, les ports éphémères utilisés pour SNAT sont appelés ports SNAT.

Les ports SNAT sont une ressource limitée qui peut être épuisée. Il est important de comprendre leur utilisation. Un seul port SNAT est utilisé par flux vers une adresse IP et un port de destination uniques. En cas de flux multiples vers la même adresse IP et le même port de destination, chaque flux utilise un seul port SNAT. Cela garantit que les flux sont uniques s’ils proviennent de la même adresse IP publique et sont dirigés vers la même adresse IP et le même port de destination. Plusieurs flux, chacun dirigé vers une adresse IP et un port de destination différents, partagent un même port SNAT. L’adresse IP et le port de destination rendent les flux uniques.

Vous pouvez utiliser la fonctionnalité [Analytique des journaux pour l’équilibreur de charge](load-balancer-monitor-log.md) et le [Journal des événements d’alerte pour surveiller la présence de messages signalant l’épuisement des ressources de port SNAT](load-balancer-monitor-log.md#alert-event-log) et vérifier l’intégrité des connexions sortantes. En cas d’épuisement des ressources de port SNAT, les flux sortants échouent jusqu'à ce que des ports SNAT soient libérés par flux existants. L’équilibrage de charge utilise un délai d’inactivité de 4 minutes pour la récupération des ports SNAT.  Consultez la section suivante, ainsi que l’article [Managing SNAT exhaustion](#snatexhaust) (Gestion de l’épuisement des ressources SNAT).

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>Machine virtuelle avec une adresse IP publique de niveau d’instance (avec ou sans équilibrage de charge)

Dans ce scénario, une adresse IP publique de niveau d’instance (ILPIP) est affectée à la machine virtuelle. Peu importe si la machine virtuelle est à charge équilibrée ou non. Lorsqu’une adresse ILPIP est utilisée, SNAT (Source Network Address Translation) n’est pas utilisé. La machine virtuelle utilise l’adresse ILPIP pour tous les flux sortants. Si votre application lance plusieurs flux sortants et que les ressources SNAT sont épuisées, vous devez envisager d’affecter une adresse ILPIP afin de limiter les contraintes de SNAT.

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>Découverte de l’adresse IP publique utilisée par une machine virtuelle donnée

Il existe de nombreuses manières de déterminer l’adresse IP source publique d’une connexion sortante. OpenDNS fournit un service qui peut vous indiquer l’adresse IP publique votre machine virtuelle. La commande nslookup vous permet d’envoyer une requête DNS pour le myip.opendns.com du nom au programme de résolution OpenDNS. Le service retourne l’adresse IP source qui a été utilisée pour envoyer la requête. Lorsque vous exécutez la requête suivante à partir de votre machine virtuelle, la réponse est l’adresse IP publique utilisée pour cette machine virtuelle.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>Empêchement de la connectivité publique

Parfois, il n’est pas souhaitable d’autoriser une machine virtuelle à créer un flux sortant, ou il peut y avoir une exigence visant à définir les destinations pouvant être atteintes avec des flux sortants ou celles pouvant démarrer des flux entrants. Dans ce cas, vous utilisez des [groupes de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md) pour gérer les destinations que la machine virtuelle peut atteindre, ainsi que les destinations publiques pouvant initier des flux entrants. Lorsque vous appliquez un groupe de sécurité réseau à une machine Virtuelle à charge équilibrée, vous devez faire attention aux [balises par défaut](../virtual-network/virtual-networks-nsg.md#default-tags) et aux [règles par défaut](../virtual-network/virtual-networks-nsg.md#default-rules).

Vous devez vous assurer que la machine virtuelle peut recevoir des demandes d’analyse d’intégrité d’Azure Load Balancer. Si un groupe de sécurité réseau bloque les demandes d’analyse d’intégrité depuis la balise par défaut AZURE_LOADBALANCER, votre analyse de l’intégrité de la machine virtuelle échoue et la machine virtuelle est marquée comme défaillante. L’équilibrage de charge arrête l’envoi de nouveaux flux vers cette machine virtuelle.

## <a name="snatexhaust"></a>Gestion de l’épuisement des ressources SNAT

Les ports éphémères utilisés pour SNAT constituent une ressource épuisable, comme décrit dans les articles [Machine virtuelle autonome sans adresse IP publique de niveau d’instance](#standalone-vm-with-no-instance-level-public-ip-address) et [Machine virtuelle à charge équilibrée sans adresse IP publique de niveau d’instance](#standalone-vm-with-no-instance-level-public-ip-address).

Si vous savez que vous initiez de nombreuses connexions sortantes TCP ou UDP vers la même adresse IP et le même port de destination, si des connexions sortantes échouent ou si l’équipe de support vous indique que le nombre de ports SNAT arrive à épuisement, plusieurs solutions peuvent être mises en place.  Passez en revue ces options et choisissez celle qui convient le mieux à votre scénario.  Plusieurs options peuvent être adaptées à votre scénario.

### <a name="modify-application-to-reuse-connections"></a>Modifier l’application pour réutiliser des connexions 
Vous pouvez réduire la demande de ports éphémères utilisés pour SNAT en réutilisant des connexions dans votre application.  Cette méthode s'applique tout particulièrement aux protocoles comme HTTP/1.1, où la réutilisation d'une connexion est la règle par défaut.  D'autres protocoles qui utilisent HTTP comme transport (par exemple REST) peuvent également en bénéficier.  La réutilisation est toujours préférable aux connexions TCP atomiques individuelles pour chaque requête, ce qui se traduit par des transactions TCP plus performantes et très efficaces.

### <a name="modify-application-to-use-connection-pooling"></a>Modifier l’application pour utiliser le regroupement de connexions
Vous pouvez utiliser un schéma de regroupement de connexions dans votre application, dans lequel les demandes sont distribuées en interne sur un ensemble fixe de connexions (chacune étant réutilisée dans la mesure du possible).  Cela limite le nombre de ports éphémères utilisés et crée un environnement plus prévisible.  Cela peut également augmenter le débit des demandes en autorisant plusieurs opérations simultanées lorsqu'une seule connexion bloque sur la réponse d'une opération.  Le regroupement de connexions existe peut-être déjà dans l'infrastructure que vous utilisez pour développer votre application ou les paramètres de configuration de votre application.  Vous pouvez combiner cette méthode avec la réutilisation des connexions afin que vos multiples demandes consomment un nombre fixe et prévisible de ports vers la même adresse IP et le même port de destination, tout en bénéficiant d'une utilisation très efficace des transactions TCP pour réduire la latence et économiser les ressources.

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>Modifier l’application pour utiliser une logique de nouvelle tentative moins agressive
Lorsque le nombre de ports éphémères utilisés pour SNAT arrive à épuisement ou que des applications échouent, des tentatives de reconnexion agressives ou par force brute sans logique de réduction ou d’interruption ne feront que générer ou accentuer le problème. Vous pouvez réduire la demande de ports éphémères en utilisant une logique de nouvelle tentative moins agressive.   Les ports éphémères ont un délai d’inactivité de 4 minutes (non modifiable). Si les nouvelles tentatives sont trop agressives, le problème d’épuisement ne pourra pas se résoudre de lui-même.  Par conséquent, il est essentiel de pouvoir évaluer la façon et la fréquence à laquelle votre application relance les transactions.

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>Assigner une adresse IP publique au niveau de l’instance à chaque machine virtuelle
Votre scénario consiste alors à [assigner une adresse IP publique au niveau de l’instance à une machine virtuelle](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer).  Tous les ports éphémères de l’adresse IP publique utilisés pour une machine virtuelle sont disponibles pour la machine virtuelle (contrairement aux scénarios dans lesquels les ports éphémères d’une adresse IP publique sont partagés avec toutes les machines virtuelles associées au pool back-end respectif).  Des compromis sont à prendre en compte, notamment le coût supplémentaire des adresses IP et l'impact potentiel de la mise en liste blanche d'un grand nombre d'adresses IP individuelles.

## <a name="limitations"></a>Limites

Si [plusieurs adresses IP (publiques) sont associées à un équilibreur de charge](load-balancer-multivip-overview.md), toutes ces adresses IP publiques sont candidates pour des flux sortants.

Azure utilise un algorithme pour déterminer le nombre de ports SNAT disponibles en fonction de la taille du pool.  Ce n’est pas configurable pour l’instant.

Les connexions sortantes ont un délai d’inactivité de 4 minutes.  Ce délai n’est pas modifiable.

Il est à noter que le nombre de ports SNAT disponibles n’est pas directement lié au nombre de connexions. Consultez les sections précédentes pour savoir à quel moment et de quelle manière des ports SNAT sont alloués, et [comment gérer cette ressource qui n’est pas inépuisable](#snatexhaust).
