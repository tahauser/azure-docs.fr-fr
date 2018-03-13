---
title: Plateformes prises en charge dans Azure Security Center | Microsoft Docs
description: "Ce document fournit une liste des systèmes d’exploitation Windows et Linux pris en charge dans Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 3b57cacec729bd2f2dd4acdbb9c15e69ab9f5c85
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="supported-platforms-in-azure-security-center"></a>Plateformes prises en charge dans Azure Security Center
La surveillance de l’état de la sécurité et des recommandations sont disponibles pour les machines virtuelles créées à l’aide des modèles de déploiement classique et Resource Manager, ainsi que des ordinateurs.

> [!NOTE]
> En savoir plus sur les [modèles de déploiement de type Classique et Resource Manager](../azure-classic-rm.md) pour les ressources Azure.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Plateformes prises en charge pour les ordinateurs et machines virtuelles Windows
Systèmes d’exploitation Windows pris en charge :

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Plateformes prises en charge pour les ordinateurs et machines virtuelles Linux
Systèmes d’exploitation Linux pris en charge :

* Ubuntu versions 12.04, 14.04, 16.04, 16.10
* Debian versions 7 et 8
* CentOS versions 6.\* et 7.*
* Red Hat Enterprise Linux (RHEL) versions 6.\* et 7.*
* SUSE Linux Enterprise Server (SLES) versions 11 SP4+ et 12.*
* Oracle Linux versions 6.\*, 7.*

> [!NOTE]
> L’analytique comportementale de machine virtuelle n’est pas encore disponible pour les systèmes d’exploitation Linux.
>
>

## <a name="vms-and-cloud-services"></a>Machines virtuelles et services cloud
Les machines virtuelles en cours d’exécution dans un service cloud sont également prises en charge. Seuls les rôles de travail et web des services cloud en cours d’exécution dans des emplacements de production sont surveillés. Pour en savoir plus sur le service cloud, consultez [Vue d’ensemble de Cloud Services](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Étapes suivantes

- [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception
- [Alertes de sécurité par type dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis) : en savoir plus sur l’analytique comportementale de machine virtuelle et l’analyse de mémoire de vidage sur incident dans Security Center
- [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure
