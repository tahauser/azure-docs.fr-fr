---
title: "Utiliser des métriques pour surveiller Azure IoT Hub | Microsoft Docs"
description: "Guide d’utilisation des métriques Azure IoT Hub pour évaluer et surveiller l’intégrité globale de votre IoT Hub."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cafe5009b8e96b147b5bbed1957024f6d96feb58
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="understand-iot-hub-metrics"></a>Comprendre les métriques IoT Hub
Les métriques IoT Hub vous offrent de meilleures données sur l’état des ressources Azure IoT de votre abonnement Azure. Grâce aux métriques IoT Hub, vous pouvez évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les statistiques accessibles à l’utilisateur sont importantes, car elles vous permettent d’effectuer le suivi de votre hub IoT et de connaître les causes des problèmes sans contacter le support Azure.

Les métriques sont activées par défaut. Vous pouvez afficher les métriques IoT Hub à partir du portail Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Affichage des métriques IoT Hub
1. Créez un hub IoT. Pour savoir comment créer un hub IoT, consultez le guide [Prise en main][lnk-get-started].
2. Ouvrez le panneau de votre hub IoT. Ici, cliquez sur **Métriques**.
   
    ![][1]
3. Dans le panneau de métriques, vous pouvez afficher les métriques IoT Hub et créer des vues personnalisées de ces dernières. Vous pouvez choisir d’envoyer vos données métriques vers un point de terminaison Event Hubs ou un compte de stockage Azure en cliquant sur **Paramètres de diagnostic**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métriques IoT Hub et instructions d’utilisation
IoT Hub fournit plusieurs métriques afin de vous donner une vue d’ensemble de l’intégrité de votre hub et du nombre total d’appareils connectés. Vous pouvez combiner les informations de plusieurs métriques pour obtenir une image plus grande de l’état du hub IoT. Le tableau suivant décrit les métriques dont chaque hub IoT effectue le suivi et la relation de chacune avec l’état global du hub IoT.

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Nombre|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Nombre|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|
|c2d.commands.egress.complete.success|Commandes terminées|Nombre|Total|Nombre de commandes cloud vers appareil terminées avec succès par l’appareil|
|c2d.commands.egress.abandon.success|Commandes abandonnées|Nombre|Total|Nombre de commandes cloud vers appareil abandonnées par l’appareil|
|c2d.commands.egress.reject.success|Commandes rejetées|Nombre|Total|Nombre de commandes cloud vers appareil rejetées par l’appareil|
|devices.totalDevices|Nombre total d’appareils|Nombre|Total|Nombre d’appareils enregistrés sur votre hub IoT|
|devices.connectedDevices.allProtocol|Appareils connectés|Nombre|Total|Nombre d’appareils connectés à votre hub IoT|
|d2c.telemetry.egress.success|Messages de télémétrie remis|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison (total)|
|d2c.telemetry.egress.dropped|Messages supprimés|Nombre|Total|Nombre de messages supprimés parce qu’ils ne correspondaient pas aux itinéraires et que l’itinéraire de secours était désactivé|
|d2c.telemetry.egress.orphaned|Messages orphelins|Nombre|Total|Nombre de messages ne correspondant à aucun itinéraire, itinéraire de secours compris|
|d2c.telemetry.egress.invalid|Messages non valides|Nombre|Total|Nombre de messages non remis en raison d’une incompatibilité avec le point de terminaison|
|d2c.telemetry.egress.fallback|Messages correspondant à une condition de secours|Nombre|Total|Nombre de messages écrits au point de terminaison de secours|
|d2c.endpoints.egress.eventHubs|Messages remis aux points de terminaison Event Hub|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison Event Hub|
|d2c.endpoints.latency.eventHubs|Latence des messages des points de terminaison Event Hub|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison Event Hub, en millisecondes|
|d2c.endpoints.egress.serviceBusQueues|Messages remis aux points de terminaison de file d’attente Service Bus|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de file d’attente Service Bus|
|d2c.endpoints.latency.serviceBusQueues|Latence des messages des points de terminaison de files d’attente Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de file d’attente Service Bus, en millisecondes|
|d2c.endpoints.egress.serviceBusTopics|Messages remis aux points de terminaison de rubrique Service Bus|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de rubrique Service Bus|
|d2c.endpoints.latency.serviceBusTopics|Latence des messages des points de terminaison de rubriques Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de rubrique Service Bus, en millisecondes|
|d2c.endpoints.egress.builtIn.events|Messages remis au point de terminaison intégré (messages/événements)|Nombre|Total|Nombre de fois où des messages ont été écrits au point de terminaison intégré (messages/événements)|
|d2c.endpoints.latency.builtIn.events|Latence de message pour le point de terminaison intégré (messages/événements)|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison prédéfini (messages/événements), en millisecondes |
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Nombre|Total|Total des lectures de représentations réussies initiées par un appareil.|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Nombre|Total|Total des lectures de représentations en échec initiées par un appareil.|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Nombre|Total|Total des mises à jour de représentations réussies initiées par un appareil.|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Nombre|Total|Total des mises à jour de représentations en échec initiées par un appareil.|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|
|c2d.methods.success|Appels de méthode directe réussis|Nombre|Total|Total des appels de méthode directe réussis.|
|c2d.methods.failure|Appels de méthode directe en échec|Nombre|Total|Total des appels de méthode directe en échec.|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Nombre|Total|Total des lectures de représentations réussies initiées par un serveur principal.|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Nombre|Total|Total des lectures de représentations en échec initiées par un serveur principal.|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Nombre|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Nombre|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|
|twinQueries.success|Requêtes de représentations réussies|Nombre|Total|Total des requêtes de représentations réussies.|
|twinQueries.failure|Requêtes de représentations en échec|Nombre|Total|Total des requêtes de représentations en échec.|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Moyenne|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Nombre|Total|Total des créations réussies de travaux de mises à jour de représentations.|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Nombre|Total|Total des créations en échec des travaux de mises à jour de représentations.|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Nombre|Total|Total des créations réussies des travaux d’appel de méthode directe.|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Nombre|Total|Total des créations en échec des travaux d’appel de méthode directe.|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Nombre|Total|Total des appels réussis pour répertorier les travaux.|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Nombre|Total|Total des appels en échec pour répertorier les travaux.|
|jobs.cancelJob.success|Annulations de travaux réussies|Nombre|Total|Total des appels réussis pour annuler un travail.|
|jobs.cancelJob.failure|Annulations de travaux en échec|Nombre|Total|Total des appels en échec pour annuler un travail.|
|jobs.queryJobs.success|Requêtes de travaux réussies|Nombre|Total|Total des appels réussis pour interroger les travaux.|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Nombre|Total|Total des appels en échec pour interroger les travaux.|
|jobs.completed|Travaux terminés|Nombre|Total|Total des travaux terminés.|
|jobs.failed|Travaux en échec|Nombre|Total|Total des travaux en échec.|

## <a name="next-steps"></a>Étapes suivantes
Les métriques IoT Hub n’étant plus un secret pour vous, suivez le lien ci-après pour en savoir plus sur la gestion d’Azure IoT Hub :

* [Surveillance des opérations][lnk-monitor]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
