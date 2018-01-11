---
title: "Vue d’ensemble des ports haute disponibilité dans Azure | Microsoft Docs"
description: "Découvrez plus d’informations sur l’équilibrage de charge des ports haute disponibilité sur un équilibreur de charge interne."
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 46e284d1636988390f3533d93bfd07399f45dc92
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="high-availability-ports-overview"></a>Vue d’ensemble des ports haute disponibilité

Azure Load Balancer Standard vous permet d’équilibrer la charge des flux TCP et UDP sur tous les ports simultanément quand vous utilisez un équilibreur de charge interne. 

>[!NOTE]
> La fonctionnalité des ports haute disponibilité est disponible avec Load Balancer Standard, actuellement en préversion. Les niveaux de disponibilité et de fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Inscrivez-vous à la préversion standard de Load Balancer pour utiliser les ports haute disponibilité avec des ressources Load Balancer Standard. Suivez également les instructions de l’inscription à la [préversion Standard](https://aka.ms/lbpreview#preview-sign-up) de Load Balancer.

Une règle de ports haute disponibilité est une variante d’une règle d’équilibrage de charge configurée sur un équilibreur de charge standard interne. Vous pouvez simplifier l’utilisation de l’équilibreur de charge en fournissant une seule règle pour équilibrer la charge de tous les flux TCP et UDP arrivant sur tous les ports d’un équilibreur de charge standard interne. La décision d’équilibrage de charge est prise par flux. Elle est basée sur la connexion à 5 tuples suivante : adresse IP source, port source, adresse IP de destination, port de destination et protocole.

La fonctionnalité des ports haute disponibilité est utile dans les scénarios critiques, comme pour la haute disponibilité et la mise à l’échelle d’appliances virtuelles réseau dans des réseaux virtuels. Elle peut également servir quand un grand nombre de ports doit avoir une charge équilibrée. 

La fonctionnalité des ports haute disponibilité est configurée quand vous définissez les ports frontaux et principaux sur **0**, et le protocole sur **Tous**. La ressource d’équilibreur de charge interne équilibre alors tous les flux TCP et UDP, quel que soit le nombre de ports.

## <a name="why-use-ha-ports"></a>Pourquoi utiliser des ports haute disponibilité ?

### <a name="nva"></a>Appliances virtuelles réseau

Vous pouvez utiliser des appliances virtuelles réseau pour sécuriser votre charge de travail Azure contre plusieurs types de menaces de sécurité. Quand des appliances virtuelles réseau sont utilisées dans ces scénarios, elles doivent être fiables, hautement disponibles et s’adapter à la demande.

Vous pouvez atteindre ces objectifs en ajoutant simplement des instances d’appliances virtuelles réseau au pool principal de l’équilibreur de charge interne Azure et en configurant une règle d’équilibreur de charge pour les ports haute disponibilité.

Les ports haute disponibilité présentent plusieurs avantages pour les scénarios de haute disponibilité des appliances virtuelles réseau :
- Basculement rapide sur des instances saines, avec des sondes d’intégrité par instance
- Meilleures performances avec une montée en charge jusqu’à *n* instances actives
- Scénarios *n*-actif et actif-passif
- Plus besoin d’utiliser des solutions complexes comme les nœuds Apache Zookeeper pour le monitoring des appliances

Le diagramme suivant présente un déploiement de réseau virtuel hub-and-spoke. Les spokes forcent le tunneling de leur trafic vers le réseau virtuel du hub et via l’appliance virtuelle réseau avant de quitter l’espace de confiance. Les appliances virtuelles réseau se trouvent derrière un équilibreur de charge standard interne avec une configuration de ports haute disponibilité. Tout le trafic peut être traité et transféré en conséquence.

![Diagramme d’un réseau virtuel hub-and-spoke avec des appliances virtuelles réseau déployées en mode de haute disponibilité](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Si vous utilisez des appliances virtuelles réseau, renseignez-vous auprès des fournisseurs respectifs sur l’utilisation des ports haute disponibilité et les scénarios pris en charge.

### <a name="load-balancing-large-numbers-of-ports"></a>Équilibrage de charge d’un grand nombre de ports

Vous pouvez aussi utiliser des ports haute disponibilité pour les applications qui nécessitent l’équilibrage de charge d’un grand nombre de ports. Vous pouvez simplifier ces scénarios à l’aide d’un [équilibreur de charge standard](https://aka.ms/lbpreview) interne avec des ports haute disponibilité. Une seule règle d’équilibrage de charge remplace plusieurs règles individuelles d’équilibrage de charge, une pour chaque port.

## <a name="region-availability"></a>Disponibilité des régions

La fonctionnalité des ports haute disponibilité est disponible dans les [mêmes régions que Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>S’inscrire à la préversion

Pour utiliser la préversion de la fonctionnalité des ports haute disponibilité dans Load Balancer Standard, inscrivez votre abonnement à la [préversion de Load Balancer Standard](https://aka.ms/lbpreview#preview-sign-up). Vous pouvez effectuer cette inscription avec Azure CLI 2.0 ou PowerShell.

>[!NOTE]
>L’inscription peut prendre jusqu’à une heure.

## <a name="limitations"></a>Limites

Voici les configurations ou exceptions prises en charge pour la fonctionnalité des ports haute disponibilité :

- Une même configuration IP frontale peut avoir une seule règle d’équilibreur de charge DSR (Direct Server Return, adresse IP flottante dans Azure) avec des ports haute disponibilité ou une seule règle d’équilibreur de charge non-DSR avec des ports haute disponibilité. Elle ne peut pas comprendre les deux à la fois.
- Une même configuration IP d’interface réseau ne peut avoir qu’une seule règle d’équilibreur de charge non-DSR avec des ports haute disponibilité. Vous ne pouvez pas configurer d’autres règles pour cette ipconfig.
- Une même configuration IP d’interface réseau peut avoir une ou plusieurs règles d’équilibreur de charge DSR avec des ports haute disponibilité, du moment que toutes leurs configurations IP frontales respectives sont uniques.
- Si toutes les règles d’équilibrage de charge ont des ports haute disponibilité (DSR uniquement), deux (ou plus) règles d’équilibreur de charge pointant vers le même pool principal peuvent coexister. Cela est vrai aussi si toutes les règles n’ont pas de port haute disponibilité (DSR et non-DSR). En cas de combinaison de règles avec et sans ports haute disponibilité, toutefois, les deux règles d’équilibrage de charge ne peuvent pas coexister.
- La fonctionnalité des ports haute disponibilité n’est pas disponible pour IPv6.
- La symétrie des flux dans les scénarios d’appliances virtuelles réseau est uniquement prise en charge avec une seule carte réseau. Consultez la description et le diagramme des [appliances virtuelles réseau](#nva). 



## <a name="next-steps"></a>Étapes suivantes

- [Configurer des ports haute disponibilité sur un équilibreur de charge standard interne](load-balancer-configure-ha-ports.md)
- [En savoir plus sur la préversion de Load Balancer Standard](https://aka.ms/lbpreview)

