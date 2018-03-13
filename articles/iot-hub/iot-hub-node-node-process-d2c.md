---
title: Routage des messages avec Azure IoT Hub (Node) | Microsoft Docs
description: "Comment traiter les messages appareil-à-cloud Azure IoT Hub en utilisant les règles de routage et les points de terminaison personnalisés pour distribuer les messages vers d’autres services principaux."
services: iot-hub
documentationcenter: node
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo
ms.openlocfilehash: f314d24250330a4dadf99d98b94c98b3db03f22c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>Routage des messages avec IoT Hub (Node)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Ce didacticiel s’appuie sur celui relatif à la [prise en main du IoT Hub].  Le didacticiel :

* Explique comment utiliser les règles de routage pour dispatcher les messages de l’appareil au cloud suivant une configuration facile.
* Indique comment isoler les messages interactifs qui nécessitent une action immédiate du serveur principal de la solution pour un traitement ultérieur.  Par exemple, un appareil peut envoyer un message d’alerte qui déclenche l’insertion d’un ticket dans un système CRM.  Par opposition, les messages de point de données tels que la télémétrie de température sont chargés dans un moteur d’analyse.

À la fin de ce didacticiel, vous exécutez trois applications console Node.js :

* **SimulatedDevice.js**, une version modifiée de l’application créée dans le didacticiel [prise en main du IoT Hub], qui envoie des messages appareil-à-cloud de point de données chaque seconde, et des messages appareil-à-cloud interactifs à intervalle aléatoire. Cette application utilise le protocole MQTT pour communiquer avec IoT Hub.
* **ReadDeviceToCloudMessages.js**, qui affiche les données de télémétrie envoyées par votre application d’appareil.
* **ReadCriticalQueue.js**, qui enlève les messages critiques de la file d’attente Service Bus attachée au hub IoT.

> [!NOTE]
> IoT Hub offre la prise en charge de Kit de développement logiciel (SDK) de plusieurs plateformes d’appareils et de plusieurs langages (notamment C, Java et JavaScript). Pour obtenir des instructions expliquant comment remplacer l’appareil de ce didacticiel par un appareil physique et comment connecter vos appareils à un hub IoT, consultez le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Une version d’utilisation complète du didacticiel [prise en main du IoT Hub] .
* Node.js version 4.0.x ou version ultérieure.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Nous recommandons également la lecture de [Stockage Azure] et [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Envoyer des messages interactifs à partir d’une application pour appareils
Dans cette section, vous allez modifier l’application pour appareils que vous avez créée dans le didacticiel [prise en main du IoT Hub] de façon à envoyer occasionnellement des messages nécessitant un traitement immédiat.

1. Utilisez un éditeur de texte pour ouvrir le fichier **simulateddevice\SimulatedDevice.js**. Ce fichier contient le code pour l’application **SimulatedDevice** que vous avez créée dans le didacticiel [prise en main du IoT Hub] .

2. Remplacez la fonction **connectCallback** par le code suivant :

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    Cette méthode ajoute de façon aléatoire la propriété `"level": "critical"` et `"level": "storage"` aux messages envoyés par l’appareil, ce qui simule un message nécessitant une action immédiate du serveur principal de l’application, ou demandant à être définitivement stocké. L’application prend en charge le routage des messages en fonction de leur corps.
   
   > [!NOTE]
   > Vous pouvez utiliser les propriétés de message pour acheminer les messages pour de nombreux scénarios, tels que le traitement de chemin d’accès à froid, en plus de l’exemple de chemin d’accès à chaud présenté ici.

2. Enregistrez et fermez le fichier **simulateddevice\SimulatedDevice.js**.

    > [!NOTE]
    > Nous vous recommandons vivement de mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] (Gestion des erreurs temporaires).

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>Ajouter la file d’attente Service Bus à votre hub IoT et y acheminer des messages

Dans cette section, vous allez créer une file d’attente Service Bus, la connecter à votre IoT Hub et configurer votre IoT hub pour envoyer des messages à la file d’attente en fonction de la présence d’une propriété sur le message. Pour plus d’informations sur la façon de traiter les messages des files d’attente Service Bus, consultez [Prise en main des files d’attente][lnk-sb-queues-node].

1. Créez une file d’attente Service Bus, comme décrit dans [Prise en main des files d’attente][lnk-sb-queues-node]. Prenez note de l’espace de noms et de la file d’attente.

    > [!NOTE]
    > Les options **Sessions** ou **Détection des doublons** ne doivent pas être activées pour les files d’attente et rubriques Service Bus utilisées comme points de terminaison IoT Hub. Si l’une de ces options est activée, le point de terminaison s’affiche comme **Inaccessible** dans le portail Azure.

2. Dans le portail Azure, ouvrez votre IoT Hub, puis cliquez sur **Points de terminaison**.

    ![Points de terminaison dans IoT hub][30]

3. En haut du panneau **Points de terminaison**, cliquez sur **Ajouter** pour ajouter votre file d’attente à votre IoT Hub. Nommez le point de terminaison **CriticalQueue**, puis utilisez les listes déroulantes pour sélectionner **File d’attente Service Bus**, l’espace de noms Service Bus dans lequel réside votre file d’attente, ainsi que le nom de votre file d’attente. Lorsque vous avez terminé, cliquez sur **OK** en bas.  

    ![Ajout d’un point de terminaison][31]

