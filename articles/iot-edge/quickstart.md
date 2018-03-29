---
title: Démarrage rapide Azure IoT Edge + Windows | Microsoft Docs
description: Essayez Azure IoT Edge en exécutant l’analyse sur un appareil Edge simulé
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f9ad01d3194ee0f8be4c3b4321c83c4bb15ea55c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Démarrage rapide : Déployer votre premier module IoT Edge à partir du portail Azure sur un appareil Windows - préversion

Dans cette démarrage rapide, utilisez l'interface de cloud Azure IoT Edge pour déployer à distance un code prédéfini sur un appareil IoT Edge. Pour accomplir cette tâche, utilisez d’abord votre appareil Windows pour simuler un appareil IoT Edge, puis déployez un module sur celui-ci.

Si vous n’avez pas d'abonnement Azure actif, créez un [compte gratuit][lnk-account] avant de commencer.

## <a name="prerequisites"></a>Prérequis


Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows pour simuler un appareil Internet des Objets (IoT). Si vous exécutez Windows sur une machine virtuelle, activez la [virtualisation imbriquée][lnk-nested] et allouez au moins 2 Go de mémoire. 

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
> Azure IoT Edge peut exécuter des conteneurs Windows ou Linux. Pour utiliser des conteneurs Windows, vous devez exécuter :
>    * Windows 10 Fall Creators Update, ou
>    * Windows Server 1709 (Build 16299), ou
>    * Windows IoT Core (Build 16299) sur un périphérique x64
>
> Pour Windows IoT Core, suivez les instructions qui se trouvent dans [Installation du runtime IoT Edge sur Windows IoT Core][lnk-install-iotcore]. Autrement, vous pouvez [configurer Docker pour qu’il utilise des conteneurs Windows][lnk-docker-containers] et valider les composants requis avec la commande PowerShell suivante (facultatif) :
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Créer un IoT hub avec l'interface de ligne de commande Azure

Créez un IoT hub dans votre abonnement Azure. Le niveau gratuit d'IoT Hub fonctionne pour ce démarrage rapide. Si vous avez utilisé IoT Hub dans le passé et déjà créé un hub gratuit, vous pouvez ignorer cette section et passez à [Inscrire un appareil IoT Edge][anchor-register]. Chaque abonnement peut avoir uniquement un IoT hub gratuit. 

1. Connectez-vous au [portail Azure][lnk-portal]. 
1. Sélectionnez le bouton **Cloud Shell**. 

   ![Bouton Cloud Shell][1]

1. Créez un groupe de ressources. Le code suivant crée un groupe de ressources appelé **IoTEdge** dans la région **Ouest des États-Unis** :

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Créez un IoT hub dans votre nouveau groupe de ressources. Le code suivant crée un hub gratuit **F1** appelé **MyIotHub** dans le groupe de ressources **IoTEdge** :

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Enregistrer un appareil IoT Edge

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurer le runtime IoT Edge

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend deux modules. Tout d’abord, l’agent IoT Edge facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge. En second lieu, le hub IoT Edge gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et un IoT Hub. 

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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

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
## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez supprimer l’appareil simulé que vous avez créé, ainsi que les conteneurs Docker qui ont été démarrés pour chaque module, utilisez la commande suivante : 

```cmd
iotedgectl uninstall
```

Lorsque vous n’avez plus besoin de l’IoT Hub que vous avez créé, vous pouvez utiliser la commande [az iot hub delete][lnk-delete] pour supprimer la ressource et tous les appareils associés :

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à déployer un module IoT Edge sur un appareil IoT Edge. Essayez à présent de déployer différents types de services Azure sous forme de modules afin d'analyser les données à la périphérie. 

* [Déployer Azure Function en tant que module](tutorial-deploy-function.md)
* [Déployer Azure Stream Analytics en tant que module](tutorial-deploy-stream-analytics.md)
* [Déployer votre propre code en tant que module](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
