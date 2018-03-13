---
title: "Prise en main d’Azure IoT Hub (.NET) | Microsoft Docs"
description: "Découvrez comment envoyer des messages appareil-vers-cloud à Azure IoT Hub à l’aide des kits SDK Azure IoT pour .NET. Créez un appareil simulé et des applications de service pour inscrire votre appareil, envoyer des messages et lire des messages d’IoT Hub."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ea8979f21fcc8a85e80db9b49377ba9a48836e2
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="connect-your-device-to-your-iot-hub-using-net"></a>Connecter votre appareil à votre IoT Hub à l’aide de .NET

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous disposerez de trois applications console .NET :

* **CreateDeviceIdentity**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre application d’appareil.
* **ReadDeviceToCloudMessages**, qui affiche les données de télémétrie envoyées par votre application d’appareil.
* **SimulatedDevice**, qui se connecte à votre IoT hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole MQTT.

Suivez les étapes de ce didacticiel pour créer les exemples d’applications de zéro. Vous pouvez également [télécharger](https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app/archive/master.zip) ou cloner une solution Visual Studio terminée à partir de Github :

```cmd/sh
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> Pour plus d’informations sur les kits de développement logiciel Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal, voir l’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Votre IoT Hub est maintenant créé et vous connaissez le nom d’hôte et la chaîne de connexion à IoT Hub dont vous avez besoin pour terminer ce qu’il reste du didacticiel.

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages appareil-à-cloud

Dans cette section, vous allez créer une application console .NET qui lit les messages des appareils vers le cloud dans l’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Azure Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages appareil-à-cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Pour découvrir comment traiter les messages appareil-à-cloud à grande échelle, reportez-vous au didacticiel [Traitement des messages appareil-à-cloud][lnk-process-d2c-tutorial]. Pour plus d’informations sur la façon de traiter les messages à partir des concentrateurs d’événements, reportez-vous au didacticiel [Prise en main des concentrateurs d’événements][lnk-eventhubs-tutorial]. (Ce didacticiel s’applique aux points de terminaison compatibles Event Hub IoT Hub).

> [!NOTE]
> Le point de terminaison compatible Event Hub pour lire des messages de l’appareil vers le cloud utilise toujours le protocole AMQP.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application console (.NET Framework)**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **ReadDeviceToCloudMessages**.

    ![Nouveau projet Visual C# Bureau classique Windows][10a]

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadDeviceToCloudMessages**, puis cliquez sur **Gérer les packages Nuget**.

1. Dans la fenêtre **Gestionnaire de package Nuget**, recherchez **WindowsAzure.ServiceBus**, cliquez sur **Installer** et acceptez les conditions d’utilisation. Cette procédure lance le téléchargement, l’installation et ajoute une référence à [Azure Service Bus][lnk-servicebus-nuget] avec toutes ses dépendances. Ce package permet à l'application de se connecter au point de terminaison compatible Event Hubs sur votre IoT hub.

1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

    ```csharp
    using Microsoft.ServiceBus.Messaging;
    using System.Threading;
    ```

1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT Hub pour le hub créé dans la section Créer un hub IoT.

    ```csharp
    static string connectionString = "{iothub connection string}";
    static string iotHubD2cEndpoint = "messages/events";
    static EventHubClient eventHubClient;
    ```

1. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested) break;
            EventData eventData = await eventHubReceiver.ReceiveAsync();
            if (eventData == null) continue;

            string data = Encoding.UTF8.GetString(eventData.GetBytes());
            Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
        }
    }
    ```

    Cette méthode utilise une instance **EventHubReceiver** pour recevoir des messages à partir de toutes les partitions de réception Appareil vers cloud IoT Hub. Notez la manière dont vous transmettez un paramètre `DateTime.Now` lorsque vous créez l’objet **EventHubReceiver** pour qu’il reçoive uniquement les messages envoyés après son démarrage. Dans un environnement de test, ce filtre permet de voir l’ensemble des messages en cours. Dans un environnement de production, votre code doit faire en sorte de traiter tous les messages. Pour plus d’informations, voir le didacticiel [Traiter les messages appareil-à-cloud IoT Hub en utilisant les itinéraires (.NET)][lnk-process-d2c-tutorial].

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

    ```csharp
    Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
    eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

    var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

    CancellationTokenSource cts = new CancellationTokenSource();

    System.Console.CancelKeyPress += (s, e) =>
    {
        e.Cancel = true;
        cts.Cancel();
        Console.WriteLine("Exiting...");
    };

    var tasks = new List<Task>();
    foreach (string partition in d2cPartitions)
    {
        tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
    }
    Task.WaitAll(tasks.ToArray());
    ```

