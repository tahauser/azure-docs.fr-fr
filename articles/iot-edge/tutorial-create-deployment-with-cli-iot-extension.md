---
title: "Déployer des modules sur des appareils IoT Edge à l’aide de l’extension IoT pour Azure CLI 2.0 | Microsoft Docs"
description: "Déployer des modules sur un appareil IoT Edge à l’aide de l’extension IoT pour Azure CLI 2.0"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 03/02/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.reviewer: kgremban
ms.openlocfilehash: 1986c9881c09ffa480103e009dc42d18aad4e2aa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>Déployer des modules sur un appareil IoT Edge à l’aide de l’extension IoT pour Azure CLI 2.0

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) est un outil à ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure telles que IoT Edge. Azure CLI 2.0 est disponible sur Windows, Linux et Mac OS.

Azure CLI 2.0 vous permet de gérer les ressources Azure IoT Hub, les instances de service Device Provisioning et les hubs liés dès l’installation. La nouvelle extension IoT enrichit Azure CLI 2.0 avec des fonctionnalités telles que la gestion des périphériques et la fonctionnalité complète de IoT Edge.

Dans ce didacticiel, vous commencez par les étapes de configuration de Azure CLI 2.0 et de l’extension IoT. Ensuite, vous découvrez comment déployer des modules sur un périphérique IoT Edge à l’aide des commandes CLI disponibles.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure. Si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?v=17.39a) dès à présent. 

* [Python 2.7x ou Python 3.x](https://www.python.org/downloads/).

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) dans votre environnement. La version d’Azure CLI 2.0 doit être au minimum 2.0.24. Utilisez `az –-version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack. Pour une installation sur Windows, le plus simple consiste à télécharger et à installer le [MSI](https://aka.ms/InstallAzureCliWindows).

* [Extension IoT pour Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) :
   1. Exécutez `az extension add --name azure-cli-iot-ext`. 
   2. Après l’installation, utilisez `az extension list` pour valider les extensions actuellement installées, ou `az extension show --name azure-cli-iot-ext` pour afficher les détails concernant l’extension IoT.
   3. Pour supprimer l’extension, utilisez `az extension remove --name azure-cli-iot-ext`.


## <a name="create-an-iot-edge-device"></a>Créer un appareil IoT Edge
Cet article explique comment créer un déploiement IoT Edge. L’exemple vous montre comment vous connecter à votre compte Azure, créer un groupe de ressources Azure (un conteneur qui contient les ressources associées à une solution Azure), créer un hub IoT, créer trois identités d’appareils IoT Edge, définir des balises et créer un déploiement IoT Edge qui cible ces appareils. 

Connectez-vous à votre compte Azure. Après avoir entré la commande de connexion suivante, vous êtes invité à utiliser un navigateur web pour vous connecter à l’aide d’un code à usage unique : 

   ```cli
   az login
   ```

Créer un groupe de ressources appelé **IoTHubCLI** dans la région Est des États-Unis : 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![Créer un groupe de ressources][2]

Créez un hub IoT appelé **CLIDemoHub** sous le groupe de ressources nouvellement créé :

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >Chaque abonnement peut avoir un hub IoT gratuit. Pour créer un hub disponible avec la commande CLI, remplacez la valeur de référence SKU par `--sku F1`. Si vous disposez déjà d’un hub disponible dans votre abonnement, vous recevez un message d’erreur lorsque vous essayez d’en créer un autre. 

Créez un appareil IoT Edge :

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![Créer un appareil IoT Edge][4]

## <a name="configure-the-iot-edge-device"></a>Configurer un appareil IoT Edge

Créer un modèle JSON de déploiement et enregistrez-le localement, sous la forme d’un fichier txt. Vous aurez besoin du chemin d’accès au fichier lorsque vous exécutez la commande apply-configuration.

Les modèles JSON de déploiement doivent toujours inclure les deux modules systèmes, edgeAgent et edgeHub. En plus de ces deux modèles, vous pouvez utiliser ce fichier pour déployer d’autres modules sur l’appareil IoT Edge. Utilisez l’exemple suivant pour configurer l’appareil IoT Edge avec un module tempSensor :

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
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

Appliquez la configuration à votre appareil IoT Edge :

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

Affichez les modules sur votre appareil IoT Edge :
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![Faire la liste des modules][6]

## <a name="next-steps"></a>étapes suivantes

* Découvrez comment [utiliser un appareil IoT Edge en tant que passerelle](how-to-create-transparent-gateway.md).

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

