---
title: Déployer une fonction Azure avec Azure IoT Edge | Microsoft Docs
description: Déployer une fonction Azure en tant que module d’un appareil Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: a43ae8f28fc32b61fb5db985ffae98f093293798
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Déployer une fonction Azure en tant que module IoT Edge - version préliminaire
Vous pouvez utiliser Azure Functions pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide à travers la création et le déploiement d’une fonction Azure qui filtre les données de capteur sur l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé pour [Windows][lnk-tutorial1-win]ou [Linux][lnk-tutorial1-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :     

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer une fonction Azure
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


La fonction Azure que vous créez dans ce didacticiel filtre les données de température générées par votre appareil et envoie uniquement des messages en amont vers Azure IoT Hub lorsque la température dépasse un seuil spécifié. 

## <a name="prerequisites"></a>Prérequis


* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide ou le didacticiel précédent.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). L’édition Community (CE) pour votre plateforme est suffisante pour ce didacticiel. 
* [Kit de développement logiciel (SDK) .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce didacticiel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.
2. Donnez un nom à votre registre, choisissez un abonnement et un groupe de ressources, puis définissez la référence (SKU) sur la valeur **De base**. 
3. Sélectionnez **Créer**.
4. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
5. Basculez **Utilisateur administrateur** sur **Activer**.
6. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans le didacticiel. 

## <a name="create-a-function-project"></a>Créer un projet Function
Les étapes suivantes vous montrent comment créer une fonction IoT Edge à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.
1. Ouvrez Visual Studio Code.
2. Pour ouvrir le terminal intégré Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré**.
3. Pour installer (ou mettre à jour) le modèle **AzureIoTEdgeFunction** dans dotnet, exécutez la commande suivante dans le terminal intégré :

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Créez un projet pour le nouveau module. La commande suivante crée le dossier du projet, **FilterFunction**, dans le dossier de travail en cours :

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

3. Sélectionnez **fichier** > **Ouvrir le dossier**, puis accédez au dossier **FilterFunction** et ouvrez le projet dans Visual Studio Code.
4. Dans l’Explorateur de Visual Studio Code, développez le dossier **EdgeHubTrigger-Csharp**, puis ouvrez le fichier **run.csx**.
5. Remplacez le contenu du fichier par le code suivant :

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

11. Enregistrez le fichier .

## <a name="publish-a-docker-image"></a>Publier une image Docker

1. Créez l’image Docker.
    1. Dans l’explorateur de Visual Studio Code, développez le dossier **Docker**. Développez le dossier correspondant à votre plateforme de conteneur, soit **linux-x64** soit **windows-nano**. 
    2. Cliquez avec le bouton droit sur le fichier **Dockerfile** et cliquez sur **Créer image Docker du module IoT Edge**. 
    3. Accédez au dossier du projet **FilterFunction**, puis cliquez sur **Sélectionner un dossier en tant que EXE_DIR**. 
    4. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filterfunction:latest`. L’adresse du registre de conteneurs est la même que celle du serveur de connexion que vous avez copiée à partir de votre registre. Elle doit respecter le format `<your container registry name>.azurecr.io`.
 
4. Connectez-vous à Docker. Dans le terminal intégré, entrez la commande suivante : 

   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Pour trouver le nom d’utilisateur, le serveur de connexion et le mot de passe à utiliser dans cette commande, accédez au [portail Azure] (https://portal.azure.com)). À partir de **Toutes les ressources**, cliquez sur la vignette de votre Azure Container Registry pour ouvrir ses propriétés, puis cliquez sur **Clés d’accès**. Copiez les valeurs dans les champs **Nom d’utilisateur**, **Mot de passe** et **Serveur de connexion**. 

3. Distribuez l’image vers votre référentiel Docker. Sélectionnez **Affichage** > **Palette de commandes**, puis recherchez **Edge: Push IoT Edge module Docker image**.
4. Dans la zone de texte contextuelle, entrez le même nom d’image que celui utilisé à l’étape 1.d.

## <a name="add-registry-credentials-to-your-edge-device"></a>Ajouter des informations d’identification du registre à votre appareil Edge
Ajoutez les informations d’identification pour votre registre au runtime Edge sur l’ordinateur sur lequel vous exécutez votre appareil Edge. Cela donne l’accès au runtime pour extraire le conteneur. 

- Pour Windows, exécutez la commande ci-dessous :
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Pour Linux, exécutez la commande ci-dessous :
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Exécuter la solution

1. Accédez à votre IoT Hub dans le **portail Azure**.
2. Accédez à **IoT Edge (version préliminaire)** et sélectionnez votre appareil IoT Edge.
1. Sélectionnez **Définir modules**. 
2. Si vous avez déjà déployé le module **tempSensor** sur cet appareil, il a peut être été renseigné automatiquement. Sinon, suivez ces étapes pour l’ajouter :
    1. Sélectionnez **Ajouter un module IoT Edge**.
    2. Dans le champ **Nom**, entrez `tempSensor`.
    3. Dans le champ **URI de l’image**, entrez `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Laissez les autres paramètres inchangés et cliquez sur **Enregistrer**.
1. Ajoutez le module **filterfunction**.
    1. Sélectionnez à nouveau **Ajouter module IoT Edge**.
    2. Dans le champ **Nom**, entrez `filterFunction`.
    3. Dans le champ **URI de l’image**, saisissez l’adresse de votre image, par exemple `<your container registry address>/filtermodule:0.0.1-amd64`. Vous pouvez trouver l’adresse de l’image complète dans la section précédente.
    74. Cliquez sur **Enregistrer**.
2. Cliquez sur **Suivant**.
3. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Le premier itinéraire transporte les messages du capteur de température au module de filtre par le biais du point de terminaison « input1 ». Le deuxième itinéraire transporte les messages du module de filtre à IoT Hub. Dans cet itinéraire, `$upstream` est une destination spéciale qui indique à Edge Hub d’envoyer les messages à IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Cliquez sur **Suivant**.
5. À l’étape **Vérifier le modèle**, cliquez sur **Envoyer**. 
6. Revenez à la page de détails de l’appareil IoT Edge et cliquez sur **Actualiser**. Vous devez voir le nouveau module **filterfunction** en cours d’exécution avec le module **tempSensor** et le **runtime IoT Edge**. 

## <a name="view-generated-data"></a>Afficher les données générées

Pour pouvoir surveiller les messages appareil vers cloud envoyés par votre appareil IoT Edge à votre IoT Hub :
1. Configurez l’extension Azure IoT Toolkit avec la chaîne de connexion pour votre IoT hub : 
    1. Dans le portail Azure, accédez à votre hub IoT et sélectionnez **Stratégies d’accès partagé**. 
    2. Sélectionnez **iothubowner**, puis copiez la valeur de **Clé primaire de la chaîne de connexion**.
    1. Dans l’Explorateur de Visual Studio Code, cliquez sur **APPAREILS IOT HUB**, puis sur **...**. 
    1. Sélectionnez **Définir la chaîne de connexion IoT Hub** et entrez la chaîne de connexion IoT Hub dans la fenêtre contextuelle. 

1. Pour surveiller les données reçues par IoT Hub, sélectionnez **Affichage** > **Palette de commandes** et recherchez **IoT: Start monitoring D2C message**. 
2. Pour arrêter de surveiller les données, utilisez la commande **IoT: Stop monitoring D2C message** dans la palette de commandes. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une fonction Azure qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Pour continuer l’exploration d’Azure IoT Edge, apprenez à utiliser un appareil IoT Edge en tant que passerelle. 

> [!div class="nextstepaction"]
> [Créer un périphérique de passerelle IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
