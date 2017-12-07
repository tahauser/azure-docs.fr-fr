---
title: "Déployer une fonction Azure avec Azure IoT Edge | Microsoft Docs"
description: "Déployer une fonction Azure en tant que module d’un appareil Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: fb295b37819788ed14f54e4123ae0fe1b52d0210
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Déployer une fonction Azure en tant que module IoT Edge - version préliminaire
Vous pouvez utiliser Azure Functions pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide à travers la création et le déploiement d’une fonction Azure qui filtre les données de capteur sur l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé pour [Windows][lnk-tutorial1-win]ou [Linux][lnk-tutorial1-lin]. Ce didacticiel vous montre comment effectuer les opérations suivantes :     

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer une fonction Azure
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


La fonction Azure que vous créez dans ce didacticiel filtre les données de température générées par votre appareil et envoie uniquement des messages en amont vers Azure IoT Hub lorsque la température dépasse un seuil spécifié. 

## <a name="prerequisites"></a>Composants requis

* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide ou le didacticiel précédent.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Vous pouvez installer l’extension à partir du panneau d’extensions dans Visual Studio Code.)
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Vous pouvez installer l’extension à partir du panneau d’extensions dans Visual Studio Code.)
* [Docker](https://docs.docker.com/engine/installation/). L’édition Community (CE) pour votre plateforme est suffisante pour ce didacticiel. 
* [Kit de développement logiciel (SDK) .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="set-up-a-docker-registry"></a>Configurer un registre Docker
Dans ce didacticiel, vous utilisez l’extension Azure IoT Edge pour Visual Studio Code pour créer une fonction Azure qui vous servira à créer une [image Docker](https://docs.docker.com/glossary/?term=image). Ensuite, vous envoyez cette image Docker à un [référentiel Docker](https://docs.docker.com/glossary/?term=repository) hébergé par un [registre Docker](https://docs.docker.com/glossary/?term=registry). Enfin, vous déployez votre image Docker empaquetée comme un [conteneur Docker](https://docs.docker.com/glossary/?term=container) du registre vers votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. Deux services de registre Docker populaires disponibles dans le cloud sont **Azure Container Registry** et **Docker Hub** :

- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/) est disponible avec un [abonnement payant](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Pour ce didacticiel, l’abonnement **De base** est suffisant. 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) offre un référentiel privé gratuit si vous vous inscrivez et obtenez un ID Docker (gratuit). 
    1. Pour vous inscrire et obtenir un ID Docker, suivez les instructions de [S’inscrire à Docker pour obtenir un ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) sur le site Docker. 

    2. Pour créer un référentiel de Docker privé, suivez les instructions de [Création d’un référentiel sur le Docker Hub](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) sur le site Docker.

Tout au long de ce didacticiel, quand cela est nécessaire, les commandes sont fournies à la fois pour Azure Container Registry et Docker Hub.

## <a name="create-a-function-project"></a>Créer un projet Function
Les étapes suivantes vous montrent comment créer une fonction IoT Edge à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.
1. Ouvrez Visual Studio Code.
2. Utilisez la commande de menu **Affichage | Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.
3. Dans le terminal intégré, entrez la commande suivante pour installer (ou mettre à jour) le modèle **AzureIoTEdgeFunction** dans dotnet :

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Dans le terminal intégré, saisissez la commande suivante pour créer un projet pour le nouveau module :

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > Cette commande crée le dossier du projet, **FilterFunction**, dans le dossier de travail en cours. Si vous souhaitez le créer dans un autre emplacement, modifiez les répertoires avant d’exécuter la commande.

3. Utilisez la commande de menu **Fichier | Ouvrir dossier**, naviguez jusqu’au dossier **FilterFunction**, puis cliquez sur **Sélectionner dossier** pour ouvrir le projet dans Visual Studio Code.
4. Dans l’Explorateur de Visual Studio Code, cliquez sur le dossier **EdgeHubTrigger-Csharp**, puis sur le fichier **run.csx** pour l’ouvrir.
5. Ajoutez l’instruction suivante après l’instruction `#r "Microsoft.Azure.Devices.Client"` :

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. Ajoutez les instructions suivantes après les instructions existantes `using` :

    ```csharp
    using Newtonsoft.Json;
    ```

1. Ajoutez les classes suivantes. Ces classes définissent le schéma attendu pour le corps des messages entrants.

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

1. Remplacez le corps de la méthode **Run** par le code suivant. Il filtre les messages en fonction de la valeur de température dans le corps du message et la valeur du seuil de température.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. Enregistrez le fichier .

## <a name="publish-a-docker-image"></a>Publier une image Docker

1. Créez l’image Docker.
    1. Dans l’explorateur de Visual Studio Code, cliquez sur le dossier **Docker** pour l’ouvrir. Puis sélectionnez le dossier pour votre plateforme de conteneur, soit **linux-x64** soit **windows-nano**. 
    2. Cliquez avec le bouton droit sur le fichier **Dockerfile** et cliquez sur **Créer image Docker du module IoT Edge**. 
    3. Dans la zone **Sélectionner un dossier**, accédez au dossier du projet, **FilterFunction**, puis cliquez sur **Sélectionner un dossier en tant que EXE_DIR**. 
    4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple, `<docker registry address>/filterfunction:latest` ; où *Adresse du registre docker* est votre ID Docker si vous utilisez Docker Hub, ou est semblable à `<your registry name>.azurecr.io`, si vous utilisez Azure Container Registry.
 
4. Connectez-vous à Docker. Dans le terminal intégré, entrez la commande suivante : 

    - Docker Hub (à l’invite, entrez vos informations d’identification) :
        
        ```csh/sh
        docker login
        ```

    - Azure Container Registry :
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Pour trouver le nom d’utilisateur, le serveur de connexion et le mot de passe à utiliser dans cette commande, accédez au [portail Azure] (https://portal.azure.com). À partir de **Toutes les ressources**, cliquez sur la vignette de votre Azure Container Registry pour ouvrir ses propriétés, puis cliquez sur **Clés d’accès**. Copiez les valeurs dans les champs **Nom d’utilisateur**, **Mot de passe** et **Serveur de connexion**. Le serveur de connexion doit être de la forme : `<your registry name>.azurecr.io`.

3. Distribuez l’image vers votre référentiel Docker. Utilisez la commande de menu **Affichage | Palette de commandes... | Edge : Envoyer image Docker de module IoT Edge** et entrez le nom de l’image dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code. Utilisez le même nom d’image que vous avez utilisé à l’étape 1.c.

## <a name="add-registry-credentials-to-your-edge-device"></a>Ajouter des informations d’identification du registre à votre appareil Edge
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

1. Accédez à votre IoT Hub dans le **portail Azure**.
2. Accédez à **IoT Edge (version préliminaire)** et sélectionnez votre appareil IoT Edge.
1. Sélectionnez **Définir modules**. 
2. Ajoutez le module **tempSensor**. Cette étape est uniquement requise si vous n’avez pas encore déployé le module **tempSensor** sur votre appareil IoT Edge.
    1. Sélectionnez **Ajouter module IoT Edge**.
    2. Dans le champ **Nom**, entrez `tempSensor`.
    3. Dans le champ **URI de l’image**, entrez `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Laissez les autres paramètres inchangés et cliquez sur **Enregistrer**.
1. Ajoutez le module **filterfunction**.
    1. Sélectionnez à nouveau **Ajouter module IoT Edge**.
    2. Dans le champ **Nom**, entrez `filterfunction`.
    3. Dans le champ **Image**, saisissez l’adresse de votre image, par exemple `<docker registry address>/filterfunction:latest`.
    74. Cliquez sur **Enregistrer**.
2. Cliquez sur **Suivant**.
3. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Les modules publient tous les messages sur le runtime Edge. Des règles déclaratives dans le runtime définissent où ces messages circulent. Pour ce didacticiel, vous avez besoin de deux itinéraires. Le premier itinéraire transporte les messages du capteur de température au module de filtre via le point de terminaison « input1 », qui est le point de terminaison que vous avez configuré avec le gestionnaire **FilterMessages**. Le deuxième itinéraire transporte les messages du module de filtre à IoT Hub. Dans cet itinéraire, `upstream` est une destination spéciale qui indique à Edge Hub d’envoyer les messages à IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Cliquez sur **Suivant**.
5. À l’étape **Vérifier le modèle**, cliquez sur **Envoyer**. 
6. Revenez à la page de détails de l’appareil IoT Edge et cliquez sur **Actualiser**. Vous devez voir le nouveau module **filterfunction** en cours d’exécution avec le module **tempSensor** et le **runtime IoT Edge**. 

## <a name="view-generated-data"></a>Afficher les données générées

Pour pouvoir surveiller les messages appareil vers cloud envoyés par votre appareil IoT Edge à votre IoT Hub :
1. Configurez l’extension Azure IoT Toolkit avec la chaîne de connexion pour votre IoT hub : 
    1. Utilisez la commande de menu **Affichage | Explorateur** pour ouvrir l’explorateur Visual Studio Code. 
    3. Dans l’explorateur, cliquez sur **IOT HUB DEVICES**, puis cliquez sur **...**. Cliquez sur **Définir la chaîne de connexion IoT Hub** et entrez la chaîne de connexion pour l’IoT Hub auquel se connecte votre appareil IoT Edge dans la fenêtre contextuelle. 

        Pour rechercher la chaîne de connexion, cliquez sur la vignette de votre IoT Hub dans le portail Azure, puis sur **Stratégies d’accès partagé**. Dans le panneau **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis copiez la chaîne de connexion IoT Hub dans la fenêtre **iothubowner**.   

1. Pour surveiller les données reçues par IoT Hub, utilisez la commande de menu **Affichage | Palette de commandes... | IoT : Démarrer l’analyse du message D2C**. 
2. Pour arrêter de surveiller les données, utilisez la commande de menu **Affichage | Palette de commandes... | IoT : Arrêter l’analyse du message D2C**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une fonction Azure qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Pour continuer l’exploration d’Azure IoT Edge, apprenez à utiliser un appareil IoT Edge en tant que passerelle. 

> [!div class="nextstepaction"]
> [Créer un périphérique de passerelle IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
