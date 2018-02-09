---
title: "Azure IoT Hub : mise en route de la connexion des appareils IoT au cloud | Microsoft Docs"
description: "Découvrez comment connecter vos starter kits et cartes IoT à Azure IoT Hub. Vos appareils peuvent envoyer des données de télémétrie à IoT Hub, et IoT Hub peut surveiller et gérer vos appareils."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: Tutoriel Azure IoT Hub
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 34742208e9189eb31310b58770ee4a22e33f56d5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-get-started-tutorials"></a>Tutoriels de prise en main d’Azure IoT Hub

Vous pouvez utiliser Azure IoT Hub et les Kits de développement logiciel (SDK) Azure IoT device pour générer des solutions Internet des objets (IoT) :

* Azure IoT Hub est un service entièrement géré dans le cloud qui connecte, surveille et gère vos appareils IoT en toute sécurité. Utilisez les Kits de développement (SDK) d’appareil Azure IoT pour implémenter vos appareils IoT.
* Utilisez une passerelle IoT dans des scénarios IoT plus complexes. Par exemple, dans les cas où vous devez tenir compte de facteurs tels que des périphériques d’ancienne génération, des coûts de bande passante, des stratégies de sécurité et de confidentialité ou le traitement de données edge. Dans ces scénarios, utilisez [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) pour implémenter une passerelle qui connecte des appareils à votre hub IoT.

## <a name="what-the-tutorials-cover"></a>Ce que couvrent les tutoriels

Ces tutoriels présentent Azure IoT Hub et les Kits de développement logiciel (SDK) d’appareil. Les tutoriels abordent des scénarios IoT courants pour faire la démonstration des fonctionnalités de IoT Hub. Ils illustrent également la manière dont IoT Hub peut être combiné avec d’autres services et outils Azure pour créer des solutions IoT plus puissantes. Dans les didacticiels, vous pouvez choisir d’utiliser des appareils IoT simulés ou réels. En outre, vous pouvez apprendre à utiliser une passerelle pour permettre aux appareils de se connecter à votre IoT Hub.

## <a name="set-up-your-device"></a>Configurer votre appareil

Configurer un appareil ou une passerelle IoT à Azure IoT Hub. Vous pouvez choisir un appareil physique ou simulé pour commencer :

| Appareil IoT                       | Langage de programmation |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino dans VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Appareil simulé sur PC           | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth] |
| Simulateur d’appareil en ligne         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
