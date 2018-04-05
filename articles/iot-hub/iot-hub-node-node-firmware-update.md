---
title: Mise à jour d’un microprogramme d’appareil avec Azure IoT Hub (Node) | Microsoft Docs
description: Guide d’utilisation de la gestion des appareils sur Azure IoT Hub pour lancer une mise à jour du microprogramme d’un appareil. Vous utilisez les SDK Azure IoT pour Node.js afin d’implémenter une application d’appareil simulé et une application de service qui déclenche la mise à jour du microprogramme.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e8ef556793534c9eb8300ee262a4b134960f862a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Utilisation de la gestion des appareils pour lancer une mise à jour du microprogramme d’un appareil (Node/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Dans le didacticiel [Prise en main de la gestion d’appareils][lnk-dm-getstarted], vous avez vu comment utiliser les primitives de [représentation d’appareil physique][lnk-devtwin] et de [méthodes directives][lnk-c2dmethod] pour redémarrer à distance un appareil. Ce didacticiel utilise les mêmes primitives IoT Hub, fournit des conseils, et montre comment effectuer une mise à jour du microprogramme simulée de bout en bout.  Ce modèle est utilisé dans l’implémentation de la mise à jour du microprogramme de l’exemple d’appareil Intel Edison.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel vous explique les procédures suivantes :

* Créez une application console Node.js qui appelle une méthode directe firmwareUpdate sur l’application d’appareil simulé via votre IoT Hub.
* Créez une application d’appareil simulé qui implémente une méthode directe **firmwareUpdate**. Cette méthode lance un processus en plusieurs étapes qui attend de télécharger l’image du microprogramme, la télécharge et enfin, l’applique. À chaque étape du processus de mise à jour, l’appareil utilise les propriétés signalées pour mettre à jour la progression.

À la fin de ce didacticiel, vous disposerez de deux applications console Node.js :

**dmpatterns_fwupdate_service.js**, qui appelle une méthode directe sur l’application d’appareil simulé, affiche la réponse, et affiche périodiquement (toutes les 500 ms) les propriétés signalées mises à jour.

**dmpatterns_fwupdate_device.js**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit une méthode directe firmwareUpdate, s’exécute pour simuler une mise à jour du microprogramme dans un processus à plusieurs états, consistant à attendre avant de télécharger l’image, à télécharger celle-ci, puis finalement à l’appliquer.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 4.0.x ou version ultérieure. <br/>  L’article [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Node.js pour ce didacticiel sur Windows ou sur Linux.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Pour créer votre IoT Hub et obtenir votre chaîne de connexion, procédez de la manière décrite dans [Prise en main de la gestion d’appareils](iot-hub-node-node-device-management-get-started.md).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Déclencher une mise à jour du microprogramme à distance sur l’appareil à l’aide d’une méthode directe
Dans cette section, vous créez une application console Node.js qui lance une mise à jour de microprogramme à distance sur un appareil. L’application utilise une méthode directe pour lancer la mise à jour et des requêtes de jumeau d’appareil pour obtenir régulièrement l’état de la mise à jour du microprogramme actif.

1. Créez un dossier vide nommé **triggerfwupdateondevice**.  Dans le dossier **triggerfwupdateondevice**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **triggerfwupdateondevice**, exécutez la commande suivante pour installer le package **azure-iot-hub** :
   
    ```
    npm install azure-iothub --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_getstarted_service.js** dans le dossier **triggerfwupdateondevice**.
4. Ajoutez les instructions ’require’ suivantes au début du fichier **dmpatterns_getstarted_service.js** :
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Ajoutez les déclarations de variable suivantes et remplacez les valeurs d’espace réservé :
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Ajoutez la fonction suivante pour rechercher et afficher la valeur de la propriété signalée firmwareUpdate.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Ajoutez la fonction suivante pour appeler la méthode firmwareUpdate afin de redémarrer l’appareil cible :
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Enfin, ajoutez la fonction suivante au code pour démarrer la séquence de mise à jour du microprogramme et commencer périodiquement à afficher les propriétés signalées :
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Enregistrez et fermez le fichier **dmpatterns_fwupdate_service.js**.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Exécuter les applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. À l’invite de commandes, dans le dossier **triggerfwupdateondevice**, exécutez la commande suivante pour déclencher le redémarrage à distance et interroger la représentation d’appareil pour déterminer le moment du dernier redémarrage.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. La réponse de l’appareil à la méthode directe s’affiche dans la console.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher une mise à jour du microprogramme à distance sur un appareil, et utilisé les propriétés signalées pour suivre la progression de la mise à jour du microprogramme.

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
