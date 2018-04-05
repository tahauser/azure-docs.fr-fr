---
title: Mise à jour d’un microprogramme d’appareil avec Azure IoT Hub (.NET/.NET) | Microsoft Docs
description: Guide d’utilisation de la gestion des appareils sur Azure IoT Hub pour lancer une mise à jour du microprogramme d’un appareil. Vous utilisez Azure IoT device SDK pour .NET afin d’implémenter une application d’appareil simulé et Azure IoT service SDK pour .NET afin d’implémenter une application de service qui déclenche la mise à jour du microprogramme.
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: 984fd7516b5c05973c45891f4eda31c41a5a2be5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Utilisation de la gestion des appareils pour lancer une mise à jour du microprogramme d’un appareil (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introduction
Dans le didacticiel [Prise en main de la gestion d’appareils][lnk-dm-getstarted], vous avez vu comment utiliser les primitives de [représentation d’appareil physique][lnk-devtwin] et de [méthodes directives][lnk-c2dmethod] pour redémarrer à distance un appareil. Ce didacticiel utilise les mêmes primitives IoT Hub et montre comment effectuer une mise à jour du microprogramme simulée de bout en bout.  Ce modèle est utilisé dans l’implémentation de la mise à jour du microprogramme de l’exemple [d’implémentation d’appareil Raspberry Pi][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel vous explique les procédures suivantes :

* Créez une application console .NET qui appelle une méthode directe **firmwareUpdate** sur l’application d’appareil simulé via votre IoT Hub.
* Créez un appareil simulé qui implémente une méthode directe **firmwareUpdate**. Cette méthode lance un processus en plusieurs étapes qui attend de télécharger l’image du microprogramme, la télécharge et enfin, l’applique. À chaque étape du processus de mise à jour, l’appareil utilise les propriétés signalées pour mettre à jour la progression.

À la fin de ce didacticiel, vous avez une application d’appareil de console .NET (C#) et une application principale de console .NET (C#) :

* **SimulatedDeviceFwUpdate**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit la méthode directe **firmwareUpdate**, s’exécute pour simuler une mise à jour du microprogramme dans un processus à plusieurs états, consistant à attendre avant de télécharger l’image, à télécharger celle-ci, puis finalement à l’appliquer.

* **TriggerFWUpdate**, qui utilise le SDK de service pour appeler à distance la méthode directe **firmwareUpdate** sur l’appareil simulé, affiche la réponse et indique périodiquement (toutes les 500 ms) les propriétés signalées mises à jour.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Pour créer votre IoT Hub et obtenir votre chaîne de connexion, procédez de la manière décrite dans [Prise en main de la gestion d’appareils](iot-hub-csharp-csharp-device-management-get-started.md).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Déclencher une mise à jour du microprogramme à distance sur l’appareil à l’aide d’une méthode directe
Dans cette section, vous créez une application de console .NET (à l’aide de C#) qui lance une mise à jour de microprogramme à distance sur un appareil. L’application utilise une méthode directe pour lancer la mise à jour et des requêtes de jumeau d’appareil pour obtenir régulièrement l’état de la mise à jour du microprogramme actif.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **TriggerFWUpdate**.

    ![Nouveau projet Visual C# Bureau classique Windows][img-createserviceapp]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **TriggerFWUpdate**, puis cliquez sur **Gérer les packages NuGet**.
3. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet][lnk-nuget-service-sdk] et ses dépendances.

    ![Fenêtre du gestionnaire de package NuGet][img-servicenuget]
4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur des espaces réservés par la chaîne de connexion pour le IoT Hub créé dans la section précédente et l’ID de votre appareil.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Ajoutez la méthode suivante à la classe **Program**. Cette méthode interroge le jumeau d'appareil pour obtenir l’état mis à jour toutes les 500 millisecondes. Elle écrit dans la console seulement lorsque l’état a changé. Dans cet exemple, pour empêcher la consommation de messages de IoT Hub supplémentaires dans votre abonnement, l’interrogation s’arrête lorsque l’appareil signale un état **applyComplete** ou une erreur.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Enfin, ajoutez les lignes suivantes à la méthode **Main** . Cette opération crée un gestionnaire de registre pour lire le jumeau d'appareil, lance la tâche d’interrogation sur un thread de travail, puis déclenche la mise à jour du microprogramme.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Générez la solution.

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous allez :

* Créer une application console .NET qui répond à une méthode directe appelée par le cloud
* Simuler une mise à jour de microprogramme déclenchée par un service principal via une méthode directe
* Utiliser les propriétés signalées pour activer les requêtes sur la représentation d’appareil afin d’identifier les appareils et l’heure de leur dernière mise à jour de microprogramme

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **SimulateDeviceFWUpdate**.
   
    ![Nouvelle application pour appareil Windows classique Visual C#][img-createdeviceapp]
    
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulateDeviceFWUpdate**, puis cliquez sur **Gérer les packages NuGet**.
3. Dans la fenêtre **Gestionnaire de package NuGet**, sélectionnez **Parcourir**, puis recherchez **microsoft.azure.devices.client**. Sélectionnez **Installer** pour installer le package **Microsoft.Azure.Devices.Client**, puis acceptez les conditions d’utilisation. Cette procédure télécharge, installe et ajoute une référence au package NuGet [ Azure IoT device SDK][lnk-nuget-client-sdk] et à ses dépendances.
   
    ![Application cliente dans la fenêtre du gestionnaire de package NuGet][img-clientnuget]
4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion de l’appareil notée dans la section **Créer une identité d’appareil**.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Ajoutez la méthode suivante pour signaler l’état au cloud via le jumeau d'appareil : 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Ajoutez la méthode suivante pour simuler le téléchargement de l’image de microprogramme :
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Ajoutez la méthode suivante pour simuler l’application de l’image de microprogramme à l’appareil :
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Ajoutez la méthode suivante pour simuler l’attente de téléchargement de l’image de microprogramme. Mettez à jour l’état sur **attente** et effacez les autres propriétés de mise à jour de microprogramme sur le jumeau d’appareil. Ces propriétés sont effacées pour supprimer toutes les valeurs existantes des précédentes mises à jour de microprogramme. Cela est nécessaire, car les propriétés déclarées sont envoyées en tant qu’opération PATCH (delta) et non via une opération PUT (un ensemble complet de propriétés qui remplace toutes les valeurs précédentes). Généralement, un appareil est informé de la disponibilité d’une mise à jour, et une stratégie définie par un administrateur a pour effet que l’appareil commence à télécharger et à appliquer la mise à jour. C’est dans cette fonction qu’intervient la logique d’activation de cette stratégie. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Ajoutez la méthode suivante pour effectuer le téléchargement. Elle met à jour l’état sur **téléchargement** via le jumeau d’appareil, appelle la méthode de simulation de téléchargement et signale un état **downloadComplete** ou **downloadFailed** via le jumeau d’appareil selon les résultats de l’opération de téléchargement. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Ajoutez la méthode suivante pour appliquer l’image. Elle met à jour l’état sur **application** via le jumeau d’appareil, appelle la méthode d’application d’image et met à jour l’état sur **applyComplete** ou **applyFailed** via le jumeau d’appareil selon les résultats de l’opération d’application. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Ajoutez la méthode suivante pour séquencer l’opération de mise à jour de microprogramme de l’attente au téléchargement de l’image via l’application de l’image à l’appareil :
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Ajoutez la méthode suivante pour gérer la méthode directe **updateFirmware** à partir du cloud. Elle extrait l’URL pour la mise à jour du microprogramme de la charge utile de message et la transmet à la tâche **doUpdate**, qui démarre sur un autre thread de pool de threads. Elle renvoie alors immédiatement la réponse de la méthode vers le cloud.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Cette méthode déclenche la mise à jour simulée à exécuter en tant que **tâche** et répond immédiatement à l’appel de méthode pour informer le service que la mise à jour du microprogramme a été démarrée. L’état de la mise à jour et son achèvement seront envoyés au service via les propriétés déclarées du jumeau d'appareil. Nous répondons à l’appel de méthode lors du démarrage de la mise à jour, plutôt qu’après son exécution, car :
> * Un véritable processus de mise à jour prendra probablement plus longtemps que le délai d’expiration des appels de méthode.
> * Un véritable processus de mise à jour nécessitera probablement un redémarrage, qui relancerait cette application avec l’objet **MetodRequest** indisponible. (La mise à jour les propriétés déclarées est toutefois possible, même après un redémarrage.) 

14. Enfin, ajoutez le code suivant à la méthode **Main** pour ouvrir la connexion à votre IoT Hub et initialiser l’écouteur de la méthode :
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Générez la solution.       

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].


## <a name="run-the-apps"></a>Exécuter les applications
Vous êtes maintenant prêt à exécuter les applications.
1. Exécutez l’application d’appareil .NET **SimulatedDeviceFWUpdate**.  Cliquez avec le bouton droit sur le projet **SimulatedDeviceFWUpdate**, puis sélectionnez **Déboguer** et **Démarrer une nouvelle instance**. Elle doit commencer à écouter les appels de méthode provenant de votre IoT Hub : 

2. Une fois que l’appareil est connecté et en attente d’appels de méthode, exécutez l’application .NET **TriggerFWUpdate** pour appeler la méthode de mise à jour de microprogramme dans l’application pour appareil simulé. Cliquez avec le bouton droit sur le projet **TriggerFWUpdate**, puis sélectionnez **Déboguer** et **Démarrer une nouvelle instance**. La séquence de mise à jour doit être écrite dans la console **SimulatedDeviceFWUpdate** et signalée via les propriétés déclarées de l’appareil dans la console **TriggerFWUpdate**. Notez que le processus prend plusieurs secondes. 
   
    ![Exécution d’application de service et d’appareil][img-combinedrun]


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher une mise à jour du microprogramme à distance sur un appareil, et utilisé les propriétés signalées pour suivre la progression de la mise à jour du microprogramme.

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/