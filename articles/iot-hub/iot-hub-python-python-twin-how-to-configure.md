---
title: "Utiliser les propriétés des jumeaux d’appareils Azure IoT Hub (Python) | Microsoft Docs"
description: "Guide d’utilisation des jumeaux d’appareils Azure IoT Hub pour configurer des appareils. Les SDK Azure IoT pour Python permettent d’implémenter une application d’appareil simulé et une application de service qui modifie la configuration d’un appareil à l’aide d’un jumeau d’appareil."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: v-masebo
ms.openlocfilehash: d0d5a30a76068eb3212124fd14e7ea1616b75708
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>Utiliser les propriétés souhaitées pour configurer des appareils (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

À la fin de ce didacticiel, vous disposerez de deux applications console Python :

* **SimulateDeviceConfiguration.py**, application d’appareil simulée qui attend une mise à jour de configuration souhaitée, et signale l’état d’un processus de mise à jour de configuration simulée.
* **SetDesiredConfigurationAndQuery.py**, application backend Python qui définit la configuration souhaitée sur un appareil et interroge le processus de mise à jour de configuration.

> [!NOTE]
> L’article [Kits Azure IoT SDK][lnk-hub-sdks] fournit des informations sur les kits Azure IoT SDK que vous pouvez utiliser pour générer des applications pour appareil et des applications principales.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Python 2.x ou 3.x][lnk-python-download]. Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion de package Python][lnk-install-pip].
* Si vous utilisez le système d’exploitation Windows, utilisez le [package redistribuable Visual C++][lnk-visual-c-redist] pour autoriser l’utilisation de DLL natives de Python.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Si vous avez suivi le didacticiel [Prise en main des représentations d’appareil][lnk-twin-tutorial], vous avez déjà un hub IoT et une identité d’appareil nommée **myDeviceId**. Vous pouvez passer à la section [Créer l’application pour appareil simulée][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>Créer l’application d’appareil simulée
Dans cette section, vous allez créer une application console Python qui se connecte à votre hub en tant que **myDeviceId**, attend une mise à jour de la configuration souhaitée, puis signale des mises à jour sur le processus de mise à jour de la configuration simulée.

1. Installez le **SDK d’appareil Azure IoT Python** en exécutant la commande suivante à l’invite de commandes :
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. À l’aide d’un éditeur de texte, créez un fichier **SimulateDeviceConfiguration.py**.

1. Ajoutez le code suivant au fichier **SimulateDeviceConfiguration.py**, puis remplacez l’espace réservé **{device connection string}** par la chaîne de connexion à l’appareil que vous avez copiée lors de la création de l’identité d’appareil **myDeviceId** :

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    L’objet **CLIENT** expose toutes les méthodes requises pour interagir avec des jumeaux d’appareils à partir de l’appareil. Dans le code ultérieur, vous attacherez un gestionnaire pour la mise à jour sur les propriétés souhaitées, puis vous ajouterez un gestionnaire supplémentaire pour vérifier qu’il existe réellement une requête de modification de configuration en comparant les configIds, qui appelle ensuite une méthode qui lance la modification de configuration. Par souci de simplicité, le code précédent utilise une valeur par défaut codée en dur pour la configuration initiale. Une application réelle chargerait probablement la configuration à partir d’un stockage local.
   
   > [!IMPORTANT]
   > Les événements de modification de propriété souhaitée étant toujours émis une seule fois lors de la connexion de l’appareil, assurez-vous de l’existence d’une modification réelle dans les propriétés souhaitées avant d’exécuter toute action.
   > 

1. Ajoutez le code suivant à la fin du fichier **SimulateDeviceConfiguration.py** :

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    La méthode **device_twin_callback** met à jour les propriétés signalées sur l’objet de jumeau d’appareil local avec la requête de mise à jour de configuration, et définit le _configId_. Une fois la mise à jour du jumeau d’appareil terminée, elle affiche un message de mise à jour. Pour économiser la bande passante, les propriétés signalées sont mises à jour en spécifiant uniquement les propriétés à modifier (nommées **TWIN_PAYLOAD** dans le code ci-dessus), au lieu de remplacer le document entier.
   
   > [!NOTE]
   > Ce didacticiel ne simule pas de comportement pour des mises à jour de configuration simultanées. Certains processus de mise à jour de configuration peuvent s’adapter à des modifications de configuration de la cible en cours de mise à jour, d’autres peuvent avoir à les mettre en file d’attente, et d’autres encore peuvent les refuser avec une condition d’erreur. Prenez en considération le comportement souhaité pour votre processus de configuration spécifique, et ajoutez la logique appropriée avant de lancer la modification de la configuration.
   > 

1. Ajoutez le code suivant à la fin du fichier **SimulateDeviceConfiguration.py** : 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Exécutez l’application d’appareil :
   
    ```cmd/sh
    node SimulateDeviceConfiguration.js
    ```
   
    Vous devriez voir le message `Device twins updated.`. Gardez l’application active.


## <a name="create-the-service-app"></a>Créer l’application de service
Dans cette section, vous créez une application console Python qui met à jour les *propriétés souhaitées* sur le jumeau d’appareil associé à **myDeviceId** avec un nouvel objet de configuration de télémétrie. Elle interroge ensuite les jumeaux d’appareils stockés dans le hub IoT, puis affiche la différence entre les configurations souhaitées et signalées de l’appareil.

1. Installez le kit **Azure IoT Python Service SDK** en exécutant la commande suivante à l’invite de commandes :
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. À l’aide d’un éditeur de texte, créez un fichier **SetDesiredAndQuery.py**.

1. Ajoutez le code suivant au fichier **SetDesiredAndQuery.py**, puis remplacez l’espace réservé **{IoTHubConnectionString}** par la chaîne de connexion IoT Hub que vous avez copiée lors de la création de votre hub et l’espace réservé **{deviceId}** par le nom de votre appareil :

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Ajoutez le code suivant à la fin du fichier **SetDesiredAndQuery.py** :

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. **SimulateDeviceConfiguration.py** étant en cours d’exécution, exécutez l’application dans une nouvelle invite de commandes avec :

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    Vous devriez voir l’ID de configuration signalé passer de **1** à **2**, avec la nouvelle fréquence d’envoi active de cinq minutes au lieu de 24 heures.


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez défini une configuration souhaitée en tant que *propriétés souhaitées* à partir d’une application backend, et écrit une application pour appareil simulée afin de détecter cette modification et de simuler un processus de mise à jour signalant son état en tant que *propriétés signalées* au jumeau d’appareil.

Utilisez les ressources suivantes :

* Envoyez des données de télémétrie à partir d’appareils en suivant le didacticiel [Bien démarrer avec IoT Hub][lnk-iothub-getstarted].
* Pour savoir comment planifier ou exécuter des opérations sur de grands ensembles d’appareils, consultez le didacticiel [Planifier et diffuser des travaux][lnk-schedule-jobs].
* Contrôlez les appareils de façon interactive (par exemple pour mettre en marche un ventilateur à partir d’une application contrôlée par l’utilisateur) en suivant le didacticiel [Utiliser des méthodes directes][lnk-methods-tutorial].

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
