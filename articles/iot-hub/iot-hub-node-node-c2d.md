---
title: Messages cloud-à-appareil avec Azure IoT Hub (Node) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour Node.js. Vous modifiez une application d’appareil simulé pour recevoir des messages cloud-à-appareil et modifiez une application principale pour envoyer des messages cloud-à-appareil.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 526fdce85df388fec04dd5df35183200c1478570
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Envoi de messages cloud à appareil avec IoT Hub (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction
Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Le didacticiel [Prise en main d’Azure IoT Hub] explique comment créer un concentrateur IoT, l’utiliser pour configurer une identité d’appareil et coder une simulation d’application d’appareil qui envoie des messages d’appareils à cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel s’appuie sur l’article [Prise en main d’Azure IoT Hub]. Cette rubrique vous explique les procédures suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.
* Recevez des messages cloud-à-appareil sur un appareil.
* À partir du serveur principal de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages du cloud vers les appareils dans le [Guide du développeur d’IoT Hub][IoT Hub developer guide - C2D].

À la fin de ce didacticiel, vous exécuterez deux applications de console Node.js :

* **SimulatedDevice**, une version modifiée de l’application créée dans [Prise en main d’Azure IoT Hub]qui se connecte à votre hub IoT et reçoit les messages entre cloud et appareils.
* **SendCloudToDeviceMessage**, qui envoie un message cloud-à-appareil à l’application pour appareil simulée par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel, et à Azure IoT Hub de manière générale, consultez le [Centre de développement Azure IoT].
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 4.0.x ou version ultérieure.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Recevoir des messages dans l’application d’appareil simulé
Dans cette section, vous modifiez l’application pour appareil simulée que vous avez créée dans [Prise en main d’Azure IoT Hub] pour recevoir des messages cloud-à-appareil à partir d’IoT Hub.

1. À l’aide d’un éditeur de texte, ouvrez le fichier SimulatedDevice.js.
2. Modifiez la fonction **connectCallback** pour gérer les messages envoyés à partir d’IoT Hub. Dans cet exemple, l’appareil appelle toujours la fonction **complete** afin de notifier IoT Hub qu’il a traité le message. La nouvelle version de la fonction **connectCallback** ressemble à l’extrait de code suivant :
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
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
   
   > [!NOTE]
   > Si vous utilisez HTTPS au lieu de MQTT ou d’AMQP comme moyen de transport, l’instance **DeviceClient** vérifie les messages à partir d’IoT Hub peu fréquemment (moins de toutes les 25 minutes). Pour plus d’informations sur les différences entre la prise en charge de MQTT, d’AMQP et de HTTPS et la limitation d’IoT Hub, voir le [Guide du développeur IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil
Dans cette section, vous allez créer une application de console Node.js qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé. Vous avez besoin de l’ID de l’appareil que vous avez ajouté dans le didacticiel [Prise en main d’Azure IoT Hub] . Vous avez également besoin de la chaîne de connexion pour votre hub que vous trouverez dans le [portail Azure].

1. Créez un dossier vide appelé **sendcloudtodevicemessage**. Dans le dossier **sendcloudtodevicemessage** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
    ```shell
    npm init
    ```
2. À l’invite de commandes, dans le dossier **sendcloudtodevicemessage**, exécutez la commande suivante pour installer le package **azure-iothub** :
   
    ```shell
    npm install azure-iothub --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **SendCloudToDeviceMessage.js** dans le dossier **sendcloudtodevicemessage**.
4. Ajoutez les instructions `require` ci-dessous au début du fichier **SendCloudToDeviceMessage.js** :
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Ajoutez le code suivant au fichier **SendCloudToDeviceMessage.js** . Remplacez la valeur d’espace réservé « {iot hub connection string} » par la chaîne de connexion pour le IoT Hub créé dans le didacticiel [Prise en main d’Azure IoT Hub]. Remplacez l’espace réservé « {device id} » par l’ID de l’appareil ajouté dans le didacticiel [Prise en main d’Azure IoT Hub] :
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Ajoutez la fonction suivante pour imprimer les résultats de l’opération sur la console :
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Ajoutez la fonction suivante pour imprimer les messages d’accusé de réception sur la console :
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Ajoutez le code suivant pour envoyer un message à votre appareil et gérer le message de commentaires lorsque l’appareil accuse réception d’un message cloud-à-appareil :
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```
9. Enregistrez et fermez le fichier **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier **simulateddevice** , exécutez la commande suivante pour envoyer la télémétrie à IoT hub et écouter les messages cloud-à-appareil :
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Exécution de l’application de périphérique simulé][img-simulated-device]
2. À l’invite de commandes dans le dossier **sendcloudtodevicemessage**, exécutez la commande suivante pour envoyer un message cloud-à-appareil, puis attendez de recevoir l’accusé de réception :
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Exécutez l’application pour envoyer la commande cloud-à-appareil][img-send-command]
   
   > [!NOTE]
   > Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling](Gestion des erreurs temporaires).
   > 
   > 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil. 

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Azure IoT Suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Prise en main d’Azure IoT Hub]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Guide du développeur IoT Hub]: iot-hub-devguide.md
[Centre de développement Azure IoT]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portail Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
