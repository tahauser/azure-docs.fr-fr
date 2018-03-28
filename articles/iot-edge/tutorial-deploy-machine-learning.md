---
title: Déployer Azure Machine Learning avec Azure IoT Edge | Microsoft Docs
description: Déployer Azure Machine Learning en tant que module sur un appareil Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4201395085dd72eb92b774eaed5980737b2e5de0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Déployer Azure Machine Learning en tant que module IoT Edge - version préliminaire

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide à travers le déploiement d’un module Azure Machine Learning qui prédit l’échec d’un appareil à partir de données de capteur sur l’appareil IoT Edge simulé que vous avez créé dans les didacticiels [Déployer Azure IoT Edge sur un appareil simulé sous Windows][lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin]. 

Ce tutoriel vous montre comment effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Créer un module Azure Machine Learning
> * Envoyer (push) un conteneur de module dans un registre de conteneurs Azure
> * Déployer un module Azure Machine Learning sur votre appareil Azure IoT Edge
> * Afficher les données générées

Le module Azure Machine Learning que vous créez dans ce didacticiel lit les données de l’environnement générées par votre appareil et étiquette les messages comme étant anormaux ou pas. 

## <a name="prerequisites"></a>Prérequis


* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide ou le premier didacticiel.
* La chaîne de connexion IoT Hub pour l’IoT Hub auquel votre appareil IoT Edge se connecte.
* Un compte Azure Machine Learning. Pour créer un compte, suivez les instructions de la page [Créer des comptes Azure Machine Learning et installer Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-services-accounts). Vous n’avez pas besoin d’installer l’application Workbench dans le cadre de ce didacticiel. 
* Gestion des modules pour Azure ML sur votre ordinateur. Pour configurer votre environnement et créer un compte, suivez les instructions de la page [Configuration de la gestion des modèles](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration).

## <a name="create-the-azure-ml-container"></a>Créer le conteneur Azure ML
Dans cette section, vous allez télécharger les fichiers de modèles entraînés et les convertir en un conteneur Azure ML.  

Sur l’ordinateur exécutant la Gestion des modules pour Azure ML, téléchargez et enregistrez [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) et [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl), que vous trouverez dans le kit de ressources Azure ML IoT sur GitHub. Ces fichiers définissent le modèle d’apprentissage de la machine entraînée qui sera déployé sur l’appareil IoT Edge. 

Utilisez le modèle entraîné pour créer un conteneur qui pourra être déployé sur des appareils IoT Edge. Utilisez la commande suivante pour :

   * enregistrer votre modèle
   * créer un manifeste
   * créer une image de conteneur Docker nommée *machinelearningmodule*
   * déployer l’image vers un cluster Azure Container Service (AKS).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Afficher le référentiel de conteneur

Vérifiez que votre image de conteneur a bien été créée et stockée dans le référentiel de conteneur Azure associé à votre environnement de Machine Learning.

1. Sur le [Portail Azure](https://portal.azure.com), accédez à **Tous les services** et sélectionnez **Registres de conteneurs**.
2. Sélectionnez votre registre. Le nom doit commencer par **mlcr** : il appartient au groupe de ressources, à l’emplacement et à l’abonnement que vous avez utilisés pour configurer la Gestion des modules.
3. Sélectionnez **Clés d’accès**.
4. Copiez **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**.  Vous en aurez besoin pour accéder au registre sur vos appareils Edge.
5. Sélectionnez **Référentiels**.
6. Sélectionnez **machinelearningmodule**.
7. Vous avez maintenant le chemin d’accès complet de l’image du conteneur. Notez le chemin d’accès de l’image pour la section suivante. Il devrait se présenter ainsi : **<nom_registre>.azureacr.io/machinelearningmodule:1**.

## <a name="add-registry-credentials-to-your-edge-device"></a>Ajouter des informations d’identification du registre à votre appareil Edge

Ajoutez les informations d’identification pour votre registre au runtime Edge sur l’ordinateur sur lequel vous exécutez votre appareil Edge. Cette commande donne au runtime l’accès nécessaire pour tirer (pull) le conteneur.

Linux :
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows :
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>Exécuter la solution

1. Accédez à votre IoT Hub sur le [portail Azure](https://portal.azure.com).
1. Accédez à **IoT Edge (version préliminaire)** et sélectionnez votre appareil IoT Edge.
1. Sélectionnez **Définir des modules**.
1. Si vous avez déjà déployé le module tempSensor sur votre appareil IoT Edge, il est possible qu’il se remplisse automatiquement. S’il ne figure pas encore dans la liste des modules, ajoutez-le.
    1. Sélectionnez **Ajouter un module IoT Edge**.
    2. Dans le champ **Nom**, entrez `tempSensor`.
    3. Dans le champ **URI de l’image**, entrez `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Sélectionnez **Enregistrer**.
1. Ajoutez le module de Machine Learning que vous avez créé.
    1. Sélectionnez **Ajouter un module IoT Edge**.
    1. Dans le champ **Nom**, entrez `machinelearningmodule`.
    1. Dans le champ **Image**, saisissez l’adresse de votre image, par exemple `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Sélectionnez **Enregistrer**.
1. Revenez à l’étape **Ajouter des modules** et sélectionnez **Suivant**.
1. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Le premier itinéraire transmet les messages du capteur de température au module Machine Learning le point de terminaison « amlInput », le point de terminaison utilisé par tous les modules Azure Machine Learning. Le deuxième itinéraire transmet les messages du module Machine Learning à IoT Hub. Dans cet itinéraire, « amlOutput » correspond au point de terminaison utilisé par tous les modules Azure Machine Learning pour transmettre des données, et « $upstream » signifie IoT Hub. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Sélectionnez **Suivant**. 
1. À l’étape **Vérifier le modèle**, sélectionnez **Envoyer**. 
1. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**.  Le nouveau module **machinelearningmodule** devrait apparaître en cours d’exécution avec les modules **tempSensor** et Runtime IoT Edge.

## <a name="view-generated-data"></a>Afficher les données générées

Vous pouvez afficher les messages appareil-à-cloud envoyés par votre appareil IoT Edge à l’aide de [l’explorateur IoT Hub](https://github.com/azure/iothub-explorer) ou l’extension du kit IoT Azure pour Visual Studio Code. 

1. Dans Visual Studio Code, sélectionnez **Appareils IoT Hub**. 
2. Sélectionnez **...**, puis **Définir la chaîne de connexion IoT Hub** dans le menu. 

   ![Menu plus Appareils IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Dans la zone de texte qui s’ouvre en haut de la page, entrez la chaîne de connexion iothubowner de votre hub IoT. Votre appareil IoT Edge devrait apparaître dans la liste Appareils IoT Hub.
4. Sélectionnez à nouveau **...**, puis **Commencer le monitoring du message D2C**.
5. Observez les messages en provenance de tempSensor toutes les cinq secondes. Le corps du message contient une propriété appelée **anomalie** qu’offre le machinelearningmodule avec la valeur true ou false. La propriété **AzureMLResponse** contient la valeur « OK » si le modèle a été exécuté correctement. 

   ![Réponse d’Azure ML dans le corps du message](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un module IoT Edge optimisé par Azure Machine Learning. Vous pouvez continuer avec l’un des autres didacticiels pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Déployer une Azure Function en tant que module](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
