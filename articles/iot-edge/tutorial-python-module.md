---
title: Module Azure IoT Edge Python | Microsoft Docs
description: Créer un module IoT Edge avec du code Python et le déployer sur un appareil de périphérie
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d5bad277e6a54b23f0e3ef7321e82d212ae885d3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Développer et déployer un module Python IoT Edge sur votre appareil simulé – Aperçu

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce didacticiel vous guide dans la création et le déploiement d’un module IoT Edge qui filtre des données de capteur. Vous utilisez l’appareil simulé IoT Edge que vous avez créé dans les didacticiels Déployer Azure IoT Edge sur un appareil simulé dans [Windows][lnk-tutorial1-win] et [Linux][lnk-tutorial1-lin]. Ce tutoriel vous montre comment effectuer les opérations suivantes :    

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer un module IoT Edge Python
> * Utiliser le Visual Studio Code et Docker pour créer une image docker et la publier dans votre registre 
> * Déployer le module sur votre appareil IoT Edge
> * Afficher les données générées


Le module IoT Edge que vous créez dans ce didacticiel filtre les données de température générées par votre appareil. Il envoie uniquement des messages en amont lorsque la température dépasse un seuil spécifié. Ce type d’analyse à la périphérie est utile pour réduire la quantité de données communiquées et stockées dans le cloud. 

> [!IMPORTANT]
> Actuellement le module Python ne peut être exécutés que dans des conteneurs Linux amd64. L’exécution dans des conteneurs Windows ou dans des conteneurs basés sur ARM est impossible. 

## <a name="prerequisites"></a>Prérequis


* L’appareil Azure IoT Edge que vous avez créé dans le démarrage rapide ou le premier didacticiel.
* Chaîne de connexion de clé primaire de l’appareil IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Extension Python pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) sur le même ordinateur que celui sur lequel Visual Studio Code est installé. L’édition Community (CE) est suffisante pour ce didacticiel. 
* [Python](https://www.python.org/downloads/)
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) pour l’installation des packages Python.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur
Dans ce didacticiel, utilisez l’extension Azure IoT Edge pour Visual Studio Code afin de créer un module et de créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.  

Vous pouvez utiliser n’importe quel registre Docker compatible pour ce didacticiel. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker populaires disponibles dans le cloud. Ce didacticiel utilise Azure Container Registry. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.
2. Donnez un nom à votre registre, choisissez un abonnement et un groupe de ressources, puis définissez la référence (SKU) sur la valeur **De base**. 
3. Sélectionnez **Créer**.
4. Une fois que votre registre de conteneurs est créé, accédez à celui-ci, puis sélectionnez **Clés d’accès**. 
5. Basculez **Utilisateur administrateur** sur **Activer**.
6. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**. Vous utiliserez ces valeurs plus loin dans le didacticiel. 

## <a name="create-an-iot-edge-module-project"></a>Créer un projet de module IoT Edge
Les étapes suivantes vous montrent comment créer un module IoT Edge Python à l’aide de Visual Studio Code et de l’extension Azure IoT Edge.
1. Dans Visual Studio Code, sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.
2. Dans le terminal intégré, entrez la commande suivante pour installer (ou mettre à jour) le **cookiecutter** :

    ```cmd/sh
    pip install -U cookiecutter
    ```

3. Créez un projet pour le nouveau module. La commande suivante crée le dossier du projet, **FilterModule**, avec le référentiel du conteneur. Le paramètre de `image_repository` doit être au format `<your container registry name>.azurecr.io/filtermodule` si vous utilisez le registre de conteneurs Azure. Dans le dossier de travail actif, entrez la commande suivante :

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Sélectionnez **Fichier** > **Ouvrir un dossier**.
5. Naviguez jusqu’au dossier **FilterModule**, puis cliquez sur **Sélectionner un dossier** pour ouvrir le projet dans VS Code.
6. Dans l’explorateur de VS Code, cliquez sur **main.py** pour l’ouvrir.
7. En haut de l’espace de noms **FilterModule**, importez la bibliothèque `json` :

    ```python
    import json
    ```

8. Ajoutez le `TEMPERATURE_THRESHOLD` et les `TWIN_CALLBACKS` sous l’ensemble des compteurs. Le seuil de température définit la valeur que la température mesurée doit dépasser pour que les données soient envoyées à IoT Hub.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

9. Mettez à jour la fonction `receive_message_callback` avec le contenu ci-dessous.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Ajoutez une nouvelle fonction `device_twin_callback`. Cette fonction sera appelée après la mise à jour des propriétés souhaitées.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

11. Dans la classe `HubManager`, ajoutez une nouvelle ligne à la méthode `__init__` pour initialiser la fonction `device_twin_callback` que vous venez d’ajouter.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Enregistrez ce fichier.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Créer une image Docker et la publier dans votre registre

1. Connectez-vous à Docker en entrant la commande suivante dans le terminal intégré VS Code : 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Utilisez le nom d’utilisateur, le mot de passe et le serveur de connexion que vous avez copiés à partir de votre registre de conteneurs Azure lors de sa création.

2. Dans l’explorateur VS Code, cliquez avec le bouton droit sur le fichier **module.json**, puis sur **Générer et envoyer (push) une image Docker de module IoT Edge**. Dans la liste déroulante contextuelle en haut de la fenêtre VS Code, sélectionnez votre plateforme de conteneur, par exemple **amd64** pour un conteneur Linux. VS Code met en conteneur le fichier `main.py` avec les dépendances requises, puis l’envoie dans le registre de conteneurs spécifié. La génération de l’image peut prendre plusieurs minutes lors de la première exécution.

3. Vous pouvez récupérer l’adresse complète de l’image conteneur avec la balise dans le terminal intégré de VS Code. Pour plus d’informations sur la définition de build et de push, consultez le fichier `module.json`.

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
    3. Dans le champ **URI de l’image**, saisissez l’adresse de votre image, par exemple `<your container registry address>/filtermodule:0.0.1-amd64`. Vous trouverez l’adresse complète de l’image dans la section précédente.
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
10. Cliquez sur **Suivant**.
11. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Les modules publient tous les messages sur le runtime Edge. Des règles déclaratives dans le runtime définissent où les messages circulent. Pour ce didacticiel, vous avez besoin de deux itinéraires. Le premier itinéraire transporte les messages du capteur de température au module de filtre via le point de terminaison « input1 », qui est le point de terminaison que vous avez configuré avec le gestionnaire **FilterMessages**. Le deuxième itinéraire transporte les messages du module de filtre à IoT Hub. Dans cet itinéraire, `upstream` est une destination spéciale qui indique à Edge Hub d’envoyer les messages à IoT Hub. 

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
