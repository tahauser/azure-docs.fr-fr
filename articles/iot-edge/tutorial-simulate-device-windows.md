---
title: Simuler Azure IoT Edge sur Windows | Microsoft Docs
description: Installer le runtime Azure IoT Edge sur un appareil simulé dans Windows et déployer votre premier module
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ae974162a460289a34443879a9e78224684d94ed
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Déployer Azure IoT Edge sur un appareil simulé dans Windows - version préliminaire

Azure IoT Edge vous permet d’analyser et de traiter les données sur vos appareils, au lieu d’envoyer (push) toutes les données dans le cloud. Les didacticiels IoT Edge décrivent comment déployer différents types de modules, créés à partir des services Azure ou d'un code personnalisé, mais vous devez d’abord avoir un appareil à tester. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

1. Création d’un IoT Hub
2. Enregistrer un appareil IoT Edge
3. Démarrer le runtime IoT Edge
4. Déployer un module

![Plan du didacticiel][2]

L’appareil simulé que vous créez dans ce didacticiel est un moniteur d’une éolienne qui génère des données de pression, d’humidité et de température. Ces données vous intéressent car vos éoliennes fonctionnent à différents niveaux d’efficacité selon les conditions météorologiques. Les autres didacticiels Azure IoT Edge s’appuient sur le travail que vous effectuez en déployant des modules qui analysent les données des informations métier. 

## <a name="prerequisites"></a>Prérequis


Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows pour simuler un appareil Internet des Objets (IoT). 

>[!TIP]
>Si vous exécutez Windows sur une machine virtuelle, activez la [virtualisation imbriquée][lnk-nested] et allouez au moins 2 Go de mémoire. 

1. Assurez-vous d’utiliser une version Windows prise en charge :
   * Windows 10 
   * Windows Server
2. Installez [Docker pour Windows][lnk-docker] et assurez-vous qu’il s’exécute correctement.
3. Installez [Python 2.7 sur Windows][lnk-python] et assurez-vous de pouvoir utiliser la commande pip.
4. Exécutez la commande suivante pour télécharger le script de contrôle IoT Edge.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge peut exécuter des conteneurs Windows ou Linux. Si vous exécutez une des versions de Windows suivantes, vous pouvez utiliser les conteneurs Windows :
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (Build 16299)
>    * Windows IoT Core (Build 16299) sur un périphérique x64
>
> Pour Windows IoT Core, suivez les instructions qui se trouvent dans [Installation du runtime IoT Edge sur Windows IoT Core][lnk-install-iotcore]. Sinon, il vous suffit de [configurer Docker pour utiliser des conteneurs Windows][lnk-docker-containers]. Utilisez la commande suivante pour valider vos prérequis :
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Créer un hub IoT

Démarrer le didacticiel en créant votre IoT Hub.
![Créer un IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

Inscrivez l’appareil IoT Edge avec votre IoT Hub récemment créé.
![Enregistrer un appareil][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurer le runtime IoT Edge

Installez et démarrez le runtime Azure IoT Edge sur votre appareil. 
![Inscrire un appareil][5]

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend deux modules. L’**agent IoT Edge** facilite le déploiement et le monitoring des modules sur l’appareil IoT Edge. Le **hub IoT Edge** gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et IoT Hub. Quand vous configurez le runtime sur votre nouvel appareil, seul l’agent IoT Edge démarre dans un premier temps. Le hub IoT Edge intervient plus tard quand vous déployez un module. 


Configurez le runtime avec votre chaîne de connexion d’appareil IoT Edge de la section précédente.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Démarrez le runtime.

```cmd
iotedgectl start
```

Vérifiez dans Docker que l’agent IoT Edge est en cours d’exécution en tant que module.

```cmd
docker ps
```

![Consulter edgeAgent dans Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Déployer un module

Gérez votre appareil Azure IoT Edge depuis le cloud pour déployer un module qui transmettra des données de télémétrie à IoT Hub.
![Inscrire un appareil][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Afficher les données générées

Dans ce didacticiel, vous avez créé un appareil IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

Rouvrez l’invite de commandes sur l’ordinateur qui exécute votre appareil simulé. Vérifiez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge. 

```cmd
docker ps
```

![Afficher trois modules sur votre appareil](./media/tutorial-simulate-device-windows/docker-ps2.png)

Affichez les messages envoyés du module tempSensor vers le cloud. 

```cmd
docker logs -f tempSensor
```

![Afficher les données dans votre module](./media/tutorial-simulate-device-windows/docker-logs.png)

Vous pouvez également afficher les données de télémétrie que l’appareil envoie à l’aide de l’[outil Explorateur d’IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un nouvel appareil IoT Edge et utilisé l’interface de cloud d’Azure IoT Edge pour déployer du code sur l’appareil. Vous possédez désormais un appareil simulé générant des données brutes sur son environnement. 

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