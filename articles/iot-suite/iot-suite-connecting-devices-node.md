---
title: "Provisionner des appareils pour la surveillance à distance en Node.js - Azure| Microsoft Docs"
description: "Explique comment connecter un périphérique à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite dans Node.js."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: b88ed25e4f434e32423be122569070d896ef7c68
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connexion de votre appareil à la solution préconfigurée de surveillance à distance (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ce didacticiel montre comment connecter un appareil physique à la solution préconfigurée de surveillance à distance. Dans ce didacticiel, vous utilisez Node.js, qui est une bonne option pour les environnements avec des contraintes minimales en ressources.

## <a name="create-a-nodejs-solution"></a>Créer une solution Node.js

Vérifiez que [Node.js](https://nodejs.org/) version 4.0.0 ou ultérieure est installé sur votre ordinateur de développement. Vous pouvez exécuter `node --version` dans la ligne de commande pour vérifier la version.

1. Créez un dossier nommé `RemoteMonitoring` sur votre ordinateur de développement. Accédez à ce dossier dans votre environnement de ligne de commande.

1. Pour télécharger et installer les packages dont vous avez besoin pour accomplir l’exemple d’application, exécutez les commandes suivantes :

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Dans le dossier `RemoteMonitoring`, créez un fichier nommé **remote_monitoring.js**. Ouvrez ce fichier dans un éditeur de texte.

1. Dans le fichier **remote_monitoring.js**, ajoutez les instructions `require` suivantes :

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Ajoutez les déclarations de variables suivantes après les instructions `require` . Remplacez les valeurs d’espace réservé `{Device Id}` et `{Device Key}` par les valeurs que vous avez notées pour l’appareil provisionné dans la solution de surveillance à distance. Utilisez le nom d’hôte IoT Hub de la solution pour remplacer `{IoTHub Name}`. Par exemple, si votre nom d’hôte IoT Hub est `contoso.azure-devices.net`, remplacez `{IoTHub Name}` par `contoso` :

    ```nodejs
    var connectionString = 'HostName={IoTHub Name}.azure-devices.net;DeviceId={Device Id};SharedAccessKey={Device Key}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Pour définir des données de télémétrie de base, ajoutez les variables suivantes :

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Pour définir des valeurs de propriété, ajoutez les variables suivantes :

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    ```

1. Ajoutez la variable suivante pour définir les propriétés déclarées à envoyer à la solution. Ces propriétés incluent les métadonnées décrivant les méthodes et les données de télémétrie utilisées par l’appareil :

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude
    }
    ```

1. Pour imprimer les résultats de l’opération, ajoutez la fonction d’assistance suivante :

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Ajoutez la fonction d’assistance suivante qui permet de rendre aléatoires les valeurs de télémétrie :

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Ajoutez la fonction suivante pour gérer les appels de méthode directe à partir de la solution. La solution utilise des méthodes directes pour agir sur les appareils :

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (!!err) {
          console.error('An error ocurred when sending a method response:\n' +
            err.toString());
        } else {
          console.log('Response to method \'' + request.methodName +
            '\' sent successfully.');
        }
      });
    }
    ```

1. Ajoutez le code suivant pour envoyer les données de télémétrie à la solution. L’application cliente ajoute des propriétés au message pour identifier le schéma du message :

    ```node.js
    function sendTelemetry(data, schema) {
      var d = new Date();
      var payload = JSON.stringify(data);
      var message = new Message(payload);
      message.properties.add('$$CreationTimeUtc', d.toISOString());
      message.properties.add('$$MessageSchema', schema);
      message.properties.add('$$ContentType', 'JSON');

      console.log('Sending device message data:\n' + payload);
      client.sendEvent(message, printErrorFor('send event'));
    }
    ```

1. Ajoutez le code suivant pour créer une instance de client :

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Ajoutez le code suivant à :

    * Ouvrir la connexion.
    * Définir un gestionnaire pour les propriétés souhaitées.
    * Envoyer les propriétés signalées.
    * Inscrire des gestionnaires pour les méthodes directes.
    * Démarrer l’envoi de la télémétrie.

    ```nodejs
    client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onDirectMethod);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
    });
    ```

1. Enregistrez les modifications dans le fichier **remote_monitoring.js**.

1. Pour démarrer l’exemple d’application, exécutez la commande suivante à l’invite de commande :

    ```cmd/sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
