---
title: Planification des travaux avec Azure IoT Hub (.NET/Node) | Microsoft Docs
description: "Procédure de planification d’une tâche Azure IoT Hub pour appeler une méthode directe sur plusieurs appareils. Vous utilisez Azure IoT device SDK pour Node.js afin d’implémenter les applications d’appareil simulé et Azure IoT service SDK pour .NET afin d’implémenter une application de service qui exécute la tâche."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: juanpere
ms.openlocfilehash: 3efe4612c6d628e070ba6e99808f79f61ead426e
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="schedule-and-broadcast-jobs-netnodejs"></a>Planifier et diffuser des travaux (.NET/Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Utilisez Azure IoT Hub pour planifier et suivre des travaux qui mettent à jour des millions d’appareils. Utilisez les travaux pour :

* Mettre à jour les propriétés souhaitées
* Mettre à jour les balises
* Appeler des méthodes directes

Un travail encapsule l’une de ces actions et suit l’exécution sur un ensemble d’appareils, défini par une requête de jumeau d’appareil. Par exemple, une application principale peut utiliser un travail pour appeler une méthode directe sur 10 000 appareils, qui les redémarre. Vous spécifiez l’ensemble des appareils avec une requête de jumeau d’appareil et planifiez le travail à exécuter ultérieurement. Le travail suit la progression à mesure que chacun de ces appareils reçoit et exécute la méthode directe de redémarrage.

Pour plus d’informations sur chacune de ces fonctionnalités, consultez les pages :

* Représentation d’appareil et propriétés : [Prise en main des représentations d’appareil][lnk-get-started-twin] et [Tutorial: How to use device twin properties][lnk-twin-props] (Didacticiel : Utilisation des propriétés de représentation d’appareil)
* Méthodes directes : [Guide du développeur IoT Hub - Méthodes directes][lnk-dev-methods] et [Tutorial: Use direct methods][lnk-c2d-methods] (Didacticiel : utilisation des méthodes directes)

Ce didacticiel vous explique les procédures suivantes :

* Créer une application pour appareils implémentant une méthode directe nommée **lockDoor**, qui peut être appelée par l’application principale. L’application pour appareils reçoit également les modifications de propriétés souhaitées envoyées par l’application principale.
* Créer une application principale qui crée un travail pour appeler la méthode directe **lockDoor** sur plusieurs appareils. Un autre travail envoie les mises à jour de propriétés souhaitées à plusieurs appareils.

À la fin de ce didacticiel, vous avez une application d’appareil de console Node.js et une application principale de console .NET (c#) :

**simDevice.js** qui se connecte au hub IoT, implémente la méthode directe **lockDoor** et gère les modifications de propriétés souhaitées.

**ScheduleJob** qui utilise les travaux pour appeler la méthode directe **lockDoor** et mettre à jour les propriétés souhaitées du jumeau de plusieurs appareils.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Node.js version 4.0.x ou version ultérieure. L’article [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Node.js pour ce didacticiel sur Windows ou sur Linux.
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planifier des travaux pour appeler une méthode directe et envoyer des mises à jour de jumeaux d’appareils

Dans cette section, vous allez créer une application console .NET (en C#) qui utilise des travaux pour appeler la méthode directe **lockDoor** et envoyer les mises à jour des propriétés souhaitées à plusieurs appareils.

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

1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT Hub pour le hub créé dans la section précédente.

    ```csharp
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static JobClient jobClient;
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
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            "deviceId='myDeviceId'",
            directMethod,
            DateTime.Now,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        var twin = new Twin();
        twin.Properties.Desired["Building"] = "43";
        twin.Properties.Desired["Floor"] = "3";
        twin.ETag = "*";

        JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
            "deviceId='myDeviceId'",
            twin,
            DateTime.Now,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Twin Update Job");
    }
    ```

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

    ```csharp
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

1. Dans l’Explorateur de solutions, sélectionnez **Définir les projets de démarrage...** et vérifiez que l’**Action** définie pour le projet **ScheduleJob** est **Démarrer**. Générez la solution.

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode directe appelée par le cloud, qui déclenche un redémarrage de l’appareil simulé et utilise les propriétés signalées pour permettre aux requêtes de la représentation d’appareil d’identifier des appareils et de déterminer le moment de leur dernier redémarrage.

1. Créez un dossier vide appelé **simDevice**.  Dans le dossier **simDevice** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes.  Acceptez toutes les valeurs par défaut :

    ```cmd/sh
    npm init
    ```

1. À l’invite de commandes, dans le dossier **simDevice**, exécutez la commande suivante pour installer les packages **azure-iot-device** et **azure-iot-device-mqtt** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Dans un éditeur de texte, créez un fichier **simDevice.js** dans le dossier **simDevice**.

1. Ajoutez les instructions ’require’ ci-dessous au début du fichier **simDevice.js** :

    ```nodejs
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

1. Ajoutez une variable **connectionString** et utilisez-la pour créer une instance de **Client**. Veillez à remplacer les espaces réservés par des valeurs appropriées à votre installation.

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Ajoutez la fonction suivante pour gérer la méthode **lockDoor**.

    ```nodejs
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

1. Ajoutez le code suivant pour inscrire le gestionnaire de la méthode **lockDoor**.

    ```nodejs
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```

1. Enregistrez et fermez le fichier **simDevice.js**.

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **simDevice**, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.

    ```cmd/sh
    node simDevice.js
    ```

1. Exécutez l’application console C# **ScheduleJob** en cliquant avec le bouton droit sur le projet **ScheduleJob**, puis en sélectionnant **Débogage** et **Démarrer une nouvelle instance**.

1. La sortie de l’appareil et des applications principales s’affiche.

    ![Exécuter les applications pour planifier des tâches][img-schedulejobs]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé un travail pour planifier une méthode directe sur un appareil et la mise à jour des propriétés de représentation de l’appareil.

Pour approfondir la prise en main d’IoT Hub et des modèles de gestion d’appareils, comme la mise à jour du microprogramme à distance, consultez le [Didacticiel : Mettre à jour un microprogramme][lnk-fwupdate].

Pour en savoir plus sur le déploiement d’une AI sur des appareils de périphérie avec Azure IoT Edge, consultez [Prise en main de IoT Edge][lnk-iot-edge].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
