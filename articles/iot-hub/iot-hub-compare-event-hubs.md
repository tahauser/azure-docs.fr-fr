---
title: Comparaison entre Azure IoT Hub et Azure Event Hub | Microsoft Docs
description: "Comparaison des services Azure IoT Hub et Azure Event Hubs mettant en avant les différences de fonction et des exemples d’utilisation. La comparaison inclut les protocoles pris en charge, la surveillance, la gestion des appareils et les téléchargements de fichiers."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: b515e05d16dda83c7d865113d5d3578c44be084f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparaison entre Azure IoT Hub et Azure Event Hub
L’une des principales utilisations IoT Hub consiste à rassembler des mesures de télémétrie relevées par des appareils. Pour cette raison, IoT Hub est souvent comparé à [Azure Event Hubs][Azure Event Hubs]. Tout comme IoT Hub, les hubs d’événements constituent un service de traitement des événements. Celui-ci fournit des entrées d’événements et de télémétrie vers le cloud à grande échelle, avec une faible latence et une grande fiabilité.

Toutefois, les services présentent de nombreuses différences répertoriées dans le tableau suivant :

| Domaine | IoT Hub | Event Hubs |
| --- | --- | --- |
| Modèles de communication | Active [device-to-cloud communications][lnk-d2c-guidance] (messagerie, chargements de fichiers et propriétés signalées) et [cloud-to-device communications][lnk-c2d-guidance] (méthodes directes, propriétés désirées). |Permet uniquement une entrée d’événement (en général utilisé pour les scénarios appareil-à-cloud). |
| Informations d’état de l’appareil | Des [jumeaux d’appareils][lnk-twins] peuvent stocker et interroger les informations d’état de l’appareil. | Aucune information d’état de l’appareil ne peut être stockée. |
| Prise en charge du protocole d’appareil |Prend en charge MQTT, MQTT sur WebSockets, AMQP, AMQP sur WebSocket et HTTPS. En outre, IoT Hub fonctionne avec la [passerelle de protocole Azure IoT][lnk-azure-protocol-gateway], une implémentation de passerelle de protocole personnalisable pour prendre en charge les protocoles personnalisés. |Prend en charge AMQP, AMQP sur WebSocket et HTTPS. |
| Sécurité |Fournit une identité par appareil et le contrôle d’accès révocable. Consultez la [section Sécurité du guide du développeur IoT Hub]. |Fournit des [stratégies d’accès partagé][Event Hubs - security] à l’échelle d’Event Hubs, avec une prise en charge de révocation limitée par le biais de [stratégies de l’éditeur][Event Hubs publisher policies]. Les solutions IoT sont souvent nécessaires pour implémenter une solution personnalisée afin de prendre en charge les informations d’identification par appareil et les mesures de lutte contre l’usurpation d’identité. |
| Surveillance des opérations |Permet aux solutions IoT de s’abonner à un vaste ensemble d’événements de gestion d’identité d’appareil et de connectivité tels que des erreurs d’authentification de chaque appareil, la limitation et les exceptions de format incorrect. Ces événements permettent d’identifier rapidement les problèmes de connectivité au niveau de chaque appareil. |Expose uniquement les mesures d’agrégation. |
| Scale |est optimisé pour prendre en charge des millions d’appareils connectés simultanément. |Limite les connexions tel que défini dans [Quotas Event Hubs][Azure Event Hubs quotas]. En revanche, Event Hubs vous permet de spécifier une partition pour chaque message envoyé. |
| Kits de développement logiciel (SDK) d’appareil |fournissent des [appareils SDK][Azure IoT SDKs] pour une grande variété de plateformes et de langages, ainsi que MQTT direct, AMQP et les API HTTPS. |sont pris en charge sur .NET, Java et C en plus des interfaces d’envoi HTTPS et AMQP. |
| Chargement de fichiers |Permet à des solutions IoT de charger des fichiers à partir d’appareils vers le cloud. Comprend un point de terminaison de notification de fichier pour l’intégration du workflow et une catégorie de surveillance des opérations pour le débogage de la prise en charge. | Non pris en charge. |
| Router des messages vers plusieurs points de terminaison | Un maximum de 10 points de terminaison personnalisés sont pris en charge. Les règles déterminent la façon dont les messages sont routés vers les points de terminaison personnalisés. Pour plus d’informations, consultez [Envoyer et recevoir des messages avec IoT Hub][lnk-devguide-messaging]. | Nécessite l’écriture et l’hébergement de code supplémentaire pour la distribution des messages. |

Pour résumer, même si le seul cas d’utilisation est l’entrée de mesures de télémétrie appareil-à-cloud, IoT Hub offre un service conçu pour la connectivité d’appareil IoT. Les propositions de valeur pour ces scénarios continuent à se développer avec des fonctionnalités spécifiques à IoT. Event Hubs est conçu pour les entrées d’événements à grande échelle, dans les contextes inter et intra centres de données.

Il n’est pas rare d’utiliser IoT Hub et Event Hubs dans la même solution. IoT Hub gère les communications appareil-à-cloud et Event Hubs assure l’entrée des événements dans des moteurs de traitement en temps réel dans les étapes ultérieures.

### <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la planification de votre déploiement IoT Hub, consultez [Mise à l’échelle, HA et DR][lnk-scaling].

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[section Sécurité du guide du développeur IoT Hub]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
