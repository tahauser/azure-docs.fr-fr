---
title: "Présentation des points de terminaison Azure IoT Hub personnalisés | Microsoft Docs"
description: "Guide du développeur : utilisation de règles de routage pour router les messages appareil-à-cloud vers des points de terminaison."
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
ms.openlocfilehash: a40fa94260b488e9c01ac09b22da8c0677d73968
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Utiliser des itinéraires de messages et des points de terminaison personnalisés pour les messages appareil-à-cloud

IoT Hub vous permet d’acheminer les [messages appareil-à-cloud][lnk-device-to-cloud] vers des points de terminaison exposés au service IoT Hub en fonction de leurs propriétés. Les règles de routage vous offrent la souplesse nécessaire pour envoyer des messages au bon endroit sans avoir besoin de services supplémentaires ou de code personnalisé. Chaque règle d’acheminement que vous configurez a les propriétés suivantes :

| Propriété      | Description |
| ------------- | ----------- |
| **Name**      | Nom unique qui identifie la règle. |
| **Source**    | Origine du flux de données qui fait l’objet du traitement. Par exemple, télémétrie des appareils. |
| **Condition** | Expression de requête de la règle de routage exécutée sur les en-têtes et le corps du message. Elle détermine s’il existe une correspondance pour le point de terminaison. Pour plus d’informations sur la construction d’une condition d’acheminement, consultez la [Référence : langage de requête pour jumeaux d’appareils et travaux][lnk-devguide-query-language]. |
| **Point de terminaison**  | Nom du point de terminaison auquel IoT Hub envoie des messages qui correspondent à la condition. Les points de terminaison doivent être dans la même région que l’IoT Hub, sinon des écritures inter-régions peuvent vous être facturées. |

Un seul message peut correspondre à la condition de plusieurs règles d’acheminement, auquel cas IoT Hub remet le message au point de terminaison associé à chaque règle ayant affiché une correspondance. Par ailleurs, IoT Hub déduplique automatiquement la remise des messages. Ainsi, si un message correspond à plusieurs règles ayant la même destination, il n’est écrit qu’une seule fois dans cette destination.

Un hub IoT a par défaut un [point de terminaison intégré][lnk-built-in]. Vous pouvez créer des points de terminaison personnalisés pour y acheminer les messages en liant d’autres services de votre abonnement au hub. IoT Hub prend actuellement en charge les conteneurs de stockage Azure, les points de terminaison personnalisés Event Hubs, les files d’attente Service Bus et les rubriques Service Bus.

Lorsque vous utilisez des points de terminaison de routage et personnalisés, les messages sont uniquement remis au point de terminaison intégré s’ils ne correspondent à aucune règle. Pour remettre des messages au point de terminaison intégré ainsi qu’à un point de terminaison personnalisé, ajoutez un itinéraire qui envoie des messages au point de terminaison des **événements**.

> [!NOTE]
> IoT Hub prend uniquement en charge l’écriture de données dans des conteneurs de stockage Azure en tant qu’objets blob.

> [!WARNING]
> Les files d’attente et rubriques Service Bus dont les options **Sessions** ou **Détection des doublons** sont activées ne sont pas prises en charge comme points de terminaison personnalisés.

Pour plus d’informations sur la création de points de terminaison personnalisés dans IoT Hub, consultez la page [Points de terminaison IoT Hub][lnk-devguide-endpoints].

Pour plus d’informations sur la lecture à partir de points de terminaison personnalisés, consultez :

* Lecture à partir des [conteneurs de stockage Azure][lnk-getstarted-storage].
* Lecture à partir [d’Event Hubs][lnk-getstarted-eh].
* Lecture à partir de [files d’attente Service Bus][lnk-getstarted-queue].
* Lecture à partir de [rubriques Service Bus][lnk-getstarted-topic].

### <a name="next-steps"></a>étapes suivantes

Pour plus d’informations sur les points de terminaison IoT Hub, consultez [Points de terminaison IoT Hub][lnk-devguide-endpoints].

Pour plus d’informations sur le langage de requête que vous utilisez pour définir des règles de routage, consultez [Langage de requête d’IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-devguide-query-language].

Le didacticiel [Traiter les messages appareil-à-cloud IoT Hub en utilisant les itinéraires][lnk-d2c-tutorial] vous montre comment utiliser des règles de routage et des points de terminaison personnalisés.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
