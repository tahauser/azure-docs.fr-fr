---
title: Simuler Azure IoT Edge sur Linux | Microsoft Docs
description: "Installer le runtime Azure IoT Edge sur un appareil simulé dans Linux et déployer votre premier module"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 55770c92f5d5959e83066b425bc6ccf2b9dcc62e
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Déployer Azure IoT Edge sur un appareil simulé dans Linux ou MacOS : préversion

Azure IoT Edge vous permet d’analyser et de traiter les données sur vos appareils, au lieu d’envoyer (push) toutes les données dans le cloud. Les didacticiels IoT Edge décrivent comment déployer différents types de modules, créés à partir des services Azure ou d'un code personnalisé, mais vous devez d’abord avoir un appareil à tester. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

1. Création d’un IoT Hub
2. Enregistrer un appareil IoT Edge
3. Démarrer le runtime IoT Edge
4. Déployer un module

![Plan du didacticiel][2]

L’appareil simulé que vous créez dans ce didacticiel est un moniteur qui génère des données de pression, d’humidité et de température. Les autres didacticiels Azure IoT Edge s’appuient sur le travail que vous effectuez en déployant des modules qui analysent les données des informations métier. 

## <a name="prerequisites"></a>Conditions préalables

Ce didacticiel utilise votre ordinateur ou machine virtuelle comme un appareil Internet des objets. Pour faire de votre machine un appareil IoT Edge, les services suivants sont requis :

* Python pip, pour installer le runtime IoT Edge.
   * Linux : `sudo apt-get install python-pip`.
   * MacOS : `sudo easy_install pip`.
* Docker, pour exécuter les modules IoT Edge
   * [Installez Docker pour Linux][lnk-docker-ubuntu] et assurez-vous qu’il s’exécute correctement. 
   * [Installez Docker pour Mac][lnk-docker-mac] et assurez-vous qu’il s’exécute correctement. 

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Démarrer le didacticiel en créant votre IoT Hub.
![Créer un IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.
![Inscrire un appareil][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Installer et démarrer le runtime IoT Edge

Installez et démarrez le runtime Azure IoT Edge sur votre appareil. 
![Inscrire un appareil][5]

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend deux modules. L’**agent IoT Edge** facilite le déploiement et le monitoring des modules sur l’appareil IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub. Quand vous configurez le runtime sur votre nouvel appareil, seul l’agent IoT Edge démarre dans un premier temps. Le hub IoT Edge intervient plus tard quand vous déployez un module. 

Sur l’ordinateur où vous allez exécuter l’appareil IoT Edge, téléchargez le script de contrôle IoT Edge :
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Configurez le runtime avec votre chaîne de connexion d'appareil IoT Edge de la section précédente :
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Démarrez le runtime :
```cmd
sudo iotedgectl start
```

Vérifiez dans Docker que l’agent IoT Edge est en cours d’exécution en tant que module :
```cmd
sudo docker ps
```

![Consulter edgeAgent dans Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmettra des données de télémétrie à IoT Hub.
![Inscrire un appareil][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Afficher les données générées

Dans ce didacticiel, vous avez créé un appareil IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

Rouvrez l’invite de commandes sur l’ordinateur qui exécute votre appareil simulé. Confirmez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge :

```cmd
sudo docker ps
```

![Afficher trois modules sur votre appareil](./media/tutorial-simulate-device-linux/docker-ps2.png)

Afficher les messages envoyés du module tempSensor vers le cloud :

```cmd
sudo docker logs -f tempSensor
```

![Afficher les données dans votre module](./media/tutorial-simulate-device-linux/docker-logs.png)

Vous pouvez également afficher les données de télémétrie que l’appareil envoie à l’aide de l’[outil Explorateur d’IoT Hub][lnk-iothub-explorer]. 

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
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
