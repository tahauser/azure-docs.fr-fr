---
title: "Surveillance avancée dans la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Ce didacticiel vous montre comment surveiller les appareils à l’aide du tableau de bord de la solution de surveillance à distance."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: fe0d936b4ee0d7703222c86c00959869b99f7851
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Effectuer une surveillance avancée à l’aide de la solution de surveillance à distance

Ce didacticiel montre les fonctionnalités du tableau de bord de la solution de surveillance à distance. Pour présenter ces fonctionnalités, le didacticiel utilise un scénario dans l’application Contoso IoT.

Dans ce didacticiel, vous utilisez deux appareils Contoso simulés pour apprendre à surveiller vos appareils à partir du tableau de bord des solutions préconfigurées. En tant qu’opérateur de Contoso, vous devez surveiller la position et le comportement de vos camions sur la route.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Filtrage des appareils dans le tableau de bord
> * Affichage des données de télémétrie en temps réel
> * Affichage des détails sur l’appareil
> * Affichage des alarmes à partir de vos appareils
> * Affichage des KPI du système

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez terminer le didacticiel [Déployer la solution de surveillance à distance préconfigurée](iot-suite-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Choisir les appareils à afficher

Pour sélectionner les appareils à afficher sur la page **Tableau de bord**, utilisez des filtres. Pour afficher uniquement les appareils de type **Truck** (camion), choisissez le filtre prédéfini **Trucks** (Camions) dans la liste déroulante :

![Filtrer les camions sur le tableau de bord](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Lorsque vous appliquez un filtre, seuls les appareils qui respectent les conditions de filtre s’affichent sur la carte de la page **Tableau de bord** :

![Affichage de camions sur la carte](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

Le filtre détermine également les appareils que vous voyez dans le graphique de **télémétrie** :

![Affichage des données de télémétrie des camions sur le tableau de bord](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Pour créer, modifier et supprimer des filtres, choisissez **Gérer les filtres**.

## <a name="view-real-time-telemetry"></a>Affichage des données de télémétrie en temps réel

La solution préconfigurée trace les données de télémétrie en temps réel détaillées dans le graphique de la page **Tableau de bord**. Le graphique de télémétrie affiche des informations de télémétrie pour les appareils sélectionnés par le filtre actif :

![Tracé de télémétrie des camions](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Pour sélectionner les valeurs de télémétrie à afficher, choisissez le type de données de télémétrie en haut du graphique :

![Tracé de télémétrie des camions](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

Pour suspendre l’affichage dynamique de la télémétrie, choisissez l’option de circulation **Flowing**. Pour réactiver l’affichage dynamique, choisissez **Pause** :

![Suspendre et redémarrer l’affichage des données de télémétrie](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)

## <a name="use-the-map"></a>Utiliser la carte

La carte affiche des informations sur les camions simulés sélectionnés par le filtre actif. Vous pouvez effectuer un zoom avant et un panoramique sur la carte pour afficher les positions avec plus ou moins de détails. Les icônes d’appareil sur la carte indiquent les **alarmes** ou **avertissements** qui sont actifs pour l’appareil. Un résumé du nombre d’**alarmes** et d’**avertissements** est affiché à gauche de la carte.

Pour afficher les détails des appareils, effectuez un panoramique et un zoom sur la carte pour localiser les appareils, puis cliquez sur l’appareil souhaité sur la carte. Les détails sont les suivants :

* Valeurs de données de télémétrie récentes
* Méthodes prises en charge par l’appareil
* Propriétés de l’appareil

![Afficher les détails des appareils dans le tableau de bord](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)

## <a name="view-alarms-from-your-devices"></a>Affichage des alarmes à partir de vos appareils

La carte met en évidence les appareils du filtre actif associés à des **alarmes** et **avertissements**. Le panneau rassemblant les **alarmes du système** affiche des informations détaillées sur les alarmes les plus récentes de vos appareils :

![Affichage des alarmes de système sur le tableau de bord](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Vous pouvez utiliser le filtre des **alarmes du système** pour ajuster la période des alarmes récentes. Par défaut, le panneau affiche les alarmes de la dernière heure écoulée :

![Filtrer les alarmes par période](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Affichage des KPI du système

La page **Tableau de bord** affiche les KPI du système :

![Filtrer les alarmes par période](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Vous pouvez utiliser le filtre des **KPI du système** pour ajuster la période d’agrégation des KPI. Par défaut, le panneau affiche les KPI agrégés pendant la dernière heure écoulée.

## <a name="next-steps"></a>étapes suivantes

Ce didacticiel vous a montré comment utiliser la page **Tableau de bord** pour filtrer et surveiller les camions simulés approvisionnés dans votre solution de surveillance à distance :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrage des appareils dans le tableau de bord
> * Affichage des données de télémétrie en temps réel
> * Affichage des détails sur l’appareil
> * Affichage des alarmes à partir de vos appareils
> * Affichage des KPI du système

La surveillance de vos appareils n’ayant plus de secrets pour vous, nous vous suggérons de découvrir les opérations suivantes :

* [Détecter les problèmes à l’aide de règles de seuil](./iot-suite-remote-monitoring-automate.md)
* [Gérer et configurer vos appareils](./iot-suite-remote-monitoring-manage.md)
* [Dépanner et corriger les problèmes d’un appareil](./iot-suite-remote-monitoring-maintain.md)
* [Tester votre solution avec des appareils simulés](iot-suite-remote-monitoring-test.md)

<!-- Next tutorials in the sequence -->