---
title: "Démarrage rapide Azure IoT Edge + Linux | Microsoft Docs"
description: "Essayez Azure IoT Edge en exécutant l’analyse sur un appareil Edge simulé"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: bfa6652eac34f88baf09f55353cf58227a20e4cf
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-linux-device---preview"></a>Démarrage rapide : Déployer votre premier module IoT Edge à partir du portail Azure sur un appareil Linux - préversion

Azure IoT Edge s’empare du pouvoir du cloud et l’offre à vos appareils Internet des Objets. Dans cette rubrique, découvrez comment utiliser l’interface de cloud pour déployer à distance un code prédéfini sur un appareil IoT Edge.

Si vous n’avez pas d'abonnement Azure actif, créez un [compte gratuit][lnk-account] avant de commencer.

## <a name="prerequisites"></a>Composants requis

Pour accomplir cette tâche, utilisez votre ordinateur ou une machine virtuelle pour simuler un appareil Internet des objets (IoT). Les services suivants sont requis pour déployer avec succès un appareil IoT Edge :

- [Installez Docker sur Linux][lnk-docker-ubuntu] et assurez-vous qu’il s’exécute correctement. 
- Python 2.7 est déjà installé sur la plupart des distributions Linux, y compris Ubuntu. Utilisez la commande suivante pour vous assurer que pip est installé : `sudo apt-get install python-pip`.

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

## <a name="register-an-iot-edge-device"></a>Inscrire un appareil IoT Edge

Créez une identité d’appareil pour votre appareil simulé afin qu’il puisse communiquer avec votre IoT Hub. Étant donné que les appareils IoT Edge se comportent et peuvent être gérés différemment des appareils IoT standard, vous déclarez qu’il s’agit d’un appareil IoT Edge dès le départ. 

1. Accédez à votre hub IoT dans le portail Azure.
1. Sélectionnez **IoT Edge (préversion)**.
1. Sélectionnez **Ajouter appareil IoT Edge**.
1. Donnez à votre appareil simulé un ID d’appareil unique.
1. Sélectionnez **Enregistrer** pour ajouter votre appareil.
1. Sélectionnez votre nouvel appareil dans la liste des appareils. 
1. Copiez la valeur de **Chaîne de connexion--clé primaire** et enregistrez-la. Vous utiliserez cette valeur pour configurer le runtime IoT Edge dans la section suivante. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Installer et démarrer le runtime IoT Edge

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Il comprend deux modules. Tout d’abord, l’agent IoT Edge facilite le déploiement et la surveillance des modules sur l’appareil IoT Edge. En second lieu, le hub IoT Edge gère les communications entre les modules sur l’appareil IoT Edge et entre l’appareil et un IoT Hub. 

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

![Voir edgeAgent dans Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Déployer un module

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Afficher les données générées

Dans ce guide de démarrage rapide, vous avez créé un nouveau périphérique IoT Edge et installé le runtime IoT Edge. Puis vous avez utilisé le portail Azure pour transmettre un module IoT Edge afin de l’exécuter sur l’appareil sans avoir à apporter des modifications à l’appareil lui-même. Dans ce cas, le module que vous transmettez crée des données environnementales que vous pouvez utiliser pour les didacticiels. 

Ouvrez l’invite de commandes sur l’ordinateur exécutant votre appareil simulé à nouveau. Confirmez que le module déployé à partir du cloud est en cours d’exécution sur votre appareil IoT Edge :

```cmd
sudo docker ps
```

![Afficher trois modules sur votre appareil](./media/tutorial-simulate-device-linux/docker-ps2.png)

Afficher les messages envoyés du module tempSensor vers le cloud :

```cmd
sudo docker logs -f tempSensor
```

![Afficher les données à partir de votre module](./media/tutorial-simulate-device-linux/docker-logs.png)

Vous pouvez également afficher les données de télémétrie que l’appareil envoie à l’aide de l’[outil Explorateur d’IoT Hub][lnk-iothub-explorer]. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’avez plus besoin de l’IoT Hub que vous avez créé, vous pouvez utiliser la commande [az iot hub delete][lnk-delete] pour supprimer la ressource et tous les appareils associés :

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à déployer un module IoT Edge sur un appareil IoT Edge. Essayez à présent de déployer différents types de services Azure sous forme de modules afin d'analyser les données à la périphérie. 

* [Déployer votre propre code en tant que module](tutorial-csharp-module.md)
* [Déployer Azure Function en tant que module](tutorial-deploy-function.md)
* [Déployer Azure Stream Analytics en tant que module](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
