---
title: "Utiliser Visual Studio Code pour développer un module C# avec Azure IoT Edge | Microsoft Docs"
description: "Développez et déployez un module C# avec Azure IoT Edge dans Visual Studio Code sans changement de contexte."
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4cf07d5c4a21fa989e7de6e996cc62424099e3e5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>Utiliser Visual Studio Code pour développer un module C# avec Azure IoT Edge
Cet article fournit des instructions détaillées pour l’utilisation de [Visual Studio Code](https://code.visualstudio.com/) comme principal outil de développement pour développer et déployer vos modules Azure IoT Edge. 

## <a name="prerequisites"></a>Prérequis
Ces didacticiels partent du principe que vous utilisez un ordinateur ou une machine virtuelle exécutant Windows ou Linux comme machine de développement. Votre appareil IoT Edge peut être un autre appareil physique, ou vous pouvez simuler votre appareil IoT Edge sur votre machine de développement.

Avant de commencer ce guide, effectuez les didacticiels suivants :
- Déployer Azure IoT Edge sur un appareil simulé dans [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) ou [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Développer et déployer un module C# IoT Edge sur votre appareil simulé](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Voici une liste de contrôle qui indique les éléments dont vous devriez disposer après avoir terminé les didacticiels précédents :

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [Script de contrôle IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Modèle AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Un IoT Hub actif avec au moins un appareil IoT Edge

Il est également utile d’installer la [prise en charge Docker pour VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) afin de mieux gérer vos images de modules et vos conteneurs.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Déployer un module Azure IoT Edge dans VS Code

### <a name="list-your-iot-hub-devices"></a>Liste de vos appareils IoT Hub
Il existe deux manières de répertorier vos appareils IoT Hub dans VS Code. Vous pouvez choisir l’une des deux méthodes pour continuer.

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>Vous connecter à votre compte Azure dans VS Code et choisir votre hub IoT
1. Dans la palette de commandes (F1 ou Ctrl + Maj + P), tapez et sélectionnez **Azure: Sign in** (Azure : Se connecter). Ensuite, sélectionnez **Copy & Open** (Copier et ouvrir). Collez (Ctrl + V) le code dans votre navigateur et sélectionnez **Continue**. Ensuite, connectez-vous à votre compte Azure. Les informations relatives à votre compte s’affichent dans la barre d’état VS Code.
2. Dans la palette de commandes, tapez et sélectionnez **IoT: Select IoT Hub** (IoT: Sélectionner IoT Hub). Sélectionnez d’abord l’abonnement dans lequel vous avez créé votre hub IoT dans le didacticiel précédent. Choisissez ensuite le hub IoT qui contient l’appareil IoT Edge.

    ![Capture d’écran de la liste des appareils](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>Définir la chaîne de connexion du hub IoT
Dans la palette de commandes, tapez et sélectionnez **IoT: Set IoT Hub Connection String** (IoT : Définir la chaîne de connexion IoT Hub). Veillez à coller la chaîne de connexion sous la stratégie **iothubowner**. (Elle se trouve dans les stratégies d’accès partagé de votre hub IoT dans le portail Azure.)
 
La liste des appareils apparaît dans IoT Hub Devices Explorer, dans la barre latérale à gauche.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Démarrer votre runtime IoT Edge et déployer un module
Installez et démarrez le runtime Azure IoT Edge sur votre appareil. Déployez un module de capteur simulé qui envoie des données de télémétrie à Azure IoT Hub.
1. Dans la palette de commandes, sélectionnez **Edge: Setup Edge** (Edge : configurer Edge) et choisissez l’ID de votre appareil IoT Edge. Vous pouvez également cliquer avec le bouton droit sur l’ID de l’appareil IoT Edge dans la **Liste des appareils** et sélectionner **Setup Edge** (Configurer Edge).

    ![Capture d’écran du runtime Setup Edge](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. Dans la palette de commandes, sélectionnez **Edge: Start Edge** (Edge : Démarrer Edge) pour démarrer votre runtime IoT Edge. Les sorties correspondantes apparaissent dans le terminal intégré.

    ![Capture d’écran du runtime Start Edge](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Vérifiez l’état du runtime IoT Edge dans l’Explorateur Docker. La couleur verte signifie qu’il est en cours d’exécution et que votre runtime IoT Edge a démarré. Votre ordinateur simule maintenant un appareil IoT Edge.

    ![Capture d’écran de l’état du runtime Edge](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Simulez un capteur qui envoie des messages à votre appareil IoT Edge. Dans la palette de commandes, tapez et sélectionnez **Edge: Generate Edge configuration file** (Edge : Générer un fichier de configuration Edge). Sélectionnez un dossier pour créer ce fichier. Dans le fichier deployment.json généré, remplacez le contenu `<registry>/<image>:<tag>` par `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` et enregistrez le fichier.

    ![Capture d’écran de module de capteur](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Sélectionnez **Edge: Create deployment for Edge device** (Edge : Créer un déploiement pour l’appareil Edge) et choisissez l’ID de l’appareil IoT Edge pour créer un déploiement. Vous pouvez aussi cliquer sur l’ID de l’appareil IoT Edge dans la liste des appareils et sélectionner **Create deployment for Edge device** (Créer un déploiement pour l’appareil Edge). 

6. Vous devez constater que votre IoT Edge commence à s’exécuter dans l’Explorateur Docker avec le capteur simulé. Cliquez avec le bouton droit sur le conteneur dans l’Explorateur Docker. Vous pouvez consulter les journaux Docker pour chaque module. En outre, vous pouvez afficher la liste des modules dans la liste des appareils.

    ![Capture d’écran de la liste des modules](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Cliquez avec le bouton droit sur l’ID de l’appareil IoT Edge pour surveiller les messages D2C dans VS Code.
8. Pour arrêter votre runtime IoT Edge et le module de capteur, tapez et sélectionnez **Edge: Stop Edge** (Edge : Arrêter Edge) dans la palette de commandes.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Développer et déployer un module C# dans VS Code
Dans le didacticiel [Développer un module C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), vous mettez à jour, générez et publiez votre image de module dans VS Code. Ensuite, vous accédez au portail Azure pour déployer votre module C#. Cette section explique comment utiliser VS Code pour déployer et surveiller votre module C#.

### <a name="start-a-local-docker-registry"></a>Démarrer un registre Docker local
Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Cette section utilise un [registre Docker local](https://docs.docker.com/registry/deploying/), qui est plus facile à tester pendant les phases initiales de développement.
Dans le **terminal intégré** de VS Code (Ctrl + `), exécutez la commande suivante pour démarrer un registre local :  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Cet exemple illustre des configurations de registre qui sont adaptées exclusivement à des fins de test. Un registre adapté à l’environnement de production doit être protégé par TLS et doit, dans l’idéal, utiliser un mécanisme de contrôle d’accès. Nous vous recommandons d’utiliser [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) pour déployer des modules IoT Edge prêts pour la production.

### <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes montrent comment créer un module IoT Edge basé sur .NET Core 2.0, à l’aide de Visual Studio Code et de l’extension Azure IoT Edge. Si vous avez terminé cette section dans le didacticiel précédent, vous pouvez ignorer la présente section.
1. Dans Visual Studio Code, sélectionnez **Afficher** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.
3. Dans le terminal intégré, entrez la commande suivante pour installer (ou mettre à jour) le modèle **AzureIoTEdgeModule** dans dotnet :

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Créez un projet pour le nouveau module. La commande suivante crée le dossier du projet, **FilterModule**, dans le dossier de travail en cours :

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Sélectionnez **Fichier** > **Ouvrir le dossier**.
4. Naviguez jusqu’au dossier **FilterModule**, puis cliquez sur **Sélectionner un dossier** pour ouvrir le projet dans VS Code.
5. Dans l’explorateur de VS Code, sélectionnez **Program.cs** pour l’ouvrir. En haut de **program.cs**, ajoutez les espaces de noms suivants :
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

8. Dans la méthode **Init**, le code crée et configure un objet **DeviceClient**. Cet objet permet au module de se connecter au runtime IoT Edge local pour envoyer et recevoir des messages. Le runtime IoT Edge fournit au module la chaîne de connexion utilisée dans la méthode **Init**. Après avoir créé l’objet **DeviceClient**, le code enregistre un rappel pour la réception des messages à partir du hub IoT Edge par le biais du point de terminaison **input1**. Remplacez la méthode `SetInputMessageHandlerAsync` par une autre et ajoutez une méthode `SetDesiredPropertyUpdateCallbackAsync` pour les mises à jour de propriétés souhaitées. Pour ce faire, remplacez la dernière ligne de la méthode **Init** par le code suivant :

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

11. Pour générer le projet, cliquez avec le bouton droit sur le fichier **FilterModule.csproj** dans l’Explorateur et sélectionnez **Build IoT Edge module** (Créer le module IoT Edge). Ce processus compile le module et exporte le fichier binaire et ses dépendances dans un dossier qui est utilisé pour créer une image Docker. 

    ![Capture d’écran de l’Explorateur Visual Studio Code](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Créer une image Docker et la publier dans votre registre

1. Dans l’Explorateur Visual Studio Code, développez le dossier **Docker**. Développez le dossier correspondant à votre plateforme de conteneur, soit **linux-x64** soit **windows-nano**.
2. Cliquez avec le bouton droit sur le fichier **Dockerfile** et sélectionnez **Build IoT Edge module Docker image** (Créer une image Docker du module IoT Edge). 

    ![Capture d’écran de l’Explorateur Visual Studio Code](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. Dans la fenêtre **Sélectionner un dossier**, recherchez ou tapez `./bin/Debug/netcoreapp2.0/publish`. Sélectionnez **Select Folder as EXE_DIR** (Sélectionner le dossier en tant que EXE_DIR).
4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filtermodule:latest`. Si vous effectuez un déploiement dans le registre local, il doit être `localhost:5000/filtermodule:latest`.
5. Distribuez l’image vers votre référentiel Docker. Utilisez la commande **Edge: Push IoT Edge module Docker image** (Edge : Transmettre l’image Docker du module IoT Edge) et entrez l’URL de l’image dans la zone de texte contextuelle en haut de la fenêtre de VS Code. Utilisez la même URL d’image qu’à l’étape précédente. Dans le journal de la console, vérifiez que l’image a été correctement envoyée.

    ![Capture d’écran de l’envoi de l’image Docker](./media/how-to-vscode-develop-csharp-module/push-image.png) ![Capture d’écran du journal de la console](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Déployer vos modules IoT Edge

1. Ouvrez le fichier `deployment.json` et remplacez la section **modules** par ce qui suit :
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

2. Remplacez la section **routes** par ce qui suit :
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > Des règles déclaratives dans le runtime définissent où ces messages circulent. Pour ce didacticiel, vous avez besoin de deux itinéraires. Le premier itinéraire transporte les messages du capteur de température au module de filtre par le biais du point de terminaison « input1 ». Il s’agit du point de terminaison que vous avez configuré avec le gestionnaire FilterMessages. Le deuxième itinéraire transporte les messages du module de filtre à IoT Hub. Dans cet itinéraire, upstream est une destination spéciale qui indique à IoT Edge Hub d’envoyer les messages à IoT Hub.

3. Enregistrez ce fichier.
4. Dans la palette de commandes, sélectionnez **Edge: Create deployment for Edge device** (Edge : Créer un déploiement pour l’appareil Edge). Sélectionnez ensuite l’ID de votre appareil IoT Edge pour créer un déploiement. Vous pouvez aussi cliquer avec le bouton droit sur l’ID de l’appareil dans la liste des appareils et sélectionner **Create deployment for Edge device** (Créer un déploiement pour l’appareil Edge).

    ![Capture d’écran de l’option Créer un déploiement](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Sélectionnez le `deployment.json` que vous avez mis à jour. Dans la fenêtre de sortie, vous pouvez voir les sorties correspondantes pour votre déploiement.

    ![Capture d’écran de la fenêtre de sortie](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Démarrez votre runtime IoT Edge dans la palette de commandes (sélectionnez **Edge: Start Edge** (Edge : Démarrer Edge)).
7. Vous pouvez voir que votre runtime IoT Edge commence à s’exécuter dans l’Explorateur Docker avec le module de filtre et de capteur simulé.

    ![Capture d’écran de l’Explorateur Docker](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Cliquez avec le bouton droit sur l’ID de l’appareil IoT Edge pour surveiller les messages D2C dans VS Code.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge et vous l’avez déployé sur un appareil IoT Edge dans VS Code. Pour en savoir plus sur les autres scénarios quand vous développez Azure IoT Edge dans VS Code, consultez le didacticiel suivant :

> [!div class="nextstepaction"]
> [Déboguer un module C# dans VS Code](how-to-vscode-debug-csharp-module.md)
