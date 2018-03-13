---
title: Routage des messages avec Azure IoT Hub (Python) | Microsoft Docs
description: "Comment traiter des messages appareil-à-cloud Azure IoT Hub en utilisant les règles de routage et les points de terminaison personnalisés pour distribuer les messages vers d’autres services de serveur principal."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: f467437afb4bf89e77668cfd3e8a824bfbde9e10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>Routage des messages avec IoT Hub (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Ce didacticiel s’appuie sur celui relatif à la [prise en main du IoT Hub].  Le didacticiel :

* Explique comment utiliser les règles de routage pour dispatcher les messages de l’appareil au cloud suivant une configuration facile.
* Indique comment isoler les messages interactifs qui nécessitent une action immédiate du serveur principal de la solution pour un traitement ultérieur.  Par exemple, un appareil peut envoyer un message d’alerte qui déclenche l’insertion d’un ticket dans un système CRM.  Par opposition, les messages de point de données tels que la télémétrie de température sont chargés dans un moteur d’analyse.

À la fin de ce didacticiel, vous exécutez trois applications de console Python :

* **SimulatedDevice.py**, une version modifiée de l’application créée dans le didacticiel [prise en main du IoT Hub], qui envoie des messages appareil-à-cloud de point de données chaque seconde, et des messages appareil-à-cloud interactifs à intervalle aléatoire. Cette application utilise le protocole MQTT pour communiquer avec IoT Hub.
* **ReadCriticalQueue.py**, qui enlève les messages critiques de la file d’attente Service Bus attachée au hub IoT.

> [!NOTE]
> IoT Hub offre la prise en charge de Kit de développement logiciel (SDK) de plusieurs plateformes d’appareils et de plusieurs langages (notamment C, Java et JavaScript). Pour obtenir des instructions expliquant comment remplacer l’appareil de ce didacticiel par un appareil physique et comment connecter vos appareils à un hub IoT, consultez le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Une version d’utilisation complète du didacticiel [prise en main du IoT Hub] .
* [Python 2.x ou 3.x][lnk-python-download]. Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion de package Python][lnk-install-pip].
* Si vous utilisez le système d’exploitation Windows, utilisez le [package redistribuable Visual C++][lnk-visual-c-redist] pour autoriser l’utilisation de DLL natives de Python.
* [Node.js 4.0 ou version ultérieure][lnk-node-download]. Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Ceci est nécessaire pour installer l’[outil IoT Hub Explorer][lnk-iot-hub-explorer].
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Nous recommandons également la lecture de [Stockage Azure] et [Azure Service Bus].


## <a name="send-interactive-messages-from-a-device-app"></a>Envoyer des messages interactifs à partir d’une application pour appareils
Dans cette section, vous allez modifier l’application pour appareils que vous avez créée dans le didacticiel [prise en main du IoT Hub] de façon à envoyer occasionnellement des messages nécessitant un traitement immédiat.

1. Utilisez un éditeur de texte pour ouvrir le fichier **SimulatedDevice.py**. Ce fichier contient le code pour l’application **SimulatedDevice** que vous avez créée dans le didacticiel [prise en main du IoT Hub] .

2. Remplacez la fonction **iothub_client_telemetry_sample_run** par le code suivant :

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
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
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

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
   
    Cette méthode ajoute de façon aléatoire la propriété `"level": "critical"` et `"level": "storage"` aux messages envoyés par l’appareil, ce qui simule un message nécessitant une action immédiate du serveur principal de l’application, ou demandant à être définitivement stocké. L’application prend en charge le routage des messages en fonction de leur corps.
   
   > [!NOTE]
   > Vous pouvez utiliser les propriétés de message pour acheminer les messages pour de nombreux scénarios, tels que le traitement de chemin d’accès à froid, en plus de l’exemple de chemin d’accès à chaud présenté ici.

1. Enregistrez et fermez le fichier **SimulatedDevice.py**.

    > [!NOTE]
    > Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling](Gestion des erreurs temporaires).


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>Ajouter une file d’attente à votre IoT Hub et y acheminer des messages

