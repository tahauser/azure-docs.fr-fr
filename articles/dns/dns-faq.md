---
title: FAQ Azure DNS | Microsoft Docs
description: Forum aux questions sur Azure DNS
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: f07f914ccf8ea6df216e3f571e38d7628b2d7fb6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-dns-faq"></a>FAQ Azure DNS

## <a name="about-azure-dns"></a>À propos d’Azure DNS

### <a name="what-is-azure-dns"></a>Présentation d’Azure DNS

Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP. Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure.

Les domaines DNS dans Azure DNS sont hébergés sur un réseau global de serveurs de noms DNS. Celui-ci utilise la mise en réseau Anycast afin que chaque requête DNS obtienne une réponse du serveur DNS disponible le plus proche. Azure DNS offre des performances élevées et une haute disponibilité pour votre domaine.

Le service Azure DNS est basé sur Azure Resource Manager. Ainsi, il tire parti de fonctionnalités de Resource Manager telles que le contrôle d’accès en fonction du rôle, les journaux d’audit et le verrouillage de ressources. Vos domaines et enregistrements peuvent être gérés via le portail Azure, des applets de commande Azure PowerShell et l’interface CLI Azure multiplateforme. Les applications nécessitant une gestion automatique de DNS peuvent s’intégrer au service par le biais de l’API REST et des Kits de développement logiciel (SDK).

### <a name="how-much-does-azure-dns-cost"></a>Combien coûte Azure DNS ?

Le modèle de facturation Azure DNS est basé sur le nombre de zones DNS hébergées dans Azure DNS et sur le nombre de requêtes DNS qu’elles reçoivent. Des remises sont proposées en fonction de l’utilisation.

