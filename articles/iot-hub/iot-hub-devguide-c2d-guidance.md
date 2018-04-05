---
title: Options cloud vers appareil Azure IoT Hub | Microsoft Docs
description: 'Guide du développeur : conseils pour l’utilisation de méthodes directes, les propriétés souhaitées des jumeaux d’appareil ou les messages cloud-à-appareil pour les communications cloud à appareil.'
services: iot-hub
documentationcenter: ''
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: 144bd8e0a954e54cf17fb88105759d0e000454fb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="cloud-to-device-communications-guidance"></a>Conseils pour les communications cloud-à-appareil
IoT Hub propose trois options pour les applications pour appareil afin d’exposer les fonctionnalités sur un appareil principal :

* [Méthodes directes][lnk-methods], pour les communications qui nécessitent une confirmation immédiate du résultat. Les méthodes directes sont souvent utilisées pour un contrôle interactif d’appareil, tel que la mise en marche d’un ventilateur.
* [Propriétés souhaitées des représentations][lnk-twins], pour les commandes de longue durée pour placer l’appareil dans l’état désiré. Par exemple, définissez l’intervalle d’envoi de la télémétrie sur 30 minutes.
* [Messages Cloud vers appareil][lnk-c2d], pour les notifications unidirectionnelles à l’application pour périphériques.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Voici une comparaison détaillée des différentes options de communication cloud-à-appareil.

|  | Méthodes directes | Propriétés souhaitées des représentations | Messages Cloud vers appareil |
| ---- | ------- | ---------- | ---- |
| Scénario | Commandes qui nécessitent une confirmation immédiate, par exemple, activer un ventilateur. | Commandes de longue durée pour placer l’appareil dans l’état désiré. Par exemple, définissez l’intervalle d’envoi de la télémétrie sur 30 minutes. | Notifications unidirectionnelles à l’application pour appareil. |
| Flux de données | Bidirectionnel. L’application pour appareil peut répondre immédiatement à la méthode. Le dispositif principal reçoit les résultats en fonction du contexte de la demande. | Unidirectionnel. L’application pour appareil reçoit une notification avec la modification de propriété. | Unidirectionnel. L’application pour appareil reçoit le message
| Durabilité | Les appareils déconnectés ne sont pas contactés. Le serveur principal de la solution est averti que l’appareil n’est pas connecté. | Les valeurs de propriété sont conservées dans la représentation d’appareil. L’appareil les lira lors de la reconnexion suivante. Les valeurs de propriété sont récupérables avec le [langage de requête d’IoT Hub][lnk-query]. | Les messages peuvent être conservés par IoT Hub jusqu’à 48 heures. |
| Cibles | Appareil unique utilisant le paramètre **deviceId** ou appareils multiples utilisant le paramètre [jobs][lnk-jobs]. | Appareil unique utilisant le paramètre **deviceId** ou appareils multiples utilisant le paramètre [jobs][lnk-jobs]. | Appareil unique par **deviceId**. |
| Taille | Demandes et réponses jusqu’à 8 Ko. | La taille maximale des propriétés souhaitée est de 8 Ko. | Messages jusqu’à 64 Ko. |
| Fréquence | Élevée. Pour plus d’informations, consultez les [limites d’IoT Hub][lnk-quotas]. | Moyenne. Pour plus d’informations, consultez les [limites d’IoT Hub][lnk-quotas]. | Faible. Pour plus d’informations, consultez les [limites d’IoT Hub][lnk-quotas]. |
| Protocole | Disponible à l’aide du protocole MQTT ou AMQP. | Disponible à l’aide du protocole MQTT ou AMQP. | Disponible sur tous les protocoles. L’appareil doit interroger lors de l’utilisation de HTTPS. |

Découvrez comment utiliser les méthodes directes, les propriétés souhaitées et les messages cloud-à-appareil grâce aux didacticiels suivants :

* [Utiliser les méthodes directes][lnk-methods-tutorial], pour les méthodes directes ;
* [Utiliser des propriétés souhaitées pour configurer des appareils][lnk-twin-properties], pour les propriétés souhaitées des jumeaux d’appareil ; 
* [Envoyer des messages cloud-à-appareil][lnk-c2d-tutorial], pour les messages cloud-à-appareil.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