Dans cette section, vous allez créer une file d’attente Service Bus et un compte de stockage, les connecter à votre IoT Hub et configurer votre IoT Hub pour envoyer des messages à la file d’attente en fonction de la présence d’une propriété sur le message et tous les messages au compte de stockage. Pour plus d’informations sur le traitement des messages des files d’attente Service Bus, consultez [Prise en main des files d’attente][lnk-sb-queues-node] et pour plus d’informations sur la gestion du stockage, consultez [Prise en main du stockage Azure][Stockage Azure].

1. Créez une file d’attente Service Bus, comme décrit dans [Prise en main des files d’attente][lnk-sb-queues-node]. Prenez note de l’espace de noms et de la file d’attente.

    > [!NOTE]
    > Les options **Sessions** ou **Détection des doublons** ne doivent pas être activées pour les files d’attente et rubriques Service Bus utilisées comme points de terminaison IoT Hub. Si l’une de ces options est activée, le point de terminaison s’affiche comme **Inaccessible** dans le portail Azure.

1. Dans le portail Azure, ouvrez votre IoT Hub, puis cliquez sur **Points de terminaison**.

    ![Points de terminaison dans IoT hub][30]

1. En haut du panneau **Points de terminaison**, cliquez sur **Ajouter** pour ajouter votre file d’attente à votre IoT Hub. Nommez le point de terminaison **CriticalQueue**, puis utilisez les listes déroulantes pour sélectionner **File d’attente Service Bus**, l’espace de noms Service Bus dans lequel réside votre file d’attente, ainsi que le nom de votre file d’attente. Lorsque vous avez terminé, cliquez sur **OK** en bas.  

    ![Ajout d’un point de terminaison][31]

1. À présent, cliquez sur **Itinéraires** dans votre IoT Hub. En haut du panneau, cliquez sur **Ajouter** afin de créer une règle qui achemine les messages vers la file d’attente que vous venez d’ajouter. 

    ![Ajout d’un itinéraire][34]

    Saisissez un nom et sélectionnez **Messages des appareils** comme source de données. Sélectionnez **CriticalQueue** comme point de terminaison personnalisé en tant que point de terminaison de la règle de routage et indiquez `level="critical"` comme chaîne de requête. Cliquez sur **Enregistrer** au bas de la page.

    ![Détails de routage][32]

    Assurez-vous que l’itinéraire de secours est défini sur **ACTIVÉ**. Ce paramètre correspond à la configuration par défaut d’un IoT Hub.

    ![Itinéraire de secours][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Facultatif) Lecture à partir du point de terminaison de la file d’attente

Dans cette section, vous créez une application console Python qui lit les messages critiques à partir du Service Bus IoT. Consultez des informations complémentaires dans [Bien démarrer avec les files d’attente][lnk-sb-queues-node]. 

1. Ouvrez une nouvelle invite de commandes et installez le Kit de développement logiciel (SDK) Azure IoT Hub Device pour Python comme suit. Fermez l’invite de commandes après l’installation.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > Si vous rencontrez des problèmes lors de l’installation du package **azure-servicebus** ou pour obtenir d’autres options d’installation, consultez le [package Python azure-servicebus][lnk-python-service-bus].

1. Créez un fichier nommé **ReadCriticalQueue.py**. Ouvrez ce fichier dans un éditeur/IDE Python de votre choix (par exemple, IDLE).

1. Ajoutez le code suivant pour importer les modules requis à partir du Kit de développement logiciel (SDK) d’appareil :

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. Ajoutez le code suivant et remplacez l’espace réservé par les données de connexion pour votre Service Bus :

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. Ajoutez le code suivant pour vous connecter et lire le Service Bus : 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. Enfin, ajoutez la fonction principale. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. Enregistrez et fermez le fichier **ReadCriticalQueue.py**. Vous êtes maintenant prêt à exécuter les applications.


## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. Ouvrez une invite de commandes et installez l’IoT Hub Explorer. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. Exécutez la commande suivante sur l’invite de commande pour commencer à analyser les messages appareil-à-cloud à partir de votre appareil. Utilisez la chaîne de connexion de votre IoT Hub dans l’espace réservé après `--login`.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. Ouvrez une nouvelle invite de commandes et accédez au répertoire contenant le fichier **SimulatedDevice.py**.

1. Exécutez le fichier **SimulatedDevice.py** qui envoie régulièrement des données de télémétrie à votre IoT Hub. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. Pour exécuter l’application **ReadCriticalQueue**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au fichier **ReadCriticalQueue.py** et exécutez la commande suivante :

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. Observez les messages de l’appareil sur l’invite de commandes exécutant l’IoT Hub Explorer de la section précédente. Observez les messages `critical` dans l’application **ReadCriticalQueue**.

    ![Messages appareil-à-cloud Python][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Facultatif) Ajouter un conteneur de stockage à votre hub IoT et y acheminer des messages

Dans cette section, vous créez un compte de stockage, vous le connectez à votre hub IoT que vous configurez ensuite pour envoyer des messages au compte, en fonction de la présence d’une propriété sur le message. Pour plus d’informations sur la gestion du stockage, consultez [Bien démarrer avec Stockage Azure][Stockage Azure].

 > [!NOTE]
   > Les comptes IoT Hub créés sous le niveau _F1 Free_ sont limités à un **point de terminaison**. Si vous n’êtes pas limité à un seul **point de terminaison**, vous pouvez configurer **StorageContainer** en plus de **CriticalQueue** et exécuter les deux simultanément.

1. Créez un compte de stockage, comme décrit dans la [Documentation Stockage Azure][lnk-storage]. Notez le nom du compte.

2. Dans le portail Azure, ouvrez votre IoT Hub, puis cliquez sur **Points de terminaison**.

3. Dans le panneau **Points de terminaison**, sélectionnez le point de terminaison **CriticalQueue** et cliquez sur **Supprimer**. Cliquez sur **Oui**, puis sur **Ajouter**. Nommez le point de terminaison **StorageContainer** et utilisez les listes déroulantes pour sélectionner **Conteneur de stockage Azure** et créer un **Compte de stockage** et un **Conteneur de stockage**.  Notez les noms.  Lorsque vous avez terminé, cliquez sur **OK** en bas. 

 > [!NOTE]
   > Les comptes IoT Hub créés sous le niveau _F1 Free_ sont limités à un **point de terminaison**. Si vous n’êtes pas limité à un **Point de terminaison**, vous n’avez pas besoin de supprimer **CriticalQueue**.

4. Cliquez sur **Itinéraires** dans votre hub IoT. En haut du panneau, cliquez sur **Ajouter** afin de créer une règle qui achemine les messages vers la file d’attente que vous venez d’ajouter. Sélectionnez **Messages des appareils** comme source de données. Indiquez `level="storage"` comme condition, puis sélectionnez **StorageContainer** comme point de terminaison personnalisé en tant que point de terminaison de la règle de routage. Cliquez sur **Enregistrer** au bas de la page.  

    Assurez-vous que l’itinéraire de secours est défini sur **ACTIVÉ**. Ce paramètre correspond à la configuration par défaut d’un IoT Hub.

1. Assurez-vous que votre précédente application **SimulatedDevice.py** est toujours en cours d’exécution. 

1. Dans le portail Azure, accédez à votre compte de stockage sous **Service BLOB**, cliquez sur **Parcourir les objets blob...**.  Sélectionnez votre conteneur, accédez au fichier JSON et cliquez dessus, puis cliquez sur **Télécharger** pour afficher les données.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à répartir de façon fiable des messages appareil-à-cloud à l’aide de la fonctionnalité d’acheminement de messages d’IoT Hub.

Pour des exemples de solutions de bout en bout qui utilisent IoT Hub, voir [Azure IoT Suite][lnk-suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

Pour en savoir plus sur le routage des messages dans IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[prise en main du IoT Hub]: iot-hub-python-getstarted.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus