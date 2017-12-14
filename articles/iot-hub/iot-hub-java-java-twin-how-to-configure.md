---
title: "Utiliser les propriétés des jumeaux d’appareils Azure IoT Hub (Java) | Microsoft Docs"
description: "Guide d’utilisation des représentations d’appareils Azure IoT Hub pour configurer des appareils. Vous utilisez Azure IoT device SDK pour Java afin d’implémenter une application d’appareil simulé et Azure IoT service SDK pour Java afin d’implémenter une application de service qui modifie une configuration d’appareil avec un jumeau d’appareil."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 72b57a2495d1a03a57923fb171ee17c0f870ddc7
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Utilisation des propriétés souhaitées pour configurer des appareils

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

À la fin de ce didacticiel, vous disposerez de deux applications console Java :

* **simulated-device**, application d’appareil simulé qui attend une mise à jour de la configuration souhaitée, et signale l’état d’un processus de mise à jour de la configuration simulée.
* **set-configuration**, application principale, qui définit la configuration souhaitée sur un appareil et interroge le processus de mise à jour de la configuration.

> [!NOTE]
> L’article [Kits Azure IoT SDK][lnk-hub-sdks] fournit des informations sur les kits Azure IoT SDK que vous pouvez utiliser pour générer des applications pour appareil et des applications principales.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* La version la plus récente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Si vous avez suivi le didacticiel [Prise en main des jumeaux d’appareils][lnk-twin-tutorial], vous avez déjà un IoT Hub et une identité d’appareil nommée **myDeviceId**. Vous pouvez donc passer à la section [Créer l’application d’appareil simulé][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Créer l’application d’appareil simulée
Dans cette section, vous allez créer une application console Java qui se connecte à votre hub en tant que **myDeviceId**, attend une mise à jour de la configuration souhaitée, puis signale des mises à jour sur le processus de mise à jour de la configuration simulée.

1. Créez un dossier vide nommé dt-get-started.

1. Dans le dossier dt-get-started, créez un projet Maven nommé **simulated-device** en entrant la commande suivante dans votre invite de commandes. Il s’agit d’une commande unique et longue :

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. À l’invite de commandes, accédez au dossier simulated-device.

1. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier simulated-device et ajoutez la dépendance suivante au nœud **dependencies** . Cette dépendance vous permet d’utiliser le package iot-device-client dans votre application pour communiquer avec votre IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-device-client** avec [la recherche Maven][lnk-maven-device-search].

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

1. Enregistrez et fermez le fichier pom.xml.

1. À l’aide d’un éditeur de texte, ouvrez le fichier source simulated-device\src\main\java\com\mycompany\app\App.java.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez **{yourdeviceconnectionstring}** par la chaîne de connexion de l’appareil que vous avez notée dans la section *Créer une identité d’appareil* :

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. Pour implémenter un gestionnaire de rappel pour les événements d’état des jumeaux d’appareils (à des fins de débogage), ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Ajoutez la classe imbriquée ci-après dans la classe **App** :

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > La classe TelemetryConfig étend la [classe Device][lnk-java-device-class] pour accéder aux rappels de propriétés souhaitées.

1. Modifiez la signature de la méthode **main** pour déclencher les exceptions suivantes :

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Ajoutez le code suivant à la méthode **main** pour instancier un **DeviceClient** et une **TelemetryConfig** :

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. Ajoutez le code suivant à la méthode **main** pour démarrer les services du jumeau d’appareil :

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. Ajoutez le code suivant à la méthode **main** pour arrêter le simulateur d’appareil si nécessaire :

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. Enregistrez et fermez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

1. Générez l’application principale **simulated-device** et corrigez les erreurs éventuelles. À l’invite de commandes, accédez au dossier simulated-device et exécutez la commande suivante :

    `mvn clean package -DskipTests`

   > [!NOTE]
   > Ce didacticiel ne simule pas de comportement pour des mises à jour de configuration simultanées. Certains processus de mise à jour de configuration peuvent s’adapter à des modifications de configuration de la cible en cours de mise à jour, d’autres peuvent avoir à les mettre en file d’attente, et d’autres encore peuvent les refuser avec une condition d’erreur. Prenez en considération le comportement souhaité pour votre processus de configuration spécifique, et ajoutez la logique appropriée avant de lancer la modification de la configuration.
   > 
   > 

## <a name="create-the-service-app"></a>Créer l’application de service
Dans cette section, vous allez créer une application console Java qui met à jour les *propriétés souhaitées* sur le jumeau d’appareil associé à **myDeviceId** avec un nouvel objet de configuration de télémétrie. Elle interroge ensuite les représentations d’appareils stockées dans l’IoT Hub, puis affiche la différence entre les configurations souhaitées et signalées de l’appareil.

1. Dans le dossier dt-get-started, créez un projet Maven nommé **set-configuration** en entrant la commande suivante dans votre invite de commandes. Il s’agit d’une commande unique et longue :

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. Dans votre invite de commandes, accédez au dossier set-configuration.

1. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier trigger-reboot et ajoutez la dépendance suivante au nœud **dependencies**. Cette dépendance vous permet d’utiliser le package iot-device-client dans votre application pour communiquer avec votre IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-service-client** avec [la recherche Maven][lnk-maven-service-search].

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

1. Enregistrez et fermez le fichier pom.xml.

1. À l’aide d’un éditeur de texte, ouvrez le fichier source set-configuration\src\main\java\com\mycompany\app\App.java.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Ajoutez les variables de niveau classe suivantes à la classe **App** . Remplacez **{youriothubconnectionstring}** par la chaîne de connexion IoT Hub que vous avez notée dans la section *Créer un hub IoT* :

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. Pour interroger et mettre à jour les jumeaux d’appareils sur l’appareil simulé, ajoutez le code suivant à la méthode **main** :

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. Enregistrez et fermez le fichier set-desired-configuration\src\main\java\com\mycompany\app\App.java.

1. Générez l’application principale **set-configuration** et corrigez les erreurs éventuelles. Dans votre invite de commandes, accédez au dossier set-configuration et exécutez la commande suivante :

    `mvn clean package -DskipTests`
   
    Ce code récupère le jumeau d’appareil de **myDeviceId**, puis met à jour ses propriétés souhaitées avec un nouvel objet de configuration de télémétrie.
    Après cela, il interroge toutes les 10 secondes les jumeaux d’appareils stockées dans l’IoT Hub, puis imprime les configurations de télémétrie souhaitées et signalées. Pour savoir comment générer des rapports riches sur tous vos appareils, voir le [langage de requête IoT Hub][lnk-query].
   
   > [!IMPORTANT]
   > Cette application interroge IoT Hub toutes les 10 secondes à titre d’exemple, jusqu’à la mise à jour de l’appareil. Utilisez des requêtes pour générer des rapports côté utilisateur sur plusieurs appareils, non pour détecter des modifications. Si votre solution nécessite des notifications d’événements d’appareil en temps réel, utilisez des [notifications jumelles][lnk-twin-notifications].
   > 

   > [!IMPORTANT]
   > Un délai pouvant atteindre une minute s’écoule entre l’opération de signalement de l’appareil et le résultat de la requête. Il permet à l’infrastructure de requête d’opérer à très grande échelle.  Pour récupérer des affichages cohérents d’un même jumeau d’appareil, utilisez la méthode **getDeviceTwin** de la classe **DeviceTwin**.
   > 
   > 

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans l’invite de commandes, exécutez la commande suivante dans le dossier simulated-device pour lancer l’écoute des appels de jumeaux d’appareils à partir de votre hub IoT :

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Dans l’invite de commandes, exécutez la commande suivante dans le dossier set-configuration pour interroger et mettre à jour les jumeaux d’appareils sur votre appareil simulé à partir de votre hub IoT :

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. L’appareil simulé répond à l’appel de méthode directe de redémarrage :

    ![L’application d’appareil simulé IoT Hub Java répond à l’appel du jumeau d’appareil][img-deviceconfigured]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez défini une configuration souhaitée en tant que *propriétés souhaitées* à partir du serveur principal et écrit une application d’appareil pour détecter cette modification et simuler un processus de mise à jour en plusieurs étapes signalant son état par le biais des propriétés signalées.

Utilisez les ressources suivantes :

* Pour savoir comment envoyer les données de télémétrie à partir d’appareils, consultez le didacticiel [Prise en main d’IoT Hub][lnk-iothub-getstarted].
* Pour savoir comment planifier ou exécuter des opérations sur de grands ensembles d’appareils, consultez le didacticiel [Planifier et diffuser des travaux][lnk-schedule-jobs].
* Pour savoir comment contrôler des appareils de façon interactive (par exemple en mettant en marche un ventilateur à partir d’une application contrôlée par l’utilisateur), consultez le didacticiel [Utiliser des méthodes directes][lnk-methods-tutorial].

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
