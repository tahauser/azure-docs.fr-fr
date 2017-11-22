---
title: Simuler Azure IoT Edge sur Windows | Microsoft Docs
description: "Installer le runtime Azure IoT Edge sur un appareil simulé dans Windows et déployer votre premier module"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 08c501b9132bb21f47f099725d1fad5556befb4c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Déployer Azure IoT Edge sur un appareil simulé dans Windows - version préliminaire

Azure IoT Edge s’empare du pouvoir du cloud et l’offre à vos appareils Internet des Objets (IoT). Ce didacticiel vous guide dans la création d’un appareil simulé IoT Edge qui génère des données de capteur. Vous allez apprendre à effectuer les actions suivantes :

![Plan du didacticiel][2]

L’appareil simulé que vous créez dans ce didacticiel est un moniteur d’une éolienne qui génère des données de pression, d’humidité et de température. Ces données vous intéressent car vos éoliennes fonctionnent à différents niveaux d’efficacité selon les conditions météorologiques. Les autres didacticiels Azure IoT Edge s’appuient sur le travail que vous effectuez en déployant des modules qui analysent les données des informations métier. 

## <a name="prerequisites"></a>Composants requis

Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows pour simuler un appareil Internet des Objets (IoT). 

>[!TIP]
>Si vous exécutez Windows sur une machine virtuelle, activez la [virtualisation imbriquée][lnk-nested] et allouez au moins 2 Go de mémoire. 

1. Assurez-vous d’utiliser une version Windows prise en charge :
   * Windows 10 
   * Windows Server
2. Installez [Docker pour Windows][lnk-docker] et assurez-vous qu’il s’exécute correctement.
3. Installez [Python 2.7 sur Windows][lnk-python] et assurez-vous de pouvoir utiliser la commande pip.
4. Exécutez la commande suivante pour télécharger le script de contrôle IoT Edge.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge peut exécuter des conteneurs Windows ou Linux. Pour utiliser des conteneurs Windows, vous devez exécuter :
>    * Windows 10 Fall Creators Update, ou
>    * Windows Server 1709 (Build 16299), ou
>    * Windows IoT Core (Build 16299) sur un périphérique x64
>
> Pour Windows IoT Core, suivez les instructions qui se trouvent dans [Installation du runtime IoT Edge sur Windows IoT Core][lnk-install-iotcore]. Autrement, vous pouvez [configurer Docker pour qu’il utilise des conteneurs Windows][lnk-docker-containers] et valider les composants requis avec la commande PowerShell suivante (facultatif) :
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Créer un hub IoT

Démarrer le didacticiel en créant votre IoT Hub.
![Créer un IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Enregistrez l’appareil IoT Edge avec votre IoT Hub récemment créé.
![Enregistrer un appareil][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurer le runtime IoT Edge

Installez et démarrez le runtime Azure IoT Edge sur votre appareil. 
![Enregistrer un appareil][5]

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend deux modules. Tout d’abord, l’agent IoT Edge facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge. En second lieu, le hub IoT Edge gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et un IoT Hub. 


Suivez les étapes suivantes pour installer et démarrer le runtime IoT Edge :

1. Configurez le runtime avec votre chaîne de connexion d’appareil IoT Edge de la section précédente.

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Démarrez le runtime.

   ```
   iotedgectl start
   ```

1. Vérifiez dans Docker que l’agent IoT Edge est en cours d’exécution en tant que module.

   ```
   docker ps
   ```

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmettra des données de télémétrie à IoT Hub.
![Enregistrer un appareil][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

Afficher les messages envoyés à partir du module tempSensor :

```cmd/sh
sudo docker logs -f tempSensor
```

Vous pouvez également afficher les données de télémétrie que l’appareil envoie à l’aide de l’[outil Explorateur d’IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un nouvel appareil IoT Edge et utilisé l’interface cloud d’Azure IoT Edge pour déployer du code sur l’appareil. Vous possédez désormais un appareil simulé générant des données brutes sur son environnement. 

Ce didacticiel constitue une étape nécessaire pour suivre tous les autres didacticiels sur IoT Edge. Vous pouvez continuer avec l’un des autres didacticiels pour savoir comment Azure IoT Edge peut vous aider à transformer ces données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Développer et déployer du code C# en tant que module](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md