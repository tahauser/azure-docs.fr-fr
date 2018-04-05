---
title: Planification des travaux avec Azure IoT Hub (Java) | Microsoft Docs
description: Procédure de planification d’une tâche Azure IoT Hub pour appeler une méthode directe et définir une propriété de votre choix sur plusieurs appareils. Vous utilisez Azure IoT device SDK pour Java afin d’implémenter les applications d’appareil simulé, et Azure IoT service SDK pour Java afin d’implémenter une application de service qui exécute la tâche.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dobett
ms.openlocfilehash: af853a378a36c97edd74ea99fc5dcce92a073fea
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-and-broadcast-jobs-java"></a>Planifier et diffuser des travaux (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Utilisez Azure IoT Hub pour planifier et suivre des travaux qui mettent à jour des millions d’appareils. Utilisez les travaux pour :

* Mettre à jour les propriétés souhaitées
* Mettre à jour les balises
* Appeler des méthodes directes

Un travail encapsule l’une de ces actions et suit l’exécution sur un ensemble d’appareils. Une requête de jumeau d’appareil définit l’ensemble d’appareils sur lesquels le travail s’exécute. Par exemple, une application principale peut utiliser un travail pour appeler une méthode directe sur 10 000 appareils et les redémarrer. Vous spécifiez l’ensemble des appareils avec une requête de jumeau d’appareil et planifiez le travail à exécuter ultérieurement. Le travail suit la progression à mesure que chacun de ces appareils reçoit et exécute la méthode directe de redémarrage.

Pour plus d’informations sur chacune de ces fonctionnalités, consultez les pages :

