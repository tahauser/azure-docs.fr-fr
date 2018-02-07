---
title: "Déployer des modules sur des appareils IoT Edge à l’aide de l’extension IoT pour Azure CLI 2.0 | Microsoft Docs"
description: "Déployer des modules sur un appareil IoT Edge à l’aide de l’extension IoT pour Azure CLI 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Déployer des modules sur un appareil IoT Edge à l’aide de l’extension IoT pour Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) est un outil à ligne de commande open source et multiplateforme destiné à la gestion des ressources Azure telles que IoT Edge. Azure CLI 2.0 est disponible sur Windows, Linux et Mac OS.

Azure CLI 2.0 vous permet de gérer les ressources Azure IoT Hub, les instances de service Device Provisioning et les hubs liés dès l’installation. La nouvelle extension IoT enrichit Azure CLI 2.0 avec des fonctionnalités telles que la gestion des périphériques et la fonctionnalité complète de IoT Edge.

Dans ce didacticiel, vous commencez par les étapes de configuration de Azure CLI 2.0 et de l’extension IoT. Ensuite, vous découvrez comment déployer des modules sur un périphérique IoT Edge à l’aide des commandes CLI disponibles.

## <a name="installation"></a>Installation 

### <a name="step-1---install-python"></a>Étape 1 - Installez Python

[Python 2.7x ou Python 3.x](https://www.python.org/downloads/) est nécessaire.

### <a name="step-2---install-azure-cli-20"></a>Étape 2 - Installez Azure CLI 2.0

Suivez les [instructions d’installation](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) pour configurer Azure CLI 2.0 dans votre environnement. Votre version de Azure CLI 2.0 doit être au minimum la version 2.0.24, ou une version ultérieure. Utilisez `az –version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack. Une méthode d’installation simple sur Windows consiste à télécharger et installer le [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Étape 3 - Installez l’extension IoT

[Le fichier Lisez-moi de l’extension IoT](https://github.com/Azure/azure-iot-cli-extension) décrit plusieurs manières d’installer l’extension. La façon la plus simple consiste à exécuter `az extension add --name azure-cli-iot-ext`. Après l’installation, vous pouvez utiliser `az extension list` pour valider les extensions actuellement installées ou `az extension show --name azure-cli-iot-ext` pour afficher les détails concernant l’extension IoT. Pour supprimer l’extension, vous pouvez utiliser `az extension remove --name azure-cli-iot-ext`.


## <a name="deploy-modules-to-an-iot-edge-device"></a>Déployer des modules sur un appareil IoT Edge
Dans ce didacticiel, vous allez apprendre à créer un déploiement IoT Edge. L’exemple vous montre comment vous connecter à votre compte Azure, créer un groupe de ressources Azure (un conteneur qui contient les ressources associées à une solution Azure), créer un hub IoT, créer trois identités d’appareils IoT Edge, définir des balises et créer un déploiement IoT Edge qui cible ces appareils. Effectuez les étapes d’installation décrites précédemment avant de commencer. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?v=17.39a) dès maintenant. 


### <a name="1-login-to-the-azure-account"></a>1. Se connecter au compte Azure
  
    az login

![se connecter][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Créer un groupe de ressources IoTHubBlogDemo dans eastus

    az group create -l eastus -n IoTHubBlogDemo

![Créer un groupe de ressources][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3. Créer un hub IoT blogDemoHub sous le groupe de ressources nouvellement créé

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Créer un hub IoT][3]


### <a name="4-create-an-iot-edge-device"></a>4. Créer un appareil IoT Edge

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![Créer un appareil IoT Edge][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5. Appliquer une configuration à l’appareil IoT Edge

Enregistrez localement votre modèle JSON de déploiement dans un fichier txt. Vous aurez besoin du chemin d’accès au fichier lorsque vous exécutez la commande apply-configuration.

Voici un exemple de modèle JSON de déploiement contenant un module tempSensor :

```json
{
  "moduleContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "tempSensor": {
      "properties.desired": {}
    }
  }
}
```

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![Appliquer la configuration][5]

### <a name="6-list-modules"></a>6. Faire la liste des modules
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![Faire la liste des modules][6]

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez créé une fonction Azure qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Pour continuer l’exploration d’Azure IoT Edge, apprenez à utiliser un appareil IoT Edge en tant que passerelle. 

> [!div class="nextstepaction"]
> [Créer un périphérique de passerelle IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

