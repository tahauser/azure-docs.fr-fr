---
title: Gérer la messagerie cloud à appareil d’Azure IoT Hub avec iothub-explorer | Microsoft Docs
description: Découvrez comment utiliser l’outil d’interface de ligne de commande iothub-explorer pour surveiller les messages appareil vers Cloud (D2C) et l’envoi de messages Cloud vers appareil C2D dans Azure IoT Hub.
services: iot-hub
documentationcenter: ''
author: shizn
manager: timlt
tags: ''
keywords: iothub explorer, messagerie cloud à appareil, iot hub cloud vers appareil, messagerie cloud vers appareil
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: 7e74c08849ae2f98d3189c0cd3d340874ea085ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Utiliser Iothub-explorer pour envoyer et recevoir des messages entre votre appareil et IoT Hub

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) dispose d’un certain nombre de commandes qui facilitent la gestion d’IoT Hub. Ce didacticiel se concentre sur l’utilisation d’iothub-explorer pour l’envoi et la réception de messages entre votre appareil et l’instance IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Contenu

Vous apprenez à utiliser iothub-explorer pour surveiller les messages appareil vers cloud et envoyer des messages cloud vers appareil. Il peut s’agir de données de capteur collectées par votre appareil, puis envoyées vers votre instance IoT Hub. Il peut s’agir de commandes envoyées par votre IoT Hub à votre appareil pour faire clignoter un voyant connecté à votre appareil.

## <a name="what-you-will-do"></a>Procédure à suivre

- Utilisez iothub-explorer pour surveiller les messages appareil vers cloud.
- Utilisez iothub-explorer pour envoyer des messages cloud vers appareil.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Le didacticiel [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) terminé, qui traite des exigences suivantes :
  - Un abonnement Azure actif.
  - Une instance Azure IoT Hub associée à votre abonnement.
  - Une application cliente qui envoie des messages à votre instance Azure IoT Hub.
- iothub-explorer. ([Installer iothub-explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>Analyse de messages appareil vers cloud

Pour analyser les messages envoyés à partir de votre appareil à votre instance IoT H+ub, procédez comme suit :

1. Ouvrez une fenêtre de console.
1. Exécutez la commande suivante :

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Obtenez `<device-id>` et `<IoTHubConnectionString>` à partir de votre instance IoT Hub. Assurez-vous d’avoir terminé le didacticiel précédent. Vous pouvez aussi essayer d’utiliser `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` si vous disposez de `HostName`, `SharedAccessKeyName` et `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Envoi de messages cloud vers appareil

Pour envoyer un message à partir de votre instance IoT Hub sur votre appareil, procédez comme suit :

1. Ouvrez une fenêtre de console.
1. Démarrez une session sur votre instance IoT Hub en exécutant la commande suivante :

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Envoyez un message à votre appareil en exécutant la commande suivante :

   ```bash
   iothub-explorer send <device-id> <message>
   ```

La commande fait clignoter le voyant connecté à votre appareil et envoie le message à ce dernier.

> [!Note]
> L’appareil n’a pas besoin de renvoyer de commande acquitter (ack) à votre instance IoT Hub à la réception du message.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à analyser des messages appareil vers cloud et à envoyer des messages cloud vers appareil entre votre appareil IoT et l’instance IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
