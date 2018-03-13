---
title: Gestion des appareils Azure IoT avec iothub-explorer | Microsoft Docs
description: "Utilisez l’outil de ligne de commande iothub-explorer pour la gestion des appareils Azure IoT Hub, avec les méthodes directes et les options de gestion des propriétés de votre choix pour la représentation."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: gestion des appareils iot azure, gestion des appareils azure iot hub, gestion des appareils iot, gestion des appareils iot hub
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.openlocfilehash: 02d3241ae4ae515a8c2deaa14f50b5b7dbd4766f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Utilisation de iothub-explorer pour la gestion des appareils Azure IoT Hub

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) est un outil en ligne de commande que vous exécutez sur un ordinateur hôte pour gérer les identités d’appareil dans votre registre de IoT Hub. Il est fourni avec des options de gestion que vous pouvez utiliser pour effectuer diverses tâches.

| Option de gestion          | Tâche                                                                                                                            |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Méthodes directes             | Faites agir un appareil comme commençant/arrêtant d’envoyer des messages ou comme redémarrant l’appareil.                                        |
| Propriétés souhaitées pour la représentation    | Mettez un appareil dans certains états, par exemple en réglant un voyant sur le vert ou en définissant l’intervalle d’envoi de télémétrie sur 30 minutes.         |
| Propriétés signalées pour la représentation   | Obtenez l’état signalé d’un appareil. Par exemple, l’appareil signale que le voyant clignote maintenant.                                    |
| Balises de représentation                  | Stocker les métadonnées spécifiques à l’appareil dans le cloud, par exemple, l’emplacement de déploiement d’un distributeur automatique.                         |
| Messages Cloud vers appareil   | Envoyez des notifications à un appareil. Par exemple, « Forte probabilité de pluie aujourd'hui. N’oubliez pas de prendre un parapluie avec vous. »              |
| Requêtes de représentations d’appareil        | Interrogez toutes les représentations d’appareil pour récupérer ceux avec des conditions arbitraires, comme l’identification des appareils qui sont disponibles pour utilisation. |

Pour plus d’explications sur les différences et des conseils sur l’utilisation de ces options, consultez [l’aide sur la communication appareil-à-cloud](iot-hub-devguide-d2c-guidance.md) et [l’aide sur la communication cloud-à-appareil](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Les représentations d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). IoT Hub conserve une représentation d’appareil pour chaque appareil que vous y connectez. Pour plus d’informations sur les représentations d’appareil, consultez [Prise en main des représentations d’appareils](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Contenu

Vous allez apprendre à utiliser iothub-explorer avec diverses options de gestion sur votre ordinateur de développement.

## <a name="what-you-do"></a>Procédure

Exécutez iothub-diverses avec diverses options de gestion.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Le didacticiel [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) terminé, qui traite des exigences suivantes :
- Un abonnement Azure actif.
- Une instance Azure IoT Hub associée à votre abonnement.
- Une application cliente qui envoie des messages à votre instance Azure IoT Hub.
- Vérifiez que votre appareil exécute l’application cliente tout au long de ce didacticiel.
- iothub-explorer, [Installez iothub-explorer](https://github.com/azure/iothub-explorer) sur votre ordinateur de développement.

## <a name="connect-to-your-iot-hub"></a>Connexion à votre IoT Hub

Connectez-vous à votre IoT Hub en exécutant la commande suivante :

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Utilisation d’iothub-explorer avec des méthodes directes

Appelez la méthode `start` dans l’application de l’appareil pour envoyer des messages à votre IoT Hub en exécutant la commande suivante :

```bash
iothub-explorer device-method <your device Id> start
```

Appelez la méthode `stop` dans l’application de l’appareil pour arrêter d’envoyer des messages à votre IoT Hub en exécutant la commande suivante :

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Utiliser iothub-explorer avec les propriétés souhaitées pour la représentation

Définissez un intervalle de propriété = 3000 en exécutant la commande suivante :

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

Cette propriété peut être lue par votre appareil.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Utiliser iothub-explorer avec les propriétés signalées pour la représentation

Obtenez les propriétés signalées de l’appareil en exécutant la commande suivante :

```bash
iothub-explorer get-twin <your device id>
```

Une des propriétés est $metadata.$lastUpdated qui indique la dernière fois que cet appareil a envoyé ou reçu un message.

## <a name="use-iothub-explorer-with-twins-tags"></a>Utiliser iothub-explorer avec les balises de représentation

Affichez les balises et les propriétés de votre appareil en exécutant la commande suivante :

```bash
iothub-explorer get-twin <your device id>
```

Ajoutez un champ role = temperature&humidity à l’appareil en exécutant la commande suivante :

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"
```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>Utiliser iothub-explorer avec les messages cloud vers appareil

Envoyez un message « Hello World » à l’appareil en exécutant la commande suivante :

```bash
iothub-explorer send <device-id> "Hello World"
```

Consultez [Utiliser Iothub-explorer pour envoyer et recevoir des messages entre votre appareil et IoT Hub](iot-hub-explorer-cloud-device-messaging.md) pour un scénario réel d’utilisation de cette commande.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Utiliser iothub-explorer avec des requêtes de représentation d’appareil

Interrogez les appareils avec une balise role = temperature&humidity en exécutant la commande suivante :

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Interrogez les appareils, à l’exception de ceux qui ont une balise role = temperature&humidity en exécutant la commande suivante :

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à utiliser iothub-explorer avec diverses options de gestion.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
