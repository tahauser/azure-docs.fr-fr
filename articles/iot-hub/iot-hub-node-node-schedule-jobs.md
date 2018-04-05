---
title: Planification des travaux avec Azure IoT Hub (Node) | Microsoft Docs
description: Procédure de planification d’une tâche Azure IoT Hub pour appeler une méthode directe sur plusieurs appareils. Vous pouvez utiliser les kits de développement logiciel (SDK) Azure IoT pour Node.js afin d’implémenter les applications d’appareil simulé et une application de service pour exécuter la tâche.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: e7e724de68e7078782e90a3f2eda9f7261638ea5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-and-broadcast-jobs-node"></a>Planifier et diffuser des travaux (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub est un service entièrement géré qui permet à une application principale de créer et de suivre des travaux qui planifient et mettent à jour des millions d’appareils.  Les travaux peuvent être utilisés pour les actions suivantes :

* Mettre à jour les propriétés souhaitées
* Mettre à jour les balises
* Appeler des méthodes directes

Sur le plan conceptuel, un travail encapsule l’une de ces actions et suit la progression de l’exécution par rapport à un ensemble d’appareils, défini par une requête de représentation d’appareil.  Par exemple, à l’aide d’un travail, une application principale peut appeler une méthode de redémarrage sur 10 000 appareils, spécifiée par une requête de représentation d’appareil et planifiée dans l’avenir.  Cette application peut ensuite suivre la progression à mesure que chacun de ces appareils reçoit et exécute la méthode de redémarrage.

Pour en savoir plus sur chacune de ces fonctionnalités, consultez les articles suivants :

* Représentation d’appareil et propriétés : [Prise en main des représentations d’appareil][lnk-get-started-twin] et [Tutorial: How to use device twin properties][lnk-twin-props] (Didacticiel : Utilisation des propriétés de représentation d’appareil)
* Méthodes directes : [Guide du développeur IoT Hub - méthodes directes][lnk-dev-methods] et [Didacticiel : méthodes directes][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel vous explique les procédures suivantes :

* Créer une application d’appareil simulé Node.js disposant d’une méthode directe compatible avec **lockDoor**, qui peut être appelée par le back end de la solution.
* Créer une application console Node.js qui appelle la méthode directe **lockDoor** sur l’application d’appareil simulé à l’aide d’un travail et met à jour les propriétés souhaitées à l’aide d’un travail d’appareil.

À la fin de ce didacticiel, vous disposerez de deux applications Node.js :

**simDevice.js**, qui se connecte à IoT Hub avec l’identité de l’appareil et reçoit une méthode directe **lockDoor**.

**scheduleJobService.js**, qui appelle une méthode directe sur l’application d’appareil simulé et met à jour les propriétés souhaitées du jumeau d’appareil à l’aide d’un travail.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 4.0.x ou version ultérieure. <br/>  L’article [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Node.js pour ce didacticiel sur Windows ou sur Linux.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous allez créer une application console Node.js qui répond à une méthode directe appelée par le cloud, ce qui déclenche une méthode **lockDoor** simulée.

1. Créez un dossier vide appelé **simDevice**.  Dans le dossier **simDevice** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **simDevice**, exécutez la commande suivante pour installer les packages Kit de développement logiciel (SDK) pour appareils **azure-iot-device** et **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Dans un éditeur de texte, créez un fichier **simDevice.js** dans le dossier **simDevice**.
4. Ajoutez les instructions ’require’ ci-dessous au début du fichier **simDevice.js** :
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Ajoutez une variable **connectionString** et utilisez-la pour créer une instance de **Client**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Ajoutez la fonction suivante pour gérer la méthode **lockDoor**.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Ajoutez le code suivant pour inscrire le gestionnaire de la méthode **lockDoor**.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Enregistrez et fermez le fichier **simDevice.js**.

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planifier des travaux pour appeler une méthode directe et mettre à jour les propriétés d’une représentation d’appareil
Dans cette section, vous créez une application console Node.js qui lance **lockDoor** à distance sur un appareil à l’aide d’une méthode directe et met à jour les propriétés de représentation d’appareil.

1. Créez un dossier vide appelé **scheduleJobService**.  Dans le dossier **scheduleJobService**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **scheduleJobService**, exécutez la commande suivante pour installer les packages Kit de développement logiciel (SDK) pour appareils **azure-iothub** et **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iothub uuid --save
    ```
3. À l’aide d’un éditeur de texte, créez un nouveau fichier **scheduleJobService.js** dans le dossier **scheduleJobService**.
4. Ajoutez les instructions ’require’ suivantes au début du fichier **dmpatterns_gscheduleJobServiceetstarted_service.js** :
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. Ajoutez les déclarations de variable suivantes et remplacez les valeurs d’espace réservé :
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. Ajoutez la fonction suivante, qui permet de surveiller l’exécution du travail :
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```
7. Ajoutez le code suivant pour planifier le travail qui appelle la méthode de l’appareil :
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
8. Ajoutez le code suivant pour planifier le travail de mise à jour de la représentation d’appareil :
   
    ```
    var twinPatch = {
       etag: '*', 
       properties: {
           desired: {
               building: '43', 
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
9. Enregistrez et fermez le fichier **scheduleJobService.js**.

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **simDevice**, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.
   
    ```
    node simDevice.js
    ```
2. À l’invite de commandes dans le dossier **scheduleJobService**, exécutez la commande suivante pour déclencher les travaux afin de verrouiller la porte et de mettre à jour le jumeau
   
    ```
    node scheduleJobService.js
    ```
3. La réponse de l’appareil à la méthode directe s’affiche dans la console.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez utilisé un travail pour planifier une méthode directe sur un appareil et la mise à jour des propriétés de représentation de l’appareil.

Pour approfondir la prise en main d’IoT Hub et des modèles de gestion d’appareils, comme la mise à jour du microprogramme à distance, consultez :

[Didacticiel : Mettre à jour un microprogramme][lnk-fwupdate]

Afin d’approfondir l’apprentissage de IoT Hub, consultez [Getting started with Azure IoT Edge][lnk-iot-edge] (Bien démarrer avec Azure IoT Edge).

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