* Jumeau d’appareil et propriétés : [Prise en main des représentations d’appareils (Node)](iot-hub-java-java-twin-getstarted.md)
* Méthodes directes : [Guide du développeur IoT Hub - Méthodes directes](iot-hub-devguide-direct-methods.md) et [Didacticiel : utilisation des méthodes directes](iot-hub-java-java-direct-methods.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel vous explique les procédures suivantes :

* Créer une application pour périphérique implémentant une méthode directe nommée **lockDoor**. L’application pour périphérique reçoit également les modifications de propriétés souhaitées envoyées par l’application principale.
* Créer une application principale qui crée un travail pour appeler la méthode directe **lockDoor** sur plusieurs appareils. Un autre travail envoie les mises à jour de propriétés souhaitées à plusieurs appareils.

À la fin de ce didacticiel, vous disposerez d’une application pour périphérique de console Java et d’une application principale de console Java :

**simulated-device** se connecte à IoT Hub et implémente la méthode directe **lockDoor** et gère les modifications de propriétés souhaitées.

**schedule-jobs** utilise des travaux pour appeler la méthode directe **lockDoor** et mettre à jour les propriétés souhaitées du jumeau de plusieurs appareils.

> [!NOTE]
> L’article relatif aux [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md) fournit des informations sur les Kits de développement logiciel (SDK) Azure que l’on peut utiliser pour générer des applications pour périphérique et des applications principales.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* La version la plus récente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Si vous préférez créer l’identité de l’appareil par programmation, lisez la section correspondante dans l’article [Connecter votre appareil à votre IoT Hub à l’aide de Java](iot-hub-java-java-getstarted.md#create-a-device-identity). Vous pouvez également utiliser l’outil [Extension IoT pour Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) pour ajouter un appareil à votre IoT Hub.

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous créez une application console Java qui utilise des travaux pour :

* Appeler la méthode directe **lockDoor** sur plusieurs appareils.
* Envoyer les propriétés souhaitées à plusieurs appareils.

Pour créer l’application :

1. Sur votre ordinateur de développement, créez un dossier vide nommé `iot-java-schedule-jobs`.

1. Dans le dossier `iot-java-schedule-jobs`, créez un projet Maven nommé **schedule-jobs** en lançant la commande ci-dessous dans votre invite de commandes. Il s’agit d’une commande unique et longue :

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Dans votre invite de commandes, accédez au dossier `schedule-jobs`.

1. Dans un éditeur de texte, ouvrez le fichier `pom.xml` dans le dossier `schedule-jobs` et ajoutez la dépendance suivante au nœud **dependencies**. Cette dépendance vous permet d’utiliser le package **iot-service-client** dans votre application pour communiquer avec votre IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-service-client** avec la [recherche Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Ajoutez le nœud **build** suivant après le nœud **dependencies**. Cette configuration ordonne à Maven d’utiliser Java 1.8 pour générer l’application :

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Enregistrez et fermez le fichier `pom.xml`.

1. Ouvrez le fichier `schedule-jobs\src\main\java\com\mycompany\app\App.java` dans un éditeur de texte.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

1. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez `{youriothubconnectionstring}` par la chaîne de connexion de votre IoT Hub, que vous avez notée dans la section *Créer un IoT Hub* :

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. Ajoutez la méthode suivante à la classe **App** pour planifier un travail et mettre à jour les propriétés **Construction** et **Plancher** souhaitées dans le jumeau d’appareil :

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

1. Pour planifier un travail afin d’appeler la méthode **lockDoor**, ajoutez la méthode suivante à la classe **App** :

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

1. Pour surveiller un travail, ajoutez la méthode suivante à la classe **App** :

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

1. Pour obtenir les détails des travaux que vous avez exécutés, ajoutez la méthode suivante :

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

1. Mettez à jour la signature de la méthode **main** pour qu’elle inclue la clause `throws` suivante :

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. Pour exécuter et surveiller les deux tâches de manière séquentielle, ajoutez le code suivant à la méthode **principal** :

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

1. Enregistrez et fermez le fichier`schedule-jobs\src\main\java\com\mycompany\app\App.java`.

1. Générez l’application **schedule-jobs** et corrigez les erreurs éventuelles. Dans votre invite de commandes, accédez au dossier `schedule-jobs` et exécutez la commande suivante :

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Créer une application d’appareil

Dans cette section, vous créez une application de console Java qui gère les propriétés souhaitées envoyées à partir de IoT Hub et implémente l’appel de méthode directe.

1. Dans le dossier `iot-java-schedule-jobs`, créez un projet Maven nommé **simulated-device** en lançant la commande suivante dans votre invite de commandes. Il s’agit d’une commande unique et longue :

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Dans votre invite de commandes, accédez au dossier `simulated-device`.

1. Dans un éditeur de texte, ouvrez le fichier `pom.xml` dans le dossier `simulated-device` et ajoutez les dépendances suivantes au nœud **dependencies**. Cette dépendance vous permet d’utiliser le package **iot-device-client** dans votre application pour communiquer avec votre IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-device-client** avec la [recherche Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Ajoutez le nœud **build** suivant après le nœud **dependencies**. Cette configuration ordonne à Maven d’utiliser Java 1.8 pour générer l’application :

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Enregistrez et fermez le fichier `pom.xml`.

1. Ouvrez le fichier `simulated-device\src\main\java\com\mycompany\app\App.java` dans un éditeur de texte.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez `{youriothubname}` par le nom de votre IoT Hub, et `{yourdevicekey}` par la valeur de clé de l’appareil que vous avez générée dans la section *Créer une identité d’appareil* :

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Cet exemple d’application utilise la variable **protocol** lorsqu’il instancie un objet **DeviceClient**.

1. Pour imprimer des informations du jumeau d’appareil dans la console, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. Pour imprimer des notifications relatives à la méthode directe dans la console, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. Pour gérer les appels de méthode directe à partir de IoT Hub, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

1. Mettez à jour la signature de la méthode **main** pour qu’elle inclue la clause `throws` suivante :

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. Ajoutez le code suivant à la méthode **main** pour :
    * créer un client d’appareil afin de communiquer avec IoT Hub.
    * créer un objet **Device** afin de stocker les propriétés du jumeau d’appareil.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

1. Pour démarrer les services du client de l’appareil, ajoutez le code suivant à la méthode **principale** :

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

1. Pour attendre que l’utilisateur appuie sur la touche **Entrée** avant l’arrêt, ajoutez le code suivant à la fin de la méthode **principal** :

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. Enregistrez et fermez le fichier `simulated-device\src\main\java\com\mycompany\app\App.java`.

1. Générez l’application **simulated-device** et corrigez les erreurs. Dans votre invite de commandes, accédez au dossier `simulated-device` et exécutez la commande suivante :

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications de console.

1. À l’invite de commande du dossier `simulated-device`, exécutez la commande suivante pour démarrer l’écoute d’application de l’appareil pour les modifications de propriété et appels de méthode directe souhaités :

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Le client de périphérique démarre](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. À l’invite de commande du dossier `schedule-jobs`, exécutez la commande suivante pour faire fonctionner l’application de service **schedule-jobs** lors de l’exécution de deux travaux. Le premier définit les valeurs de propriété de votre choix et le second appelle la méthode directe :

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![L’application de service Java IoT Hub crée deux travaux](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. L’application pour périphérique gère la modification de propriété souhaitée et l’appel de méthode directe :

    ![Le client d’appareil répond aux modifications](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre des identités de l’IoT Hub. Vous avez créé une application principale pour exécuter deux travaux. Le premier travail a défini des valeurs de propriété de votre choix, et le deuxième a appelé une méthode directe.

Utilisez les ressources suivantes :

* Envoyez des données de télémétrie à partir d’appareils en suivant le didacticiel [Bien démarrer avec IoT Hub](iot-hub-java-java-getstarted.md).
* Contrôlez les appareils de façon interactive (par exemple pour mettre en marche un ventilateur à partir d’une application contrôlée par l’utilisateur) en suivant le didacticiel [Utiliser des méthodes directes](iot-hub-java-java-direct-methods.md).
