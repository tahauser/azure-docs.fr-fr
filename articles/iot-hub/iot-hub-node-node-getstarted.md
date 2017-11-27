---
title: "Prise en main d’Azure IoT Hub (Node) | Microsoft Docs"
description: "Découvrez comment envoyer des messages appareil-vers-cloud à Azure IoT Hub à l’aide des kits SDK IoT pour Node.js. Créez un appareil simulé et des applications de service pour inscrire votre appareil, envoyer des messages et lire des messages d’IoT Hub."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b360d5a08abed7d65d8c39f7a957412656f7825
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>Connexion du périphérique simulé à votre hub IoT à l’aide du nœud

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous disposez de trois applications console Node.js :

* **CreateDeviceIdentity.js**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre application d’appareil simulé ;
* **ReadDEviceToCloudMessages.js**, qui affiche les données de télémétrie envoyées par votre application d’appareil simulé ;
* **SimulatedDevice.js**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole MQTT.

> [!NOTE]
> L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks] fournit des informations sur les kits de développement logiciel Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 4.0.x ou version ultérieure.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Votre IoT Hub est maintenant créé. Vous disposez du nom d’hôte et de la chaîne de connexion de l’IoT Hub dont vous avez besoin pour effectuer la suite du didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité d’appareil

Dans cette section, vous créez une application console Node.js qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil peut uniquement se connecter à IoT Hub s’il possède une entrée dans le registre des identités. Reportez-vous à la section **Registre d’identité** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Exécutez cette application pour générer l’ID d’appareil unique et la clé que votre appareil utilise pour s’identifier lorsqu’il envoie des messages de l’appareil au cloud.

1. Créez un dossier vide appelé `createdeviceidentity`. Dans le dossier `createdeviceidentity`, créez un fichier package.json en saisissant la commande suivante dans votre invite de commandes. Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

2. Dans votre invite de commandes, dans le dossier `createdeviceidentity`, exécutez la commande suivante pour installer le package SDK du service `azure-iothub` :

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **CreateDeviceIdentity.js** dans le dossier `createdeviceidentity`.

4. Ajoutez l’instruction `require` ci-dessous au début du fichier **CreateDeviceIdentity.js** :

    ```nodejs
    'use strict';

    var iothub = require('azure-iothub');
    ```

5. Ajoutez le code suivant au fichier **CreateDeviceIdentity.js**. Remplacez la valeur d’espace réservé avec la chaîne de connexion IoT Hub correspondant au concentrateur créé dans la section précédente :

    ```nodejs
    var connectionString = '{iothub connection string}';

    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Ajoutez le code suivant pour créer une définition d’appareil dans le registre des identités de votre IoT Hub. Ce code crée un appareil si l’ID correspondant n’existe pas dans le registre des identités. Dans le cas contraire, il retourne la clé de l’appareil existant :

    ```nodejs
    var device = {
      deviceId: 'myFirstNodeDevice'
    }
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

7. Enregistrez et fermez le fichier **CreateDeviceIdentity.js** .

8. Pour exécuter l’application `createdeviceidentity`, exécutez la commande suivante à l’aide de l’invite de commandes dans le dossier `createdeviceidentity` :

    ```cmd/sh
    node CreateDeviceIdentity.js 
    ```

9. Prenez note de **l’ID de l’appareil** et de la **clé de l’appareil**. Vous aurez besoin de ces valeurs ultérieurement lorsque vous créerez une application qui se connecte à IoT Hub en tant qu’appareil.

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID et les clés des appareils à utiliser comme informations d’identification de sécurité, et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity].

<a id="D2C_node"></a>
## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages appareil-à-cloud

Dans cette section, vous créez une application console Node.js qui lit les messages appareil-à-cloud à partir d’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-process-d2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel de [prise en main d’Event Hubs][lnk-eventhubs-tutorial] fournit des informations complémentaires applicables aux points de terminaison compatibles avec les concentrateurs d’événements IoT Hub.

> [!NOTE]
> Le point de terminaison compatible Event Hub pour lire des messages de l’appareil vers le cloud utilise toujours le protocole AMQP.

1. Créez un dossier vide appelé `readdevicetocloudmessages`. Dans le dossier `readdevicetocloudmessages`, créez un fichier package.json en saisissant la commande suivante dans votre invite de commandes. Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

2. À l’aide de votre invite de commandes, dans le dossier `readdevicetocloudmessages`, exécutez la commande suivante pour installer le package **azure-event-hubs** :

    ```cmd/sh
    npm install azure-event-hubs --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **ReadDeviceToCloudMessages.js** dans le dossier `readdevicetocloudmessages`.

4. Ajoutez les instructions `require` ci-dessous au début du fichier **ReadDeviceToCloudMessages.js** :

    ```nodejs
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Ajoutez la déclaration de variable suivante et remplacez la valeur de l’espace réservé par la chaîne de connexion IoT Hub pour votre hub :

    ```nodejs
    var connectionString = '{iothub connection string}';
    ```

6. Ajoutez les deux fonctions suivantes qui impriment la sortie sur la console :

    ```nodejs
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Ajoutez le code ci-dessous pour créer l’instance **EventHubClient**, ouvrez la connexion à votre IoT Hub et créez un récepteur pour chaque partition. Cette application utilise un filtre lorsqu’elle crée un récepteur afin que ce dernier lise uniquement les messages envoyés à l’IoT Hub une fois que le récepteur a commencé à s’exécuter. Dans un environnement de test, ce filtre permet de voir seulement l’ensemble des messages en cours. Dans un environnement de production, votre code doit faire en sorte de traiter tous les messages. Pour plus d’informations, voir le didacticiel [Traitement des messages appareil-à-cloud IoT Hub][lnk-process-d2c-tutorial] :

    ```nodejs
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Enregistrez et fermez le fichier **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui simule un appareil envoyant des messages appareil-à-cloud à un IoT Hub.

1. Créez un dossier vide appelé `simulateddevice`. Dans le dossier `simulateddevice`, créez un fichier package.json en saisissant la commande suivante dans votre invite de commandes. Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

2. Dans l’invite de commandes du dossier `simulateddevice`, exécutez la commande suivante pour installer le package SDK d’appareil **azure-iot-device** et le package **azure-iot-device-mqtt** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **SimulatedDevice.js** dans le dossier `simulateddevice`.

4. Ajoutez les instructions `require` ci-dessous au début du fichier **SimulatedDevice.js** :

    ```nodejs
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Ajoutez une variable `connectionString` et utilisez-la pour créer une instance **Client**. Remplacez `{youriothostname}` par le nom du IoT Hub que vous avez créé à la section *Créer un hub IoT*. Remplacez `{yourdevicekey}` par la valeur de clé d’appareil que vous avez générée dans la section *Création d’une identité d’appareil* :

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';

    var client = clientFromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour afficher la sortie de l’application :

    ```nodejs
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Créez un rappel et utilisez la fonction **setInterval** pour envoyer un message à votre IoT Hub toutes les secondes :

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. Ouvrez la connexion à votre IoT Hub et commencez à envoyer des messages :

    ```nodejs
    client.open(connectCallback);
    ```

9. Enregistrez et fermez le fichier **SimulatedDevice.js** .

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’aide de l’invite de commandes, dans le dossier `readdevicetocloudmessages`, exécutez la commande suivante pour commencer l’analyse de votre concentrateur IoT Hub :

    ```cmd/sh
    node ReadDeviceToCloudMessages.js 
    ```

    ![Application Node.js du service IoT Hub pour surveiller les messages appareil-à-cloud][7]

2. À l’aide de l’invite de commande, dans le dossier `simulateddevice`, exécutez la commande suivante pour commencer à envoyer des données de télémétrie vers votre concentrateur IoT :

    ```cmd/sh
    node SimulatedDevice.js
    ```

    ![Application Node.js de l’appareil IoT Hub pour envoyer les messages appareil-à-cloud][8]

3. La vignette **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au IoT Hub :

    ![Vignette Utilisation du portail Azure indiquant le nombre de messages envoyés à IoT Hub][43]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre des identités de l’IoT Hub. Vous avez utilisé cette identité d’appareil pour permettre à l’appareil simulé d’envoyer des messages Appareil vers cloud à l’IoT Hub. Vous avez également créé une application qui affiche les messages reçus par l’IoT Hub.

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Connexion de votre appareil][lnk-connect-device]
* [Prise en main de la gestion d’appareils][lnk-device-management]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iot-edge]

Pour découvrir comment étendre votre solution IoT et traiter les messages appareil-à-cloud à grande échelle, consultez le didacticiel [Traitement des messages appareil-à-cloud][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
