---
title: "Mise à l’échelle Azure IoT Hub | Microsoft Docs"
description: "Mise à l’échelle de votre IoT hub pour prendre en charge votre débit de messages prévu. Inclut un résumé du débit pris en charge pour chaque niveau et des options pour le partitionnement."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00043293eb57768f0117e912bb67f02d088934f3
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="scale-your-iot-hub-solution"></a>Mettre à l’échelle votre solution IoT Hub
Azure IoT Hub peut prendre en charge jusqu’à un million d’appareils connectés simultanément. Pour plus d’informations, consultez la [tarification IoT Hub][lnk-pricing]. Chaque unité IoT Hub autorise un certain nombre de messages quotidiens.

Pour mettre correctement à l’échelle votre solution, étudiez votre utilisation particulière d’IoT Hub. Prenez plus particulièrement en compte le débit maximal requis pour les catégories d’opérations suivantes :

* Messages Appareil vers cloud
* Messages Cloud vers appareil
* Opérations du registre d’identité

Outre ces informations sur le débit, consultez les [quotas et limitations IoT Hub][IoT Hub quotas and throttles] et concevez votre solution en conséquence.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Débit de message Appareil vers cloud et Cloud vers appareil.
La meilleure façon de dimensionner une solution IoT Hub consiste à évaluer le trafic sur une base par unité.

Les messages Appareil vers cloud respectent les recommandations de débit soutenu.

| Niveau | Débit soutenu | Vitesse de transmission soutenue |
| --- | --- | --- |
| S1 |Jusqu’à 1 111 Ko/minute par unité<br/>(1,5 Go/jour/unité) |Moyenne de 278 messages/minute par unité<br/>(400 000 messages/jour par unité) |
| S2 |Jusqu’à 16 Mo/minute par unité<br/>(22,8 Go/jour/unité) |Moyenne de 4167 messages/minute par unité<br/>(6 millions de messages/jour par unité) |
| S3 |Jusqu’à 814 Mo/minute par unité<br/>(1144,4 Go/jour/unité) |Moyenne de 208 333 messages/minute par unité<br/>(300 millions de messages/jour par unité) |

## <a name="identity-registry-operation-throughput"></a>Débit des opérations de registre d’identité
Les opérations de registre d’identité IoT Hub ne sont pas censées être des opérations d’exécution, car elles sont principalement liées à l’approvisionnement d’appareils.

Pour obtenir les pics de performances spécifiques, consultez [Quotas et limitations IoT Hub][IoT Hub quotas and throttles].

## <a name="sharding"></a>Partitionnement
Un hub IoT unique peut recevoir des millions d’appareils, mais votre solution nécessite parfois des caractéristiques de performances spécifiques qu’un seul hub IoT ne peut pas assurer. Dans ce cas, il est recommandé de partitionner vos appareils en plusieurs IoT hubs. L’utilisation de plusieurs IoT Hubs permet de simplifier les pics de trafic et d’obtenir le débit requis ou les taux d’opération requis.

## <a name="next-steps"></a>Étapes suivantes
Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
