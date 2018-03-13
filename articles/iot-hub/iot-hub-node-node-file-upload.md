---
title: "Charger les fichiers d’un appareil sur Azure IoT Hub avec Node | Microsoft Docs"
description: "Découvrez comment charger les fichiers d’un appareil sur le cloud avec le kit Azure IoT device SDK pour Node.js. Les fichiers téléchargés sont stockés dans un conteneur d’objets blob de stockage Azure."
services: iot-hub
documentationcenter: nodejs
author: msebolt
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo
ms.openlocfilehash: cff0f2fc664e0c09bfa1f8f0e0d488a049a6f448
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ce didacticiel s’appuie sur le code du didacticiel [Envoyer des messages du cloud vers un appareil avec IoT Hub](iot-hub-node-node-c2d.md) pour montrer comment utiliser les [fonctions de chargement de fichiers d’IoT Hub](iot-hub-devguide-file-upload.md) afin de charger un fichier sur le [Stockage Blob Azure](../storage/index.yml). Ce didacticiel explique les procédures suivantes :

- Fournissez en toute sécurité à un appareil un URI d’objet blob Azure pour le chargement d’un fichier.
- Utilisez les notifications de chargement de fichier IoT Hub pour déclencher le traitement du fichier dans votre serveur principal d’application.

Les didacticiels [Bien démarrer avec IoT Hub](iot-hub-node-node-getstarted.md) et [Envoyer des messages du cloud vers un appareil avec IoT Hub](iot-hub-node-node-c2d.md) illustrent les fonctionnalités de base de la messagerie d’un appareil vers le cloud et du cloud vers un appareil offertes par IoT Hub. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Par exemple : 

* Fichiers volumineux qui contiennent des images
* vidéos
* Données de vibration échantillonnées à une fréquence élevée
* Un certain type de données prétraitées.

Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory](../data-factory/introduction.md) ou de la pile [Hadoop](../hdinsight/index.md). Lorsque vous avez besoin de charger des fichiers à partir d’un appareil, vous pouvez quand même exploiter la sécurité et la fiabilité d’IoT Hub.

À la fin de ce didacticiel, vous exécuterez deux applications de console Node.js :

* **SimulatedDevice.js**, qui charge un fichier de stockage à l’aide d’un URI SAP fourni par votre hub IoT.
* **ReadFileUploadNotification**, qui reçoit les notifications de chargement de fichier à partir de votre hub IoT.

> [!NOTE]
> IoT Hub prend en charge de nombreuses plateformes d’appareils et de nombreux langages (notamment C, .NET, Javascript, Python et Java) par le biais des kits Azure IoT device SDK. Pour obtenir des instructions pas à pas expliquant comment connecter votre appareil à Azure IoT Hub, voir le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 4.0.x ou version ultérieure.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Charger un fichier à partir d’une application d’appareil

Dans cette section, créez l’application d’appareil pour charger un fichier sur IoT Hub.

1. Créez un dossier vide appelé ```simulateddevice```.  Dans le dossier ```simulateddevice```, créez un fichier package.json en saisissant la commande suivante dans votre invite de commandes.  Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

1. Dans l’invite de commandes du dossier ```simulateddevice```, exécutez la commande suivante pour installer le package SDK d’appareil **azure-iot-device** et le package **azure-iot-device-mqtt** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. À l’aide d’un éditeur de texte, créez un fichier **SimulatedDevice.js** dans le dossier ```simulateddevice```.

1. Ajoutez les instructions ```require``` ci-dessous au début du fichier **SimulatedDevice.js** :

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Ajoutez une variable ```deviceconnectionstring``` et utilisez-la pour créer une instance **Client**.  Remplacez ```{deviceconnectionstring}``` par le nom de l’appareil que vous avez créé dans la section _Créer un hub IoT_ :

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Par souci de simplicité, la chaîne de connexion est dans le code ; cette pratique n’étant pas recommandée, vous pouvez envisager des méthodes de stockage plus sécurisées pour ce secret suivant votre architecture et votre cas d’usage.

1. Ajoutez le code suivant pour connecter le client :

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Créez un rappel et utilisez la fonction **uploadToBlob** pour charger le fichier.

    ```nodejs
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. Enregistrez et fermez le fichier **SimulatedDevice.js** .

1. Copiez un fichier image dans le dossier `simulateddevice` et renommez-le `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Recevoir une notification de téléchargement de fichier

Dans cette section, vous allez créer une application console Node.js qui reçoit des messages de notification de chargement de fichiers envoyés par IoT Hub.

Vous pouvez utiliser la chaîne de connexion **iothubowner** de votre hub IoT pour suivre cette section. Vous trouverez la chaîne de connexion dans le panneau **Stratégie d’accès partagé** du [portail Azure](https://portal.azure.com/).

1. Créez un dossier vide appelé ```fileuploadnotification```.  Dans le dossier ```fileuploadnotification```, créez un fichier package.json en saisissant la commande suivante dans votre invite de commandes.  Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

1. Depuis votre invite de commandes, dans le dossier ```fileuploadnotification```, exécutez la commande suivante pour installer le package SDK **azure-iothub** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. À l’aide d’un éditeur de texte, créez un fichier **FileUploadNotification.js** dans le dossier ```fileuploadnotification```.

1. Ajoutez les instructions ```require``` ci-dessous au début du fichier **FileUploadNotification.js** :

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Ajoutez une variable ```iothubconnectionstring``` et utilisez-la pour créer une instance **Client**.  Remplacez ```{iothubconnectionstring}``` par la chaîne de connexion du hub IoT créé dans la section _Créer un hub IoT_ :

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Par souci de simplicité, la chaîne de connexion est dans le code ; cette pratique n’étant pas recommandée, vous pouvez envisager des méthodes de stockage plus sécurisées pour ce secret suivant votre architecture et votre cas d’usage.

1. Ajoutez le code suivant pour connecter le client :

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Ouvrez le client et utilisez la fonction **getFileNotificationReceiver** pour recevoir les mises à jour d’état.

    ```nodejs
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Enregistrez et fermez le fichier **FileUploadNotification.js**.

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

Dans une invite de commandes, exécutez la commande suivante dans le dossier `fileuploadnotification` :

```cmd/sh
node FileUploadNotification.js
```

Dans une invite de commandes, exécutez la commande suivante dans le dossier `simulateddevice` :

```cmd/sh
node SimulatedDevice.js
```

La capture d’écran suivante montre la sortie de l’application **SimulatedDevice** :

![Sortie de l’application simulated-device](./media/iot-hub-node-node-file-upload/simulated-device.png)

La capture d’écran suivante montre la sortie de l’application **FileUploadNotification** :

![Sortie de l’application read-file-upload-notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Vous pouvez utiliser le portail pour afficher le fichier chargé dans le conteneur de stockage que vous avez configuré :

![Fichier chargé](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et fonctionnalités d’IoT Hub avec les articles suivants :

* [Créer un IoT Hub par programme][lnk-create-hub]
* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

<!-- Links -->
[Centre de développement Azure IoT]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