## <a name="create-a-device-app"></a>Créer une application d’appareil

Dans cette section, vous allez créer une application console .NET qui simule un appareil envoyant des messages des appareils vers le cloud à un IoT Hub.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application console (.NET Framework)**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **SimulatedDevice**.

    ![Nouveau projet Visual C# Bureau classique Windows][10b]

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulatedDevice**, puis cliquez sur **Gérer les packages NuGet**.

1. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **Microsoft.Azure.Devices.Client**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices.Client**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Device SDK NuGet][lnk-device-nuget] et ses dépendances.

1. Ajoutez l'instruction `using` suivante en haut du fichier **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Newtonsoft.Json;
    ```

1. Ajoutez les champs suivants à la classe **Program** . Remplacez `{iot hub hostname}` par le nom d’hôte IoT Hub que vous avez récupéré dans le cadre de la section « Créer un hub IoT ». Remplacez `{device key}` par la clé d’appareil que vous avez récupérée au cours de la section « Création d’une identité d’appareil ».

    ```csharp
    static DeviceClient deviceClient;
    static string iotHubUri = "{iot hub hostname}";
    static string deviceKey = "{device key}";
    ```

1. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
    ```

    Cette méthode envoie un nouveau message Appareil vers cloud toutes les secondes. Ce message contient un objet JSON sérialisé avec l’ID de l’appareil et des nombres générés de manière aléatoire pour simuler un thermomètre et un capteur d’humidité.

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

    ```csharp
    Console.WriteLine("Simulated device\n");
    deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey ("myFirstDevice", deviceKey), TransportType.Mqtt);
    deviceClient.ProductInfo = "HappyPath_Simulated-CSharp";
    SendDeviceToCloudMessagesAsync();
    Console.ReadLine();
    ```

    Par défaut, la méthode de création **Create** d’une application .NET Framework crée une instance **DeviceClient** qui utilise le protocole AMQP pour communiquer avec IoT Hub. Pour utiliser le protocole MQTT ou HTTPS, remplacez la méthode **Create** afin de pouvoir spécifier le protocole. Les clients UWP et PCL utilisent le protocole HTTPS par défaut. Si vous utilisez le protocole HTTPS, vous devez également ajouter le package NuGet **Microsoft.AspNet.WebApi.Client** à votre projet de manière à inclure l’espace de noms **System.Net.Http.Formatting**.

Ce didacticiel vous accompagne tout au long des étapes de création d’une application d’appareil IoT Hub. Vous pouvez également utiliser l’extension [Connected Service for Azure IoT Hub][lnk-connected-service] de Visual Studio pour ajouter le code nécessaire à votre application d’appareil.

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, dans l’explorateur de solutions, cliquez avec le bouton droit sur votre solution, puis sur **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis **Démarrer** en tant qu’action pour les projets **ReadDeviceToCloudMessages** et **SimulatedDevice**.

    ![Propriétés de démarrage de projet][41]

1. Appuyez sur **F5** pour démarrer les deux applications. La sortie de console à partir de l’application **SimulatedDevice** affiche les messages envoyés par votre application d’appareil à votre IoT Hub. La sortie de console à partir de l’application **ProcessDeviceToCloudMessages** affiche les messages reçus par votre IoT Hub.

    ![Sortie de console à partir d’applications][42]

1. La vignette **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au IoT Hub :

    ![Vignette Utilisation du portail Azure][43]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre d’identités de l’IoT Hub. Vous avez utilisé cette identité d’appareil pour permettre à l’application d’appareil d’envoyer des messages appareil-à-cloud à l’IoT Hub. Vous avez également créé une application qui affiche les messages reçus par l’IoT Hub.

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Connexion de votre appareil][lnk-connect-device]
* [Prise en main de la gestion d’appareils][lnk-device-management]
* [Bien démarrer avec IoT Edge][lnk-iot-edge]

Pour découvrir comment étendre votre solution IoT et traiter les messages appareil-à-cloud à grande échelle, consultez le didacticiel [Traitement des messages appareil-à-cloud][lnk-process-d2c-tutorial].

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
