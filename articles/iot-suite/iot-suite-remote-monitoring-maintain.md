---
title: "Dépanner des appareils dans la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Ce didacticiel vous montre comment dépanner et corriger les problèmes d’appareils dans la solution de surveillance à distance."
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
ms.openlocfilehash: dd01246075a5c0db0ed49133ed51fb56d8fcf8e5
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Dépanner et corriger les problèmes d’un appareil

Ce didacticiel vous montre comment utiliser la page **Maintenance** dans la solution pour dépanner et corriger les problèmes d’un appareil. Pour présenter ces fonctionnalités, le didacticiel utilise un scénario dans l’application Contoso IoT.

Contoso fait le test d’un nouvel appareil **Prototype** sur le terrain. En tant qu’opérateur de Contoso, vous remarquerez pendant le test que l’appareil **Prototype** déclenche de façon inattendue une alerte de température dans le tableau de bord. Vous devez commencer à étudier le comportement de cet appareil **Prototype** défectueux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Utiliser la page **Maintenance** pour examiner l’alarme
> * Appeler une méthode d’appareil pour résoudre le problème

## <a name="prerequisites"></a>configuration requise

Pour suivre ce didacticiel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez terminer le didacticiel [Déployer la solution de surveillance à distance préconfigurée](iot-suite-remote-monitoring-deploy.md).

## <a name="use-the-maintenance-dashboard"></a>Utiliser le tableau de bord de maintenance

Sur la page **Tableau de bord**, vous remarquez des alarmes de température inattendue engendrées par la règle associée aux appareils **Prototype** :

![Affichage des alarmes sur le tableau de bord](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

Pour examiner le problème, choisissez l’option **Explorer l’alarme** disponible à côté de celle-ci :

![Explorer l’alarme à partir du tableau de bord](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

Des informations détaillées sur l’alerte s’affichent :

* Lorsque l’alarme a été déclenchée
* Informations d’état sur les appareils associés à l’alarme
* Données de télémétrie des appareils associés à l’alarme

![Détails de l’alarme](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

Pour accuser réception de l’alarme, sélectionnez les **Occurrences de l’alarme** et choisissez **Accuser réception**. Cette action permet de montrer aux autres opérateurs que vous avez vu l’alarme et que vous travaillez dessus.

![Accuser réception des alarmes](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

Lorsque vous accusez réception de l’alarme, l’état de l’occurrence devient **Réception confirmée**.

Dans la liste, vous pouvez voir l’appareil **Prototype** chargé de déclencher l’alarme de température :

![Liste des périphériques à l’origine de l’alarme](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Résoudre le problème

Pour résoudre le problème avec l’appareil **Prototype**, vous devez appeler la méthode **DecreaseTemperature** sur l’appareil.

Pour agir sur un appareil, sélectionnez-le dans la liste des appareils, puis choisissez **Planifier**. Le modèle d’appareil **Prototype** définit quatre méthodes que doivent prendre en charge les appareils :

![Afficher les méthodes prises en charge par l’appareil](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

Choisissez **DecreaseTemperature** et choisissez **DecreaseTemperature** comme le nom du travail. Choisissez ensuite **Appliquer** :

![Créer le travail pour réduire la température](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

Pour suivre l’état du travail sur la page **Maintenance**, sélectionnez **Travaux**. Utilisez la vue **Travaux** pour effectuer le suivi de tous les travaux et appels de méthodes de la solution :

![Surveiller le travail pour réduire la température](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

Pour afficher les détails d’un travail ou d’un appel de méthode en particulier, sélectionnez-le dans la liste au sein de la vue **Travaux** :

![Affichage des détails d’une tâche](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Utiliser la page **Maintenance** pour examiner l’alarme
> * Appeler une méthode d’appareil pour résoudre le problème

Vous avez maintenant appris à gérer les problèmes d’appareil, l’étape suivante suggérée consiste à apprendre comment [Tester votre solution avec des appareils simulés](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->