Pour plus d’informations, consultez la [page de tarification d’Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Quel est le contrat de niveau de service (SLA) d’Azure DNS ?

Azure garantit qu’au moins 99,99 % du temps, les requêtes DNS valides recevront une réponse à partir d’au moins un de nos serveurs de noms Azure DNS.

Pour plus d’informations, consultez la [page du contrat de niveau de service (SLA) d’Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Qu’est-ce qu’une « zone DNS » ? Est-ce la même chose qu’un domaine DNS ? 

Un domaine est un nom unique dans le système de noms de domaine, par exemple, « contoso.com ».

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Par exemple, le domaine « contoso.com » peut contenir plusieurs enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web). Ces enregistrements seraient hébergés dans la zone DNS « contoso.com ».

Un nom de domaine est *simplement un nom*, tandis qu’une zone DNS est une ressource de données contenant les enregistrements DNS pour un nom de domaine. Azure DNS vous permet d’héberger une zone DNS et de gérer les enregistrements DNS pour un domaine dans Azure. Il fournit également des serveurs de noms DNS pour répondre aux requêtes DNS provenant d’Internet.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Dois-je acheter un nom de domaine DNS pour utiliser Azure DNS ? 

Pas nécessairement.

Vous n’avez pas besoin d’acheter un domaine pour héberger une zone DNS dans Azure DNS. Vous pouvez créer une zone DNS à tout moment sans être propriétaire d’un nom de domaine. Les requêtes DNS pour cette zone ne sont résolues que si elles sont dirigées vers les serveurs de noms Azure DNS affectés à la zone.

Vous devez acheter le nom de domaine si vous voulez lier votre zone DNS à la hiérarchie DNS mondiale. Cette liaison permet aux requêtes DNS du monde entier de trouver votre zone DNS et d’obtenir en réponse vos enregistrements DNS.

## <a name="azure-dns-features"></a>Fonctionnalités d’Azure DNS

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS prend-il en charge le routage du trafic DNS ou le basculement du point de terminaison ?

Le routage du trafic DNS et le basculement du point de terminaison sont fournis par Azure Traffic Manager. Azure Traffic Manager est un service Azure distinct qui peut être utilisé avec Azure DNS. Pour plus d’informations, consultez la [présentation de Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS prend uniquement en charge l’hébergement de domaines DNS « statiques », dans lequel chaque requête DNS pour un enregistrement DNS donné reçoit toujours la même réponse DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS prend-il en charge l’inscription de nom de domaine ?

Non. Azure DNS ne prend actuellement pas en charge l'achat de noms de domaine. Si vous voulez acheter des domaines, vous devez utiliser un bureau d’enregistrement de noms de domaine tiers. Le bureau d’enregistrement facture généralement des frais annuels peu élevés. Les domaines peuvent être hébergés dans Azure DNS pour la gestion des enregistrements DNS. Pour plus d’informations, consultez [Déléguer un domaine à Azure DNS](dns-domain-delegation.md) .

L’achat de domaine est une fonctionnalité qui est suivie dans le backlog Azure. Vous pouvez utiliser le site de commentaires pour [enregistrer votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS prend-il en charge DNSSEC ?

Non. Azure DNS ne prend actuellement pas en charge DNSSEC.

DNSSEC est une fonctionnalité qui est suivie dans le backlog Azure DNS. Vous pouvez utiliser le site de commentaires pour [enregistrer votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS prend-il en charge les transferts de zone (AXFR/IXFR) ?

Non. Azure DNS ne prend actuellement pas en charge les transferts de zone. Les zones DNS peuvent être [importées dans Azure DNS à l’aide de la CLI Azure](dns-import-export.md). Les enregistrements DNS peuvent être gérés via le [portail de gestion Azure DNS](dns-operations-recordsets-portal.md), notre [API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), le [Kit de développement logiciel (SDK)](dns-sdk.md), les [applets de commande PowerShell](dns-operations-recordsets.md) ou la[CLI](dns-operations-recordsets-cli.md).

Le transfert de zone est une fonctionnalité qui est suivie dans le backlog Azure DNS. Vous pouvez utiliser le site de commentaires pour [enregistrer votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS prend-il en charge les redirections d’URL ?

Non. Les services de redirection d’URL ne sont pas réellement un service DNS. Ils s’appliquent au niveau HTTP, plutôt qu’au niveau DNS. Certains fournisseurs DNS incluent un service de redirection d’URL dans leur offre globale. Ceci n’est actuellement pas pris en charge par Azure DNS.

La fonctionnalité de redirection d’URL est suivie dans le backlog Azure DNS. Vous pouvez utiliser le site de commentaires pour [enregistrer votre support pour cette fonctionnalité](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

## <a name="using-azure-dns"></a>Utilisation d’Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Puis-je co-héberger un domaine utilisant Azure DNS et un autre fournisseur DNS ?

Oui. Azure DNS prend en charge le co-hébergement de domaines avec d’autres services DNS.

Pour cela, vous devez modifier les enregistrements NS du domaine (qui déterminent les fournisseurs recevant des requêtes DNS pour le domaine) afin qu’ils pointent vers les serveurs de noms des deux fournisseurs. Vous pouvez modifier ces enregistrements NS à trois emplacements : dans Azure DNS, dans l’autre fournisseur et dans la zone parente (généralement configurée par le biais du bureau d’enregistrement de nom de domaine). Pour plus d’informations sur la délégation DNS, consultez [Délégation de domaine DNS](dns-domain-delegation.md).

Vous devez également vous assurer que les enregistrements DNS du domaine sont synchronisés entre les deux fournisseurs DNS. Azure DNS ne prend actuellement pas en charge les transferts de zone DNS. Les enregistrements DNS doivent être synchronisés à l’aide du [portail de gestion Azure DNS](dns-operations-recordsets-portal.md), de l’[API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), du [SDK](dns-sdk.md), des [applets de commande PowerShell](dns-operations-recordsets.md) ou de l’interface [CLI](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Dois-je déléguer mon domaine sur les quatre serveurs de noms Azure DNS ?

Oui. Azure DNS affecte quatre serveurs de noms à chaque zone DNS, pour l’isolement des pannes et une meilleure résilience. Pour pouvoir obtenir le contrat de niveau de service (SLA) Azure DNS, vous devez déléguer votre domaine sur les quatre serveurs de noms.

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

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Les serveurs de noms Azure DNS effectuent-ils des résolutions sur IPv6 ? 

Oui. Les serveurs de noms Azure DNS sont à double pile (disposent d’adresses IPv4 et IPv6). Pour rechercher l’adresse IPv6 des serveurs de noms Azure DNS affectée à votre zone DNS, vous pouvez utiliser un outil tel que nslookup (par exemple, `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Comment configurer un nom de domaine international (IDN) dans Azure DNS ?

Les noms de domaines internationaux (IDN) encodent chaque nom DNS à l’aide de « [punycode](https://en.wikipedia.org/wiki/Punycode) ». Les requêtes DNS utilisent ces noms codés en punycode.

Vous pouvez configurer des noms de domaines internationaux (IDN) dans Azure DNS en convertissant tout d’abord le nom de zone ou de jeu d’enregistrements en punycode. Azure DNS n’intègre actuellement pas la conversion de/en punycode.

## <a name="private-dns"></a>DNS privé

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS prend-il en charge les domaines « privés » ?
La prise en charge de domaines « privés » est implémentée à l’aide de la fonctionnalité Private Zones.  Cette fonctionnalité est actuellement disponible en préversion publique.  Les zones privées sont gérées à l’aide des mêmes outils que les zones Azure DNS accessibles sur Internet, mais elles peuvent être résolues uniquement à partir de vos réseaux virtuels spécifiés.  Consultez la [vue d’ensemble](private-dns-overview.md) pour plus d’informations.

À l’heure actuelle, Private Zones n’est pas pris en charge dans le portail Azure. 

Pour plus d’informations sur les autres options DNS internes dans Azure, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Quelle est la différence entre le réseau virtuel d’inscription et le réseau virtuel de résolution dans le contexte des zones privées ? 
Vous pouvez lier des réseaux virtuels à une zone privée DNS de deux manières : en tant que réseau virtuel d’inscription ou en tant que réseau virtuel de résolution. Dans les deux cas, les machines virtuelles sur le réseau virtuel pourront effectuer une résolution correcte par rapport aux enregistrements de la zone privée. Toutefois, si vous spécifiez un réseau virtuel comme étant un réseau virtuel d’inscription, Azure inscrit automatiquement les enregistrements DNS (inscription dynamique) dans la zone pour les machines virtuelles du réseau virtuel. De plus, quand une machine virtuelle sur un réseau virtuel d’inscription est supprimée, Azure supprime aussi automatiquement l’enregistrement DNS correspondant de la zone privée liée. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones fonctionne-il d’une région Azure à une autre ?
Oui. Private Zones est pris en charge pour la résolution DNS entre les réseaux virtuels de différentes régions Azure, même sans l’appairage explicite des réseaux virtuels, tant que ceux-ci sont spécifiés en tant que réseaux virtuels de résolution pour la zone privée. Les clients auront peut-être besoin que les réseaux virtuels soient appairés pour que le trafic TCP/HTTP circule d’une région à une autre. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>La connectivité à Internet à partir de réseaux virtuels est-elle requise pour Private Zones ?
Non. Private Zones fonctionne conjointement avec les réseaux virtuels, et permet aux clients de gérer les domaines pour les machines virtuelles ou d’autres ressources dans et entre les réseaux virtuels. Aucune connectivité à Internet n’est nécessaire pour la résolution de noms. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>La même zone privée peut-elle être utilisée pour plusieurs réseaux virtuels pour la résolution ? 
Oui. Les clients peuvent associer jusqu’à 10 réseaux virtuels de résolution à une même zone privée.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Un réseau virtuel qui appartient à un autre abonnement peut-il être ajouté en tant que réseau virtuel de résolution à une zone privée ? 
Oui, tant que l’utilisateur a l’autorisation d’opération d’écriture sur les réseaux virtuels et sur la zone DNS privé. Notez que l’autorisation d’écriture peut être allouée à plusieurs rôles RBAC. Par exemple, le rôle RBAC Contributeur de réseaux classiques dispose d’autorisations d’écriture sur les réseaux virtuels. Pour plus d’informations sur les rôles RBAC, consultez [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md).

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Les enregistrements DNS de machines virtuelles inscrits automatiquement dans une zone privée sont-ils supprimés automatiquement quand les machines virtuelles sont supprimées par le client ?
Oui. Si vous supprimez une machine virtuelle sur un réseau virtuel d’inscription, nous supprimons automatiquement les enregistrements DNS qui ont été inscrits dans la zone du fait qu’il s’agit d’un réseau virtuel d’inscription. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Un enregistrement de machine virtuelle inscrit automatiquement dans une zone privée (à partir d’un réseau virtuel d’inscription) peut-il être supprimé manuellement ? 
Non. À l’heure actuelle, les enregistrements DNS de machine virtuelle qui sont inscrits automatiquement dans une zone privée à partir d’un réseau virtuel d’inscription ne sont pas visibles ou modifiables par les clients. Toutefois, vous pouvez remplacer ces enregistrements DNS inscrits automatiquement par un enregistrement DNS créé manuellement dans la zone. Voir la question et la réponse suivantes qui traitent de ce problème.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Que se passe-t-il quand nous essayons de créer manuellement un enregistrement DNS dans une zone privée qui a le même nom d’hôte qu’une machine virtuelle existante (inscrite automatiquement) sur un réseau virtuel d’inscription ? 
Si vous tentez de créer manuellement un enregistrement DNS dans une zone privée qui a le même nom d’hôte qu’une machine virtuelle existante (inscrite automatiquement) sur un réseau virtuel d’inscription, nous autorisons le nouvel enregistrement DNS à remplacer automatiquement l’enregistrement de machine virtuelle inscrit automatiquement. De plus, si vous essayez par la suite de supprimer de la zone cet enregistrement DNS créé manuellement, la suppression réussit et l’inscription automatique a lieu à nouveau (l’enregistrement DNS est recréé automatiquement dans la zone) tant que les machines virtuelles existent encore et qu’une adresse IP privée leur est attachée. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Que se passe-t-il quand nous dissocions un réseau virtuel d’inscription d’une zone privée ? Les enregistrements de machines virtuelles du réseau virtuel inscrits automatiquement sont-ils également supprimés de la zone ?
Oui. Si vous dissociez un réseau virtuel d’inscription d’une zone privée (autrement dit si vous mettez à jour la zone DNS pour supprimer le réseau virtuel d’inscription associé), Azure supprime de la zone tous les enregistrements de machines virtuelles inscrits automatiquement. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Que se passe-t-il quand nous supprimons un réseau virtuel d’inscription (ou de résolution) qui est lié à une zone privée ? Devons-nous mettre manuellement à jour la zone privée afin de dissocier le réseau virtuel en tant que réseau virtuel d’inscription (ou de résolution) de la zone ?
Oui. Quand vous supprimez un réseau virtuel d’inscription (ou de résolution) sans le dissocier au préalable d’une zone privée, Azure laisse votre opération de suppression réussir, mais le réseau virtuel n’est pas supprimé automatiquement de votre zone privée. Vous devez dissocier manuellement le réseau virtuel de la zone privée. Pour cette raison, nous vous recommandons de dissocier votre réseau virtuel de votre zone privée avant de le supprimer.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>La résolution DNS à l’aide du nom de domaine complet (FQDN) par défaut (internal.cloudapp.net) fonctionnerait-elle quand même si une zone privée (par exemple contoso.local) était liée à un réseau virtuel ? 
Oui. La fonctionnalité Private Zones ne remplace pas les résolutions DNS par défaut utilisant la zone internal.cloudapp.net fournie par Azure. Elle est proposée en tant qu’amélioration ou fonctionnalité supplémentaire. Dans les deux cas (que vous vous reposiez sur internal.cloudapp.net fournie par Azure ou sur votre propre zone privée), nous vous conseillons d’utiliser le nom de domaine complet de la zone par rapport à laquelle vous souhaitez résoudre. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Le suffixe DNS sur les machines virtuelles au sein d’un réseau virtuel lié serait-il remplacé par celui de la zone privée ? 
Non. À l’heure actuelle, le suffixe DNS sur les machines virtuelles de votre réseau virtuel lié restera le suffixe par défaut fourni par Azure (« *.internal.cloudapp.net »). Vous pouvez toutefois changer manuellement ce suffixe DNS sur vos machines virtuelles en le remplaçant par celui de la zone privée. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Existe-t-il des limitations pour Private Zones dans cette préversion ?
Oui. La préversion publique a les limitations suivantes :
* 1 réseau virtuel d’inscription 1 par zone privée.
* Jusqu’à 10 réseaux virtuels d’inscription par zone privée.
* Un réseau virtuel donné ne peut être lié qu’à une seule zone privée en tant que réseau virtuel d’inscription.
* Un réseau virtuel donné peut être lié à 10 zones privées en tant que réseau virtuel résolution.
* Si un réseau virtuel d’inscription est spécifié, les enregistrements DNS pour les machines virtuelles de ce réseau virtuel qui sont inscrits dans la zone privée ne seront pas visibles ou récupérables à partir de l’API/Powershell/interface CLI, mais les enregistrements de machine virtuelle sont tout de même enregistrés et résolus avec succès.
* DNS inversé fonctionne uniquement pour l’espace IP privé sur le réseau virtuel d’inscription.
* DNS inversé pour une adresse IP privée qui n’est pas inscrite dans la zone privée (par exemple l’adresse IP privée d’une machine virtuelle sur un réseau virtuel qui est lié en tant que réseau virtuel de résolution à une zone privée) retournera « internal.cloudapp.net » comme suffixe DNS, mais ce suffixe ne pourra pas être résolu.   
* Le réseau virtuel doit être vide (autrement dit, aucune machine virtuelle avec une carte réseau attachée) lors de la liaison initiale à une zone privée en tant que réseau virtuel d’inscription ou de résolution. Toutefois, le réseau virtuel peut ensuite être non vide pour les liaisons ultérieures à d’autres zones privées en tant que réseau virtuel d’inscription ou de résolution. 
* À l’heure actuelle, le transfert conditionnel n’est pas pris en charge, par exemple pour l’activation de la résolution entre les réseaux Azure et locaux. Pour accéder à de la documentation expliquant comment implémenter ce scénario par le biais d’autres mécanismes, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Existe-t-il des quotas ou des limites applicables aux zones ou aux enregistrements pour les zones privées ?
Il n’existe aucune limite distincte quant au nombre de zones autorisées par abonnement, ou au nombre de jeux d’enregistrements par zone, pour les zones privées. Les zones publiques et privées sont prises en compte pour les limites DNS globales comme documenté [ici](../azure-subscription-service-limits.md#dns-limits).

### <a name="is-there-portal-support-for-private-zones"></a>Existe-t-il une prise en charge du portail pour les zones privées ?
Les zones privées qui sont déjà créées par le biais de mécanismes autre que le portail (API/PowerShell/interface CLI/SDK) seront visibles sur le portail Azure, mais les clients ne pourront pas créer de nouvelles zones privées ou gérer des associations avec des réseaux virtuels. De plus, pour les réseaux virtuels associés en tant que réseaux virtuels d’inscription, les enregistrements de machine virtuelle inscrits automatiquement ne seront pas visibles à partir du portail. 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur Azure DNS](dns-overview.md)
<br>
[En savoir plus sur l’utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md)
<br>
[En savoir plus sur les zones et enregistrements DNS](dns-zones-records.md)
<br>
[Prise en main d’Azure DNS](dns-getstarted-portal.md)

