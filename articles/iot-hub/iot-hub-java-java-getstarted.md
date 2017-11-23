---
title: Prise en main avec Azure IoT Hub pour Java | Microsoft Docs
description: "Découvrez comment envoyer des messages appareil-vers-cloud à Azure IoT Hub à l’aide des kits SDK Azure IoT pour Java. Créez un appareil simulé et des applications de service pour inscrire votre appareil, envoyer des messages et lire des messages d’IoT Hub."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 70dae4a8-0e98-4c53-b5a5-9d6963abb245
ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b78179a5e7c7eaab7a18a1937aeb25c99b2789a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="connect-your-device-to-your-iot-hub-using-java"></a>Connecter votre appareil à votre IoT Hub à l’aide de Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous disposerez de trois applications console Java :

* **create-device-identity**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre application d’appareil.
* **read-d2c-messages**, qui affiche les données de télémétrie envoyées par votre application d’appareil.
* **simulated-device**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole MQTT.

> [!NOTE]
> L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks] fournit des informations sur les kits de développement logiciel Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* La version la plus récente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Dans la dernière étape, prenez note de la valeur de la **Clé primaire**. Cliquez sur **Points de terminaison**, puis sur le point de terminaison intégré **Événements**. Dans le panneau **Propriétés**, notez le **Nom compatible avec le hub d’événements** et l’**adresse du point de terminaison compatible avec le hub d’événements**. Ces trois valeurs sont nécessaires pour créer votre application **read-d2c-messages**.

![Panneau de messagerie IoT Hub du portail Azure][6]

Votre IoT Hub est maintenant créé. Vous disposez du nom d’hôte IoT Hub, de la chaîne de connexion IoT Hub, de la clé primaire IoT Hub, du nom compatible avec le hub d’événements et du point de terminaison compatible avec le hub d’événements, dont vous avez besoin pour terminer ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité d’appareil
Dans cette section, vous allez créer une application console Java qui crée une identité d’appareil dans le registre d’identités de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Reportez-vous à la section **Registre d’identité** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application de console, elle génère un ID d’appareil et une clé uniques que votre appareil peut utiliser pour s’identifier pour lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Créez un dossier vide appelé iot-java-get-started. Dans le dossier iot-java-get-started, créez un projet Maven nommé **create-device-identity** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au dossier create-device-identity.

3. Dans un éditeur de texte, ouvrez le fichier pom.xml situé dans le dossier create-device-identity et ajoutez la dépendance suivante au nœud **dependencies** . Cette dépendance permet d’utiliser le package client du service IoT dans votre application :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-service-client** avec la [recherche Maven][lnk-maven-service-search].

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.Device;
    import com.microsoft.azure.sdk.iot.service.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Ajoutez les variables de niveau classe suivantes à la classe **App**, en remplaçant **{yourhubconnectionstring}** par la valeur que vous avez notée précédemment :

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    ```
[!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

8. Modifiez la signature de la méthode **main** pour inclure les exceptions suivantes :

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```

9. Ajoutez le code suivant comme corps de la méthode **main** . Si ce n’est déjà fait, ce code crée un appareil appelé *javadevice* dans votre registre d’identités IoT Hub. Il affiche ensuite l’ID et la clé de l’appareil dont vous aurez besoin par la suite :

    ```java
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    // Create a device that's enabled by default, 
    // with an autogenerated key.
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      // If the device already exists.
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }

    // Display information about the
    // device you created.
    System.out.println("Device Id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Enregistrez et fermez le fichier App.java.

11. Pour générer l’application **create-device-identity** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier create-device-identity :

    ```cmd/sh
    mvn clean package -DskipTests
    ```

12. Pour exécuter l’application **create-device-identity** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier create-device-identity :

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Prenez note de **l’ID de l’appareil** et de la **clé de l’appareil**. Vous aurez besoin de ces valeurs ultérieurement lorsque vous créerez une application qui se connecte à IoT Hub en tant qu’appareil.

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID et les clés des appareils à utiliser comme informations d’identification de sécurité, et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages appareil-à-cloud

Dans cette section, vous allez créer une application console Java qui lit les messages des appareils vers le cloud dans l’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec les [hubs d’événements][lnk-event-hubs-overview] pour vous permettre de lire les messages des appareils vers le cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-process-d2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel [Prise en main des hubs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs et s’applique aux points de terminaison compatibles Event Hubs exposés par l’IoT Hub.

> [!NOTE]
> Le point de terminaison compatible Event Hub pour lire des messages de l’appareil vers le cloud utilise toujours le protocole AMQP.

1. Dans le dossier iot-java-get-started que vous avez créé à la section *Création d’une identité d’appareil*, créez un projet Maven appelé **read-d2c-messages** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au dossier read-d2c-messages.

3. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier read-d2c-messages et ajoutez la dépendance suivante au nœud **dependencies** . Cette dépendance vous permet d’utiliser le package eventhubs-client dans votre application pour effectuer la lecture à partir du point de terminaison compatible Event Hub :

    ```xml
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.15.0</version> 
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;

    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.function.*;
    ```

7. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez les chaînes **{youriothubkey}**, **{youreventhubcompatibleendpoint}** et **{youreventhubcompatiblename}** par les valeurs que vous avez notées précédemment :

    ```java
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Ajoutez la méthode **receiveMessages** suivante à la classe **App**. Cette méthode crée une instance **EventHubClient** permettant de vous connecter au point de terminaison compatible Event Hub, puis crée une instance **PartitionReceiver** de façon asynchrone pour lire les données à partir d’une partition Event Hub. La méthode boucle en continu et imprime les détails du message jusqu’à ce que l’application s’arrête.

    ```java
    // Create a receiver on a partition.
    private static EventHubClient receiveMessages(final String partitionId) {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      } catch (Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        // Create a receiver using the
        // default Event Hubs consumer group
        // that listens for messages from now on.
        client.createReceiver(EventHubClient.DEFAULT_CONSUMER_GROUP_NAME, partitionId, Instant.now())
          .thenAccept(new Consumer<PartitionReceiver>() {
            public void accept(PartitionReceiver receiver) {
              System.out.println("** Created receiver on partition " + partitionId);
              try {
                while (true) {
                  Iterable<EventData> receivedEvents = receiver.receive(100).get();
                  int batchSize = 0;
                  if (receivedEvents != null) {
                    System.out.println("Got some evenst");
                    for (EventData receivedEvent : receivedEvents) {
                      System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s",
                        receivedEvent.getSystemProperties().getOffset(),
                        receivedEvent.getSystemProperties().getSequenceNumber(),
                        receivedEvent.getSystemProperties().getEnqueuedTime()));
                      System.out.println(String.format("| Device ID: %s",
                        receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
                      System.out.println(String.format("| Message Payload: %s",
                        new String(receivedEvent.getBytes(), Charset.defaultCharset())));
                      batchSize++;
                    }
                  }
                  System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId, batchSize));
                }
              } catch (Exception e) {
                System.out.println("Failed to receive messages: " + e.getMessage());
              }
            }
          });
        } catch (Exception e) {
          System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

   > [!NOTE]
   > Cette méthode utilise un filtre lorsqu’elle crée le récepteur afin que ce dernier lise uniquement les messages envoyés à l’IoT Hub une fois que le récepteur a commencé à s’exécuter. Dans un environnement de test, cette technique permet de voir l’ensemble des messages en cours. Dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, consultez le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-process-d2c-tutorial].

9. Modifiez la signature de la méthode **main** en y incluant l’exception suivante :

    ```java
    public static void main( String[] args ) throws IOException
    ```

10. Ajoutez le code suivant à la méthode **main** dans la classe **App**. Ce code crée les deux instances **EventHubClient** et **PartitionReceiver** et vous permet de fermer l’application lorsque vous avez terminé le traitement des messages :

    ```java
    // Create receivers for partitions 0 and 1.
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    } catch (Exception e) {
      System.exit(1);
    }
    ```

    > [!NOTE]
    > Ce code repose sur l’hypothèse selon laquelle vous avez créé votre IoT Hub dans le niveau F1 (gratuit). Un IoT Hub gratuit comporte deux partitions nommées « 0 » et « 1 ».

11. Enregistrez et fermez le fichier App.java.

12. Pour générer l’application **read-d2c-messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier read-d2c-messages :

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Créer une application d’appareil
Dans cette section, vous allez créer une application console Java qui simule un appareil envoyant des messages des appareils vers le cloud à un IoT Hub.

1. Dans le dossier iot-java-get-started que vous avez créé à la section *Création d’une identité d’appareil*, créez un projet Maven appelé **simulated-device** en entrant la commande suivante à l’invite de commandes. Il s’agit d’une commande unique et longue :

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au dossier simulated-device.

3. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier simulated-device et ajoutez les dépendances suivantes au nœud **dependencies** . Cette dépendance vous permet d’utiliser le package iothub-java-client dans votre application pour communiquer avec votre IoT Hub et pour sérialiser les objets Java dans JSON :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-device-client** avec la [recherche Maven][lnk-maven-device-search].

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.google.gson.Gson;

    import java.io.*;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez la chaîne **{youriothubname}** par le nom de votre IoT Hub, et **{yourdevicekey}** par la valeur clé d’appareil que vous avez générée dans la section *Création d’une identité d’appareil* :

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Cet exemple d’application utilise la variable **protocol** lorsqu’il instancie un objet **DeviceClient**. Vous pouvez utiliser le protocole MQTT, AMQP ou HTTPS pour communiquer avec le IoT Hub.

8. Ajoutez la classe imbriquée **TelemetryDataPoint** suivante dans la classe **App** pour spécifier les données de télémétrie envoyées par votre appareil à votre IoT Hub :

    ```java
    private static class TelemetryDataPoint {
      public String deviceId;
      public double temperature;
      public double humidity;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Ajoutez la classe imbriquée **EventCallback** suivante dans la classe **App** pour afficher l’état d’accusé de réception que l’IoT Hub renvoie lorsqu’il traite un message provenant de l’application d’appareil. Cette méthode avertit également le thread principal de l’application lorsque le message a été traité :
   
    ```java
    private static class EventCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Ajoutez la classe imbriquée **MessageSender** suivante dans la classe **App**. La méthode **run** de cette classe génère un échantillon de données de télémétrie à envoyer à votre IoT Hub et attend un accusé de réception avant d’envoyer le message suivant :

    ```java
    private static class MessageSender implements Runnable {
      public void run()  {
        try {
          double minTemperature = 20;
          double minHumidity = 60;
          Random rand = new Random();
    
          while (true) {
            double currentTemperature = minTemperature + rand.nextDouble() * 15;
            double currentHumidity = minHumidity + rand.nextDouble() * 20;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.temperature = currentTemperature;
            telemetryDataPoint.humidity = currentHumidity;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            msg.setProperty("temperatureAlert", (currentTemperature > 30) ? "true" : "false");
            msg.setMessageId(java.util.UUID.randomUUID().toString()); 
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

    Cette méthode envoie un nouveau message Appareil vers cloud une seconde après que l’IoT Hub a accusé réception du message précédent. Ce message contient un objet JSON sérialisé avec l’ID de l’appareil et des nombres générés de manière aléatoire pour simuler un thermomètre et un capteur d’humidité.

11. Remplacez la méthode **main** par le code suivant qui crée un thread pour envoyer des messages des appareils vers le cloud à votre IoT Hub :

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.closeNow();
    }
    ```

12. Enregistrez et fermez le fichier App.java.

13. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulated-device :

    ```cmd/sh
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier read-d2c dossier, exécutez la commande suivante pour commencer à analyser la première partition de votre IoT Hub :

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Application Java du service IoT Hub pour surveiller les messages appareil-à-cloud][7]

2. À l’invite de commande dans le dossier simulated-device, exécutez la commande suivante pour commencer à envoyer les données de télémétrie à votre IoT Hub :

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Application Java de l’appareil IoT Hub pour envoyer les messages appareil-à-cloud][8]

3. La vignette **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au IoT Hub :

    ![Vignette Utilisation du portail Azure indiquant le nombre de messages envoyés à IoT Hub][43]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre des identités de l’IoT Hub. Vous avez utilisé cette identité d’appareil pour permettre à l’application d’appareil d’envoyer des messages appareil-à-cloud à l’IoT Hub. Vous avez également créé une application qui affiche les messages reçus par l’IoT Hub.

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Connexion de votre appareil][lnk-connect-device]
* [Prise en main de la gestion d’appareils][lnk-device-management]
* [Explore Azure IoT Edge architecture on Linux][lnk-iot-edge] (Découvrir l’architecture Azure IoT Edge sur Linux)

Pour découvrir comment étendre votre solution IoT et traiter les messages appareil-à-cloud à grande échelle, consultez le didacticiel [Traitement des messages appareil-à-cloud][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-eventhubs-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22
