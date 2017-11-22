---
title: FAQ Azure DNS | Microsoft Docs
description: Forum aux questions sur Azure DNS
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: subsarma
ms.openlocfilehash: 1a2a23fb5de5b5b491f061512a15f7acb6721446
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="azure-dns-faq"></a>FAQ Azure DNS

## <a name="about-azure-dns"></a>À propos d’Azure DNS

### <a name="what-is-azure-dns"></a>Présentation d’Azure DNS

Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP. Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure.

Les domaines DNS dans Azure DNS sont hébergés sur un réseau global de serveurs de noms DNS. Nous utilisons la mise en réseau Anycast, afin que chaque requête DNS obtienne une réponse du serveur DNS disponible le plus proche. Cette technique offre des performances élevées et une haute disponibilité pour votre domaine.

Le service Azure DNS est basé sur Azure Resource Manager. Ainsi, il tire parti de fonctionnalités de Resource Manager telles que le contrôle d’accès en fonction du rôle, les journaux d’audit et le verrouillage de ressources. Vos domaines et enregistrements peuvent être gérés via le portail Azure, des applets de commande Azure PowerShell et l’interface CLI Azure multiplateforme. Les applications nécessitant une gestion automatique de DNS peuvent s’intégrer au service par le biais de l’API REST et des Kits de développement logiciel (SDK).

### <a name="how-much-does-azure-dns-cost"></a>Combien coûte Azure DNS ?

Le modèle de facturation Azure DNS est basé sur le nombre de zones DNS hébergées dans Azure DNS et sur le nombre de requêtes DNS qu’elles reçoivent. Des remises sont proposées en fonction de l’utilisation.

