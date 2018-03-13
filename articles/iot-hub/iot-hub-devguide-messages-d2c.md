---
title: "Présentation de la messagerie d’appareil-à-cloud Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur : comment utiliser la messagerie d’appareil-à-cloud avec IoT Hub. Inclut des informations sur l’envoi de données de télémétrie et autres, ainsi que sur l’utilisation du routage pour remettre les messages."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 48b904818c80b9175d45b88345634f11cf4a4812
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Envoyer des messages appareil-à-cloud sur IoT Hub

Pour envoyer des alertes et des données de télémétrie de série chronologique à partir de vos appareils vers le back-end de votre solution, envoyez des messages appareil-à-cloud depuis votre appareil vers votre hub IoT. Pour une description des autres options appareil-à-cloud prises en charge par IoT Hub, consultez [Recommandations sur les communications appareil-à-cloud][lnk-d2c-guidance].

Vous envoyez des messages appareil-à-cloud via un point de terminaison côté appareil (**/devices/{deviceId}/messages/events**). Les règles d’acheminement acheminent ensuite vos messages vers l’un des points de terminaison côté service sur votre IoT Hub. Les règles de routage utilisent les en-têtes et corps des messages appareil-à-cloud pour déterminer où les acheminer. Par défaut, les messages sont acheminés vers le point de terminaison côté service intégré (**messages/events**) compatible avec [Event Hubs][lnk-event-hubs]. Ainsi, vous pouvez utiliser [l’intégration et les SDK standard Event Hubs][lnk-compatible-endpoint] pour recevoir des messages appareil-à-cloud dans le back-end de votre solution.

IoT Hub implémente les messages appareil-à-cloud à l’aide d’un modèle de messagerie en streaming. Les messages appareil-à-cloud d’IoT Hub s’apparentent plus à des *événements* [Event Hubs][lnk-event-hubs] qu’à des *messages* [Service Bus][lnk-servicebus] dans la mesure où de nombreux événements transmis par le service peuvent être lus par plusieurs lecteurs.

La messagerie appareil-à-cloud avec IoT Hub présente les caractéristiques suivantes :

* Les messages appareil-à-cloud sont durables et sont conservés dans le point de terminaison **messages/events** par défaut d’un hub IoT jusqu’à sept jours.
* Les messages appareil-à-cloud ne doivent pas dépasser 256 Ko et peuvent être groupés en lots pour optimiser les envois. Les lots ne peuvent pas dépasser 256 Ko.
* Comme l’explique la section [Contrôler l’accès à IoT Hub][lnk-devguide-security], IoT Hub permet l’authentification et le contrôle d’accès par appareil.
* IoT Hub vous permet de créer jusqu'à 10 points de terminaison personnalisés. Les messages sont remis aux points de terminaison selon les itinéraires configurés sur votre IoT Hub. Pour plus d’informations, consultez [Règles de routage](#routing-rules).
* IoT Hub autorise des millions d’appareils connectés simultanément (voir [Quotas et la limitation][lnk-quotas]).
* IoT Hub n’autorise pas le partitionnement arbitraire. Les messages appareil-à-cloud sont partitionnés selon leur **deviceId**d’origine.

Pour plus d’informations sur les différences entre IoT Hub et Event Hubs, consultez [Comparaison entre Azure IoT Hub et Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Envoyer du trafic autre que la télémétrie

Souvent, en plus des données de télémétrie, les appareils envoient des messages et des requêtes qui nécessitent une exécution et une gestion séparées dans le backend de la solution. Par exemple, les alertes critiques qui doivent déclencher une action spécifique dans le backend. Vous pouvez écrire une [règle de routage][lnk-devguide-custom] qui envoie ces types de messages à un point de terminaison dédié à leur traitement en fonction d’un en-tête sur le message ou d’une valeur dans le corps de message.

Pour plus d’informations sur la meilleure façon de traiter ce genre de messages, consultez [Didacticiel : traiter les messages appareil-à-cloud IoT Hub][lnk-d2c-tutorial].

## <a name="route-device-to-cloud-messages"></a>Acheminer des messages appareil-à-cloud

Vous avez deux options pour acheminer des messages appareil-à-cloud vers vos applications principales :

* Utiliser le [Point de terminaison compatible avec Event Hub][lnk-compatible-endpoint] prédéfini pour permettre aux applications principales de lire les messages appareil-à-cloud que le hub reçoit. Pour obtenir des informations sur le point de terminaison compatible avec Event Hub prédéfini, consultez [Lire des messages appareil-à-cloud à partir du point de terminaison intégré][lnk-devguide-builtin].
* Utiliser des règles de routage pour envoyer des messages à des points de terminaison personnalisés dans votre hub IoT. Les points de terminaison personnalisés permettent à vos applications principales de lire les messages appareil-à-cloud à l’aide d’Event Hubs, de files d’attente Service Bus ou de rubriques Service Bus. Pour en savoir plus sur les points de terminaison de routage et personnalisés, consultez [Utiliser des points de terminaison et des règles de routage personnalisés pour les messages appareil-à-cloud][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Propriétés de détection d’usurpation d’identité

Pour éviter l’usurpation d’appareil dans les messages appareil-à-cloud, IoT Hub marque tous les messages avec les propriétés suivantes :

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Les deux premières propriétés contiennent le **deviceId** et le **generationId** de l’appareil d’origine, conformément aux [Propriétés d’identité des appareils][lnk-device-properties].

La propriété **ConnectionAuthMethod** contient un objet sérialisé JSON avec les propriétés suivantes :

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les SDK que vous pouvez utiliser pour envoyer des messages appareil-à-cloud, consultez [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks].

Les didacticiels de [prise en main][lnk-get-started] vous montrent comment envoyer des messages appareil-à-cloud à partir d’appareils physiques et d’appareils simulés. Pour plus de détails, consultez le didacticiel [Traiter les messages appareil-à-cloud IoT Hub en utilisant les itinéraires][lnk-d2c-tutorial].

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
