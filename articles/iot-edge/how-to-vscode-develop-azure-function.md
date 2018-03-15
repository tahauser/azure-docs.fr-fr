---
title: "Utiliser Visual Studio Code pour développer et déployer des fonctions Azure Functions sur Azure IoT Edge | Microsoft Docs"
description: "Développer et déployer une fonction Azure Functions en C# avec Azure IoT Edge dans VS Code sans changement de contexte"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 219474a4577a76f5ceb9a9efaa3c349d633de047
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Utiliser Visual Studio Code pour développer et déployer des fonctions Azure Functions sur Azure IoT Edge

Cet article fournit des instructions détaillées sur l’utilisation de [Visual Studio Code](https://code.visualstudio.com/) comme principal outil de développement et de déploiement de vos fonctions Azure Functions sur IoT Edge. 

## <a name="prerequisites"></a>Prérequis
Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

Assurez-vous d’avoir suivi les didacticiels suivants avant de démarrer ce guide.
- Déployer Azure IoT Edge sur un appareil simulé dans [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) ou [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Déployer Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Voici une liste de contrôle qui indique les éléments dont vous devriez disposer après avoir terminé les didacticiels précédents.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [Kit de développement logiciel (SDK) .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Script de contrôle IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modèle AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- Un hub IoT actif avec au moins un appareil IoT Edge.

Il est également conseillé d’installer la [prise en charge Docker pour VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) afin de mieux gérer vos images de modules et vos conteneurs.

> [!NOTE]
> Azure Functions sur IoT Edge prend uniquement en charge C# pour le moment.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Déployer Azure IoT Functions dans VS Code
Dans le didacticiel [Déployer Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function), vous avez mis à jour, généré et publié vos images de module de fonction dans VS Code, avant de vous rendre dans le portail Azure pour déployer Azure Functions. Cette section explique comment utiliser VS Code pour déployer et surveiller vos fonctions Azure Functions.

### <a name="start-a-local-docker-registry"></a>Démarrer un registre Docker local
Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Cette section utilise un [registre Docker local](https://docs.docker.com/registry/deploying/), qui est plus facile à tester pendant les premières phases de développement.
Dans le **terminal intégré** de VS Code (Ctrl + `), exécutez les commandes suivantes pour démarrer un registre local.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> L’exemple ci-dessus montre les configurations de registre qui sont exclusivement adaptées à des fins de test. Un registre adapté à l’environnement de production doit être protégé par TLS et doit, dans l’idéal, utiliser un mécanisme de contrôle d’accès. Nous vous recommandons d’utiliser [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) pour déployer des modules IoT Edge prêts pour la production.

### <a name="create-a-function-project"></a>Créer un projet Function
Les étapes suivantes montrent vous comment créer un module IoT Edge basé sur .NET core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge. Si vous avez terminé cette section dans le didacticiel précédent, vous pouvez ignorer la présente section.

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.
2. Pour installer (ou mettre à jour) le modèle **AzureIoTEdgeFunction** dans dotnet, exécutez la commande suivante dans le terminal intégré :

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Créez un projet pour le nouveau module. La commande suivante crée le dossier du projet, **FilterFunction**, dans le dossier de travail en cours :

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Sélectionnez **fichier > Ouvrir le dossier**, puis accédez au dossier **FilterFunction** et ouvrez le projet dans Visual Studio Code.
5. Naviguez jusqu’au dossier **FilterFunction**, puis cliquez sur **Sélectionner un dossier** pour ouvrir le projet dans VS Code.
6. Dans l’Explorateur de Visual Studio Code, développez le dossier **EdgeHubTrigger-Csharp**, puis ouvrez le fichier **run.csx**.
7. Remplacez le contenu du fichier par le code suivant :

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
   ```

8. Enregistrez le fichier .

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Créer une image Docker et la publier dans votre registre

1. Dans l’explorateur de Visual Studio Code, développez le dossier **Docker**. Développez le dossier correspondant à votre plateforme de conteneur, soit **linux-x64** soit **windows-nano**.
2. Cliquez avec le bouton droit sur le fichier **Dockerfile** et cliquez sur **Créer image Docker du module IoT Edge**. 

    ![Générer une image Docker](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. Accédez au dossier du projet **FilterFunction**, puis cliquez sur **Sélectionner un dossier en tant que EXE_DIR**. 
4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filterfunction:latest`. Si vous effectuez un déploiement dans le registre local, il doit être `localhost:5000/filterfunction:latest`.
5. Distribuez l’image vers votre référentiel Docker. Utilisez la commande **Edge : transmettre l’image Docker du module IoT Edge** et entrez l’URL de l’image dans la zone de texte contextuelle en haut de la fenêtre de VS Code. Utilisez la même URL d’image qu’à l’étape précédente.
    ![Transmettre une image Docker](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>Déployer votre fonction dans IoT Edge

1. Ouvrez le fichier `deployment.json` et remplacez la section **modules** par le contenu ci-dessous :
   ```json
   "tempSensor": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
         "createOptions": ""
      }
   },
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. Remplacez la section **routes** par le contenu suivant :
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > Des règles déclaratives dans le runtime définissent où ces messages circulent. Pour ce didacticiel, vous avez besoin de deux itinéraires. Le premier itinéraire transporte les messages du capteur de température à la fonction de filtre via le point de terminaison « input1 », qui est le point de terminaison que vous avez configuré avec le gestionnaire FilterMessages. Le deuxième itinéraire transporte les messages de la fonction de filtre vers IoT Hub. Dans cet itinéraire, upstream est une destination spéciale qui indique à Edge Hub d’envoyer les messages à IoT Hub.

3. Enregistrez ce fichier.
4. Dans la Palette de commandes, sélectionnez **Edge: Create deployment for Edge device** (Edge : Créer un déploiement pour l’appareil Edge). Sélectionnez ensuite l’ID de votre appareil IoT Edge pour créer un déploiement. Sinon, cliquez avec le bouton droit de la souris sur l’ID de l’appareil dans la liste des appareils et sélectionnez **Create deployment for Edge device** (Créer un déploiement pour l’appareil Edge).

    ![Créer un déploiement](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. Sélectionnez le `deployment.json` que vous avez mis à jour. Dans la fenêtre de sortie, vous pouvez voir les sorties correspondantes pour votre déploiement.
6. Dans la Palette de commandes, démarrez votre runtime Edge. **Edge : Démarrer Edge**
7. Vous pouvez voir que votre runtime IoT Edge commence à s’exécuter dans l’Explorateur Docker avec la fonction de filtre et de capteur simulée.

    ![Exécution de la solution](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. Cliquez avec le bouton droit de la souris sur l’ID de l’appareil Edge pour surveiller les messages D2C dans VS Code.

    ![Surveiller les messages](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une fonction Azure Function sur IoT Edge et l’avez déployée sur un appareil IoT Edge dans VS Code. Vous pouvez consulter les didacticiels suivants pour en savoir plus sur les autres scénarios pouvant survenir lors du développement d’Azure IoT Edge dans Visual Studio Code.

> [!div class="nextstepaction"]
> [Déboguer Azure Functions dans VS Code](how-to-vscode-debug-azure-function.md)
