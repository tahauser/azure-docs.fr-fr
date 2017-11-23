---
title: "Module C# d’Azure IoT Edge | Microsoft Docs"
description: "Créer un module IoT Edge avec du code C# et le déployer sur un appareil Edge"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb7674d8c292e7d571a94ac4625b0858a90704b3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Développer et déployer un module C# IoT Edge sur votre appareil simulé - Aperçu

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide à travers la création et le déploiement d’un module IoT Edge qui filtre les données de capteur sur l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé pour [Windows][lnk-tutorial1-win]ou [Linux][lnk-tutorial1-lin]. Ce didacticiel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utiliser le Visual Studio Code pour créer un module IoT Edge basé sur .NET core 2.0
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil et envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. 

## <a name="prerequisites"></a>Composants requis

* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide ou le didacticiel précédent.
* La chaîne de connexion IoT Hub pour l’IoT Hub auquel votre appareil IoT Edge se connecte.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Vous pouvez installer l’extension à partir du panneau d’extensions dans Visual Studio Code.)
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Vous pouvez installer l’extension à partir du panneau d’extensions dans Visual Studio Code.)
* Extension Azure IoT Edge pour Visual Studio Code
* [Docker](https://docs.docker.com/engine/installation/). L’édition Community (CE) pour votre plateforme est suffisante pour ce didacticiel. Veillez à l’installer sur l’ordinateur sur lequel vous exécutez VS Code.
* [Kit de développement logiciel (SDK) .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Choisir un registre Docker ou en créer un
Dans ce didacticiel, vous utilisez l’extension Azure IoT Edge pour Visual Studio Code pour créer un module et créer une [image Docker](https://docs.docker.com/glossary/?term=image). Ensuite, vous envoyez cette image Docker à un [référentiel Docker](https://docs.docker.com/glossary/?term=repository) hébergé sur un [registre Docker](https://docs.docker.com/glossary/?term=registry). Enfin, vous déployez votre image Docker empaquetée comme un [conteneur Docker](https://docs.docker.com/glossary/?term=container) du registre vers votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. Deux services de registre Docker populaires disponibles dans le cloud sont **Azure Container Registry** et **Docker Hub** :

- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/) est disponible avec un [abonnement payant](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Pour ce didacticiel, l’abonnement **De base** est suffisant. 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) offre un référentiel privé gratuit si vous vous inscrivez et obtenez un ID Docker (gratuit). 
    1. Pour vous inscrire et obtenir un ID Docker, suivez les instructions de [S’inscrire à Docker pour obtenir un ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) sur le site Docker. 

    2. Pour créer un référentiel de Docker privé, suivez les instructions de [Création d’un référentiel sur le Docker Hub](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) sur le site Docker.

Tout au long de ce didacticiel, quand cela est nécessaire, les commandes seront fournies à la fois pour le Azure Container Registry et Docker Hub.

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes montrent vous comment créer un module IoT Edge basé sur .NET core 2.0 à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.
1. Ouvrez VS Code.
2. Utilisez la commande de menu **Affichage | Terminal intégré** pour ouvrir le terminal intégré VS Code.
3. Dans le terminal intégré, entrez la commande suivante pour installer (ou mettre à jour) le modèle **AzureIoTEdgeModule** dans dotnet :

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Dans le terminal intégré, saisissez la commande suivante pour créer un projet pour le nouveau module :

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > Cette commande crée le dossier du projet, **FilterModule**, dans le dossier de travail en cours. Si vous souhaitez le créer dans un autre emplacement, modifiez les répertoires avant d’exécuter la commande.
 
3. Utilisez la commande de menu **Fichier | Ouvrir dossier**, naviguez jusqu’au dossier **FilterModule**, puis cliquez sur **Sélectionner dossier** pour ouvrir le projet dans VS Code.
4. Dans l’explorateur de VS Code, cliquez sur **Program.cs** pour l’ouvrir.
5. Ajoutez le champ suivant à la classe **Program**.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Ajoutez les classes suivantes à la classe **Program**. Ces classes définissent le schéma attendu pour le corps des messages entrants.

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

1. Dans la méthode **Init**, le code crée et configure un objet **DeviceClient**. Cet objet permet au module de se connecter au runtime Azure IoT Edge local pour envoyer et recevoir des messages. Le paramètre de chaîne de connexion utilisé dans la méthode **Init** est fourni pour le module par le runtime IoT Edge dans la variable d’environnement **EdgeHubConnectionString**. Après avoir créé le **DeviceClient**, le code enregistre un rappel pour la réception de messages à partir du hub IoT Edge via le point de terminaison **input1**. Remplacez la méthode utilisée comme rappel pour la gestion des messages avec une nouvelle et joignez un rappel pour les mises à jour de propriétés souhaitées sur le module jumeau. Pour ce faire, remplacez la dernière ligne de la méthode **Init** par le code suivant :

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. Ajoutez la méthode suivante à la classe **program** pour mettre à jour le champ **temperatureThreshold** en fonction des propriétés souhaitées envoyées par le service principal via le module jumeau. Tous les modules ont leur propre module jumeau. Un double de module permet à un service principal de configurer le code s’exécutant à l’intérieur d’un module.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"].exists())
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

1. Remplacez la méthode **PipeMessage** par la méthode suivante. Cette méthode est appelée chaque fois que le module reçoit un message d’Edge Hub. Il filtre les messages en fonction de la valeur de température dans le corps du message et le seuil de température défini via le module jumeau.

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

11. Créez le projet. Utilisez la commande de menu **Affichage | Explorateur** pour ouvrir l’explorateur VS Code. Dans l’explorateur, cliquez sur le fichier **FilterModule.csproj**, cliquez sur **Créer un module IoT Edge**, compilez le module et exportez le fichier binaire et ses dépendances dans un dossier dans lequel l’image Docker sera créée à partir de l’étape suivante.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Créer une image Docker et la publier dans votre registre

1. Créez l’image Docker.
    1. Dans l’explorateur de code de Visual Studio, cliquez sur le dossier **Docker** pour l’ouvrir. Puis sélectionnez le dossier pour votre plateforme de conteneur, soit **linux-x64** soit **windows-nano**. 
    2. Cliquez avec le bouton droit sur le fichier **Dockerfile** et cliquez sur **Créer image Docker du module IoT Edge**. 
    3. Dans la zone **Sélectionner dossier**, recherchez ou saisissez `./bin/Debug/netcoreapp2.0/publish`. Cliquez sur **Sélectionnez dossier comme EXE_DIR**.
    4. Dans la zone de texte contextuelle en haut de la fenêtre de VS Code, entrez le nom de l’image. Par exemple : `<docker registry address>/filtermodule:latest` ; où *Adresse du registre docker* est votre ID Docker si vous utilisez Docker Hub, ou semblable à `<your registry name>.azurecr.io`, si vous utilisez Azure Container Registry.
 
4. Connectez-vous à Docker. Dans le terminal intégré, entrez la commande suivante : 

    - Docker Hub (à l’invite, entrez vos informations d’identification) :
        
        ```csh/sh
        docker login
        ```

    - Pour Azure Container Registry :
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Pour trouver le nom d’utilisateur, le serveur de connexion et le mot de passe à utiliser dans cette commande, accédez au [portail Azure] (https://portal.azure.com). À partir de **Toutes les ressources**, cliquez sur la vignette de votre Azure Container Registry pour ouvrir ses propriétés, puis cliquez sur **Clés d’accès**. Copiez les valeurs dans les champs **Nom d’utilisateur**, **Mot de passe** et **Serveur de connexion**. Le serveur de connexion doit être de la forme : `<your registry name>.azurecr.io`.

3. Distribuez l’image vers votre référentiel Docker. Utilisez la commande de menu **Affichage | Palette de commandes... | Edge : Envoyer image Docker de module IoT Edge** et entrez le nom de l’image dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code. Utilisez le même nom d’image que vous avez utilisé à l’étape 1.c.

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Ajout d’informations d’identification du registre au runtime Edge sur votre appareil Edge
Ajoutez les informations d’identification pour votre registre au runtime Edge sur l’ordinateur sur lequel vous exécutez votre appareil Edge. Cela donne l’accès au runtime pour extraire le conteneur. 

- Pour Windows, exécutez la commande ci-dessous :
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Pour Linux, exécutez la commande ci-dessous :
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Exécuter la solution

1. Accédez à votre IoT Hub dans le [portail Azure](https://portal.azure.com).
2. Accédez à **IoT Edge (version préliminaire)** et sélectionnez votre appareil IoT Edge.
3. Sélectionnez **Définir modules**. 
2. Ajoutez le module **tempSensor**. Cette étape est uniquement requise si vous n’avez pas encore déployé le module **tempSensor** sur votre appareil IoT Edge.
    1. Sélectionnez **Ajouter module IoT Edge**.
    2. Dans le champ **Nom**, saisissez `tempSensor`.
    3. Dans le champ **URI de l’image**, entrez `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Laissez les autres paramètres inchangés et cliquez sur **Enregistrer**.
9. Ajouter **filtermodule**
    1. Sélectionnez à nouveau **Ajouter module IoT Edge**.
    2. Dans le champ **Nom**, saisissez `filtermodule`.
    3. Dans le champ **Image**, saisissez l’adresse de votre image, par exemple `<docker registry address>/filtermodule:latest`.
    4. Cochez la case **Modifier le module jumeau**.
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
13. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Les modules publient tous les messages sur le runtime Edge. Des règles déclaratives dans le runtime définissent où ces messages circulent. Pour ce didacticiel, vous avez besoin de deux itinéraires. Le premier itinéraire transporte les messages du capteur de température au module de filtre via le point de terminaison « input1 », qui est le point de terminaison que vous avez configuré avec le gestionnaire **FilterMessages**. Le deuxième itinéraire transporte les messages du module de filtre à IoT Hub. Dans cet itinéraire, `upstream` est une destination spéciale qui indique à Edge Hub d’envoyer les messages à IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Cliquez sur **Suivant**.
5. À l’étape **Vérifier le modèle**, cliquez sur **Envoyer**. 
6. Revenez à la page de détails de l’appareil IoT Edge et cliquez sur **Actualiser**. Vous devez voir le nouveau module **filtermodule** en cours d’exécution avec le module **tempSensor** et le **runtime IoT Edge**. 

## <a name="view-generated-data"></a>Afficher les données générées

Pour pouvoir surveiller les messages appareil vers cloud envoyés par votre appareil IoT Edge à votre IoT Hub :
1. Configurez l’extension Azure IoT Toolkit avec la chaîne de connexion pour votre IoT hub : 
    1. Utilisez la commande de menu **Affichage | Explorateur** pour ouvrir l’explorateur VS Code. 
    3. Dans l’explorateur, cliquez sur **IOT HUB DEVICES**, puis cliquez sur **...**. Cliquez sur **Définir la chaîne de connexion IoT Hub** et entrez la chaîne de connexion pour l’IoT Hub qui se connecte votre appareil IoT Edge dans la fenêtre contextuelle. 

        Pour rechercher la chaîne de connexion, cliquez sur la vignette de votre IoT Hub dans le portail Azure, puis sur **Stratégies d’accès partagé**. Dans le panneau **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis copiez la chaîne de connexion IoT Hub dans la fenêtre **iothubowner**.   

1. Pour surveiller les données reçues par IoT Hub, utilisez la commande de menu **Affichage | Palette de commandes... | IoT : Démarrer l’analyse du message D2C**. 
2. Pour arrêter l’analyse des données, utilisez la commande de menu **Affichage | Palette de commandes... | IoT : Arrêter l’analyse du message D2C**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un module IoT Edge qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Vous pouvez continuer avec un des didacticiels suivants pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Déployer Azure Function en tant que module](tutorial-deploy-function.md)
> [Déployer Azure Stream Analytics en tant que module](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
