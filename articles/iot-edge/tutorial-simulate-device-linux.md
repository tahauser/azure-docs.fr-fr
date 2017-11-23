---
title: Simuler Azure IoT Edge sur Linux | Microsoft Docs
description: "Installer le runtime Azure IoT Edge sur un appareil simulé dans Linux et déployer votre premier module"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 041919fd729880d429e08d8942f8d1ee087ccf61
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Déployer Azure IoT Edge sur un appareil simulé dans Linux - préversion

Azure IoT Edge vous permet d’effectuer une analyse et un traitement des données sur vos appareils, au lieu de transférer toutes les données vers le cloud. Les didacticiels IoT Edge montrent comment déployer différents types de modules, construits à partir des services Azure ou d'un code personnalisé, mais vous avez besoin au préalable d’un appareil à tester. 

Ce didacticiel vous guide dans la création d’un appareil simulé IoT Edge puis dans le déploiement d'un module qui génère des données de capteur. Vous allez apprendre à effectuer les actions suivantes :

![Plan du didacticiel][2]

L’appareil simulé que vous créez dans ce didacticiel est un moniteur qui génère des données de pression, d’humidité et de température. Les autres didacticiels Azure IoT Edge s’appuient sur le travail que vous effectuez en déployant des modules qui analysent les données des informations métier. 

## <a name="prerequisites"></a>Composants requis

Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Linux pour simuler un appareil Internet des Objets (IoT). Les services suivants sont requis pour déployer avec succès un appareil IoT Edge :

- [Installez Docker pour Linux][lnk-docker-ubuntu] et assurez-vous qu’il s’exécute correctement. 
- Python 2.7 est déjà installé sur la plupart des distributions Linux, y compris Ubuntu. Utilisez la commande suivante pour vous assurer que pip est installé : `sudo apt-get install python-pip`.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Démarrer le didacticiel en créant votre IoT Hub.
![Créer un IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Inscrire un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.
![Inscrire un appareil][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Installer et démarrer le runtime IoT Edge

Installez et démarrez le runtime Azure IoT Edge sur votre appareil. 
![Inscrire un appareil][5]

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend deux modules. Tout d’abord, l’agent IoT Edge facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge. En second lieu, le hub IoT Edge gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et un IoT Hub. 

Suivez les étapes suivantes pour installer et démarrer le runtime IoT Edge :

1. Sur l’ordinateur où vous allez exécuter l’appareil IoT Edge, téléchargez le script de contrôle IoT Edge.

   ```
   sudo pip install -U azure-iot-edge-runtime-ctl
   ```

1. Configurez le runtime avec votre chaîne de connexion d'appareil IoT Edge à partir de la section précédente.

   ```
   sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Démarrez le runtime.

   ```
   sudo iotedgectl start
   ```

1. Vérifiez dans Docker que l’agent IoT Edge est en cours d’exécution en tant que module.

   ```
   sudo docker ps
   ```

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmettra des données de télémétrie à IoT Hub.
![Inscrire un appareil][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l'exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

Afficher les messages envoyés à partir du module tempSensor :

```cmd/sh
docker logs -f tempSensor
```

Vous pouvez également afficher les données de télémétrie que l’appareil envoie à l’aide de l['outil Explorateur d'IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un nouvel appareil IoT Edge et utilisé l’interface de cloud d’Azure IoT Edge pour déployer du code sur l’appareil. Vous possédez désormais un appareil simulé générant des données brutes sur son environnement. 

Ce didacticiel constitue une étape nécessaire pour suivre tous les autres didacticiels sur IoT Edge. Vous pouvez continuer avec l’un des autres didacticiels pour savoir comment Azure IoT Edge peut vous aider à transformer ces données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Développer et déployer du code C# en tant que module](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
