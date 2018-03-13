---
title: "Configurer un réseau virtuel pour un Cache Redis Azure Premium | Microsoft Docs"
description: "Découvrez comment créer et gérer la prise en charge de réseau virtuel pour vos instances de Cache Redis Azure de niveau Premium"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: wesmc
ms.openlocfilehash: 5ed5af627fa8ec8007f095face2cbf115ead4b27
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium
Le Cache Redis Azure offre différents types de caches permettant de choisir en toute flexibilité parmi plusieurs tailles et fonctionnalités de caches, notamment les fonctionnalités de couche Premium telles que le clustering, la persistance et la prise en charge du réseau virtuel. Un réseau VNet est un réseau privé dans le cloud. Lorsqu’une instance de Cache Redis Azure est configurée avec un réseau virtuel, elle n’est pas adressable publiquement et est accessible uniquement à partir de machines virtuelles et d’applications sur le réseau virtuel. Cet article décrit comment configurer la prise en charge de réseau virtuel pour une instance Premium de Cache Redis Azure.

> [!NOTE]
> Le Cache Redis Azure prend en charge les réseaux virtuels classiques et de Gestionnaire de ressources.
> 
> 

Pour plus d’informations sur les autres fonctionnalités du cache Premium, consultez [Introduction au niveau Premium du Cache Redis Azure](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Pourquoi un réseau virtuel ?
Le déploiement d’un [Réseau virtuel Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) offre une sécurité et une isolation améliorées pour votre Cache Redis Azure, ainsi que des sous-réseaux, des stratégies de contrôle d’accès et d’autres fonctionnalités permettant de restreindre davantage l’accès.

## <a name="virtual-network-support"></a>Prise en charge des réseaux virtuels
La configuration de la prise en charge du réseau virtuel (VNet) s’effectue dans le panneau **Nouveau cache Redis** lors de la création du cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Une fois que vous avez sélectionné le niveau tarifaire Premium, vous pouvez configurer l’intégration du réseau virtuel Redis en sélectionnant un réseau virtuel situé dans le même abonnement et le même emplacement que votre cache. Pour utiliser un nouveau réseau virtuel, vous devez tout d’abord le créer en suivant les étapes indiquées dans [Créer un réseau virtuel à l’aide du Portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [Créer un réseau virtuel (classique) à l’aide du Portail ](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Revenez ensuite au panneau **Nouveau cache Redis** pour créer et configurer votre cache Premium.

Pour configurer le réseau virtuel pour votre nouveau cache, cliquez sur **Réseau virtuel** dans le panneau **Nouveau cache Redis**, puis sélectionnez le réseau virtuel souhaité dans la liste déroulante.

![Réseau virtuel][redis-cache-vnet]

Sélectionnez le sous-réseau souhaité dans la liste déroulante **Sous-réseau**, puis spécifiez **l’adresse IP statique** souhaitée. Si vous utilisez un réseau virtuel classique, le champ **Adresse IP statique** est facultatif. Si aucune adresse IP statique n’est spécifiée, une option sera choisie dans le sous-réseau sélectionné.

> [!IMPORTANT]
> Lorsque vous déployez un Cache Redis Azure vers un réseau virtuel Gestionnaire des ressources, le cache doit se trouver dans un sous-réseau dédié ne contenant pas de ressources autres que des instances du Cache Redis Azure. Si vous tentez de déployer un Cache Redis Azure sur un réseau virtuel Gestionnaire des ressources vers un sous-réseau contenant d’autres ressources, le déploiement échoue.
> 
> 

![Réseau virtuel][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure réserve dans chaque sous-réseau des adresses IP qui ne peuvent pas être utilisées. Les première et dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, et 3 adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez [Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Outre les adresses IP utilisées par l’infrastructure réseau virtuel Azure, chaque d’instance Redis dans le sous-réseau utilise deux adresses IP par partition et une adresse IP supplémentaire pour l’équilibrage de charge. Un cache non cluster est considéré comme ayant une seule partition.
> 
> 

Une fois le cache créé, vous pouvez afficher la configuration du réseau virtuel en cliquant sur **Réseau virtuel** dans le **menu Ressource**.

![Réseau virtuel][redis-cache-vnet-info]

Pour vous connecter à votre instance du Cache Redis Azure lorsque vous utilisez un réseau virtuel, spécifiez le nom d’hôte de votre cache dans la chaîne de connexion, comme indiqué dans l’exemple suivant :

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Forum aux questions sur le réseau virtuel de Cache Redis Azure
La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la mise à l’échelle du Cache Redis Azure.

* [Quels sont les problèmes de configuration les plus courants au niveau du Cache Redis Azure et des réseaux virtuels ?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Comment puis-je vérifier que mon cache fonctionne dans un réseau virtuel ?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Puis-je utiliser des réseaux virtuels avec un cache De base ou Standard ?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Pourquoi la création d’un cache Redis échoue-t-elle dans certains sous-réseaux mais pas d’autres ?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Quelles sont les exigences d’espace d’adressage du sous-réseau ?](#what-are-the-subnet-address-space-requirements)
* [Toutes les fonctionnalités fonctionnent-elles lorsque vous hébergez un cache dans un réseau virtuel ?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Quels sont les problèmes de configuration les plus courants au niveau du Cache Redis Azure et des réseaux virtuels ?
Lorsque le Cache Redis Azure est hébergé dans un réseau virtuel, les ports dans les tableaux suivants sont utilisés. 

>[!IMPORTANT]
>Si ces ports dans les tableaux suivants sont bloqués, le cache risque de ne pas fonctionner correctement. Le blocage d’un ou plusieurs de ces ports constitue le problème de configuration le plus courant lorsque vous utilisez le Cache Redis Azure dans un réseau virtuel.
> 
> 

- [Configuration requise de port sortant](#outbound-port-requirements)
- [Configuration requise de port entrant](#inbound-port-requirements)

### <a name="outbound-port-requirements"></a>Configuration requise de port sortant

Il existe sept configurations requises de port sortant.

- Si vous le souhaitez, toutes les connexions sortantes vers Internet peuvent être effectuées via un appareil d’audit local du client.
- Trois des ports acheminent le trafic vers des points de terminaison Azure se chargeant du stockage Azure et d’Azure DNS.
- Les plages de ports restantes et pour les communications internes du sous-réseau Redis. Aucune règle de groupe de sécurité réseau de sous-réseau n’est requise pour les communications sur sous-réseau Redis interne.

| Port(s) | Direction | Protocole de transfert | Objectif | Adresse IP locale | Adresse IP distante |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Règle de trafic sortant |TCP |Dépendances Redis sur Azure Storage/l’infrastructure à clé publique (Internet) | (sous-réseau Redis) |* |
| 53 |Règle de trafic sortant |TCP/UDP |Dépendances Redis sur le DNS (Internet/réseau virtuel) | (sous-réseau Redis) |* |
| 8443 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) | (sous-réseau Redis) |
| 10221-10231 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) | (sous-réseau Redis) |
| 20226 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |
| 13000-13999 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |
| 15000-15999 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |
| 6379-6380 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |


### <a name="inbound-port-requirements"></a>Configuration requise des ports entrants

Il existe huit configurations requises de port entrant. Les requêtes entrantes dans ces plages proviennent d’autres services hébergés dans le même réseau virtuel ou des communications sur sous-réseau Redis interne.

| Port(s) | Direction | Protocole de transfert | Objectif | Adresse IP locale | Adresse IP distante |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Trafic entrant |TCP |Communication client avec Redis, équilibrage de charge Azure | (sous-réseau Redis) | (sous-réseau Redis), Réseau virtuel, Azure Load Balancer |
| 8443 |Trafic entrant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |
| 8500 |Trafic entrant |TCP/UDP |Équilibrage de charge Azure | (sous-réseau Redis) |Azure Load Balancer |
| 10221-10231 |Trafic entrant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(Sous-réseau Redis) Azure Load Balancer |
| 13000-13999 |Trafic entrant |TCP |Communication client avec les clusters Redis, équilibrage de charge Azure | (sous-réseau Redis) |Réseau virtuel, Azure Load Balancer |
| 15000-15999 |Trafic entrant |TCP |Communication client avec les clusters Redis, équilibrage de charge Azure | (sous-réseau Redis) |Réseau virtuel, Azure Load Balancer |
| 16001 |Trafic entrant |TCP/UDP |Équilibrage de charge Azure | (sous-réseau Redis) |Azure Load Balancer |
| 20226 |Trafic entrant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |

### <a name="additional-vnet-network-connectivity-requirements"></a>Conditions supplémentaires pour la connectivité réseau VNET

Il existe des exigences de connectivité réseau pour le Cache Redis Azure qui peuvent ne pas être initialement satisfaites dans un réseau virtuel. Le Cache Redis Azure nécessite tous les éléments suivants pour fonctionner correctement lorsqu’il est utilisé dans un réseau virtuel.

* Connectivité réseau sortante à des points de terminaison Azure Storage dans le monde entier. Cela inclut les points de terminaison situés dans la même région que le cache Redis Azure, ainsi que les points de terminaison de stockage situés dans d’ **autres** régions Azure. Les points de terminaison du Stockage Azure se résolvent dans les domaines DNS suivants : *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* et *file.core.windows.net*. 
* Connectivité réseau sortante à *ocsp.msocsp.com*, *mscrl.microsoft.com* et *crl.microsoft.com*. Cette connectivité est nécessaire pour prendre en charge la fonctionnalité SSL.
* La configuration DNS pour le réseau virtuel doit être capable de résoudre tous les points de terminaison et les domaines mentionnés dans les points précédents. La configuration DNS requise peut être satisfaite en garantissant qu'une infrastructure DNS valide est configurée et gérée pour le réseau virtuel.
* Connectivité réseau sortante aux points de terminaison de surveillance Azure suivants qui se résolvent dans les domaines DNS suivants : shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net et azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Comment puis-je vérifier que mon cache fonctionne dans un réseau virtuel ?

>[!IMPORTANT]
>Lors de la connexion à une instance de Cache Redis Azure hébergée dans un réseau virtuel, vos clients de cache doivent figurer dans le même réseau virtuel ou dans un réseau virtuel pour lequel l’homologation de réseau virtuel est activée. Cela inclut les applications de test ou les outils de requête de diagnostic. Quel que soit l’endroit où l’application cliente est hébergée, les groupes de sécurité réseau doivent être configurés de façon à ce que le trafic réseau du client soit autorisé à atteindre l’instance Redis.
>
>

Une fois les exigences de port configurées comme décrit dans la section précédente, vous pouvez vérifier que votre cache fonctionne en procédant comme suit.

- [Redémarrez](cache-administration.md#reboot) tous les nœuds de cache. Si toutes les dépendances de cache requises sont inaccessibles (comme décrit dans [Configuration requise des ports entrants](cache-how-to-premium-vnet.md#inbound-port-requirements) et [Configuration requise des ports sortants](cache-how-to-premium-vnet.md#outbound-port-requirements)), le cache ne redémarre plus correctement.
- Une fois les nœuds de cache redémarrés (comme indiqué par l’état du cache dans le portail Azure), vous pouvez effectuer les tests suivants :
  - Vous pouvez effectuer un test ping du point de terminaison du cache (en utilisant le port 6380) à partir d’une machine figurant dans le même réseau virtuel que le cache, à l’aide de la commande [tcping](https://www.elifulkerson.com/projects/tcping.php). Par exemple : 
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Si l’outil `tcping` signale que le port est ouvert, cela signifie que le cache est disponible pour connexion à partir de clients dans le réseau virtuel.

  - Un autre test consiste à créer un client de cache de test (qui peut être une simple application console utilisant StackExchange.Redis) qui se connecte au cache et ajoute et récupère des éléments de celui-ci. Installez l’exemple d’application cliente sur une machine virtuelle figurant dans le même réseau virtuel que le cache, puis exécutez l’application pour vérifier la connectivité au cache.


### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Puis-je utiliser des réseaux virtuels avec un cache De base ou Standard ?
Vous ne pouvez utiliser des réseaux virtuels qu’avec les caches de niveau Premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Pourquoi la création d’un cache Redis échoue-t-elle dans certains sous-réseaux mais pas d’autres ?
Si vous déployez un Cache Redis Azure sur un réseau virtuel Gestionnaire des ressources, le cache doit se trouver dans un sous-réseau dédié qui ne contient aucun autre type de ressource. Si vous tentez de déployer un Cache Redis Azure sur un sous-réseau de réseau virtuel Gestionnaire des ressources vers un sous-réseau contenant d’autres ressources, le déploiement échoue. Vous devez supprimer les ressources existantes dans le sous-réseau avant de pouvoir créer un nouveau cache Redis.

Vous pouvez déployer plusieurs types de ressources sur un réseau virtuel classique, à condition de disposer de suffisamment d’adresses IP disponibles.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quelles sont les exigences d’espace d’adressage du sous-réseau ?
Azure réserve dans chaque sous-réseau des adresses IP qui ne peuvent pas être utilisées. Les première et dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, et 3 adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez [Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Outre les adresses IP utilisées par l’infrastructure réseau virtuel Azure, chaque d’instance Redis dans le sous-réseau utilise deux adresses IP par partition et une adresse IP supplémentaire pour l’équilibrage de charge. Un cache non cluster est considéré comme ayant une seule partition.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Toutes les fonctionnalités fonctionnent-elles lorsque vous hébergez un cache dans un réseau virtuel ?
Quand votre cache fait partie d’un réseau virtuel, seuls les clients de ce réseau virtuel peuvent accéder au cache. Par conséquent, les fonctionnalités de gestion de cache suivantes ne fonctionnent pas pour l’instant.

* Console redis. Console Redis s’exécutant dans votre navigateur local situé à l’extérieur du réseau virtuel, il ne peut pas se connecter à votre cache.

## <a name="use-expressroute-with-azure-redis-cache"></a>Utiliser ExpressRoute avec le Cache Redis Azure
Les clients peuvent connecter un circuit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à leur infrastructure de réseau virtuel pour étendre leur réseau local à Azure. 

Par défaut, un circuit ExpressRoute nouvellement créé n’effectue pas de tunneling forcé (publication d’un routage par défaut, 0.0.0.0/0) sur un réseau virtuel. Par conséquent, la connectivité Internet sortante est autorisée directement à partir du réseau virtuel, et les applications clientes sont en mesure de se connecter à d’autres points de terminaison Azure, dont Cache Redis Azure.

Toutefois, une configuration de client courante consiste à utiliser un tunneling forcé (publication d’un routage par défaut), qui force le trafic Internet sortant à circuler localement à la place. Ce flux de trafic interrompt la connectivité avec le Cache Redis Azure si le trafic sortant est bloqué localement, par exemple, quand l’instance du Cache Redis Azure ne peut pas communiquer avec ses dépendances.

La solution consiste à définir un (ou plusieurs) itinéraires définis par l’utilisateur (UDR) sur le sous-réseau qui contient le Cache Redis Azure. Un itinéraire défini par l'utilisateur définit des itinéraires spécifiques au sous-réseau qui seront respectés au lieu de l'itinéraire par défaut.

Si possible, il est recommandé d'utiliser la configuration suivante :

* La configuration ExpressRoute annonce 0.0.0.0/0 et par défaut, tunnelise de force tout le trafic sortant sur site.
* L’UDR appliqué au sous-réseau contenant le Cache Redis Azure définit 0.0.0.0/0 avec un itinéraire de travail pour le trafic TCP/IP vers le réseau internet public, par exemple, en définissant le type de tronçon suivant vers « Internet ».

L’effet combiné de ces étapes est que l’UDR de niveau sous-réseau a la priorité sur le tunneling forcé ExpressRoute, garantissant ainsi un accès Internet sortant à partir du Cache Redis Azure.

La connexion à une instance du Cache Redis Azure à partir d’une application locale à l’aide d’ExpressRoute n’est pas un scénario d’utilisation classique pour des raisons de performances (pour des performances optimales, les clients du Cache Redis Azure doivent être situés dans la même région que le Cache Redis Azure).

>[!IMPORTANT] 
>Les itinéraires définis dans un UDR **doivent** être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L'exemple suivant utilise la plage d'adresses 0.0.0.0/0 étendue qui peut potentiellement être remplacée accidentellement par les annonces de routage à l'aide de plages d'adresses plus spécifiques.

>[!WARNING]  
>Le cache Redis Azure n’est pas pris en charge avec les configurations ExpressRoute qui **publient incorrectement de façon croisée des itinéraires à partir du chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée**. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent des annonces de routage en provenance de Microsoft pour un grand ensemble de plages d’adresses IP Microsoft Azure. Si ces plages d’adresses sont incorrectement publiées de façon croisée sur le chemin d’accès d’homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l’instance du Cache Redis Azure sont incorrectement acheminés de force vers l’infrastructure réseau locale d’un client. Ce flux réseau interrompt le Cache Redis Azure. La solution à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d'accès d'homologation publique vers le chemin d'accès d'homologation privée.


Vous trouverez des informations générales sur les itinéraires définis par l'utilisateur dans cette [présentation](../virtual-network/virtual-networks-udr-overview.md).

Pour plus d’informations sur ExpressRoute, voir [Aperçu technique d’ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment utiliser davantage de fonctionnalités de cache de niveau Premium.

* [Introduction au niveau Premium du Cache Redis Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

