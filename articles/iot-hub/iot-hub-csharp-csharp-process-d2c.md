---
title: Routage des messages avec Azure IoT Hub (.Net) | Microsoft Docs
description: "Comment traiter des messages appareil-à-cloud Azure IoT Hub en utilisant les règles de routage et les points de terminaison personnalisés pour distribuer les messages vers d’autres services principaux."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 4794f81787fd575c34fa8a2ef66431d85dcf4a5d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="routing-messages-with-iot-hub-net"></a>Routage des messages avec IoT Hub (.Net)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Ce didacticiel s’appuie sur celui relatif à la [prise en main du IoT Hub]. Le didacticiel :

* Explique comment utiliser les règles de routage pour dispatcher les messages de l’appareil au cloud suivant une configuration facile.
* Indique comment isoler les messages interactifs qui nécessitent une action immédiate du serveur principal de la solution pour un traitement ultérieur. Par exemple, un appareil peut envoyer un message d’alerte qui déclenche l’insertion d’un ticket dans un système CRM. Par opposition, les messages de point de données tels que la télémétrie de température sont chargés dans un moteur d’analyse.

À la fin de ce didacticiel, vous exécutez trois applications console .NET :

* **SimulatedDevice**, une version modifiée de l’application créée dans le didacticiel [prise en main du IoT Hub] qui envoie des messages de point de données des appareils vers le cloud chaque seconde et des messages interactifs des appareils vers le cloud toutes les 10 secondes.
* **ReadDeviceToCloudMessages**, qui affiche les données non critiques de télémétrie envoyées par votre application d’appareil.
* **ReadCriticalQueue** enlève de la file d’attente Service Bus les messages critiques envoyés par votre application d’appareil. Cette file d’attente est attachée à IoT hub.

> [!NOTE]
> IoT Hub offre la prise en charge de Kit de développement logiciel (SDK) de plusieurs plateformes d’appareils et de plusieurs langages (notamment C, Java et JavaScript). Pour savoir comment remplacer l’appareil simulé de ce didacticiel par un appareil physique, voir le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

Nous recommandons également la lecture de [Stockage Azure] et [Azure Service Bus].

## <a name="send-interactive-messages"></a>Envoyer des messages interactifs

Modifiez l’application d’appareil créée dans le didacticiel [prise en main du IoT Hub] de façon à envoyer occasionnellement des messages interactifs.

Dans Visual Studio, dans le projet **SimulatedDevice**, remplacez la méthode `SendDeviceToCloudMessagesAsync` par le code suivant :

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Cette méthode ajoute de façon aléatoire la propriété `"level": "critical"` et `"level": "storage"` aux messages envoyés par l’appareil, ce qui simule un message nécessitant une action immédiate du serveur principal de l’application, ou demandant à être définitivement stocké. L’application prend en charge le routage des messages en fonction de leur corps.

> [!NOTE]
> Vous pouvez utiliser les propriétés de message pour acheminer les messages pour de nombreux scénarios, tels que le traitement de chemin d’accès à froid, en plus de l’exemple de chemin d’accès à chaud présenté ici.

> [!NOTE]
> Nous vous recommandons vivement de mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling] (Gestion des erreurs temporaires).

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Router les messages vers une file d’attente dans votre IoT hub

Dans cette section, vous allez :

* créer une file d’attente Service Bus ;
* la connecter à votre IoT Hub ;
* configurer votre IoT Hub pour envoyer des messages à la file d’attente, en fonction de la présence d’une propriété dans le message.

Pour plus d’informations sur la façon de traiter les messages des files d’attente Service Bus, consultez [Prise en main des files d’attente][Service Bus queue].

1. Créez une file d’attente Service Bus, comme décrit dans [Prise en main des files d’attente][Service Bus queue]. La file d’attente doit être située dans les mêmes région et abonnement que votre IoT hub. Prenez note de l’espace de noms et de la file d’attente.

    > [!NOTE]
    > Les options **Sessions** ou **Détection des doublons** ne doivent pas être activées pour les files d’attente et rubriques Service Bus utilisées comme points de terminaison IoT Hub. Si l’une de ces options est activée, le point de terminaison s’affiche comme **Inaccessible** dans le portail Azure.

2. Dans le portail Azure, ouvrez votre IoT Hub, puis cliquez sur **Points de terminaison**.
    
    ![Points de terminaison dans IoT hub][30]

3. En haut du panneau **Points de terminaison**, cliquez sur **Ajouter** pour ajouter votre file d’attente à votre IoT Hub. Nommez le point de terminaison **CriticalQueue**, puis utilisez les listes déroulantes pour sélectionner **File d’attente Service Bus**, l’espace de noms Service Bus dans lequel réside votre file d’attente, ainsi que le nom de votre file d’attente. Lorsque vous avez terminé, cliquez sur **Enregistrer** en bas.
    
    ![Ajout d’un point de terminaison][31]
    
4. À présent, cliquez sur **Itinéraires** dans votre IoT Hub. En haut du panneau, cliquez sur **Ajouter** afin de créer une règle qui achemine les messages vers la file d’attente que vous venez d’ajouter. Sélectionnez **DeviceTelemetry** comme source de données. Saisissez `level="critical"` comme condition, puis sélectionnez la file d’attente que vous venez d’ajouter comme point de terminaison personnalisé en tant que point de terminaison de la règle de routage. Lorsque vous avez terminé, cliquez sur **Enregistrer** en bas.
    
    ![Ajout d’un itinéraire][32]
    
    Assurez-vous que l’itinéraire de secours est défini sur **ACTIVÉ**. Il s’agit de la configuration par défaut d’une instance IoT Hub.
    
    ![Itinéraire de secours][33]

## <a name="read-from-the-queue-endpoint"></a>Lecture à partir du point de terminaison de la file d’attente

Dans cette section, vous allez lire les messages à partir du point de terminaison de la file d’attente.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application console (.NET Framework)**. Nommez le projet **ReadCriticalQueue**.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadCriticalQueue**, puis cliquez sur **Gérer les packages NuGet**. Cela affiche la fenêtre **Gestionnaire de packages NuGet**.

3. Recherchez **WindowsAzure.ServiceBus**, cliquez sur **Installer**, puis acceptez les conditions d’utilisation. Cette opération lance le téléchargement, l’installation et ajoute une référence à Azure Service Bus avec toutes ses dépendances.

4. Ajoutez les instructions **using** suivantes au début du fichier **Program.cs** :
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Enfin, ajoutez les lignes suivantes à la méthode **Main** . Remplacez la chaîne de connexion par des autorisations **Listen** pour la file d’attente :
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre solution, puis sélectionnez **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez **Démarrer** en tant qu’action pour les projets **ReadDeviceToCloudMessages**, **SimulatedDevice** et **ReadCriticalQueue**.
2. Appuyez sur **F5** pour lancer les trois applications de console. L’application **ReadDeviceToCloudMessages** reçoit uniquement les messages non critiques provenant de l’application **SimulatedDevice**, tandis que l’application **ReadCriticalQueue** reçoit uniquement les messages critiques.
   
   ![Trois applications de console][50]

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
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/
[Guide du développeur IoT Hub]: iot-hub-devguide.md
[prise en main du IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