Pour plus d’informations, consultez la [page de tarification d’Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Quel est le contrat de niveau de service (SLA) d’Azure DNS ?

Nous garantissons qu’au moins 99,99 % du temps, les requêtes DNS valides recevront une réponse à partir de l’un de nos serveurs de noms Azure DNS au minimum.

Pour plus d’informations, consultez la [page du contrat de niveau de service (SLA) d’Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Qu’est-ce qu’une « zone DNS » ? Est-ce la même chose qu’un domaine DNS ? 

Un domaine est un nom unique dans le système de noms de domaine, par exemple, « contoso.com ».

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Par exemple, le domaine « contoso.com » peut contenir plusieurs enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web). Ils seraient hébergés dans la zone DNS « contoso.com ».

Un nom de domaine est *simplement un nom*, tandis qu’une zone DNS est une ressource de données contenant les enregistrements DNS pour un nom de domaine. Azure DNS vous permet d’héberger une zone DNS et de gérer les enregistrements DNS pour un domaine dans Azure. Il fournit également des serveurs de noms DNS pour répondre aux requêtes DNS provenant d’Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Dois-je acheter un nom de domaine DNS pour utiliser Azure DNS ? 

Pas nécessairement.

Vous n’avez pas besoin d’acheter un domaine pour héberger une zone DNS dans Azure DNS. Vous pouvez créer une zone DNS à tout moment sans être propriétaire d’un nom de domaine. Les requêtes DNS pour cette zone ne sont résolues que si elles sont dirigées vers les serveurs de noms Azure DNS affectés à la zone.

Vous devez acheter le nom de domaine si vous voulez lier votre zone DNS à la hiérarchie DNS mondiale. Cela permet aux requêtes DNS du monde entier de trouver votre zone DNS et d’obtenir en réponse vos enregistrements DNS.

## <a name="azure-dns-features"></a>Fonctionnalités d’Azure DNS

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS prend-il en charge le routage du trafic DNS ou le basculement du point de terminaison ?

Le routage du trafic DNS et le basculement du point de terminaison sont fournis par Azure Traffic Manager. Il s’agit d’un service Azure distinct qui peut être utilisé avec Azure DNS. Pour plus d’informations, consultez la [présentation de Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS prend uniquement en charge l’hébergement de domaines DNS « statiques », dans lequel chaque requête DNS pour un enregistrement DNS donné reçoit toujours la même réponse DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS prend-il en charge l’inscription de nom de domaine ?

Non. Azure DNS ne prend actuellement pas en charge l'achat de noms de domaine. Si vous voulez acheter des domaines, vous devez utiliser un bureau d’enregistrement de noms de domaine tiers. Le bureau d’enregistrement facture généralement des frais annuels peu élevés. Les domaines peuvent être hébergés dans Azure DNS pour la gestion des enregistrements DNS. Pour plus d’informations, consultez [Déléguer un domaine à Azure DNS](dns-domain-delegation.md) .

Il s’agit d’une fonctionnalité que nous suivons dans notre file d’attente de travaux en souffrance. Vous pouvez utiliser notre site de commentaires pour [enregistrer votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS prend-il en charge les domaines « privés » ?
La prise en charge de domaines « privés » est implémentée à l’aide de zones DNS privées.  Cette fonctionnalité est actuellement disponible en préversion.  Les zones DNS privées sont managées à l’aide des mêmes outils que les zones DNS Azure accessibles sur Internet, mais elles peuvent uniquement être résolues à partir de vos réseaux virtuels spécifiés.  Consultez la [vue d’ensemble](private-dns-overview.md) pour plus d’informations.

Pour plus d’informations sur les autres options DNS internes dans Azure, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS prend-il en charge DNSSEC ?

Non. Azure DNS ne prend actuellement pas en charge DNSSEC.

Il s’agit d’une fonctionnalité que nous suivons dans notre file d’attente de travaux en souffrance. Vous pouvez utiliser notre site de commentaires pour [enregistrer votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS prend-il en charge les transferts de zone (AXFR/IXFR) ?

Non. Azure DNS ne prend actuellement pas en charge les transferts de zone. Les zones DNS peuvent être [importées dans Azure DNS à l’aide de la CLI Azure](dns-import-export.md). Les enregistrements DNS peuvent être gérés via le [portail de gestion Azure DNS](dns-operations-recordsets-portal.md), notre [API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), le [Kit de développement logiciel (SDK)](dns-sdk.md), les [applets de commande PowerShell](dns-operations-recordsets.md) ou la[CLI](dns-operations-recordsets-cli.md).

Il s’agit d’une fonctionnalité que nous suivons dans notre file d’attente de travaux en souffrance. Vous pouvez utiliser notre site de commentaires pour [enregistrer votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS prend-il en charge les redirections d’URL ?

Non. Les services de redirection d’URL ne sont pas réellement un service DNS. Ils s’appliquent au niveau HTTP, plutôt qu’au niveau DNS. Certains fournisseurs DNS incluent un service de redirection d’URL dans leur offre globale. Ceci n’est actuellement pas pris en charge par Azure DNS.

Cette fonctionnalité est suivie dans notre file d’attente de travaux en souffrance. Vous pouvez utiliser notre site de commentaires pour [enregistrer votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Utilisation d’Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Puis-je co-héberger un domaine utilisant Azure DNS et un autre fournisseur DNS ?

Oui. Azure DNS prend en charge le co-hébergement de domaines avec d’autres services DNS.

Pour cela, vous devez modifier les enregistrements NS du domaine (qui déterminent les fournisseurs recevant des requêtes DNS pour le domaine) afin qu’ils pointent vers les serveurs de noms des deux fournisseurs. Ces enregistrements NS doivent être modifiés dans 3 emplacements : dans Azure DNS, dans l’autre fournisseur et dans la zone parent (généralement configurée via le registre de noms de domaine). Pour plus d’informations sur la délégation DNS, consultez [Délégation de domaine DNS](dns-domain-delegation.md).

Vous devez également vous assurer que les enregistrements DNS du domaine sont synchronisés entre les deux fournisseurs DNS. Azure DNS ne prend actuellement pas en charge les transferts de zone DNS. Vous devez synchroniser les enregistrements DNS à l’aide du [portail de gestion Azure DNS](dns-operations-recordsets-portal.md), de notre [API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), du [Kit de développement logiciel (SDK)](dns-sdk.md), des [applets de commande PowerShell](dns-operations-recordsets.md) ou de la[CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>Dois-je déléguer mon domaine sur les 4 serveurs de noms DNS Azure ?

Oui. Azure DNS affecte 4 serveurs de noms à chaque zone DNS, pour l’isolement des pannes et une meilleure résilience. Pour pouvoir obtenir le contrat de niveau de service (SLA) Azure DNS, vous devez déléguer votre domaine sur les 4 serveurs de noms.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Quelles sont les limites d’utilisation d’Azure DNS ?

Les limites par défaut suivantes s’appliquent lors de l’utilisation du DNS Azure :

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Puis-je déplacer une zone Azure DNS entre des groupes de ressources ou entre des abonnements ?

Oui. Les zones DNS peuvent être déplacées entre des groupes de ressources ou entre des abonnements.

Le déplacement d’une zone DNS n’a aucun impact sur les requêtes DNS. Les serveurs de noms affectés à la zone restent les mêmes, et les requêtes DNS sont traitées, dans l’ensemble, comme d’habitude.

Pour plus d’informations et pour obtenir des instructions sur le déplacement des zones DNS, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou abonnement](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Combien de temps faut-il pour que les modifications DNS s’appliquent ?

Les nouvelles zones DNS et nouveaux enregistrements DNS sont généralement appliqués rapidement sur les serveurs de noms Azure DNS, de l’ordre de quelques secondes.

L’application des modifications apportées aux enregistrements DNS existants peut prendre un peu plus de temps, mais sont toujours appliquées sur les serveurs de noms Azure DNS dans un délai de 60 secondes. Dans ce cas, la mise en cache DNS en dehors d’Azure DNS (par des clients DNS et des programmes de résolution DNS récursifs) peut également avoir un impact sur le délai d’application d’une modification DNS. Cette durée de cache peut être contrôlée à l’aide de la propriété de durée de vie (TTL) de chaque jeu d’enregistrements.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Comment puis-je protéger mes zones DNS contre une suppression accidentelle ?

Azure DNS est géré à l’aide d’Azure Resource Manager, et tire profit des fonctionnalités de contrôle d’accès proposées par Azure Resource Manager. Le contrôle d’accès en fonction du rôle permet de contrôler les utilisateurs disposant d’un accès en lecture ou écriture aux zones et jeux d’enregistrements DNS. Des verrouillages de ressources peuvent être appliqués pour empêcher toute modification ou suppression accidentelle de zones et jeux d’enregistrements DNS.

Pour plus d’informations, consultez [Protection des zones et enregistrements DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Comment configurer des enregistrements SPF dans Azure DNS ?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6-"></a>Les serveurs de noms Azure DNS effectuent-ils des résolutions sur IPv6 ? 

Oui. Les serveurs de noms Azure DNS sont à double pile (disposent d’adresses IPv4 et IPv6). Pour rechercher l’adresse IPv6 des serveurs de noms Azure DNS affectée à votre zone DNS, vous pouvez utiliser un outil tel que nslookup (par exemple, `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Comment configurer un nom de domaine international (IDN) dans Azure DNS ?

Les noms de domaines internationaux (IDN) encodent chaque nom DNS à l’aide de « [punycode](https://en.wikipedia.org/wiki/Punycode) ». Les requêtes DNS utilisent ces noms codés en punycode.

Vous pouvez configurer des noms de domaines internationaux (IDN) dans Azure DNS en convertissant tout d’abord le nom de zone ou de jeu d’enregistrements en punycode. Azure DNS n’intègre actuellement pas la conversion de/en punycode.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur Azure DNS](dns-overview.md)
<br>
[En savoir plus sur l’utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md)
<br>
[En savoir plus sur les zones et enregistrements DNS](dns-zones-records.md)
<br>
[Prise en main d’Azure DNS](dns-getstarted-portal.md)

