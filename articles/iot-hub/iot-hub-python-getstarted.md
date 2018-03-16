---
title: "Prise en main d’Azure IoT Hub (Python) | Microsoft Docs"
description: "Découvrez comment envoyer des messages appareil-vers-cloud à Azure IoT Hub à l’aide des kits SDK IoT pour Python. Créez un appareil simulé et des applications de service pour inscrire votre appareil, envoyer des messages et lire des messages d’IoT Hub."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2018
ms.author: dkshir
ms.custom: na
ms.openlocfilehash: 2c14176a3d45b85ae94c27549e5d6448e240ed98
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Connexion du périphérique simulé à votre IoT Hub à l’aide de Python
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous disposerez de deux applications Python :

* **CreateDeviceIdentity.py**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre application de périphérique simulé.
* **SimulatedDevice.py**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment et envoie régulièrement un message de télémétrie en utilisant le protocole MQTT.

> [!NOTE]
> L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks] fournit des informations sur les kits de développement logiciel Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Python 2.x ou 3.x][lnk-python-download]. Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion de package Python][lnk-install-pip].
* Si vous utilisez le système d’exploitation Windows, utilisez le [package redistribuable Visual C++][lnk-visual-c-redist] pour autoriser l’utilisation de DLL natives de Python.
* [Node.js 4.0 ou version ultérieure][lnk-node-download]. Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Ceci est nécessaire pour installer l’[outil IoT Hub Explorer][lnk-iot-hub-explorer].
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.

> [!NOTE]
> Les packages *pip* pour `azure-iothub-service-client` et `azure-iothub-device-client` sont actuellement disponibles uniquement pour les systèmes d’exploitation Windows. Pour Linux/Mac OS, veuillez vous reporter aux sections spécifiques de Linux et Mac OS sur l’article [Prepare your development environment for Python][lnk-python-devbox] (Préparer votre environnement de développement pour Python).
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Votre IoT Hub est maintenant créé. Utilisez le nom d’hôte et la chaîne de connexion de l’IoT Hub pour effectuer la suite du didacticiel.

> [!NOTE]
> Vous pouvez également créer facilement votre IoT Hub sur une ligne de commande, à l’aide de l’interface de ligne de commande (CLI) Python ou Node.js basée sur Azure. L’article [Création d’un IoT Hub à l’aide de l’interface Azure CLI 2.0][lnk-azure-cli-hub] décrit les étapes rapides à suivre. 
> 

## <a name="create-a-device-identity"></a>Création d’une identité d’appareil
Cette section présente les étapes pour créer une application console Python qui crée une identité d’appareil dans le registre d’identités de votre IoT Hub. Un appareil peut uniquement se connecter à IoT Hub s’il possède une entrée dans le registre des identités. Reportez-vous à la section **Registre d’identité** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application de console, elle génère un ID d’appareil et une clé uniques que votre appareil peut utiliser pour s’identifier pour lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Ouvrez une invite de commandes et installez le **Kit de développement logiciel (SDK) Azure IoT Hub Service pour Python**. Fermez l’invite de commandes après avoir installé le Kit de développement logiciel (SDK).

    ```
    pip install azure-iothub-service-client
    ```

2. Créez un fichier Python nommé **CreateDeviceIdentity.py**. Ouvrez-le dans [un éditeur/IDE Python de votre choix][lnk-python-ide-list], par exemple, [IDLE][lnk-idle] par défaut.

3. Ajoutez le code suivant pour importer les modules requis à partir du Kit de développement logiciel (SDK) de service :

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Ajoutez le code suivant, en remplaçant la valeur d’espace réservé pour `[IoTHub Connection String]` par la chaîne de connexion pour l’IoT Hub créé dans la section précédente. Vous pouvez utiliser n’importe quel nom comme `DEVICE_ID`.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. Ajoutez la fonction suivante pour imprimer les informations sur l’appareil.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Ajoutez la fonction suivante pour créer l’identification d’appareil à l’aide du Gestionnaire du Registre. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Enfin, ajoutez la fonction principale comme suit et enregistrez le fichier.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. Sur l’invite de commandes, exécutez le fichier **CreateDeviceIdentity.py** comme suit :

    ```python
    python CreateDeviceIdentity.py
    ```
6. L’appareil simulé doit être créé. Notez les informations **deviceId** et **primaryKey** de cet appareil. Vous aurez besoin de ces valeurs ultérieurement lorsque vous créerez une application qui se connecte à IoT Hub en tant qu’appareil.

    ![Création réussie de l’appareil][1]

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID et les clés des appareils à utiliser comme informations d’identification de sécurité, et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity].
> 
> 


## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Cette section présente les étapes pour créer une application console Python qui simule un appareil et envoie des messages appareil-à-cloud à votre IoT Hub.

