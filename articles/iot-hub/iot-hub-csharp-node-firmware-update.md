---
title: Mise à jour d’un microprogramme d’appareil avec Azure IoT Hub (.NET/Node) | Microsoft Docs
description: Guide d’utilisation de la gestion des appareils sur Azure IoT Hub pour lancer une mise à jour du microprogramme d’un appareil. Vous utilisez Azure IoT device SDK pour Node.js afin d’implémenter une application d’appareil simulé et Azure IoT service SDK pour .NET afin d’implémenter une application de service qui déclenche la mise à jour du microprogramme.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.openlocfilehash: e54ba3e3015c7175814c7f4e3330ad2de3819136
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>Utilisation de la gestion des appareils pour lancer une mise à jour du microprogramme d’un appareil (.NET/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Dans le didacticiel [Prise en main de la gestion d’appareils][lnk-dm-getstarted], vous avez vu comment utiliser les primitives de [représentation d’appareil physique][lnk-devtwin] et de [méthodes directives][lnk-c2dmethod] pour redémarrer à distance un appareil. Ce didacticiel utilise les mêmes primitives IoT Hub et montre comment effectuer une mise à jour du microprogramme simulée de bout en bout.  Ce modèle est utilisé dans l’implémentation de la mise à jour du microprogramme de l’exemple [d’implémentation d’appareil Raspberry Pi][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel vous explique les procédures suivantes :

* Créez une application console .NET qui appelle une méthode directe firmwareUpdate sur l’application d’appareil simulé via votre IoT Hub.
* Créez un appareil simulé qui implémente une méthode directe **firmwareUpdate**. Cette méthode lance un processus en plusieurs étapes qui attend de télécharger l’image du microprogramme, la télécharge et enfin, l’applique. À chaque étape du processus de mise à jour, l’appareil utilise les propriétés signalées pour mettre à jour la progression.

À la fin de ce didacticiel, vous avez une application d’appareil de console Node.js et une application principale de console .NET (c#) :

**dmpatterns_fwupdate_service.js**, qui appelle une méthode directe sur l’application d’appareil simulé, affiche la réponse, et affiche périodiquement (toutes les 500 ms) les propriétés signalées mises à jour.

**TriggerFWUpdate**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit une méthode directe firmwareUpdate, s’exécute pour simuler une mise à jour du microprogramme dans un processus à plusieurs états, consistant à attendre avant de télécharger l’image, à télécharger celle-ci, puis finalement à l’appliquer.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Node.js version 4.0.x ou version ultérieure. <br/>  L’article [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Node.js pour ce didacticiel sur Windows ou sur Linux.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Pour créer votre IoT Hub et obtenir votre chaîne de connexion, procédez de la manière décrite dans [Prise en main de la gestion d’appareils](iot-hub-csharp-node-device-management-get-started.md).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Déclencher une mise à jour du microprogramme à distance sur l’appareil à l’aide d’une méthode directe
Dans cette section, vous créez une application de console .NET (à l’aide de C#) qui lance une mise à jour de microprogramme à distance sur un appareil. L’application utilise une méthode directe pour lancer la mise à jour et des requêtes de jumeau d’appareil pour obtenir régulièrement l’état de la mise à jour du microprogramme actif.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **TriggerFWUpdate**.

    ![Nouveau projet Visual C# Bureau classique Windows][img-createapp]

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **TriggerFWUpdate** puis cliquez sur **Gérer les packages NuGet...**.
1. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet][lnk-nuget-service-sdk] et ses dépendances.

    ![Fenêtre du gestionnaire de package NuGet][img-servicenuget]
1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur des espaces réservés par la chaîne de connexion pour le IoT Hub créé dans la section précédente et l’ID de votre appareil.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. Ajoutez la méthode suivante à la classe **Program** :
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. Ajoutez la méthode suivante à la classe **Program** :

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

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. Dans l’Explorateur de solutions, sélectionnez **Définir les projets de démarrage...** et vérifiez que l’**Action** définie pour le projet **TriggerFWUpdate** est **Démarrer**.

1. Générez la solution.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Exécuter les applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Dans Visual Studio, cliquez avec le bouton droit sur le projet **TriggerFWUpdate**, sélectionnez **Déboguer**, puis **Démarrer une nouvelle instance**.

3. La réponse de l’appareil à la méthode directe s’affiche dans la console.

    ![Le microprogramme a été mis à jour][img-fwupdate]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher une mise à jour du microprogramme à distance sur un appareil, et utilisé les propriétés signalées pour suivre la progression de la mise à jour du microprogramme.

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/