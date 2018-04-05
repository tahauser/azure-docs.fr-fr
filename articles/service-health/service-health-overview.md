---
title: Présentation de Service Health | Microsoft Docs
description: Obtenez des informations personnalisées concernant l’incidence des problèmes et de la maintenance actuels et futurs d’Azure sur vos applications Azure.
services: Resource health
documentationcenter: ''
author: rboucher
manager: ''
editor: ''
ms.assetid: ''
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: robb
ms.openlocfilehash: f0efe96684d77fb83b69a4da12d312872da2f768
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="service-health"></a>Service Health
Service Health fournit un tableau de bord personnalisable qui suit l’intégrité de vos services Azure dans les régions où vous les utiliser. Dans ce tableau de bord, vous pouvez suivre les événements actifs, notamment les problèmes de service en cours, la maintenance planifiée à venir ou les conseils d’intégrité pertinents. Lorsque des événements deviennent inactifs, ils sont placés dans votre historique d’intégrité pendant 90 jours. Enfin, vous pouvez utiliser le tableau de bord Service Health pour créer et gérer des alertes d’intégrité de service qui vous informent de façon proactive des problèmes de service vous concernant.

## <a name="service-health-events"></a>Événements Service Health
Service Health suit trois types d’événements d’intégrité qui peuvent avoir une incidence sur vos ressources :
1. **Problèmes liés aux services** : problèmes touchant les services Azure qui vous affectent en ce moment même. 
2. **Maintenance planifiée** : maintenance à venir qui pourra avoir une incidence sur la disponibilité de vos services.  
3. **Health advisories** (Avis concernant l’intégrité) : modifications apportées aux services Azure qui nécessitent votre attention. Ce type d’événement se produit par exemple lorsque des fonctionnalités Azure sont déconseillées ou si vous dépassez un quota d’utilisation.

## <a name="get-started-with-service-health"></a>Prise en main de Service Health
Pour lancer votre tableau de bord Service Health, sélectionnez la vignette Service Health dans le tableau de bord du portail. Si vous avez supprimé la vignette ou utilisez un tableau de bord personnalisé, recherchez Service Health dans Plus de services (en bas à gauche de votre tableau de bord).

![Prise en main de Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Afficher les problèmes actuels qui ont une incidence sur vos services
La vue **Problèmes liés aux services** affiche tous les problèmes touchant actuellement les services Azure qui ont une incidence sur vos ressources. Vous pouvez savoir quand le problème a commencé et quels services et régions sont concernés. Vous pouvez également accéder aux informations les plus récentes pour comprendre ce qui est fait pour résoudre le problème. 

![Gérer des problèmes liés au service](./media/service-health-overview/azure-service-health-overview-2.png)

Choisissez l’onglet **Impact potentiel** pour afficher la liste spécifique des ressources que vous détenez pouvant être affectées par le problème. Vous pouvez télécharger une liste au format CSV de ces ressources pour la partager avec votre équipe.

![Gestion des problèmes liés au service - Impact](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Obtenir des liens et des explications téléchargeables 
Vous pouvez obtenir un lien pour le problème afin de l’utiliser dans votre système de gestion des problèmes. Vous pouvez télécharger des fichiers PDF et parfois CSV pour les partager avec des collaborateurs qui n’ont pas accès au portail Azure.   

![Gestion des problèmes liés aux services - Gestion des problèmes](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Obtenir l’aide du support Microsoft
Contactez le support si votre ressource présente encore un état incorrect après la résolution du problème.  Utilisez les liens de support sur la droite de la page.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Épingler une carte d’intégrité personnalisée à votre tableau de bord
Filtrez Service Health pour afficher vos abonnements, régions et types de ressources stratégiques. Enregistrez le filtre et épinglez une carte mondiale d’intégrité personnalisée à votre tableau de bord du portail. 

![Mappage de l’intégrité personnalisée des filtres](./media/service-health-overview/azure-service-health-overview-6a.png)

![Épingler un mappage de l’intégrité personnalisée](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurer des alertes d’intégrité de service
Service Health s’intègre à Azure Monitor pour vous avertir au moyen d’e-mails, de SMS et de notifications webhook quand certaines de vos ressources stratégiques sont impactées. Configurez une alerte de journal d’activité pour l’événement d’intégrité de service approprié. Acheminez cette alerte aux personnes appropriées de votre organisation à l’aide de groupes d’actions. Pour plus d’informations sur la configuration d’alertes pour Service Health, consultez [cet article](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

# <a name="next-steps"></a>Étapes suivantes
Configurez des alertes afin d’être averti des problèmes d’intégrité. Pour plus d’informations sur la configuration d’alertes pour Service Health, consultez [cet article](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
