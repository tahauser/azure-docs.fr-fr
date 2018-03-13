---
title: "Présentation de la référence Standard d’Azure Load Balancer | Microsoft Docs"
description: "Présentation de la référence Standard d’Azure Load Balancer"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2018
ms.author: kumud
ms.openlocfilehash: 8f98fd7773acb960ae79e743663aceb216f217c4
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Présentation de la référence Standard d’Azure Load Balancer (préversion)

Utilisées ensemble, la référence SKU Standard d’Azure Load Balancer et la référence SKU Standard d’adresse IP publique vous permettent de créer des architectures hautement évolutives et fiables. Les applications qui utilisent la référence Standard de Load Balancer peuvent tirer parti de nouvelles fonctionnalités, notamment d’une faible latence, d’un débit élevé et de la mise à l’échelle pour des millions de flux et pour toutes les applications TCP et UDP.

>[!NOTE]
> La référence SKU Standard de Load Balancer est actuellement en préversion. Le niveau de disponibilité et la fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour vos services de production, utilisez la [référence SKU De base de Load Balancer](load-balancer-overview.md) de la version publique. Pour utiliser la [version préliminaire des zones de disponibilité](https://aka.ms/availabilityzones) avec cette version préliminaire, il vous faut une [inscription différente](https://aka.ms/availabilityzones), en plus de l’inscription à la [version préliminaire standard](#preview-sign-up) de Load Balancer.

## <a name="why-use-load-balancer-standard"></a>Pourquoi utiliser la référence Standard de Load Balancer ?

Vous pouvez utiliser la référence Standard de Load Balancer pour l’intégralité de la plage de centres de données virtuels. Des déploiements à petite échelle aux architectures multizones étendues et complexes, la référence Standard de Load Balancer permet de tirer parti des capacités suivantes :

- [Une mise à l’échelle d’entreprise](#enterprisescale) peut être obtenue avec la référence Standard de Load Balancer. Cette fonctionnalité peut être utilisée avec n’importe quelle instance de machine virtuelle dans un réseau virtuel et avec un maximum de 1 000 instances de machine virtuelle.

- [De nouvelles informations de diagnostic](#diagnosticinsights) sont disponibles pour vous aider à comprendre, gérer et résoudre les problèmes de ce composant essentiel de votre centre de données virtuel. Utilisez Azure Monitor (préversion) pour afficher, filtrer et grouper les nouvelles métriques multidimensionnelles et mesurer en continu l’intégrité du chemin de données. Surveillez vos données, du serveur frontal à la machine virtuelle, les sondes d’intégrité de point de terminaison, les tentatives de connexion TCP et les connexions sortantes.

- [Les groupes de sécurité réseau](#nsg) sont désormais nécessaires pour toute instance de machine virtuelle associée aux références SKU Standard de Load Balancer ou d’adresse IP publique. Ils assurent une meilleure sécurité pour votre scénario.

- [Les ports de haute disponibilité (HA, High Availability) offrent haute fiabilité](#highreliability) et mise à l’échelle pour les appliances virtuelles réseau et d’autres scénarios d’application. Les ports HA équilibrent la charge de tous les ports d’un serveur frontal Azure Load Balancer interne sur un pool d’instances de machine virtuelle.

- [Les connexions sortantes](#outboundconnections) utilisent désormais un nouveau modèle d’allocation de port SNAT (Source Network Address Translation) qui fournit une meilleure résilience et une meilleure mise à l’échelle.

- [La référence Standard de Load Balancer avec des zones de disponibilité](#availabilityzones) peut être utilisée pour construire des architectures zonales et redondantes dans une zone. Ces deux architectures peuvent inclure l’équilibrage de charge entre les zones. Vous pouvez obtenir une redondance dans une zone sans dépendance vis-à-vis des enregistrements DNS. Une seule adresse IP est redondante dans une zone par défaut  et peut atteindre toutes les machines virtuelles d’un réseau virtuel dans une région sur toutes les zones de disponibilité.


Vous pouvez utiliser la référence Standard de Load Balancer dans des configurations publiques ou internes pour prendre en charge les scénarios de base suivants :

- Équilibrer la charge du trafic entrant sur des instances de serveur principal saines.
- Réacheminer les ports du trafic entrant sur une instance de serveur principal unique.
- Traduire le trafic sortant d’une adresse IP privée dans le réseau en une adresse IP publique.

### <a name = "enterprisescale"></a>Mise à l’échelle d’entreprise

 Utilisez la référence Standard de Load Balancer pour concevoir votre centre de données virtuel hautes performances et prendre en charge toute application TCP ou UDP. Utilisez des instances de machine virtuelle autonomes ou jusqu’à 1 000 instances de groupes de machines virtuelles identiques dans un pool principal. Continuez à exploiter la faible latence de réacheminement, les performances de débit élevé et la mise à l’échelle de millions de flux sur un service Azure entièrement géré.
 
La référence Standard de Load Balancer peut réacheminer le trafic sur toute instance de machine virtuelle dans un réseau virtuel d’une région. Les tailles de pool principal peuvent autoriser jusqu’à 1 000 instances avec toute combinaison des scénarios de machine virtuelle suivants :

- Machines virtuelles autonomes sans groupe à haute disponibilité
- Machines virtuelles autonomes avec groupes à haute disponibilité
- Groupes de machines virtuelles identiques (jusqu’à 1 000 instances)
- Plusieurs groupes de machines virtuelles identiques
- Mélanges de machines virtuelles et de groupes de machines virtuelles identiques

Les groupes à haute disponibilité ne sont plus requis. Cependant, vous pouvez choisir d’en utiliser pour bénéficier des autres avantages qu’ils offrent.

### <a name = "diagnosticinsights"></a>Informations de diagnostic

La référence Standard de Load Balancer offre de nouvelles fonctionnalités de diagnostic multidimensionnel pour les configurations Load Balancer publiques et internes. Ces nouvelles métriques sont fournies par le biais d’Azure Monitor (préversion) et utilisent toutes les fonctions connexes, y compris la possibilité d’intégration à divers consommateurs en aval.

| Métrique | DESCRIPTION |
| --- | --- |
| Disponibilité VIP | La référence Standard de Load Balancer teste en continu le chemin de données d’une région vers le serveur frontal Load Balancer, jusqu’à la pile SDN qui prend en charge votre machine virtuelle. Tant que les instances saines restent, la mesure suit le même chemin que le trafic à charge équilibrée de vos applications. Le chemin de données utilisé par vos clients est également validé. La mesure est invisible pour votre application et n’interfère pas avec les autres opérations.|
| Disponibilité DIP | La référence Standard de Load Balancer utilise un service de détection d’intégrité distribué qui surveille l’intégrité du point de terminaison de votre application en fonction de vos paramètres de configuration. Cette métrique fournit un agrégat ou une vue filtrée par point de terminaison de chaque point de terminaison d’instance dans le pool Load Balancer.  Vous pouvez observer comment Load Balancer voit l’intégrité de votre application comme indiqué par votre configuration de sonde d’intégrité.
| Paquets SYN | La référence Standard de Load Balancer ne termine pas les connexions TCP et n’interagit pas avec les flux de paquets UDP et TCP. Les flux et leurs établissements de liaisons sont toujours entre la source et l’instance de machine virtuelle. Pour mieux résoudre les problèmes posés par vos scénarios de protocole TCP, vous pouvez utiliser les compteurs de paquets SYN pour comprendre le nombre de tentatives de connexion TCP effectuées. La métrique indique le nombre de paquets SYN TCP reçus.|
| Connexions SNAT | La référence Standard de Load Balancer indique le nombre de flux sortants usurpés sur le serveur frontal d’adresse IP publique. Les ports SNAT constituent une ressource qui n’est pas inépuisable. Cette métrique peut donner une idée de l’importance du rôle joué par SNAT dans votre application pour les flux sortants.  Les compteurs relatifs aux flux SNAT sortants réussis et mis en échec sont indiqués et peuvent être utilisés pour comprendre l’intégrité de vos flux sortants et résoudre les problèmes associés.|
| Compteurs d’octets | La référence Standard de Load Balancer indique les données traitées par serveur frontal.|
| Compteurs de paquets | La référence Standard de Load Balancer indique les paquets traités par serveur frontal.|

### <a name = "highreliability"></a>Haute fiabilité

Configurez des règles d’équilibrage de charge pour mettre à l’échelle votre application et qu’elle soit très fiable. Vous pouvez configurer des règles pour chaque port ou vous pouvez utiliser les ports HA pour équilibrer tout le trafic, quel que soit le numéro de port TCP ou UDP.  

Vous pouvez utiliser la nouvelle fonctionnalité de ports HA pour déverrouiller un éventail de scénarios, y compris la haute disponibilité et la mise à l’échelle d’appliances virtuelles réseau internes. Cette fonctionnalité s’avère également utile pour d’autres scénarios, par exemple lorsqu’il est difficile ou qu’il n’est pas souhaitable de spécifier les ports individuels. Les ports HA assurent la redondance et la mise à l’échelle en autorisant autant d’instances que nécessaire. Votre configuration n’est plus limitée aux scénarios actifs/passifs. Vos configurations de sonde d’intégrité protègent votre service en réacheminant le trafic uniquement sur les instances saines.

Les fournisseurs d’appliances virtuelles réseau peuvent offrir à leurs clients des scénarios résilients et entièrement pris en charge par le fournisseur. Le point de défaillance unique est supprimé, et plusieurs instances actives sont prises en charge pour la mise à l’échelle. Vous pouvez mettre à l’échelle deux instances ou plus selon les capacités de votre appliance. Contactez votre fournisseur d’appliances virtuelles réseau pour obtenir des conseils supplémentaires pour ces scénarios.

### <a name = "availabilityzones"></a>Zones de disponibilité

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Développez la résilience de votre application avec l’utilisation de zones de disponibilité dans des régions prises en charge. Les zones de disponibilité sont actuellement en préversion dans des régions spécifiques et nécessitent un abonnement supplémentaire.

### <a name="automatic-zone-redundancy"></a>Redondance dans une zone automatique

Vous pouvez choisir si Load Balancer doit fournir un serveur frontal zonal ou redondant dans une zone pour chacune de vos applications. Vous pouvez facilement créer une redondance dans une zone avec la référence Standard de Load Balancer. Une adresse IP de serveur frontal unique est automatiquement redondante dans une zone. Un serveur frontal redondant dans une zone est servi par toutes les zones de disponibilité d’une région simultanément. Un chemin de données redondant dans une zone est créé pour les connexions entrantes et sortantes. La redondance dans une zone dans Azure ne nécessite pas plusieurs adresses IP et enregistrements DNS. 

Cette redondance dans une zone est disponible pour les serveurs frontaux publics ou internes. Votre adresse IP publique, ainsi que l’adresse IP privée de serveur frontal pour votre service Load Balancer interne peuvent être redondantes dans une zone.

Utilisez le script suivant pour créer une adresse IP publique redondante dans une zone pour votre service Load Balancer interne. Si vous utilisez des modèles Resource Manager existants dans votre configuration, ajoutez la section **sku** à ces modèles.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Utilisez le script suivant pour créer une adresse IP de serveur frontal redondant dans une zone pour votre service Load Balancer interne. Si vous utilisez des modèles Resource Manager existants dans votre configuration, ajoutez la section **sku** à ces modèles.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Si votre serveur frontal d’adresse IP publique est redondant dans une zone, les connexions sortantes établies à partir d’instances de machine virtuelle deviennent automatiquement redondantes dans une zone. Le serveur frontal est protégé contre les défaillances de zone. Votre allocation de port SNAT survit également aux défaillances de zone.

#### <a name="cross-zone-load-balancing"></a>Équilibrage de charge entre les zones

L’équilibrage de charge entre les zones est disponible dans une région pour le pool principal et offre une flexibilité maximale à vos instances de machine virtuelle. Un serveur frontal fournit des flux à toutes les machines virtuelles dans le réseau virtuel, quelle que soit la zone de disponibilité de l’instance de machine virtuelle.

Vous pouvez également spécifier une zone particulière pour vos instances de serveur frontal et de serveur principal, afin d’aligner votre chemin de données et vos ressources avec une zone spécifique.

Les sous-réseaux et réseaux virtuels ne sont jamais limités par une zone. Il vous suffit de définir un pool principal avec les instances de machine virtuelle de votre choix, et votre configuration est terminée.

#### <a name="zonal-deployments"></a>Déploiements zonaux

Vous pouvez également aligner votre serveur frontal d’équilibrage de charge sur une zone spécifique en définissant un serveur frontal zonal. Un serveur frontal zonal est servi par la zone de disponibilité unique désignée seulement. Lorsqu’il est combiné à des instances de machine virtuelle zonales, vous pouvez aligner des ressources sur des zones spécifiques.

Une adresse IP publique créée dans une zone spécifique existe toujours dans cette zone uniquement. Il n’est pas possible de modifier la zone d’une adresse IP publique. Si vous souhaitez avoir une adresse IP publique qui peut être associée à des ressources dans plusieurs zones, vous devez créer une adresse IP publique redondante dans une zone à la place.

Utilisez le script suivant pour créer une adresse IP publique zonale dans la zone de disponibilité 1. Si vous utilisez des modèles Resource Manager existants dans votre configuration, ajoutez la section **sku** à ces modèles.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Utilisez le script suivant pour créer un serveur frontal Load Balancer interne dans la zone de disponibilité 1.

Si vous utilisez des modèles Resource Manager existants dans votre configuration, ajoutez la section **sku** à ces modèles. Définissez également la propriété **zones** dans la configuration IP frontale pour la ressource enfant.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Ajoutez un équilibrage de charge entre les zones pour votre pool principal en plaçant dans le pool vos instances de machine virtuelle qui se trouvent dans un réseau virtuel.

La ressource Standard de Load Balancer est toujours régionale et redondante dans une zone, là où les zones de disponibilité sont prises en charge. Vous pouvez déployer une adresse IP publique ou un serveur frontal Load Balancer interne n’ayant aucune zone affectée à une région. La prise en charge des zones de disponibilité n’affecte pas la fonctionnalité de déploiement. Si une région obtient des zones de disponibilité plus tard, un serveur frontal Load Balancer interne ou une adresse IP publique déjà déployé devient automatiquement redondant dans une zone. Le chemin de données redondant dans une zone n’implique pas 0 % de pertes de paquets.

### <a name = "nsg"></a>Groupes de sécurité réseau

Les références Standard de Load Balancer et Standard d’adresse IP publique s’intègrent totalement au réseau virtuel, et des Groupes de sécurité réseau sont donc nécessaires. Ils permettent de mettre le flux de trafic sur liste verte. Vous pouvez les utiliser pour bénéficier d’un contrôle total sur le trafic vers votre déploiement. Il n’est plus nécessaire d’attendre la fin des autres flux de trafic.

Associez des Groupes de sécurité réseau à des sous-réseaux ou aux interfaces réseau des instances de machine virtuelle dans le pool principal. Utilisez cette configuration avec les références Standard de Load Balancer et Standard d’adresse IP publique en cas d’utilisation en tant qu’adresse IP publique de niveau d’instance. Le Groupe de sécurité réseau doit explicitement mettre sur liste verte le trafic que vous souhaitez autoriser pour que celui-ci puisse avoir lieu.

Pour plus d’informations sur les Groupes de sécurité réseau et la façon de les appliquer à votre scénario, consultez [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Connexions sortantes

La référence Standard de Load Balancer fournit des connexions sortantes pour les machines virtuelles à l’intérieur du réseau virtuel lorsqu’un équilibreur de charge utilise un algorithme SNAT d’usurpation d’identité de port. L’algorithme SNAT d’usurpation d’identité de port offre davantage de robustesse et une meilleure mise à l’échelle.

Lorsqu’une ressource Load Balancer publique est associée à des instances de machine virtuelle, la source de chaque connexion sortante est réécrite. La source est réécrite depuis l’espace d’adressage IP privé du réseau virtuel dans l’adresse IP publique frontale de l’équilibreur de charge.

Lorsque les connexions sortantes sont utilisées avec un serveur frontal redondant dans une zone, elles sont également redondantes dans une zone, et les allocations de port SNAT survivent aux défaillances de zone.

Le nouvel algorithme de la référence Standard de Load Balancer préalloue les ports SNAT à l’interface réseau de chaque machine virtuelle. Lorsqu’une interface réseau est ajoutée au pool, les ports SNAT sont préalloués selon la taille du pool. Le tableau suivant présente les préallocations de port pour six niveaux de taille de pool de principal :

| Taille du pool (instances de machine virtuelle) | Nombre préalloué de ports SNAT |
| --- | --- |
| 1 - 50 | 1 024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

Les ports SNAT ne traduisent pas directement en nombre de connexions sortantes. Un port SNAT peut être réutilisé pour plusieurs destinations uniques. Pour plus d’informations, consultez l’article sur les [connexions sortantes](load-balancer-outbound-connections.md).

Si la taille du pool principal augmente et passe à un niveau supérieur, la moitié des ports alloués est récupérée. Les connexions associées à un port récupéré expirent et doivent être établies une nouvelle fois. Les nouvelles tentatives de connexion réussissent immédiatement. Si la taille du pool principal diminue et passe à un niveau inférieur, le nombre de ports SNAT disponibles augmente. Dans ce cas, les connexions existantes ne sont pas affectées.

La référence Standard de Load Balancer dispose d’une option de configuration supplémentaire, fonctionnant sur une base par règle. Vous pouvez déterminer le serveur frontal utilisé pour l’algorithme SNAT d’usurpation d’identité de port lorsque plusieurs serveurs frontaux sont disponibles.

Lorsque seule la référence Standard de Load Balancer traite les instances de machine virtuelle, les connexions SNAT sortantes sont indisponibles. Vous pouvez restaurer cette capacité explicitement en assignant également des instances de machine virtuelle à un équilibreur de charge public ou en assignant des adresses IP publiques en tant qu’adresses IP publiques de niveau d’instance directement à chaque instance de machine virtuelle. Cette option de configuration peut être nécessaire pour certains scénarios d’application et de système d’exploitation. 

### <a name="port-forwarding"></a>Réacheminement de port

Les références Standard et De base de Load Balancer permettent de configurer des règles NAT entrantes pour mapper un port de serveur frontal à une instance de serveur principal individuelle. En configurant ces règles, vous pouvez exposer des points de terminaison de protocole RDP (Remote Desktop Protocol) et SSH ou effectuer d’autres scénarios d’application.

La référence Standard de Load Balancer continue de fournir la fonction de réacheminement de port via des règles NAT entrantes. Utilisées avec des serveurs frontaux redondants dans une zone, les règles NAT entrantes deviennent redondantes dans une zone et survivent aux défaillances de zone.

### <a name="multiple-front-ends"></a>Plusieurs serveurs frontaux

Configurez plusieurs serveurs frontaux pour une conception flexible dans laquelle les applications ont besoin que plusieurs adresses IP individuelles soient exposées (par exemple, des sites web TLS ou des points de terminaison de groupes de disponibilité AlwaysOn SQL). 

La référence Standard de Load Balancer continue de fournir plusieurs serveurs frontaux lorsque vous devez exposer un point de terminaison d’application spécifique sur une adresse IP unique.

Pour plus d’informations sur la configuration de plusieurs adresses IP frontales, consultez [Adresses IP virtuelles multiples pour Azure Load Balancer](load-balancer-multivip-overview.md).

## <a name = "sku"></a>À propos des références SKU

Les références SKU sont uniquement disponibles dans le modèle de déploiement Azure Resource Manager. Cette préversion introduit deux références SKU pour les ressources de Load Balancer et d’adresse IP publique : De base et Standard. Les fonctionnalités, les caractéristiques de performances, les limitations et certains comportements intrinsèques diffèrent d’une référence SKU à l’autre. Les machines virtuelles peuvent être utilisées avec l’une ou l’autre des références SKU. Pour les ressources de Load Balancer et d’adresse IP publique, les références SKU restent des attributs facultatifs. Lorsqu’elles sont omises dans une définition de scénario, la configuration par défaut est celle de la référence De base.

>[!IMPORTANT]
>La référence SKU d’une ressource n’est pas mutable. Vous ne pouvez pas modifier la référence SKU d’une ressource existante.  

### <a name="load-balancer"></a>Équilibreur de charge

La [ressource de Load Balancer existante](load-balancer-overview.md) devient la référence SKU De base et reste disponible publiquement et inchangée.

La référence SKU Standard de Load Balancer est nouvelle actuellement en préversion. Le 1er août 2017, la version de l’API pour Microsoft.Network/loadBalancers a ajouté la propriété **sku** à la définition de ressource :

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Dans les régions offrant des zones de disponibilité, la référence Standard de Load Balancer est automatiquement résiliente dans la zone. Si le service Load Balancer a été déclaré comme zonal, il n’est pas automatiquement résilient dans la zone.

### <a name="public-ip"></a>Adresse IP publique

La [ressource d’adresse IP publique existante](../virtual-network/virtual-network-ip-addresses-overview-arm.md) devient la référence SKU De base et reste disponible publiquement avec toutes ses fonctionnalités, caractéristiques de performances et limitations.

La référence SKU Standard d’adresse IP publique est nouvelle et actuellement en préversion. Le 1er août 2017, la version de l’API pour Microsoft.Network/publicIPAddresses a ajouté la propriété **sku** à la définition de ressource :

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Contrairement à la référence De base d’adresse IP publique, qui propose plusieurs méthodes d’allocation, la référence Standard d’adresse IP publique offre toujours une méthode d’allocation statique.

Dans les régions offrant des zones de disponibilité, la référence Standard d’adresse IP publique est automatiquement résiliente dans la zone. Si l’adresse IP publique a été déclarée comme zonale, elle n’est pas automatiquement résiliente dans la zone. Une adresse IP publique zonale ne peut pas être transférée d’une zone à une autre.

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
>Les références SKU qui correspondent doivent être utilisées pour les ressources de Load Balancer et d’adresse IP publique. Vous ne pouvez pas avoir à la fois des ressources de référence SKU De base et de référence SKU Standard. Vous ne pouvez pas associer une machine virtuelle, des machines virtuelles dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques aux deux références SKU simultanément.
>

## <a name="region-availability"></a>Disponibilité des régions

La référence Standard de Load Balancer est actuellement disponible dans toutes les régions cloud publiques.

>[!IMPORTANT]
> Pendant une courte période, l’accès aux régions extérieures au lancement initial (Est des États-Unis 2, Centre des États-Unis, Europe du Nord, Centre-Ouest des États-Unis, Europe de l’Ouest, Asie du Sud-Est) nécessite l’inscription à des fonctionnalités d’abonnement supplémentaires (AllowLBPreviewWave2 et AllowLBPreviewWave3).  [Procédez comme suit](#additionalpreviewregions). Effectuez toutes les opérations, même si vous vous êtes déjà inscrit pour AllowLBPreview.
> Cette condition ne sera plus obligatoire d’ici quelques semaines.

## <a name="sku-service-limits-and-abilities"></a>Fonctions et limites de service des références SKU

[Les limites de service pour la mise en réseau Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits) s’appliquent par région et par abonnement. 

Le tableau suivant compare les limites et fonctions des références SKU De base et Standard de Load Balancer :

| Équilibreur de charge | De base | standard |
| --- | --- | --- |
| Taille de pool principal | jusqu’à 100 | jusqu’à 1 000 |
| Limite de pool principal | Groupe à haute disponibilité | réseau virtuel, région |
| Conception de pool principal | Machines virtuelles dans un groupe à haute disponibilité, groupe de machines virtuelles identiques dans un groupe à haute disponibilité | N’importe quelle instance de machine virtuelle dans le réseau virtuel |
| Ports HA | Non pris en charge | Disponible |
| Diagnostics | Limités, publics uniquement | Disponible |
| Disponibilité VIP  | Non pris en charge | Disponible |
| Mobilité d’adresses IP rapide | Non pris en charge | Disponible |
|Scénarios de zones de disponibilité | Zonaux uniquement | Équilibrage de charge entre les zones, redondant dans une zone, zonal |
| Algorithme SNAT sortant | À la demande | Préalloué |
| Sélection du serveur frontal SNAT sortant | Non configurable, plusieurs candidats | Configuration facultative pour réduire les candidats |
| Groupe de sécurité réseau | Facultatif sur la carte d’interface réseau/le sous-réseau | Obligatoire |

Le tableau suivant compare les limites et fonctions des références SKU d’adresse IP publique De base et Standard :

| Adresse IP publique | De base | standard |
| --- | --- | --- |
| Scénarios de zones de disponibilité | Zonaux uniquement | Redondants dans une zone (par défaut), zonaux (facultatif) | 
| Mobilité d’adresses IP rapide | Non pris en charge | Disponible |
| Disponibilité VIP | Non pris en charge | Disponible |
| Counters | Non pris en charge | Disponible |
| Groupe de sécurité réseau | Facultatif sur la carte d’interface réseau | Obligatoire |


## <a name="preview-sign-up"></a>S’inscrire à la préversion

Pour découvrir la préversion de la référence SKU Standard de Load Balancer et à la référence SKU Standard d’adresse IP publique associée, inscrivez votre abonnement.  L’inscription de votre abonnement vous fait bénéficier d’un accès par le biais de PowerShell ou Azure CLI 2.0. Pour effectuer l’inscription, procédez comme suit :

>[!NOTE]
>La prise d’effet globale de l’inscription de la fonctionnalité Standard de Load Balancer peut prendre jusqu’à une heure. Si vous souhaitez utiliser Load Balancer Standard avec des [Zones de disponibilité](https://aka.ms/availabilityzones), une [inscription distincte](https://aka.ms/availabilityzones) est requise pour la préversion AZ.

<a name="additionalpreviewregions"></a>
>[!IMPORTANT]
> Pendant une courte période, l’accès aux régions extérieures au lancement initial (Est des États-Unis 2, Centre des États-Unis, Europe du Nord, Centre-Ouest des États-Unis, Europe de l’Ouest, Asie du Sud-Est) nécessite l’inscription à des fonctionnalités d’abonnement supplémentaires (AllowLBPreviewWave2 et AllowLBPreviewWave3).  Les étapes ci-dessous ont été modifiées afin d’autoriser des fonctionnalités d’abonnement supplémentaires. Effectuez toutes les opérations, même si vous vous êtes déjà inscrit pour AllowLBPreview. Cette condition ne sera plus obligatoire d’ici quelques semaines.


### <a name="sign-up-by-using-azure-cli-20"></a>S’inscrire à l’aide d’Azure CLI 2.0

1. Inscrire la fonctionnalité auprès du fournisseur :

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave2 --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave3 --namespace Microsoft.Network
    ```
    
2. L’opération peut prendre jusqu’à 10 minutes. Vous pouvez vérifier l’état de l’opération à l’aide de la commande suivante :

    ```cli
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreview']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave2']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave3']" --output json
    ```
    
    Passez à l’étape suivante lorsque l’état d’inscription de la fonctionnalité renvoie « Inscrit » pour chaque fonctionnalité d’abonnement ci-dessus. Exemple :
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
4. Complétez l’inscription à la préversion en réinscrivant votre abonnement avec le fournisseur de ressources :

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    

### <a name="sign-up-by-using-powershell"></a>S’inscrire à l’aide de PowerShell

1. Inscrire la fonctionnalité auprès du fournisseur :

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```
    
2. L’opération peut prendre jusqu’à 10 minutes. Vous pouvez vérifier l’état de l’opération à l’aide de la commande suivante :

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```

  Passez à l’étape suivante lorsque l’état d’inscription de la fonctionnalité renvoie « Inscrit » pour chaque fonctionnalité d’abonnement ci-dessus. Exemple :

    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. Complétez l’inscription à la préversion en réinscrivant votre abonnement avec le fournisseur de ressources :

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>Tarifs

La facturation de la référence SKU Standard de Load Balancer est basée sur les règles configurées et les données traitées. Aucuns frais ne sont facturés lors de la durée de la préversion. Consultez les pages de tarification de [Load Balancer](https://aka.ms/lbpreviewpricing) et [d’adresse IP publique](https://aka.ms/lbpreviewpippricing) pour plus d’informations.

Les clients peuvent continuer à profiter de la référence SKU De base de Load Balancer sans rien débourser.

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent au moment de la préversion et sont susceptibles de changer :

- Actuellement, les instances de serveur principal Load Balancer ne peuvent pas être placées dans des réseaux virtuels homologués. Toutes les instances de serveur principal doivent être dans la même région.
- Les références SKU ne sont pas mutables. Vous ne pouvez pas modifier la référence SKU d’une ressource existante.
- Une machine virtuelle autonome, les instances de machine virtuelle dans un groupe à haute disponibilité et un groupe de machines virtuelles identiques peuvent être utilisées avec les deux références SKU simultanément. Les combinaisons de machines virtuelles ne peuvent pas être utilisées avec les deux références SKU simultanément. Le mélange de références SKU au sein d’une même configuration n’est pas autorisé.
- L’utilisation d’une référence Standard de Load Balancer interne avec une instance de machine virtuelle (ou une partie d’un groupe à haute disponibilité) désactive les [connexions sortantes SNAT par défaut](load-balancer-outbound-connections.md). Vous pouvez restaurer cette fonction sur une machine virtuelle autonome, des instances de machine virtuelle dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques. Vous pouvez également restaurer la fonction permettant d’établir des connexions sortantes. Pour cela, assignez simultanément une référence Standard de Load Balancer public ou une référence Standard d’adresse IP publique en tant qu’adresse IP publique de niveau d’instance à la même instance de machine virtuelle. Une fois l’assignation effectuée, l’algorithme SNAT d’usurpation d’identité de port sur une adresse IP publique est de nouveau disponible.
- Les instances de machine virtuelle doivent éventuellement être rassemblées dans des groupes à haute disponibilité pour obtenir une mise à l’échelle intégrale du pool principal. Jusqu’à 150 groupes à haute disponibilité et machines virtuelles autonomes peuvent être placés dans un seul pool principal.
- IPv6 n’est pas pris en charge.
- Dans le contexte des zones de disponibilité, un serveur frontal ne peut pas passer de zonal à redondant dans une zone, et vice versa. Un serveur frontal créé comme redondant dans une zone reste redondant dans une zone. Un serveur frontal créé comme zonal reste zonal.
- Dans le contexte des zones de disponibilité, une adresse IP publique zonale ne peut pas être déplacée d’une zone à l’autre.
- Les [alertes Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-alerts.md) ne sont pas prises en charge pour l’instant.
- Le portail ne prend pas encore en charge les régions développées en préversion.  Veuillez utiliser les outils client comme les modèles, Azure CLI 2.0 ou PowerShell comme solution de contournement.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [la référence De base de Load Balancer](load-balancer-overview.md)
- En savoir plus sur les [zones de disponibilité](../availability-zones/az-overview.md)
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).
- En savoir plus sur les autres [fonctionnalités de mise en réseau](../networking/networking-overview.md) clés d’Azure.
- En savoir plus sur les [métriques exposées](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) dans [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
