---
title: "Utiliser Visual Studio Code pour développer un module C# avec Azure IoT Edge | Microsoft Docs"
description: "Développer et déployer un module C# avec Azure IoT Edge dans VS Code sans changement de contexte"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cad28b4e6d4e46058641da19795cd71efdbd0c92
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour développer un module C# avec Azure IoT Edge
Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio Code](https://code.visualstudio.com/) comme principal outil de développement pour développer et déployer vos modules IoT Edge. 

## <a name="prerequisites"></a>configuration requise
Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

Assurez-vous d’avoir suivi les didacticiels suivants avant de démarrer ce guide.
- Déployer Azure IoT Edge sur un appareil simulé dans [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) ou [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Développer et déployer un module C# IoT Edge sur votre appareil simulé](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Voici une liste de contrôle qui indique les éléments dont vous devriez disposer après avoir terminé les didacticiels précédents.

- [Visual Studio Code](https://code.visualstudio.com/). 
- [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
- [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [Kit de développement logiciel (SDK) .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
- [Python 2.7](https://www.python.org/downloads/)
- [Script de contrôle IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modèle AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Un IoT Hub actif avec au moins un appareil IoT Edge.

Il est également conseillé d’installer la [prise en charge Docker pour VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) afin de mieux gérer vos images de modules et vos conteneurs.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Déployer un module Azure IoT Edge dans VS Code

### <a name="list-your-iot-hub-devices"></a>Liste de vos appareils IoT Hub
Il existe deux manières de répertorier vos appareils IoT Hub dans VS Code. Vous pouvez choisir l’une des deux méthodes pour continuer.

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>Connectez-vous à votre compte Azure dans VSCode et sélectionnez votre IoT Hub
1. Dans la Palette de commandes (F1 ou Ctrl + Maj + P), tapez et sélectionnez **Azure: Sign in** (Azure : connexion). Cliquez ensuite sur **Copier* & ouvrir** dans la fenêtre contextuelle. Collez (Ctrl + V) le code dans votre navigateur et cliquez sur le bouton Continuer. Connectez-vous à votre compte Azure. Les informations relatives à votre compte s’affichent dans la barre d’état VS Code.
2. Dans la Palette de commandes (F1 ou Ctrl + Maj + P), tapez et sélectionnez **IoT: Select IoT Hub** (IoT : sélectionner IoT Hub). Sélectionnez d’abord l’abonnement dans lequel vous avez créé votre IoT Hub dans le didacticiel précédent. Choisissez ensuite l’IoT Hub qui contient l’appareil IoT Edge.

    ![Liste des appareils](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-iot-hub-connection-string"></a>Définition de la chaîne de connexion de l’IoT Hub
Dans la Palette de commandes (F1 ou Ctrl + Maj + P), tapez et sélectionnez **IoT: Set IoT Hub Connection String** (IoT : définir la chaîne de connexion IoT Hub). Assurez-vous que vous collez la chaîne de connexion sous la stratégie **iothubowner** (que vous trouverez dans les stratégies d’accès partagé de votre IoT Hub dans le portail Azure).
 
La liste des appareils apparaît dans IoT Hub Device Explorer, dans la barre latérale gauche.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Démarrer votre runtime IoT Edge et déployer un module
Installez et démarrez le runtime Azure IoT Edge sur votre appareil. Déployez un module de capteur simulé qui envoie des données de télémétrie à IoT Hub.
1. Dans la Palette de commandes, sélectionnez **Edge: Setup Edge** (Edge : configurer Edge) et choisissez l’ID de votre appareil IoT Edge. Vous pouvez également cliquer avec le bouton droit sur l’ID de l’appareil Edge dans la liste des appareils et sélectionner **Setup Edge** (Configurer Edge).

    ![Configurer le runtime Edge](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. Dans la Palette de commandes, sélectionnez **Edge: Start Edge** (Edge : démarrer Edge) pour démarrer votre runtime Edge. Les sorties correspondantes apparaissent dans le terminal intégré.

    ![Démarrer le runtime Edge](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Vérifiez l’état du runtime Edge dans l’Explorateur Docker. La couleur verte signifie qu’il est en cours d’exécution. Votre runtime IoT Edge a démarré avec succès.

    ![Le runtime Edge est en cours d’exécution](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. À présent que votre runtime Edge est en cours d’exécution, cela signifie que votre ordinateur simule un appareil Edge. L’étape suivante consiste à simuler un capteur qui envoie des messages à votre appareil Edge. Dans la Palette de commandes, tapez et sélectionnez **Edge: Generate Edge configuration file** (Edge : générer un fichier de configuration Edge). Sélectionnez un dossier pour créer ce fichier. Dans le fichier deployment.json ainsi généré, remplacez le contenu `<registry>/<image>:<tag>` par `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` et enregistrez le fichier.

    ![Module de capteur](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Sélectionnez **Edge: Create deployment for Edge device** (Edge : créer un déploiement pour l’appareil Edge) et choisissez l’ID de l’appareil Edge pour créer un nouveau déploiement. Sinon, vous pouvez cliquer sur l’ID de l’appareil Edge dans la liste des appareils et sélectionner **Create deployment for Edge device** (Créer un déploiement pour l’appareil Edge). 

6. Vous devez voir que votre IoT Edge commence à s’exécuter dans l’Explorateur Docker avec le capteur simulé. Cliquez avec le bouton droit de la souris sur le conteneur dans l’Explorateur Docker. Vous pouvez consulter les journaux Docker pour chaque module. En outre, vous pouvez afficher la liste des modules dans la liste des appareils.

    ![Liste des modules](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Cliquez avec le bouton droit de la souris sur l’ID de l’appareil Edge pour surveiller les messages D2C dans VS Code.
8. Pour arrêter votre runtime IoT Edge et le module de capteur, vous pouvez taper et sélectionner **Edge: Stop Edge** (Edge : arrêter Edge) dans la Palette de commandes.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Développer et déployer un module C# dans VS Code
Le didacticiel [Développer un module C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module) vous indique comment mettre à jour, générer et publier votre image de module dans VS Code, puis vous rendre sur le portail Azure pour déployer votre module C#. Cette section explique comment utiliser VS Code pour déployer et surveiller votre module C#.

### <a name="start-a-local-docker-registry"></a>Démarrer un registre Docker local
Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Cette section utilise un [registre Docker local](https://docs.docker.com/registry/deploying/), qui est plus facile à tester pendant les premières phases de développement.
Dans le **terminal intégré** de VS Code (Ctrl + `), exécutez les commandes suivantes pour démarrer un registre local.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> L’exemple ci-dessus montre les configurations de registre qui sont exclusivement adaptées à des fins de test. Un registre adapté à l’environnement de production doit être protégé par TLS et doit, dans l’idéal, utiliser un mécanisme de contrôle d’accès. Nous vous recommandons d’utiliser [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) pour déployer des modules IoT Edge prêts pour la production.

### <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes montrent vous comment créer un module IoT Edge basé sur .NET core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge. Si vous avez terminé cette section dans le didacticiel précédent, vous pouvez ignorer la présente section.
1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.
3. Dans le terminal intégré, entrez la commande suivante pour installer (ou mettre à jour) le modèle **AzureIoTEdgeModule** dans dotnet :

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Créez un projet pour le nouveau module. La commande suivante crée le dossier du projet, **FilterModule**, dans le dossier de travail en cours :

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Sélectionnez **Fichier** > **Ouvrir un dossier**.
4. Naviguez jusqu’au dossier **FilterModule**, puis cliquez sur **Sélectionner un dossier** pour ouvrir le projet dans VS Code.
5. Dans l’explorateur de VS Code, cliquez sur **Program.cs** pour l’ouvrir. En haut de **program.cs**, ajoutez les espaces de noms ci-dessous.
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

6. Ajoutez la variable `temperatureThreshold` à la classe **Program**. Cette variable définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à IoT Hub. 

   ```csharp
   static int temperatureThreshold { get; set; } = 25;
   ```

7. Ajoutez les classes `MessageBody`, `Machine` et `Ambient` à la classe **Program**. Ces classes définissent le schéma attendu pour le corps des messages entrants.

    ```csharp
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

8. Dans la méthode **Init**, le code crée et configure un objet **DeviceClient**. Cet objet permet au module de se connecter au runtime Azure IoT Edge local pour envoyer et recevoir des messages. La chaîne de connexion utilisée dans la méthode **Init** est fournie pour le module par le runtime IoT Edge. Après avoir créé le **DeviceClient**, le code enregistre un rappel pour la réception de messages à partir du hub IoT Edge via le point de terminaison **input1**. Remplacez la méthode `SetInputMessageHandlerAsync` par une autre et ajoutez une méthode `SetDesiredPropertyUpdateCallbackAsync` pour les mises à jour de propriétés souhaitées. Pour ce faire, remplacez la dernière ligne de la méthode **Init** par le code suivant :

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Ajoutez la méthode `onDesiredPropertiesUpdate` à la classe **Program**. Cette méthode reçoit des mises à jour sur les propriétés souhaitées à partir du double de module et met à jour la variable **temperatureThreshold** en conséquence. Tous les modules ont leur propre double, ce qui vous permet de configurer le code exécuté à l’intérieur d’un module directement à partir du cloud.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. Remplacez la méthode `PipeMessage` par la méthode `FilterMessages`. Cette méthode est appelée chaque fois que le module reçoit un message d’IoT Edge Hub. Il filtre les messages qui signalent des températures situées sous le seuil de température défini via le double de module. Il ajoute également la propriété **MessageType** au message avec la valeur définie sur **Alerte**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Pour générer le projet, cliquez avec le bouton droit sur le fichier **FilterModule.csproj** dans l’Explorateur et cliquez sur **Créer le module IoT Edge**. Ce processus compile le module et exporte le fichier binaire et ses dépendances dans un dossier qui est utilisé pour créer une image Docker. 

    ![Générer le module](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Créer une image Docker et la publier dans votre registre

1. Dans l’explorateur de Visual Studio Code, développez le dossier **Docker**. Développez le dossier correspondant à votre plateforme de conteneur, soit **linux-x64** soit **windows-nano**.
2. Cliquez avec le bouton droit sur le fichier **Dockerfile** et cliquez sur **Créer image Docker du module IoT Edge**. 

    ![Générer une image Docker](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. Dans la fenêtre **Sélectionner un dossier**, recherchez ou saisissez `./bin/Debug/netcoreapp2.0/publish`. Cliquez sur **Sélectionnez dossier comme EXE_DIR**.
4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filtermodule:latest`. Si vous effectuez un déploiement dans le registre local, il doit être `localhost:5000/filtermodule:latest`.
5. Distribuez l’image vers votre référentiel Docker. Utilisez la commande **Edge : transmettre l’image Docker du module IoT Edge** et entrez l’URL de l’image dans la zone de texte contextuelle en haut de la fenêtre de VS Code. Utilisez la même URL d’image qu’à l’étape précédente. Dans le journal de la console, vérifiez que l’image a été correctement envoyée.

    ![Envoyer l’image Docker](./media/how-to-vscode-develop-csharp-module/push-image.png) ![Image Docker envoyée](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Déployer vos modules IoT Edge

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
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Remplacez la section **routes** par le contenu suivant :
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > Des règles déclaratives dans le runtime définissent où ces messages circulent. Pour ce didacticiel, vous avez besoin de deux itinéraires. Le premier itinéraire transporte les messages du capteur de température au module de filtre via le point de terminaison « input1 », qui est le point de terminaison que vous avez configuré avec le gestionnaire FilterMessages. Le deuxième itinéraire transporte les messages du module de filtre à IoT Hub. Dans cet itinéraire, upstream est une destination spéciale qui indique à Edge Hub d’envoyer les messages à IoT Hub.

3. Enregistrez ce fichier.
4. Dans la Palette de commandes, sélectionnez **Edge: Create deployment for Edge device** (Edge : Créer un déploiement pour l’appareil Edge). Sélectionnez ensuite l’ID de votre appareil IoT Edge pour créer un déploiement. Sinon, cliquez avec le bouton droit de la souris sur l’ID de l’appareil dans la liste des appareils et sélectionnez **Create deployment for Edge device** (Créer un déploiement pour l’appareil Edge).

    ![Créer un déploiement](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Sélectionnez le `deployment.json` que vous avez mis à jour. Dans la fenêtre de sortie, vous pouvez voir les sorties correspondantes pour votre déploiement.

    ![Déploiement réussi](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Dans la Palette de commandes, démarrez votre runtime Edge. **Edge : Démarrer Edge**
7. Vous pouvez voir que votre runtime IoT Edge commence à s’exécuter dans l’Explorateur Docker avec le module de filtre et de capteur simulé.

    ![La solution IoT Edge est en cours d’exécution](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Cliquez avec le bouton droit de la souris sur l’ID de l’appareil Edge pour surveiller les messages D2C dans VS Code.


## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge et l’avez déployé sur un appareil IoT Edge dans VS Code. Vous pouvez consulter les didacticiels suivants pour en savoir plus sur les autres scénarios pouvant survenir lors du développement d’Azure IoT Edge dans VS Code.

> [!div class="nextstepaction"]
> [Déboguer un module C# dans VS Code](how-to-vscode-debug-csharp-module.md)
