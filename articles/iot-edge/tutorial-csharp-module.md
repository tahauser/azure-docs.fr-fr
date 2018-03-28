---
title: Module C# d’Azure IoT Edge | Microsoft Docs
description: Créer un module IoT Edge avec du code C# et le déployer sur un appareil Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 605f0cfe34e4fda14030bb38686095882846c7c0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Développer et déployer un module C# IoT Edge sur votre appareil simulé - Aperçu

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utilisez l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé dans [Windows][lnk-tutorial1-win] et [Linux][lnk-tutorial1-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utiliser le Visual Studio Code pour créer un module IoT Edge basé sur .NET core 2.0
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

## <a name="prerequisites"></a>Prérequis


* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide ou le premier didacticiel.
* Chaîne de connexion de clé primaire de l’appareil IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) sur le même ordinateur que celui sur lequel Visual Studio Code est installé. L’édition Community (CE) est suffisante pour ce didacticiel. 
* [Kit de développement logiciel (SDK) .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce didacticiel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.
2. Donnez un nom à votre registre, choisissez un abonnement et un groupe de ressources, puis définissez la référence (SKU) sur la valeur **De base**. 
3. Sélectionnez **Créer**.
4. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
5. Basculez **Utilisateur administrateur** sur **Activer**.
6. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus tard dans le didacticiel, au moment de la publication de l’image Docker dans votre registre et au moment de l’ajout des informations d’identification du registre dans le runtime Edge. 

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes montrent vous comment créer un module IoT Edge basé sur .NET core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.
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
5. Dans l’explorateur de VS Code, cliquez sur **Program.cs** pour l’ouvrir.

   ![Ouvrez Program.cs.][1]

6. En haut de l’espace de noms **FilterModule**, ajoutez trois instructions `using` pour les types utilisés ultérieurement sur :

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
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

8. Dans la méthode **Init**, le code crée et configure un objet **DeviceClient**. Cet objet permet au module de se connecter au runtime Azure IoT Edge local pour envoyer et recevoir des messages. La chaîne de connexion utilisée dans la méthode **Init** est fournie pour le module par le runtime IoT Edge. Après avoir créé le **DeviceClient**, le code lit la valeur TemperatureThreshold à partir des propriétés souhaitées du module jumeau et enregistre un rappel pour la réception de messages à partir du hub IoT Edge via le point de terminaison **input1**. Remplacez la méthode `SetInputMessageHandlerAsync` par une autre et ajoutez une méthode `SetDesiredPropertyUpdateCallbackAsync` pour les mises à jour de propriétés souhaitées. Pour ce faire, remplacez la dernière ligne de la méthode **Init** par le code suivant :

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    if (moduleTwinCollection["TemperatureThreshold"] != null)
    {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    }

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
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
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
            var deviceClient = (DeviceClient)userContext;
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

   ![Créer le module IoT Edge][2]

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Créer une image Docker et la publier dans votre registre

1. Dans l’explorateur de Visual Studio Code, développez le dossier **Docker**. Développez le dossier correspondant à votre plateforme de conteneur, soit **linux-x64** soit **windows-nano**.

   ![Sélectionner la plateforme de conteneur Docker][3]

2. Cliquez avec le bouton droit sur le fichier **Dockerfile** et cliquez sur **Créer image Docker du module IoT Edge**. 
3. Dans la fenêtre **Sélectionner un dossier**, recherchez ou saisissez `./bin/Debug/netcoreapp2.0/publish`. Cliquez sur **Sélectionnez dossier comme EXE_DIR**.
4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filtermodule:latest`. L’adresse du registre de conteneurs est la même que celle du serveur de connexion que vous avez copiée à partir de votre registre. Elle doit respecter le format `<your container registry name>.azurecr.io`.
5. Connectez-vous à Docker à l’aide du nom d’utilisateur, du mot de passe et du serveur de connexion que vous avez copiés à partir de votre registre de conteneurs Azure lors de sa création. Dans le terminal intégré VS Code, entrez la commande suivante : 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

6. Envoyez l’image vers votre registre de conteneurs. Sélectionnez **Affichage** > **Palette de commandes** et recherchez la commande de menu **Edge : Transmettre l’image Docker du module IoT Edge**. Entrez le nom de l’image dans la zone de texte contextuelle en haut de la fenêtre VS Code. Utilisez le nom d’image que vous avez utilisé à l’étape 4.
7. Pour afficher votre image dans le portail Azure, accédez à votre registre de conteneurs Azure, puis sélectionnez **Référentiels**. Vous devez voir **filtermodule** dans la liste.

## <a name="add-registry-credentials-to-edge-runtime"></a>Ajout d’informations d’identification de registre au runtime Edge
Ajoutez les informations d’identification pour votre registre au runtime Edge sur l’ordinateur sur lequel vous exécutez votre appareil Edge. Ces informations d’identification donnent l’accès au runtime pour extraire le conteneur. 

- Pour Windows, exécutez la commande ci-dessous :
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Pour Linux, exécutez la commande ci-dessous :
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Exécuter la solution

1. Accédez à votre IoT Hub dans le [portail Azure](https://portal.azure.com).
2. Accédez à **IoT Edge (version préliminaire)** et sélectionnez votre appareil IoT Edge.
3. Sélectionnez **Définir modules**. 
2. Vérifiez que le module **tempSensor** est automatiquement rempli. S’il ne l’est pas, procédez comme suit :
    1. Sélectionnez **Ajouter un module IoT Edge**.
    2. Dans le champ **Nom**, entrez `tempSensor`.
    3. Dans le champ **URI de l’image**, entrez `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Laissez les autres paramètres inchangés et cliquez sur **Enregistrer**.
9. Ajoutez le module **filterModule** que vous avez créé à la section précédente. 
    1. Sélectionnez **Ajouter un module IoT Edge**.
    2. Dans le champ **Nom**, entrez `filterModule`.
    3. Dans le champ **URI de l’image**, saisissez l’adresse de votre image, par exemple `<your container registry address>/filtermodule:latest`.
    4. Cochez la case **Activer**. Cela vous permet de modifier le double de module. 
    5. Remplacez le JSON dans la zone de texte pour le double de module jumeau par le texte JSON suivant : 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Cliquez sur **Enregistrer**.
12. Cliquez sur **Suivant**.
13. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Les modules publient tous les messages sur le runtime Edge. Des règles déclaratives dans le runtime définissent où les messages circulent. Pour ce didacticiel, vous avez besoin de deux itinéraires. Le premier itinéraire transporte les messages du capteur de température au module de filtre via le point de terminaison « input1 », qui est le point de terminaison que vous avez configuré avec le gestionnaire **FilterMessages**. Le deuxième itinéraire transporte les messages du module de filtre à IoT Hub. Dans cet itinéraire, `upstream` est une destination spéciale qui indique à Edge Hub d’envoyer les messages à IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Cliquez sur **Suivant**.
5. À l’étape **Vérifier le modèle**, cliquez sur **Envoyer**. 
6. Revenez à la page de détails de l’appareil IoT Edge et cliquez sur **Actualiser**. Vous devez voir le nouveau module **filtermodule** en cours d’exécution avec le module **tempSensor** et le **runtime IoT Edge**. 

## <a name="view-generated-data"></a>Afficher les données générées

Pour pouvoir surveiller les messages appareil vers cloud envoyés par votre appareil IoT Edge à votre IoT Hub :
1. Configurez l’extension Azure IoT Toolkit avec la chaîne de connexion pour votre IoT hub : 
    1. Ouvrez l’explorateur VS Code en sélectionnant **Affichage** > **Explorateur**. 
    3. Dans l’explorateur, cliquez sur **IOT HUB DEVICES**, puis cliquez sur **...**. Cliquez sur **Définir la chaîne de connexion IoT Hub** et entrez la chaîne de connexion pour l’IoT Hub auquel se connecte votre appareil IoT Edge dans la fenêtre contextuelle. 

        Pour rechercher la chaîne de connexion, cliquez sur la vignette de votre IoT Hub dans le portail Azure, puis sur **Stratégies d’accès partagé**. Dans le panneau **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis copiez la chaîne de connexion IoT Hub dans la fenêtre **iothubowner**.   

1. Pour surveiller les données reçues par IoT Hub, sélectionnez **Affichage** > **Palette de commandes** et recherchez **IoT : Démarrer l’analyse du message D2C**. 
2. Pour arrêter de surveiller les données, utilisez la commande de menu **IoT : Arrêter l’analyse du message D2C**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Vous pouvez continuer avec un des didacticiels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Déployer Azure Function en tant que module](tutorial-deploy-function.md)
> [Déployer Azure Stream Analytics en tant que module](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
