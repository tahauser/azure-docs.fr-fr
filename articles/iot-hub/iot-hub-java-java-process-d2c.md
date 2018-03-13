---
title: Routage des messages avec Azure IoT Hub (Java) | Microsoft Docs
description: "Comment traiter des messages appareil-à-cloud Azure IoT Hub en utilisant les règles de routage et les points de terminaison personnalisés pour distribuer les messages vers d’autres services de serveur principal."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: 92ab10e5b8487e03d92b69114a2e3c5302f95ed6
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-java"></a>Routage des messages avec IoT Hub (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Les autres didacticiels ([Prise en main d’IoT Hub] et [Envoyer des messages cloud-à-appareil avec IoT Hub][lnk-c2d]) vous expliquent comment utiliser la fonctionnalité de base de messagerie appareil-à-cloud et cloud-à-appareil offerte par IoT Hub.

Ce didacticiel s’appuie sur le code indiqué dans le didacticiel [Prise en main d’IoT Hub] et explique comment utiliser une méthode de configuration d’itinéraire pour traiter des messages appareil-à-cloud de façon évolutive. Ce didacticiel vous indique comment traiter les messages qui nécessitent une action immédiate du serveur principal de la solution. Par exemple, un appareil peut envoyer un message d’alerte qui déclenche l’insertion d’un ticket dans un système CRM. Par opposition, les messages de point de données sont simplement chargés dans un moteur d’analyse. Par exemple, les données de télémétrie de température d’un appareil qui doivent être enregistrées pour analyse ultérieure constituent un message de point de données.

À la fin de ce didacticiel, vous exécutez trois applications de console Java :

* **simulated-device**, une version modifiée de l’application créée dans le didacticiel [Prise en main d’IoT Hub] qui envoie des messages de point de données des appareils vers le cloud chaque seconde et des messages interactifs des appareils vers le cloud toutes les 10 secondes. Cette application utilise le protocole AMQPS pour communiquer avec IoT Hub.
* **read-d2c-messages** affiche les données de télémétrie envoyées par votre application pour appareils.
* **read-critical-queue** enlève les messages critiques de la file d’attente Service Bus attachée au hub IoT.

> [!NOTE]
> IoT Hub offre la prise en charge de Kit de développement logiciel (SDK) de plusieurs plateformes d’appareils et de plusieurs langages (notamment C, Java et JavaScript). Pour obtenir des instructions expliquant comment remplacer l’appareil de ce didacticiel par un appareil physique et comment connecter vos appareils à un hub IoT, consultez le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Une version d’utilisation complète du didacticiel [Prise en main d’IoT Hub] .
* La version la plus récente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Nous recommandons également la lecture de [Stockage Azure] et [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Envoyer des messages interactifs à partir d’une application pour appareils
Dans cette section, vous allez modifier l’application pour appareils que vous avez créée dans le didacticiel [Prise en main d’IoT Hub] de façon à envoyer occasionnellement des messages nécessitant un traitement immédiat.

1. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java. Ce fichier contient le code pour l’application **simulated-device** que vous avez créée dans le didacticiel [Prise en main d’IoT Hub] .

2. Remplacez la classe **MessageSender** par le code suivant :

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        if (new Random().nextDouble() > 0.5) {
                            msgStr = "This is a critical message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "critical");
                        } else {
                            msgStr = "This is a storage message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "storage");
                        }
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Cette méthode ajoute de façon aléatoire la propriété `"level": "critical"` et `"level": "storage"` aux messages envoyés par l’appareil, ce qui simule un message nécessitant une action immédiate du serveur principal de l’application, ou demandant à être définitivement stocké. L’application prend en charge le routage des messages en fonction de leur corps.
   
   > [!NOTE]
   > Vous pouvez utiliser les propriétés de message pour acheminer les messages pour de nombreux scénarios, tels que le traitement de chemin d’accès à froid, en plus de l’exemple de chemin d’accès à chaud présenté ici.

2. Enregistrez et fermez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

    > [!NOTE]
    > Nous vous recommandons vivement de mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] (Gestion des erreurs temporaires).

3. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulated-device :

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Ajouter une file d’attente à votre IoT Hub et y acheminer des messages

Dans cette section, vous allez créer une file d’attente Service Bus, la connecter à votre IoT Hub et configurer votre IoT hub pour envoyer des messages à la file d’attente en fonction de la présence d’une propriété sur le message. Pour plus d’informations sur la façon de traiter les messages des files d’attente Service Bus, consultez [Prise en main des files d’attente][lnk-sb-queues-java].

1. Créez une file d’attente Service Bus, comme décrit dans [Prise en main des files d’attente][lnk-sb-queues-java]. Prenez note de l’espace de noms et de la file d’attente.

    > [!NOTE]
    > Les options **Sessions** ou **Détection des doublons** ne doivent pas être activées pour les files d’attente et rubriques Service Bus utilisées comme points de terminaison IoT Hub. Si l’une de ces options est activée, le point de terminaison s’affiche comme **Inaccessible** dans le portail Azure.

2. Dans le portail Azure, ouvrez votre IoT Hub, puis cliquez sur **Points de terminaison**.

    ![Points de terminaison dans IoT hub][30]

3. En haut du panneau **Points de terminaison**, cliquez sur **Ajouter** pour ajouter votre file d’attente à votre IoT Hub. Nommez le point de terminaison **CriticalQueue**, puis utilisez les listes déroulantes pour sélectionner **File d’attente Service Bus**, l’espace de noms Service Bus dans lequel réside votre file d’attente, ainsi que le nom de votre file d’attente. Lorsque vous avez terminé, cliquez sur **Enregistrer** en bas.

    ![Ajout d’un point de terminaison][31]

4. À présent, cliquez sur **Itinéraires** dans votre IoT Hub. En haut du panneau, cliquez sur **Ajouter** afin de créer une règle qui achemine les messages vers la file d’attente que vous venez d’ajouter. Sélectionnez **DeviceTelemetry** comme source de données. Saisissez `level="critical"` comme condition, puis sélectionnez la file d’attente que vous venez d’ajouter comme point de terminaison personnalisé en tant que point de terminaison de la règle de routage. Lorsque vous avez terminé, cliquez sur **Enregistrer** en bas.

    ![Ajout d’un itinéraire][32]

    Assurez-vous que l’itinéraire de secours est défini sur **ACTIVÉ**. Ce paramètre correspond à la configuration par défaut d’un IoT Hub.

    ![Itinéraire de secours][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Facultatif) Lecture à partir du point de terminaison de la file d’attente

Vous pouvez éventuellement lire les messages à partir du point de terminaison de la file d’attente en suivant les instructions fournies dans [Prise en main des files d’attente][lnk-sb-queues-java]. Nommez l’application **read-critical-queue**.

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les trois applications.

1. Pour exécuter l’application **read-d2c-messages**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier read-d2c et exécutez la commande suivante :

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Exécution de read-d2c-messages][readd2c]

2. Pour exécuter l’application **read-critical-queue**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier read-critical-queue et exécutez la commande suivante :

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Exécution de read-critical-messages][readqueue]

3. Pour exécuter l’application **simulated-device**, dans l’invite de commandes ou l’interpréteur de commandes, accédez au dossier simulated-device, puis exécutez la commande suivante :

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Exécuter simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Facultatif) Ajouter un conteneur de stockage à votre hub IoT et y acheminer des messages

Dans cette section, vous créez un compte de stockage, vous le connectez à votre hub IoT que vous configurez ensuite pour envoyer des messages au compte, en fonction de la présence d’une propriété sur le message. Pour plus d’informations sur la gestion du stockage, consultez [Bien démarrer avec Stockage Azure][Stockage Azure].

 > [!NOTE]
   > Si vous n’êtes pas limité à un seul **point de terminaison**, vous pouvez configurer **StorageContainer** en plus de **CriticalQueue** et exécuter les deux simultanément.

1. Créez un compte de stockage, comme décrit dans la [Documentation Stockage Azure] [lnk-storage]. Notez le nom du compte.

2. Dans le portail Azure, ouvrez votre IoT Hub, puis cliquez sur **Points de terminaison**.

3. Dans le panneau **Points de terminaison**, sélectionnez le point de terminaison **CriticalQueue** et cliquez sur **Supprimer**. Cliquez sur **Oui**, puis sur **Ajouter**. Nommez le point de terminaison **StorageContainer** et utilisez les listes déroulantes pour sélectionner **Conteneur de stockage Azure** et créer un **Compte de stockage** et un **Conteneur de stockage**.  Notez les noms.  Lorsque vous avez terminé, cliquez sur **OK** en bas. 

 > [!NOTE]
   > Si vous n’êtes pas limité à un **Point de terminaison**, vous n’avez pas besoin de supprimer **CriticalQueue**.

4. Cliquez sur **Itinéraires** dans votre hub IoT. En haut du panneau, cliquez sur **Ajouter** afin de créer une règle qui achemine les messages vers la file d’attente que vous venez d’ajouter. Sélectionnez **Messages des appareils** comme source de données. Indiquez `level="storage"` comme condition, puis sélectionnez **StorageContainer** comme point de terminaison personnalisé en tant que point de terminaison de la règle de routage. Cliquez sur **Enregistrer** au bas de la page.  

    Assurez-vous que l’itinéraire de secours est défini sur **ACTIVÉ**. Ce paramètre correspond à la configuration par défaut d’un IoT Hub.

1. Assurez-vous que vos applications précédentes sont toujours en cours d’exécution. 

1. Dans le portail Azure, accédez à votre compte de stockage sous **Service BLOB**, cliquez sur **Parcourir les objets blob...**.  Sélectionnez votre conteneur, accédez au fichier JSON et cliquez dessus, puis cliquez sur **Télécharger** pour afficher les données.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à répartir de façon fiable des messages appareil-à-cloud à l’aide de la fonctionnalité d’acheminement de messages d’IoT Hub.

Le didacticiel [Envoi de messages cloud-à-appareil avec IoT Hub][lnk-c2d] vous montre comment envoyer des messages à vos appareils à partir de votre serveur principal de solution.

Pour des exemples de solutions de bout en bout qui utilisent IoT Hub, voir [Azure IoT Suite][lnk-suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

Pour en savoir plus sur le routage des messages dans IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Prise en main d’IoT Hub]: iot-hub-java-java-getstarted.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Gestion des erreurs temporaires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
