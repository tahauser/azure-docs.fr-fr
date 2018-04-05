---
title: Vue d’ensemble des ports haute disponibilité dans Azure | Microsoft Docs
description: Découvrez plus d’informations sur l’équilibrage de charge des ports haute disponibilité sur un équilibreur de charge interne.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: 09c51441d393de5d801e7a4c259b711a527349d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="high-availability-ports-overview"></a>Vue d’ensemble des ports haute disponibilité

Azure Standard Load Balancer vous permet d’équilibrer la charge des flux TCP et UDP sur tous les ports simultanément quand vous utilisez un équilibreur de charge interne. 

Une règle de ports haute disponibilité est une variante d’une règle d’équilibrage de charge configurée sur un équilibreur de charge standard interne. Vous pouvez simplifier l’utilisation de votre équilibreur de charge en fournissant une seule règle pour équilibrer la charge de tous les flux TCP et UDP arrivant sur tous les ports d’un équilibreur de charge standard interne. La décision d’équilibrage de charge est prise par flux. Elle est basée sur la connexion à 5 tuples suivante : adresse IP source, port source, adresse IP de destination, port de destination et protocole.

La fonctionnalité des ports haute disponibilité est utile dans les scénarios critiques, comme pour la haute disponibilité et la mise à l’échelle d’appliances virtuelles réseau dans des réseaux virtuels. Elle peut également servir quand un grand nombre de ports doit avoir une charge équilibrée. 

La fonctionnalité des ports haute disponibilité est configurée quand vous définissez les ports frontaux et principaux sur **0**, et le protocole sur **Tous**. La ressource d’équilibreur de charge interne équilibre alors tous les flux TCP et UDP, quel que soit le nombre de ports.

## <a name="why-use-ha-ports"></a>Pourquoi utiliser des ports haute disponibilité ?

### <a name="nva"></a>Appliances virtuelles réseau

Vous pouvez utiliser des appliances virtuelles réseau pour sécuriser votre charge de travail Azure contre plusieurs types de menaces de sécurité. Quand des appliances virtuelles réseau sont utilisées dans ces scénarios, elles doivent être fiables, hautement disponibles et s’adapter à la demande.

Vous pouvez atteindre ces objectifs en ajoutant simplement des instances d’appliances virtuelles réseau au pool principal de l’équilibreur de charge interne Azure et en configurant une règle d’équilibreur de charge pour les ports haute disponibilité.

Les ports haute disponibilité présentent plusieurs avantages pour les scénarios de haute disponibilité des appliances virtuelles réseau :
- Basculement rapide sur des instances saines, avec des sondes d’intégrité par instance
- Meilleures performances avec la montée en puissance vers *n* instances actives
- Scénarios *n*-actif et actif-passif
- Plus besoin d’utiliser des solutions complexes comme les nœuds Apache Zookeeper pour le monitoring des appliances

Le diagramme suivant présente un déploiement de réseau virtuel hub-and-spoke. Les spokes forcent le tunneling de leur trafic vers le réseau virtuel du hub et via l’appliance virtuelle réseau avant de quitter l’espace de confiance. Les appliances virtuelles réseau se trouvent derrière un équilibreur de charge standard interne avec une configuration de ports haute disponibilité. Tout le trafic peut être traité et transféré en conséquence.

![Diagramme d’un réseau virtuel hub-and-spoke avec des appliances virtuelles réseau déployées en mode de haute disponibilité](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Si vous utilisez des appliances virtuelles réseau, renseignez-vous auprès des fournisseurs respectifs sur l’utilisation des ports haute disponibilité et les scénarios pris en charge.

### <a name="load-balancing-large-numbers-of-ports"></a>Équilibrage de charge d’un grand nombre de ports

Vous pouvez aussi utiliser des ports haute disponibilité pour les applications qui nécessitent l’équilibrage de charge d’un grand nombre de ports. Vous pouvez simplifier ces scénarios à l’aide d’un [équilibreur de charge standard](load-balancer-standard-overview.md) interne avec des ports haute disponibilité. Une seule règle d’équilibrage de charge remplace plusieurs règles individuelles d’équilibrage de charge, une pour chaque port.

## <a name="region-availability"></a>Disponibilité des régions

La fonctionnalité de ports haute disponibilité est disponible dans toutes les régions Azure globales.

## <a name="supported-configurations"></a>Configurations prises en charge

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Une seule configuration de ports haute disponibilité d’adresse IP non flottante (pas de retour direct du serveur) sur l’équilibreur de charge standard interne

Il s’agit d’une configuration de ports haute disponibilité de base. La configuration suivante vous permet de configurer un équilibrage de charge de ports haute disponibilité sur une seule adresse IP frontale :
- Lors de la configuration de votre équilibreur de charge standard, cochez la case **Ports HA** dans la configuration de règle d’équilibreur de charge. 
- Vérifiez que l’option **IP flottante** est définie sur **Désactivée**.

Cette configuration n’autorise pas d’autre configuration de règle d’équilibrage de charge sur la ressource d’équilibreur de charge actuelle, ni d’autre configuration de ressource d’équilibreur de charge interne pour l’ensemble donné d’instances de serveur principal.

Vous pouvez toutefois configurer un équilibreur de charge standard public pour les instances de serveur principal en plus de cette règle de port haute disponibilité.

## <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Une seule configuration de ports haute disponibilité d’adresse IP flottante (retour direct du serveur) sur l’équilibreur de charge standard interne

De la même façon, vous pouvez configurer votre équilibreur de charge pour utiliser une règle d’équilibrage de charge avec **Port HA** avec un seul serveur frontal et l’option **IP flottante** définie sur **Activée**. 

Cette configuration vous permet d’ajouter des règles d’équilibrage de charge d’adresse IP flottante et/ou un équilibreur de charge public. Toutefois, vous ne pouvez pas utiliser de configuration d’équilibrage de charge de port haute disponibilité d’adresse IP non flottante en plus de cette configuration.

## <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>Plusieurs configurations de ports haute disponibilité sur l’équilibreur de charge standard interne

Si votre scénario requiert que vous configuriez plusieurs serveurs frontaux de ports haute disponibilité pour le même pool principal, vous pouvez y parvenir en : 
- configurant plusieurs adresses IP privées frontales pour une ressource d’équilibreur de charge standard interne ;
- configurant plusieurs règles d’équilibrage de charge, où chaque règle a une adresse IP frontale unique sélectionnée ;
- sélectionnant l’option **Ports HA** et en définissant l’option **IP flottante** sur **Activée** pour toutes les règles d’équilibrage de charge.

## <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>Équilibreur de charge interne avec ports haute disponibilité et équilibreur de charge public sur les mêmes instances de serveur principal

Vous pouvez configurer **une** ressource d’équilibreur de charge standard publique pour les ressources de serveur principal, ainsi qu’un seul équilibreur de charge standard interne avec des ports haute disponibilité.

>[!NOTE]
>Actuellement, cette fonctionnalité est disponible via les modèles Azure Resource Manager, mais pas via le portail Azure.

## <a name="limitations"></a>Limites

- La configuration des ports haute disponibilité est disponible uniquement pour l’équilibreur de charge interne ; elle n’est pas disponible pour un équilibreur de charge public.

- La combinaison d’une règle d’équilibrage de charge de ports haute disponibilité et d’une règle d’équilibrage de charge sans ports haute disponibilité n’est pas prise en charge.

- La fonctionnalité des ports haute disponibilité n’est pas disponible pour IPv6.

- La symétrie des flux dans les scénarios d’appliances virtuelles réseau est uniquement prise en charge avec une seule carte réseau. Consultez la description et le diagramme des [appliances virtuelles réseau](#nva). Toutefois, si une NAT de destination peut fonctionner dans votre scénario, vous pouvez l’utiliser pour vous assurer que l’équilibreur de charge interne envoie le trafic de retour à la même appliance virtuelle réseau.


## <a name="next-steps"></a>Étapes suivantes

- [Configurer des ports haute disponibilité pour un équilibreur de charge interne](load-balancer-configure-ha-ports.md)
- [Présentation de la référence Standard d’Azure Load Balancer (préversion)](load-balancer-standard-overview.md)
