---
title: Provisionner un appareil TPM simulé auprès du service Azure IoT Hub à l’aide de Node.js | Microsoft Docs
description: 'Démarrage rapide d’Azure : Créer et provisionner un appareil TPM simulé pour le service Azure IoT Hub Device Provisioning à l’aide du Kit de développement logiciel (SDK) Node.js'
services: iot-dps
keywords: ''
author: msebolt
ms.author: v-masebo
ms.date: 03/01/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b73379baa2b0b73fb1501a4356db10279ac41185
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Créer et approvisionner un appareil TPM simulé auprès du service IoT Hub Device Provisioning à l’aide du Kit de développement logiciel (SDK) Node.js

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ces étapes indiquent comment créer un appareil simulé sur votre ordinateur de développement exécutant le système d’exploitation Windows, comment exécuter le simulateur Windows TPM en tant que [Module de sécurité matériel (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) de l’appareil et comment utiliser l’exemple de code pour connecter cet appareil au service d’approvisionnement d’appareil et à votre IoT hub. 

Veillez à compléter les étapes décrites dans la section relative à la [configuration du service d’approvisionnement d’appareil Azure IoT Hub avec le portail Azure](./quick-setup-auto-provision.md) avant de continuer.

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Préparer l’environnement 

1. Vérifiez que la [version 4.0 ou supérieure de Node.js](https://nodejs.org) est installée sur votre machine.

1. Assurez-vous que l’élément `git` est installé sur votre machine et est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez la section relative aux [outils clients de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder à la dernière version des outils `git` à installer, qui inclut **Git Bash**, l’application de ligne de commande que vous pouvez utiliser pour interagir avec votre référentiel Git local. 


## <a name="simulate-a-tpm-device"></a>Simuler un appareil TPM

1. Ouvrez une invite de commandes ou Git Bash. Clonez le référentiel GitHub `azure-utpm-c` :
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git
    ```

1. Accédez au dossier racine GitHub et exécutez le simulateur [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview). Il écoute un socket sur les ports 2321 et 2322. Ne fermez pas cette fenêtre de commande ; vous devez laisser ce simulateur s’exécuter jusqu’à la fin de ce guide de démarrage rapide : 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Créez un dossier vide appelé **registerdevice**. Dans le dossier **registerdevice**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Veillez à répondre à toutes les questions posées par `npm` ou acceptez les paramètres par défaut, s’ils vous conviennent :
   
    ```cmd/sh
    npm init
    ```

1. Installez les packages précurseurs suivants :

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Il existe quelques erreurs connues lors de l’installation des packages ci-dessus. Pour résoudre ces problèmes, exécutez `npm install --global --production windows-build-tools` en utilisant une invite de commandes en mode **Exécuter en tant qu’administrateur**, exécutez `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` après avoir remplacé le chemin par votre version installée, puis exécutez de nouveau les commandes d’installation ci-dessus.
    >

1. Installez les packages suivants qui contiennent les composants utilisés pendant l’inscription :

    - un client de sécurité qui fonctionne avec TPM : `azure-iot-security-tpm`
    - un mode de transport pour connecter l’appareil au service Device Provisioning : `azure-iot-provisioning-device-http` ou `azure-iot-provisioning-device-amqp`
    - un client pour utiliser le mode de transport et le client de sécurité : `azure-iot-provisioning-device`

    Une fois l’appareil inscrit, vous pouvez utiliser les packages du client IoT Hub Device habituels pour connecter l’appareil à l’aide des informations d’identifications fournies lors de l’inscription. Éléments requis :

    - le client d’appareil : `azure-iot-device`
    - un mode de transport : `azure-iot-device-amqp`, `azure-iot-device-mqtt` ou `azure-iot-device-http`
    - le client de sécurité, déjà installé : `azure-iot-security-tpm`

    > [!NOTE]
    > Les exemples ci-dessous utilisent les modes de transports `azure-iot-provisioning-device-http` et `azure-iot-device-mqtt`.
    > 

    Vous pouvez installer tous ces packages d’un coup en exécutant la commande suivante dans l’invite de commandes, dans le dossier **registerdevice** :

        ```cmd/sh
        npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
        ```

1. Avec un éditeur de texte, créer un fichier **ExtractDevice.js** dans le dossier **registerdevice**.

1. Ajoutez les instructions `require` ci-dessous au début du fichier **ExtractDevice.js** :
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Ajoutez la fonction suivante pour implémenter la méthode :
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Enregistrez et fermez le fichier **ExtractDevice.js**. Exécutez l’exemple :

    ```cmd/sh
    node ExtractDevice.js
    ```

1. La fenêtre Sortie affiche la **_paire de clés de type EK_** et l’**_ID d’inscription_** nécessaires à l’inscription de l’appareil. Notez ces valeurs. 


## <a name="create-a-device-entry"></a>Créer une entrée d’appareil

1. Connectez-vous au portail Azure, cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre service Device Provisioning.

1. Dans le panneau de résumé du service d’approvisionnement d’appareil, sélectionnez **Gérer les inscriptions**. Sélectionnez l’onglet **Inscriptions individuelles** et cliquez sur le bouton **Ajouter** dans la partie supérieure. 

1. Sous l’**entrée Ajouter la liste d’inscription**, entrez les informations suivantes :
    - Sélectionnez **TPM** comme *mécanisme* d’attestation d’identité.
    - Entrez l’*ID d’inscription* et la *paire de clés de type EK (Endorsement Key)* pour votre appareil de module de plateforme sécurisée.
    - Si vous le souhaitez, vous pouvez fournir les informations suivantes :
        - Sélectionnez un hub IoT lié à votre service d’approvisionnement.
        - Entrez un ID d’appareil unique. Veillez à éviter les données sensibles lorsque vous affectez un nom à votre appareil.
        - Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.
    - Cela fait, cliquez sur le bouton **Enregistrer**. 

    ![Saisir les informations d’inscription d’appareil dans le panneau du portail](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Lorsque l’inscription aboutit, *l’ID d’inscription* de votre appareil s’affiche dans la liste sous l’onglet *Inscriptions individuelles*. 


## <a name="register-the-device"></a>Inscrire l’appareil

1. Dans le portail Azure, sélectionnez le panneau **Vue d’ensemble** du service Device Provisioning et notez les valeurs **_Point de terminaison d’appareil global_** et **_Étendue de l’ID_**.

    ![Extraire des informations du point de terminaison DPS à partir du panneau du portail](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Avec un éditeur de texte, créer un fichier **RegisterDevice.js** dans le dossier **registerdevice**.

1. Ajoutez les instructions `require` ci-dessous au début du fichier **RegisterDevice.js** :
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > Le **kit de développement logiciel (SDK) Azure IoT pour Node.js** prend en charge des protocoles supplémentaires comme _AMQP_, _AMQP WS_ et _MQTT WS_.  Pour plus d’exemples, consultez les [exemples de kit de développement logiciel (SDK) du service Device Provisioning pour Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. Ajoutez les variables **globalDeviceEndpoint** et **idScope**, et utilisez-les pour créer une instance **ProvisioningDeviceClient**. Remplacez les valeurs **{globalDeviceEndpoint}** et **{idScope}** par les valeurs **_Global Device Endpoint_** et **_ID Scope_** de l’**Étape 1** :
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Ajoutez la fonction suivante pour implémenter la méthode sur l’appareil :
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Enregistrez et fermez le fichier **RegisterDevice.js**. Exécutez l’exemple :

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Notez les messages qui simulent le démarrage et la connexion de l’appareil au service d’approvisionnement d’appareil pour obtenir des informations concernant votre IoT Hub. En cas de réussite de l’approvisionnement de votre appareil simulé au IoT Hub lié à votre service d’approvisionnement, l’ID d’appareil s’affiche sur le panneau **Appareils IoT** du hub. 

    ![L’appareil est inscrit avec le hub IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à manipuler et explorer l’exemple de client d’appareil, ne nettoyez pas les ressources créées lors de ce démarrage rapide. Sinon, procédez aux étapes suivantes pour supprimer toutes les ressources créées lors de ce démarrage rapide.

1. Fermez la fenêtre de sortie de l’exemple de client d’appareil sur votre machine.
1. Fermez la fenêtre du simulateur TPM sur votre machine.
1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service Device Provisioning. Ouvrez le panneau **Gérer les inscriptions** pour votre service, puis cliquez sur l’onglet **Inscriptions individuelles**. Sélectionnez l’*ID D’INSCRIPTION* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure. 
1. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Ouvrez le panneau **IoT Devices** (Appareils IoT) pour votre hub, sélectionnez *l’ID D’APPAREIL* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure.


## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un appareil simulé TPM sur votre machine et l’avez approvisionné vers votre hub IoT à l’aide du service IoT Hub Device Provisioning. Pour savoir comment inscrire un appareil TPM au moyen d’un programme, poursuivez avec le démarrage rapide correspondant. 

> [!div class="nextstepaction"]
> [Démarrage rapide : Inscrire l’appareil TPM auprès du service IoT Hub Device Provisioning](quick-enroll-device-tpm-node.md)