4. À présent, cliquez sur **Itinéraires** dans votre IoT Hub. En haut du panneau, cliquez sur **Ajouter** afin de créer une règle qui achemine les messages vers la file d’attente que vous venez d’ajouter. Sélectionnez **Messages des appareils** comme source de données. Indiquez `level="critical"` comme condition, puis sélectionnez **CriticalQueue** comme point de terminaison personnalisé en tant que point de terminaison de la règle de routage. Cliquez sur **Enregistrer** au bas de la page.  

    ![Ajout d’un itinéraire][32]

    Assurez-vous que l’itinéraire de secours est défini sur **ACTIVÉ**. Ce paramètre correspond à la configuration par défaut d’un IoT Hub.

    ![Itinéraire de secours][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Facultatif) Lecture à partir du point de terminaison de la file d’attente

Dans cette section, vous créez une application console Node.js qui lit les messages critiques à partir du Service Bus IoT. Consultez des informations complémentaires dans [Bien démarrer avec les files d’attente][lnk-sb-queues-node]. 

1. Créez un dossier vide appelé `readcriticalqueue`. Dans le dossier `readcriticalqueue`, créez un fichier package.json en saisissant la commande suivante dans votre invite de commandes. Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

2. Depuis votre invite de commandes, dans le dossier `readcriticalqueue`, exécutez la commande suivante pour installer le package **azure** :

    ```cmd/sh
    npm install azure --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **ReadCriticalQueue.js** dans le dossier `readcriticalqueue`.

4. Ajoutez au début du fichier **ReadCriticalQueue.js** les instructions `require` ci-dessous :

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. Ajoutez la déclaration de variable suivante et remplacez la valeur de l’espace réservé par la chaîne de connexion et le nom de file d’attente de Service Bus IoT :

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. Ajoutez les deux fonctions suivantes qui impriment la sortie sur la console :

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. Enregistrez et fermez le fichier **ReadCriticalQueue.js**.

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les trois applications.

1. Pour exécuter l’application **ReadDeviceToCloudMessages.js**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier readdevicetocloudmessages et exécutez la commande suivante :

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![Exécution de read-d2c-messages][readd2c]

2. Pour exécuter l’application **ReadCriticalQueue.js**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier readcriticalqueue et exécutez la commande suivante :

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![Exécution de read-critical-messages][readqueue]

3. Pour exécuter l’application **SimulatedDevice.js**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier simulateddevice, puis exécutez la commande suivante :

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![Exécuter simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Facultatif) Ajouter un conteneur de stockage à votre hub IoT et y acheminer des messages

Dans cette section, vous créez un compte de stockage, vous le connectez à votre hub IoT que vous configurez ensuite pour envoyer des messages au compte, en fonction de la présence d’une propriété sur le message. Pour plus d’informations sur la gestion du stockage, consultez [Bien démarrer avec Stockage Azure][Stockage Azure].

 > [!NOTE]
   > Si vous n’êtes pas limité à un **Point de terminaison**, vous pouvez configurer **StorageContainer** en plus de **CriticalQueue** et exécuter les deux simultanément.

1. Créez un compte de stockage, comme décrit dans la [Documentation Stockage Azure][lnk-storage]. Notez le nom du compte.

2. Dans le portail Azure, ouvrez votre IoT Hub, puis cliquez sur **Points de terminaison**.

3. Dans le panneau **Points de terminaison**, sélectionnez le point de terminaison **CriticalQueue** et cliquez sur **Supprimer**. Cliquez sur **Oui**, puis sur **Ajouter**. Nommez le point de terminaison **StorageContainer** et utilisez les listes déroulantes pour sélectionner **Conteneur de stockage Azure** et créer un **Compte de stockage** et un **Conteneur de stockage**.  Notez les noms.  Lorsque vous avez terminé, cliquez sur **OK** en bas. 

 > [!NOTE]
   > Si vous n’êtes pas limité à un **Point de terminaison**, vous n’avez pas besoin de supprimer **CriticalQueue**.

4. Cliquez sur **Itinéraires** dans votre hub IoT. En haut du panneau, cliquez sur **Ajouter** afin de créer une règle qui achemine les messages vers la file d’attente que vous venez d’ajouter. Sélectionnez **Messages des appareils** comme source de données. Indiquez `level="storage"` comme condition, puis sélectionnez **StorageContainer** comme point de terminaison personnalisé en tant que point de terminaison de la règle de routage. Cliquez sur **Enregistrer** au bas de la page.  

    Assurez-vous que l’itinéraire de secours est défini sur **ACTIVÉ**. Ce paramètre correspond à la configuration par défaut d’un IoT Hub.

1. Assurez-vous que votre précédente application **SimulatedDevice.js** est toujours en cours d’exécution. 

1. Dans le Portail Azure, accédez à votre compte de stockage, sous **Service BLOB**, cliquez sur **Parcourir les objets blob...**   Sélectionnez votre conteneur, accédez au fichier JSON et cliquez dessus, puis cliquez sur **Télécharger** pour afficher les données.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à répartir de façon fiable des messages appareil-à-cloud à l’aide de la fonctionnalité d’acheminement de messages d’IoT Hub.

Le didacticiel [Envoi de messages cloud-à-appareil avec IoT Hub][lnk-c2d] vous montre comment envoyer des messages à vos appareils à partir de votre serveur principal de solution.

Pour des exemples de solutions de bout en bout qui utilisent IoT Hub, voir [Azure IoT Suite][lnk-suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

Pour en savoir plus sur le routage des messages dans IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[prise en main du IoT Hub]: iot-hub-node-node-getstarted.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Gestion des erreurs temporaires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/