---
title: "Présentation des messages Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur - Messagerie d’appareil-à-cloud et de cloud-à-appareil avec IoT Hub. Comprend des informations sur les formats de message et les protocoles de communication pris en charge."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 7fd89bebf9d7497ad5b13c438b362256d3408219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Messagerie d’appareil-à-cloud et de cloud-à-appareil avec IoT Hub

Utilisez la messagerie IoT Hub pour communiquer avec vos appareils en :

* envoyant des messages [appareil-à-cloud][lnk-d2c] depuis vos appareils vers le back-end de votre solution ;
* envoyant des messages [cloud-à-appareil][lnk-c2d] depuis le back-end de la solution vers vos appareils.

Les principales propriétés de la fonctionnalité de messagerie IoT Hub sont la fiabilité et la durabilité des messages. Ces propriétés activent la résilience de la connectivité intermittente côté appareils et des pics de chargement dans le traitement d’événements côté cloud. IoT Hub implémente *au moins une fois* des garanties de remise pour l’envoi de messages appareil-à-cloud et cloud-à-appareil.

Pour obtenir une présentation des fonctionnalités IoT Hub, consultez les articles [Azure et IoT][lnk-azure-iot] et [Présentation du service Azure IoT Hub][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Quand utiliser la messagerie IoT Hub

Utilisez les messages appareil-à-cloud pour envoyer des alertes et des données de télémétrie de série chronologique à partir de votre application pour appareil, et des messages cloud-à-appareil pour envoyer des notifications unidirectionnelles à votre application pour appareil.

* Reportez-vous à [l’aide sur la communication appareil-à-cloud][lnk-d2c-guidance] en cas de doute entre l’utilisation des messages appareil-à-cloud, des propriétés signalées ou du chargement de fichiers.
* Reportez-vous à [l’aide sur la communication cloud-à-appareil][lnk-c2d-guidance] en cas de doute entre l’utilisation des messages cloud-à-appareil, des propriétés de votre choix ou des méthodes directes.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez la [messagerie appareil-à-cloud][lnk-d2c] IoT Hub.
* Découvrez la [messagerie cloud-à-appareil][lnk-c2d] IoT Hub.

[lnk-azure-iot]: iot-hub-what-is-azure-iot.md
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md