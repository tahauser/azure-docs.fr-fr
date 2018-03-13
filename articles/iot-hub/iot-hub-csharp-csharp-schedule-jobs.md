---
title: Planifier des travaux avec Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: "Procédure de planification d’une tâche Azure IoT Hub pour appeler une méthode directe sur plusieurs appareils. Vous utilisez le Kit de développement logiciel Azure IoT device SDK pour .NET afin d’implémenter les applications d’appareil simulé et une application de service pour exécuter le travail."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 012/16/2018
ms.author: v-masebo
ms.openlocfilehash: 0cdc23683489e103b061044856d68a8b014d3535
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Planifier et diffuser des travaux (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Utilisez Azure IoT Hub pour planifier et suivre des travaux qui mettent à jour des millions d’appareils. Utilisez les travaux pour :

* Mettre à jour les propriétés souhaitées
* Mettre à jour les balises
* Appeler des méthodes directes

Un travail encapsule l’une de ces actions et suit l’exécution sur un ensemble d’appareils, défini par une requête de jumeau d’appareil. Par exemple, une application principale peut utiliser un travail pour appeler une méthode directe sur 10 000 appareils, qui les redémarre. Vous spécifiez l’ensemble des appareils avec une requête de jumeau d’appareil et planifiez le travail à exécuter ultérieurement. Le travail suit la progression à mesure que chacun de ces appareils reçoit et exécute la méthode directe de redémarrage.

Pour plus d’informations sur chacune de ces fonctionnalités, consultez les pages :

* Représentation d’appareil et propriétés : [Prise en main des représentations d’appareil][lnk-get-started-twin] et [Tutorial: How to use device twin properties][lnk-twin-props] (Didacticiel : Utilisation des propriétés de représentation d’appareil)
* Méthodes directes : [Guide du développeur IoT Hub - Méthodes directes][lnk-dev-methods] et [Tutorial: Use direct methods][lnk-c2d-methods] (Didacticiel : utilisation des méthodes directes)

Ce didacticiel vous explique les procédures suivantes :

* Création d’une application pour appareils implémentant une méthode directe nommée **LockDoor**, qui peut être appelée par l’application back-end.
* Création d’une application back-end qui crée un travail pour appeler la méthode directe **LockDoor** sur plusieurs appareils. Un autre travail envoie les mises à jour de propriétés souhaitées à plusieurs appareils.

À la fin de ce didacticiel, vous disposerez de deux applications console .NET (C#) :

**SimulateDeviceMethods**, qui se connecte à votre Hub IoT et implémente la méthode directe **LockDoor**.

**ScheduleJob**, qui utilise des travaux pour appeler la méthode directe **LockDoor** et mettre à jour les propriétés souhaitées du jumeau d’appareil sur plusieurs appareils.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous allez créer une application console .NET qui répond à une méthode directe appelée par le serveur principal de la solution.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **SimulateDeviceMethods**.
   
    ![Nouvelle application pour appareil Windows classique Visual C#][img-createdeviceapp]
    
1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulateDeviceMethods**, puis cliquez sur **Gérer les packages NuGet...**.

1. Dans la fenêtre **Gestionnaire de package NuGet**, sélectionnez **Parcourir**, puis recherchez **microsoft.azure.devices.client**. Sélectionnez **Installer** pour installer le package **Microsoft.Azure.Devices.Client**, puis acceptez les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence au package NuGet [ Azure IoT device SDK][lnk-nuget-client-sdk] et à ses dépendances.
   
    ![Application cliente dans la fenêtre du gestionnaire de package NuGet][img-clientnuget]

1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion de l’appareil que vous avez notée dans la section précédente :

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;

1. Add the following to implement the direct method on the device:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }

1. Add the following to implement the device twins listener on the device:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Enfin, ajoutez le code suivant à la méthode **Main** pour ouvrir la connexion à votre IoT Hub et initialiser l’écouteur de la méthode :
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. Enregistrez votre travail et générez votre solution.         

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une nouvelle tentative de connexion), comme suggéré dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planifier des travaux pour appeler une méthode directe et envoyer des mises à jour de jumeaux d’appareils

Dans cette section, vous allez créer une application console .NET (en C#) qui utilise des travaux pour appeler la méthode directe **LockDoor** et envoyer les mises à jour des propriétés souhaitées à plusieurs appareils.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **ScheduleJob**.

    ![Nouveau projet Visual C# Bureau classique Windows][img-createapp]

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ScheduleJob**, puis cliquez sur **Gérer les packages NuGet...**.

1. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette étape lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet][lnk-nuget-service-sdk] et ses dépendances.

    ![Fenêtre du gestionnaire de package NuGet][img-servicenuget]

1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Ajoutez l’instruction `using` suivante si elle ne figure pas déjà dans les instructions par défaut.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Ajoutez les champs suivants à la classe **Program** . Remplacez les espaces réservés par la chaîne de connexion IoT Hub du Hub que vous avez créé dans la section précédente et par le nom de votre appareil.

    ```csharp
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Ajoutez une autre méthode à la classe **Program** :

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Pour plus d’informations sur la syntaxe de requête, consultez l’article [Langage de requête IoT Hub][lnk-query].
    > 

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Enregistrez votre travail et générez votre solution. 


## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur votre solution, puis cliquez sur **Générer**, **Plusieurs projets de démarrage**. Assurez-vous que `SimulateDeviceMethods` figure en haut de la liste, suivi de `ScheduleJob`. Définissez leurs deux actions sur **Démarrer**, puis cliquez sur **OK**.

1. Exécutez les projets en cliquant sur **Démarrer** ou accédez au menu **Déboguer** et cliquez sur **Démarrer le débogage**.

1. La sortie de l’appareil et des applications principales s’affiche.

    ![Exécuter les applications pour planifier des tâches][img-schedulejobs]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé un travail pour planifier une méthode directe sur un appareil et la mise à jour des propriétés de représentation de l’appareil.

Pour approfondir la prise en main d’IoT Hub et des modèles de gestion d’appareils, comme la mise à jour du microprogramme à distance, consultez le [Didacticiel : Mettre à jour un microprogramme][lnk-fwupdate].

Pour en savoir plus sur le déploiement d’une AI sur des appareils de périphérie avec Azure IoT Edge, consultez [Prise en main de IoT Edge][lnk-iot-edge].

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: iot-hub-csharp-csharp-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-csharp-csharp-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-csharp-csharp-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-query-language