1. Ouvrez une nouvelle invite de commandes et installez le Kit de développement logiciel (SDK) Azure IoT Hub Device pour Python comme suit. Fermez l’invite de commandes après l’installation.

    ```
    pip install azure-iothub-device-client
    ```
2. Créez un fichier nommé **SimulatedDevice.py**. Ouvrez ce fichier dans un éditeur/IDE Python de votre choix (par exemple, IDLE).

3. Ajoutez le code suivant pour importer les modules requis à partir du Kit de développement logiciel (SDK) d’appareil.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Ajoutez le code suivant et remplacez l’espace réservé pour `[IoTHub Device Connection String]` par la chaîne de connexion pour votre appareil. La chaîne de connexion d’appareil est généralement au format `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Utilisez les informations **deviceId** et **primaryKey** de l’appareil que vous avez créé dans la section précédente pour remplacer les informations `<deviceId>` et `<primaryKey>`, respectivement. Remplacez `<hostName>` par le nom d’hôte de votre IoT Hub, généralement `<IoT hub name>.azure-devices.net`.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Ajoutez le code suivant pour définir un rappel de confirmation d’envoi. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Ajoutez le code suivant pour initialiser le client d’appareil.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        client.set_option("product_info", "HappyPath_Simulated-Python")
        return client
    ```
7. Ajoutez la fonction suivante pour mettre en forme et envoyer un message à partir de votre périphérique simulé vers votre IoT Hub.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Enfin, ajoutez la fonction principale. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Enregistrez et fermez le fichier **SimulatedDevice.py**. Vous êtes maintenant prêt à exécuter cette application.

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Réception de messages à partir de votre appareil simulé
Pour recevoir des messages de télémétrie à partir de votre appareil, vous devez utiliser un point de terminaison compatible avec les [concentrateurs d’événement][lnk-event-hubs-overview] exposé par l’IoT Hub, qui lit les messages appareil-à-cloud. Consultez le didacticiel [Prise en main des concentrateurs d’événement][lnk-eventhubs-tutorial] pour obtenir des informations complémentaires sur la façon de traiter les messages à partir de votre point de terminaison compatible avec les concentrateurs d’événements pour votre IoT Hub. Les concentrateurs d’événements ne prennent actuellement pas en charge la télémétrie dans Python. Vous pouvez donc créer une application console [Node.js](iot-hub-node-node-getstarted.md#D2C_node) ou [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) basée sur les concentrateurs d’événements pour lire les messages appareil-à-cloud à partir de l’IoT Hub. Ce didacticiel décrit comment vous pouvez utiliser l’[outil IoT Hub Explorer][lnk-iot-hub-explorer] pour lire ces messages de l’appareil.

1. Ouvrez une invite de commandes et installez l’IoT Hub Explorer. 

    ```
    npm install -g iothub-explorer
    ```

2. Exécutez la commande suivante sur l’invite de commande pour commencer à analyser les messages appareil-à-cloud à partir de votre appareil. Utilisez la chaîne de connexion de votre IoT Hub dans l’espace réservé après `--login`.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Ouvrez une nouvelle invite de commandes et accédez au répertoire contenant le fichier **SimulatedDevice.py**.

4. Exécutez le fichier **SimulatedDevice.py** qui envoie régulièrement des données de télémétrie à votre IoT Hub. 
   
    ```
    python SimulatedDevice.py
    ```
5. Observez les messages de l’appareil sur l’invite de commandes exécutant l’IoT Hub Explorer de la section précédente. 

    ![Messages appareil-à-cloud Python][2]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre des identités de l’IoT Hub. Vous avez utilisé cette identité d’appareil pour permettre à l’appareil simulé d’envoyer des messages Appareil vers cloud à l’IoT Hub. Vous avez observé les messages reçus par l’IoT Hub à l’aide de l’outil IoT Hub explorer. 

Pour approfondir l’utilisation du Kit de développement logiciel (SDK) Python pour Azure IoT Hub, consultez [ce référentiel Git Hub][lnk-python-github]. Pour passer en revue les fonctionnalités de messagerie du Kit de développement (SDK) d’Azure IoT Hub Service pour Python, vous pouvez télécharger et exécuter le fichier [iothub_messaging_sample.py][lnk-messaging-sample]. Pour une simulation côté appareil à l’aide du Kit de développement logiciel (SDK) Azure IoT Hub pour Python, vous pouvez télécharger et exécuter le fichier [iothub_client_sample.py][lnk-client-sample].

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Connexion de votre appareil][lnk-connect-device]
* [Prise en main de la gestion d’appareils][lnk-device-management]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iot-edge]

Pour découvrir comment étendre votre solution IoT et traiter les messages appareil-à-cloud à grande échelle, consultez le didacticiel [Traitement des messages appareil-à-cloud][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
