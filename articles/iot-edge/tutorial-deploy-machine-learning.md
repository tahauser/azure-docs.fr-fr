---
title: "Déployer Azure Machine Learning avec Azure IoT Edge | Microsoft Docs"
description: "Déployer Azure Machine Learning en tant que module sur un appareil Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e061e599f365bf3d343cb59b8dc6a61e06627517
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Déployer Azure Machine Learning en tant que module IoT Edge - préversion

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide à travers le déploiement d’un module Azure Machine Learning qui prédit l’échec d’un appareil sur la base de données de capteur sur l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé pour [Windows][lnk-tutorial1-win]ou [Linux][lnk-tutorial1-lin]. Vous allez apprendre à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Déployer un module Azure Machine Learning sur votre appareil Azure IoT Edge
> * Afficher les données générées

Lorsque vous souhaitez utiliser votre propre modèle [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) dans votre solution, vous allez [déployer un modèle](https://aka.ms/aml-iot-edge-doc) pour IoT Edge et l’héberger dans un registre de conteneurs tel que [Azure Container Registry](../container-registry/index.yml) ou Docker.

## <a name="prerequisites"></a>Prérequis

* L’appareil Azure IoT Edge que vous avez créé dans le guide de démarrage rapide ou le premier didacticiel.
* La chaîne de connexion IoT Hub pour le hub IoT auquel votre appareil IoT Edge se connecte.
* Le conteneur Azure ML

## <a name="create-the-azure-ml-container"></a>Créer le conteneur Azure ML
Pour créer votre conteneur Azure ML, suivez les instructions fournies dans [AI Toolkit pour Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).

## <a name="run-the-solution"></a>Exécuter la solution

1. Accédez à votre hub IoT sur le [portail Azure](https://portal.azure.com).
1. Accédez à **IoT Edge (préversion)** et sélectionnez votre appareil IoT Edge.
1. Sélectionnez **Définir modules**.
1. Sélectionnez **Ajouter module IoT Edge**.
1. Dans le champ **Nom**, entrez `tempSensor`.
1. Dans le champ **URI de l’image**, entrez `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Laissez les autres paramètres inchangés et sélectionnez **Enregistrer**.
1. Toujours à l’étape **Ajouter des modules**, sélectionnez **Ajouter module IoT Edge**.
1. Dans le champ **Nom**, entrez le nom du conteneur que vous avez créé dans la section précédente. Reportez-vous à [AI Toolkit pour Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection) pour trouver le nom.
1. Dans le champ **Image**, entrez l’URI de l’image du conteneur que vous avez créé dans la section précédente. Reportez-vous à [AI Toolkit pour Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection) pour trouver l’image.
1. Cliquez sur **Enregistrer**.
1. De retour à l’étape **Ajouter des modules**, cliquez sur **Suivant**.
1. Mettre à jour des itinéraires pour votre module :
1. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Les modules publient tous les messages sur le runtime Edge. Des règles déclaratives dans le runtime définissent où ces messages circulent. Pour ce didacticiel, vous avez besoin de deux itinéraires. Le premier itinéraire transmet les messages du capteur de température au module Machine Learning le point de terminaison « amlInput », le point de terminaison utilisé par tous les modules Azure Machine Learning. Le deuxième itinéraire transmet les messages du module Machine Learning à IoT Hub. Dans cet itinéraire, « amlOutput » correspond au point de terminaison utilisé par tous les modules Azure Machine Learning pour transmettre des données, et « $upstream » est une destination spéciale qui indique à Edge Hub d’envoyer des messages à IoT Hub. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Cliquez sur **Suivant**. 
1. À l’étape « Vérifier le modèle », cliquez sur « Envoyer ». 
1. Revenez à la page de détails de l’appareil et cliquez sur « Actualiser ».  Vous devez voir le nouveau module « machinelearningmodule » en cours d’exécution avec le module « module tempSensor » et le « runtime IoT Edge ».

## <a name="view-generated-data"></a>Afficher les données générées

 Dans VS Code, utilisez la commande de menu **Affichage | Palette de commandes... | IoT : Démarrer l’analyse du message D2C** pour surveiller les données reçues par IoT Hub. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un module IoT Edge optimisé par Azure Machine Learning. Vous pouvez continuer avec l’un des autres didacticiels pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Déployer une Azure Function en tant que module](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
