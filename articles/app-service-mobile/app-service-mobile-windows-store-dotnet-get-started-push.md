---
title: "Ajout de notifications Push à votre plateforme Windows universelle (UWP) | Microsoft Docs"
description: "Découvrez comment utiliser Azure App Service Mobile Apps et Azure Notification Hubs pour envoyer des notifications Push à votre application de plateforme Windows universelle (UWP)."
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: 
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: d51fa215ace8b33acb5d36b98e9a1a86e3296a65
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="add-push-notifications-to-your-windows-app"></a>Ajouter des notifications Push à votre application Windows
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d'ensemble
Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide Windows](app-service-mobile-windows-store-dotnet-get-started.md) afin qu'une notification Push soit envoyée chaque fois qu'un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devrez ajouter le package d’extension de notification Push. Consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pour plus d’informations.

## <a name="configure-hub"></a>Configurer un nouveau hub de notification
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Inscription de votre application pour les notifications Push
Vous devez soumettre votre application au Windows Store, puis configurer votre projet de serveur pour l’intégrer avec Windows Notification Services (WNS) pour envoyer des notifications push.

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d’application UWP, puis cliquez sur **Store** > **Associer l’application au Windows Store...**.

    ![Associer une application au Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. Dans l'Assistant, cliquez sur **Suivant**, connectez-vous à votre compte Microsoft, saisissez un nom pour votre application dans **Réserver un nouveau nom d'application**, puis cliquez sur **Réserver**.
3. Une fois l’inscription de l’application créée, sélectionnez le nouveau nom d’application, cliquez sur **Suivant**, puis sur **Associer**. Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.  
4. Accédez au [Centre de développement Windows](https://dev.windows.com/en-us/overview), connectez-vous à l'aide de votre compte Microsoft, cliquez sur la nouvelle inscription d'application dans **Mes applications**, puis développez **Services** > **Notifications Push**.
5. Sur la page **Notifications Push**, cliquez sur **Site des services Live** sous **Microsoft Azure Mobile Services**.
6. Dans la page d’inscription, notez la valeur sous **Secrets de l’application** et **SID du package**. Vous les utiliserez ensuite pour configurer le backend de votre application mobile.

    ![Associer une application au Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application. L’ **ID d’application** est utilisé avec la clé secrète pour configurer l’authentification du compte Microsoft.
   >
   >

## <a name="configure-the-backend-to-send-push-notifications"></a>Configurer le serveur principal pour l’envoi de notifications Push
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Mise à jour du serveur pour l'envoi de notifications Push
Utilisez la procédure ci-dessous qui correspond à votre type de projet de serveur principal &mdash;[serveur principal .NET](#dotnet) ou [serveur principal Node.js](#nodejs).

### <a name="dotnet"></a>Projet de serveur principal .NET
1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez Microsoft.Azure.NotificationHubs et cliquez sur **Installer**. Cette commande installe la bibliothèque cliente Notification Hubs.
2. Développez **Contrôleurs**, ouvrez TodoItemController.cs et ajoutez les instructions using suivantes :

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. Dans la méthode **PostTodoItem**, ajoutez le code suivant après l’appel à **InsertAsync** :

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Ce code indique au hub de notification d'envoyer une notification Push après l'insertion d’un nouvel élément.
4. Publier à nouveau le projet de serveur

### <a name="nodejs"></a>Projet de back-end Node.js
1. Si vous ne l’avez pas déjà fait, [téléchargez le projet de démarrage rapide](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou utilisez [l’éditeur en ligne du portail Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Remplacez le code présent dans le fichier todoitem.js par le code suivant :

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    Ce code envoie une notification toast WNS contenant item.text quand un nouvel élément todo est inséré.
3. Quand vous modifiez le fichier sur votre ordinateur local, republiez le projet serveur.

## <a id="update-app"></a>Ajout de notifications Push à votre application
Ensuite, votre application doit s’inscrire pour les notifications Push au démarrage. Si vous avez déjà activé l'authentification, assurez-vous que l'utilisateur est connecté avant d'essayer de s'inscrire aux notifications Push.

1. Ouvrez le fichier de projet **App.xaml.cs** et ajoutez les instructions `using` suivantes :

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
2. Dans le même fichier, ajoutez la définition de la méthode **InitNotificationsAsync** à la classe **App** :

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Ce code récupère l'URI de canal ChannelURI pour l'application dans WNS et l'inscrit avec votre application App Service Mobile App.
3. En haut du handle d’événements **OnLaunched** dans **App.xaml.cs**, ajoutez le modificateur **async** à la définition de méthode et ajoutez l’appel suivant à la nouvelle méthode **InitNotificationsAsync**, comme dans l’exemple suivant :

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Cela garantit l'inscription de l'URI de canal ChannelURI de courte durée chaque fois que l'application est lancée.
4. Recréez votre projet d’application UWP. Votre application est maintenant prête à recevoir des notifications toast.

## <a id="test"></a>Tester les notifications push dans votre application
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Étapes suivantes
Apprenez-en plus sur les notifications Push :

* [Utilisation du client géré pour Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications)  
  Les modèles vous apportent la souplesse nécessaire pour envoyer des notifications push multiplateformes et localisées. Apprenez à inscrire des modèles.
* [Diagnostiquer les problèmes de notification Push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Il existe différentes raisons pour lesquelles les notifications peuvent être perdues ou n’arrivent pas sur les appareils. Cette rubrique vous explique comment analyser et déterminer la cause première des défaillances de notification Push.

Vous pouvez poursuivre avec l’un des didacticiels suivants :

* [Ajouter l’authentification à votre application](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal d’applications mobiles. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
