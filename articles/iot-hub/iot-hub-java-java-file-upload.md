---
title: "Charger des fichiers sur Azure IoT Hub à partir d’appareils en Java | Microsoft Docs"
description: "Guide pratique pour charger des fichiers sur le cloud à partir d’un appareil avec Azure IoT device SDK pour Java. Les fichiers téléchargés sont stockés dans un conteneur d’objets blob de stockage Azure."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: b6e1ef5a5b3f9298134a7c312ac7d1927cf41a7f
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ce didacticiel s’appuie sur le code du didacticiel [Envoyer des messages du cloud vers un appareil avec IoT Hub](iot-hub-java-java-c2d.md) pour montrer comment utiliser les [fonctions de chargement de fichiers d’IoT Hub](iot-hub-devguide-file-upload.md) afin de charger un fichier sur le [Stockage Blob Azure](../storage/index.yml). Ce didacticiel explique les procédures suivantes :

- Fournissez en toute sécurité à un appareil un URI d’objet blob Azure pour le chargement d’un fichier.
- Utilisez les notifications de chargement de fichier IoT Hub pour déclencher le traitement du fichier dans votre serveur principal d’application.

Les didacticiels [Bien démarrer avec IoT Hub](iot-hub-java-java-getstarted.md) et [Envoyer des messages du cloud vers un appareil avec IoT Hub](iot-hub-java-java-c2d.md) illustrent les fonctionnalités de base de la messagerie d’un appareil vers le cloud et du cloud vers un appareil offertes par IoT Hub. Le didacticiel [Traiter les messages appareil-à-cloud](iot-hub-java-java-process-d2c.md) décrit un moyen de stocker en toute fiabilité les messages appareil-à-cloud dans le stockage d’objets blob Azure. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Par exemple : 

* Fichiers volumineux qui contiennent des images
* vidéos
* Données de vibration échantillonnées à une fréquence élevée
* Un certain type de données prétraitées.

Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory](../data-factory/introduction.md) ou de la pile [Hadoop](../hdinsight/index.md). Lorsque vous avez besoin de charger des fichiers à partir d’un appareil, vous pouvez quand même exploiter la sécurité et la fiabilité d’IoT Hub.

À la fin de ce didacticiel, vous exécuterez deux applications de console Java :

* **simulated-device**, version modifiée de l’application créée dans le cadre du didacticiel [Envoyer des messages du cloud vers un appareil avec IoT Hub]. Cette application charge un fichier de stockage à l’aide d’un URI SAP fourni par votre IoT Hub.
* **read-file-upload-notification**, qui reçoit les notifications de chargement de fichiers envoyées par le hub IoT.

> [!NOTE]
> IoT Hub prend en charge de nombreuses plateformes d’appareils et de nombreux langages (notamment C, .NET et JavaScript) par le biais des kits Azure IoT device SDK. Pour obtenir des instructions pas à pas expliquant comment connecter votre appareil à Azure IoT Hub, voir le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* La version la plus récente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Charger un fichier à partir d’une application pour appareils

Dans cette section, vous allez modifier l’application pour appareils que vous avez créée dans [Envoyer des messages du cloud vers un appareil avec IoT Hub](iot-hub-java-java-c2d.md) pour charger un fichier sur IoT Hub.

1. Copiez un fichier image dans le dossier `simulated-device` et renommez-le `myimage.png`.

1. Ouvrez le fichier `simulated-device\src\main\java\com\mycompany\app\App.java` dans un éditeur de texte.

1. Ajoutez la déclaration de variable à la classe **App** :

    ```java
    private static String fileName = "myimage.png";
    ```

1. Pour traiter les messages de rappel d’état du chargement de fichiers, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. Pour charger des images sur IoT Hub, ajoutez la méthode suivante à la classe **App** :

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

1. Modifiez la méthode **main** de façon à appeler la méthode **uploadFile**, comme dans l’extrait de code suivant :

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

1. Utilisez la commande suivante pour générer l’application **simulated-device** et vérifiez l’absence d’erreurs :

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>Recevoir une notification de téléchargement de fichier

Dans cette section, vous allez créer une application console Java qui reçoit des messages de notification de chargement de fichiers envoyés par IoT Hub.

Vous avez besoin de la chaîne de connexion **iothubowner** de votre hub IoT pour suivre cette section. Vous trouverez la chaîne de connexion sur le panneau **Stratégie d’accès partagé** du [Portail Azure](https://portal.azure.com/).

1. Créez un projet Maven nommé **read-file-upload-notification** en lançant la commande ci-dessous dans votre invite de commandes. Il s’agit d’une commande unique et longue :

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. Dans votre invite de commandes, accédez au nouveau dossier `read-file-upload-notification`.

1. Dans un éditeur de texte, ouvrez le fichier `pom.xml` dans le dossier `read-file-upload-notification` et ajoutez la dépendance suivante au nœud **dependencies**. L’ajout de la dépendance vous permet d’utiliser le package **iothub-java-service-client** dans votre application pour communiquer avec votre service d’IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-service-client** avec la [recherche Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Enregistrez et fermez le fichier `pom.xml`.

1. Ouvrez le fichier `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` dans un éditeur de texte.

1. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. Ajoutez les variables de niveau classe ci-après à la classe **App** :

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. Pour imprimer des informations sur le chargement de fichiers dans la console, ajoutez la classe imbriquée suivante à la classe **App** :

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Recieve file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

1. Pour démarrer le thread qui écoute les notifications de chargement de fichiers, ajoutez le code suivant à la méthode **main** :

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

1. Enregistrez et fermez le fichier `read-file-upload-notification\src\main\java\com\mycompany\app\App.java`.

1. Utilisez la commande suivante pour générer l’application **read-file-upload-notification** et vérifiez l’absence d’erreurs :

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

Dans une invite de commandes, exécutez la commande suivante dans le dossier `read-file-upload-notification` :

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Dans une invite de commandes, exécutez la commande suivante dans le dossier `simulated-device` :

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

La capture d’écran suivante montre la sortie de l’application **simulated-device** :

![Sortie de l’application simulated-device](media/iot-hub-java-java-upload/simulated-device.png)

La capture d’écran suivante montre la sortie de l’application **read-file-upload-notification** :

![Sortie de l’application read-file-upload-notification](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Vous pouvez utiliser le portail pour afficher le fichier chargé dans le conteneur de stockage que vous avez configuré :

![Fichier chargé](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et fonctionnalités d’IoT Hub avec les articles suivants :

* [Créer un IoT Hub par programme][lnk-create-hub]
* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Simulation d’un appareil avec IoT Edge][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Centre de développement Azure IoT]: http://azure.microsoft.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md


