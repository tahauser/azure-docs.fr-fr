---
title: "Bien démarrer avec la gestion des appareils Azure IoT Hub (Node) | Microsoft Docs"
description: "Découvrez comment utiliser la gestion des appareils IoT Hub pour lancer un redémarrage d’appareil à distance. Vous utilisez le SDK Azure IoT pour Node.js afin d’implémenter une application d’appareil simulé qui inclut une méthode directe et une application de service qui appelle la méthode directe."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: juanpere
ms.openlocfilehash: 0aeba8560de46ae2032304f133b66ad0a70f1354
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="get-started-with-device-management-node"></a>Prise en main de la gestion d’appareils (Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ce didacticiel vous explique les procédures suivantes :

* Utiliser le portail Azure pour créer un IoT Hub et une identité d’appareil dans celui-ci.
* Créer une application d’appareil simulé disposant d’une méthode directe permettant le redémarrage de cet appareil. Les méthodes directes sont appelées à partir du cloud.
* Créer une application console Node.js qui appelle une méthode directe de redémarrage sur l’application d’appareil simulé via votre IoT Hub.

À la fin de ce didacticiel, vous disposerez de deux applications console Node.js :

**dmpatterns_getstarted_device.js**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit une méthode directe de redémarrage, simule un redémarrage physique et indique l’heure du dernier redémarrage.

**dmpatterns_getstarted_service.js**, qui appelle une méthode directe sur l’application de l’appareil simulé, affiche la réponse et affiche les propriétés signalées pour la mise à jour.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 4.0.x ou version ultérieure. <br/>  L’article [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Node.js pour ce didacticiel sur Windows ou sur Linux.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous allez :

* Créer une application console Node.js qui répond à une méthode directe appelée par le cloud
* Déclencher un redémarrage d’appareil simulé
* Utiliser les propriétés signalées pour activer les requêtes sur le jumeau d’appareil afin d’identifier les appareils et l’heure de leur dernier redémarrage

1. Créez un dossier vide nommé **manageddevice**.  Dans le dossier **simulateddevice**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour installer le package du kit Device SDK **azure-iot-device** et le package **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_getstarted_device.js** dans le dossier **manageddevice**.
4. Ajoutez les instructions 'require' suivantes au début du fichier **dmpatterns_getstarted_device.js** :
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Ajoutez une variable **connectionString** et utilisez-la pour créer une instance de **Client**.  Remplacez la chaîne de connexion par la chaîne de connexion de votre appareil.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Ajoutez la fonction suivante pour implémenter la méthode directe sur l’appareil
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Ouvrez la connexion à votre hub IoT et démarrez l’écouteur de la méthode directe :
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Enregistrez et fermez le fichier **dmpatterns_getstarted_device.js**.

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Déclencher un redémarrage à distance sur l’appareil à l’aide d’une méthode directe
Dans cette section, vous créez une application console Node.js qui lance un redémarrage à distance sur un appareil avec une méthode directe. L’application utilise des requêtes du jumeau d’appareil pour déterminer l’heure du dernier redémarrage de cet appareil.

1. Créez un dossier vide nommé **triggerrebootondevice**.  Dans le dossier **triggerrebootondevice**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **triggerrebootondevice**, exécutez la commande suivante pour installer le package du kit Device SDK **azure-iothub** et le package **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iothub --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_getstarted_service.js** dans le dossier **triggerrebootondevice**.
4. Ajoutez les instructions ’require’ suivantes au début du fichier **dmpatterns_getstarted_service.js** :
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Ajoutez les déclarations de variable suivantes et remplacez les valeurs d’espace réservé :
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Ajoutez la fonction suivante pour appeler la méthode device afin de redémarrer l’appareil cible :
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```
7. Ajoutez la fonction suivante pour interroger l’appareil et obtenir l’heure du dernier redémarrage :
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
8. Ajoutez le code suivant pour appeler les fonctions qui déclenchent la méthode directe de redémarrage et la requête sur le dernier redémarrage :
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Enregistrez et fermez le fichier **dmpatterns_getstarted_service.js**.

## <a name="run-the-apps"></a>Exécuter les applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. À l’invite de commandes, dans le dossier **triggerrebootondevice**, exécutez la commande suivante pour déclencher le redémarrage à distance et interroger le jumeau d’appareil pour déterminer l’heure du dernier redémarrage.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. La réponse de l’appareil à la méthode directe s’affiche dans la console.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
