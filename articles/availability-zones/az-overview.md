---
title: "Vue d’ensemble des zones de disponibilité | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des zones de disponibilité Azure."
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 53c5c340dc4e91db29fbbb45893fabe6eec2bc5d
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Vue d’ensemble des zones de disponibilité dans Azure (préversion)

Les zones de disponibilité vous permettent de vous protéger contre les défaillances au niveau du centre de données. Elles se trouvent à l’intérieur d’une région Azure, et chacune possède sa propre source d’alimentation, son propre réseau et son propre système de refroidissement indépendants. Pour garantir la résilience, un minimum de trois zones distinctes sont activées dans toutes les régions. La séparation physique et logique des zones de disponibilité dans une région protège les applications et les données des défaillances au niveau de la zone. 

![affichage conceptuel d’une zone devenant indisponible dans une région](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Régions prenant en charge les zones de disponibilité

- Est des États-Unis 2
- Europe de l'Ouest

## <a name="services-that-support-availability-zones"></a>Régions qui prennent en charge les zones de disponibilité

Les services Azure qui prennent en charge les zones de disponibilité sont les suivants :

- Machines virtuelles Linux
- Machines virtuelles Windows
- Groupes zonaux de machines virtuelles identiques
- Managed Disks
- Load Balancer

## <a name="supported-virtual-machine-size-families"></a>Tailles de machine virtuelle prises en charge

- Av2
- Dv2
- DSv2


## <a name="get-started-with-the-availability-zones-preview"></a>Prise en main de la préversion des zones de disponibilité

La préversion des zones de disponibilité est disponible dans les régions États-Unis de l’Est 2 et Europe de l’Ouest pour certains services Azure spécifiques. 

1. [Inscrivez-vous à la préversion des zones de disponibilité](http://aka.ms/azenroll). 
2. Connectez-vous à votre abonnement Azure.
3. Choisissez une région qui prend en charge les zones de disponibilité.
4. Utilisez un des liens suivants pour commencer à utiliser les zones de disponibilité avec votre service. 
    - [Créer une machine virtuelle](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Créer un groupe zonal de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md)
    - [Ajouter un disque géré à l’aide de PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Équilibreur de charge](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Étapes suivantes
- [Modèles de démarrage rapide](http://aka.ms/azqs)
