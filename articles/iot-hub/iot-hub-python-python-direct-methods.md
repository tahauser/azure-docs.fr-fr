---
title: "Méthodes directes Azure IoT Hub (Python) | Microsoft Docs"
description: "Utilisation des méthodes directes Azure IoT Hub. Vous utilisez les SDK Azure IoT pour Python afin d’implémenter une application d’appareil simulé qui inclut une méthode directe et une application de service qui appelle la méthode directe."
services: iot-hub
documentationcenter: 
author: msebolt
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: 9dac7b45894c2da0dcd32e456c8806faadf814e9
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="use-direct-methods-on-your-iot-device-with-python"></a>Utilisation de méthodes directes sur votre appareil IoT avec Python
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

À la fin de ce didacticiel, vous disposerez de deux applications console Python :

* **CallMethodOnDevice.py**, qui appelle une méthode dans l’application d’appareil simulé et affiche la réponse.
* **SimulatedDevice.py**, qui se connecte à votre hub IoT avec l’identité d’appareil créée précédemment, puis répond à la méthode appelée par le cloud.

> [!NOTE]
> L’article [Kits Azure IoT SDK][lnk-hub-sdks] fournit des informations sur les kits Azure IoT SDK que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et votre backend de solution.
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Python 2.x ou 3.x][lnk-python-download]. Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion de package Python][lnk-install-pip].
* Si vous utilisez le système d’exploitation Windows, utilisez le [package redistribuable Visual C++][lnk-visual-c-redist] pour autoriser l’utilisation de DLL natives de Python.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Création d’une application d’appareil simulé
Dans cette section, vous créez une application console Python qui répond à une méthode appelée par le cloud.

1. À l’aide d’un éditeur de texte, créez un fichier **SimulatedDevice.py**.

1. Ajoutez les instructions et variables `import` ci-dessous au début du fichier **SimulatedDevice.py** :
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    
    WAIT_COUNT = 5
    METHOD_CONTEXT = 0
    METHOD_CALLBACKS = 0

    # chose MQTT or MQTT_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "<deviceConnectionString>"
    ```

1. Ajoutez les fonctions suivantes pour implémenter la méthode directe et le rappel de méthode d’appareil sur l’appareil :
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS
    
        if method_name == "DeviceMethod":
            onDeviceMethod()
        
        print ( "\nMethod callback called with:\nmethodName = %s\npayload = %s\ncontext = %s" % (method_name, payload, user_context) )
        METHOD_CALLBACKS += 1
    
        print ( "Total calls confirmed: %d\n" % METHOD_CALLBACKS )
        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200
    
        return device_method_return_value

    def onDeviceMethod():
        print ( "Direct method called." )
    ```

1. Ajoutez la fonction suivante pour ouvrir la connexion à votre hub IoT et initialiser l’écouteur de la méthode :
   
    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

        return client
    ```

1. Ajoutez les fonctions principales :

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for direct method call, press Ctrl-C to exit" )

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
        print ( "Starting the IoT Hub Python direct methods sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Enregistrez et fermez le fichier **SimulatedDevice.py**.

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une nouvelle tentative de connexion), comme suggéré dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 


## <a name="call-a-method-on-a-device"></a>Appeler une méthode sur un appareil
Dans cette section, vous créez une application console Python qui appelle une méthode sur l’application d’appareil simulé, puis affiche la réponse.


1. À l’aide d’un éditeur de texte, créez un fichier **CallMethodOnDevice.py**.

1. Ajoutez les instructions et variables `import` ci-dessous au début du fichier **CallMethodOnDevice.py** :
   
    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "<IoTHubConnectionString>"
    DEVICE_ID = "<deviceId>"

    METHOD_NAME = "DeviceMethod"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    ```

1. Ajoutez les fonctions suivantes pour implémenter la méthode directe et le rappel de méthode d’appareil sur l’appareil :
   
    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Device Method called" )
            print ( "Device Method name       : {0}".format(METHOD_NAME) )
            print ( "Device Method payload    : {0}".format(METHOD_PAYLOAD) )
            print ( "" )
            print ( "Response status          : {0}".format(response.status) )
            print ( "Response payload         : {0}".format(response.payload) )

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )
    ```

1. Ajoutez la fonction principale :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceMethod Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

1. Enregistrez et fermez le fichier **CallMethodOnDevice.py**.

## <a name="run-the-apps"></a>Exécuter les applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, exécutez la commande suivante pour commencer à écouter les appels de méthode à partir de votre hub IoT :
   
    ```cmd/sh
    python SimulatedDevice.py
    ```
   
    ![][7]

1. À l’aide de l’invite de commandes, exécutez la commande suivante pour commencer la surveillance de votre hub IoT :
   
    ```cmd/sh
    python CallMethodOnDevice.py
    ```
   
    ![][8]

1. Vous voyez l’appareil réagir à la méthode en imprimant le message, et l’application qui a appelé la méthode afficher la réponse de l’appareil :
   
    ![][9]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un nouveau hub IoT dans le portail Azure, puis créé une identité d’appareil dans le registre des identités du hub IoT. Vous avez utilisé cette identité d’appareil pour permettre à l’application d’appareil simulé de réagir à des méthodes appelées par le cloud. Vous avez également créé une application qui appelle des méthodes sur l’appareil et affiche la réponse de celui-ci. 

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Prise en main d’IoT Hub]
* [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-python-python-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-python-python-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-python-python-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Prise en main d’IoT Hub]: iot-hub-node-node-getstarted.md
