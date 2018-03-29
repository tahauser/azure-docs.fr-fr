---
title: Ajout de notifications Push à votre application Xamarin.Forms | Microsoft Docs
description: Découvrez comment utiliser les services Azure pour envoyer des notifications Push multiplateforme à vos applications Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 0bea00d411205541684e807182abd3236c09bd9d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Ajout de notifications Push à votre application Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d'ensemble
Dans ce didacticiel, vous ajoutez des notifications Push à tous les projets qui résultent du [démarrage rapide Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Cela signifie qu’une notification Push est envoyée à tous les clients inter-plateformes à chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devrez ajouter le package d’extension de notification Push. Consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pour plus d’informations.

## <a name="prerequisites"></a>Prérequis

Pour iOS, vous avez besoin d’une [appartenance au programme pour développeurs Apple](https://developer.apple.com/programs/ios/) et d’un appareil iOS physique. Les [notifications Push ne sont pas prises en charge par le simulateur iOS](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurer un hub de notification
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Mettre à jour le projet de serveur pour l'envoi de notifications Push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configurer et exécuter le projet Android (facultatif)
Terminez cette section pour activer les notifications Push pour le projet Android Xamarin.Forms pour Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Activation de Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configurer le serveur principal Mobile Apps pour l’envoi de demandes de notifications Push à l’aide de FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Ajouter les notifications Push au projet Android
Une fois le serveur principal configuré avec FCM, vous pouvez ajouter des codes et des composants au client pour vous inscrire auprès de FCM. Vous pouvez également vous inscrire aux notifications Push avec Azure Notification Hubs via le serveur principal Mobile Apps et recevoir des notifications.

1. Dans le projet **Droid**, cliquez avec le bouton droit sur **Références > Gérer les Packages NuGet...**.
1. Dans la fenêtre du gestionnaire de packages NuGet, recherchez le package **Xamarin.Firebase.Messaging** et ajoutez-le au projet.
1. Dans les propriétés de projet pour le projet **Droid**, configurez l’application pour qu’elle soit compilée avec Android 7.0 ou une version ultérieure.
1. Ajoutez le fichier **google-services.json**, téléchargé à partir de la console Firebase, à la racine du projet **Droid** et définissez son action de compilation sur **GoogleServicesJson**. Pour plus d’informations, consultez [Ajouter le fichier JSON de Google Services](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Inscription auprès de Firebase Cloud Messaging

1. Ouvrez le fichier **AndroidManifest.xml** et insérez les éléments `<receiver>` suivants dans l’élément `<application>` :

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>Implémentation du service Firebase Instance ID

1. Ajoutez une nouvelle classe au projet **Droid** nommé `FirebaseRegistrationService`, et assurez-vous que les instructions `using` suivantes figurent en haut du fichier :

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. Remplacez la classe vide `FirebaseRegistrationService` par le code suivant :

        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class FirebaseRegistrationService : FirebaseInstanceIdService
        {
            const string TAG = "FirebaseRegistrationService";

            public override void OnTokenRefresh()
            {
                var refreshedToken = FirebaseInstanceId.Instance.Token;
                Log.Debug(TAG, "Refreshed token: " + refreshedToken);
                SendRegistrationTokenToAzureNotificationHub(refreshedToken);
            }

            void SendRegistrationTokenToAzureNotificationHub(string token)
            {
                // Update notification hub registration
                Task.Run(async () =>
                {
                    await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
                });
            }
        }

    La classe `FirebaseRegistrationService` est chargée de générer des jetons de sécurité qui autorisent l’application à accéder à FCM. La méthode `OnTokenRefresh` est appelée lorsque l’application reçoit un jeton d’inscription de FCM. La méthode récupère le jeton à partir de la propriété `FirebaseInstanceId.Instance.Token`, qui est mise à jour de façon asynchrone par FCM. La méthode `OnTokenRefresh` est rarement appelée, car le jeton est mis à jour uniquement lorsque l’application est installée ou désinstallée, lorsque l’utilisateur supprime des données d’application, lorsque l’application efface l’ID d’instance, ou lorsque la sécurité du jeton a été compromise. En outre, le service FCM Instance ID demande que l’application actualise son jeton périodiquement, généralement tous les 6 mois.

    La méthode `OnTokenRefresh` appelle également la méthode `SendRegistrationTokenToAzureNotificationHub`, qui est utilisée pour associer un jeton d’inscription utilisateur avec le Hub de Notification Azure à Azure Notification Hub.

#### <a name="registering-with-the-azure-notification-hub"></a>Inscription auprès d’Azure Notification Hub

1. Ajoutez une nouvelle classe au projet **Droid** nommé `AzureNotificationHubService`, et assurez-vous que les instructions `using` suivantes figurent en haut du fichier :

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. Remplacez la classe vide `AzureNotificationHubService` par le code suivant :

        public class AzureNotificationHubService
        {
            const string TAG = "AzureNotificationHubService";

            public static async Task RegisterAsync(Push push, string token)
            {
                try
                {
                    const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                    JObject templates = new JObject();
                    templates["genericMessage"] = new JObject
                    {
                        {"body", templateBody}
                    };

                    await push.RegisterAsync(token, templates);
                    Log.Info("Push Installation Id: ", push.InstallationId.ToString());
                }
                catch (Exception ex)
                {
                    Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
                }
            }
        }

    La méthode `RegisterAsync` crée un modèle de message de notification simple en tant que JSON et l’inscrit pour recevoir des notifications de modèle à partir du hub de notification, à l’aide du jeton d’inscription Firebase. Cela garantit que les notifications envoyées depuis Azure Notification Hub ciblent l’appareil représenté par le jeton d’inscription.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Affichage du contenu d’une notification Push

1. Ajoutez une nouvelle classe au projet **Droid** nommé `FirebaseNotificationService`, et assurez-vous que les instructions `using` suivantes figurent en haut du fichier :

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V7.App;
        using Android.Util;
        using Firebase.Messaging;

1. Remplacez la classe vide `FirebaseNotificationService` par le code suivant :

        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class FirebaseNotificationService : FirebaseMessagingService
        {
            const string TAG = "FirebaseNotificationService";

            public override void OnMessageReceived(RemoteMessage message)
            {
                Log.Debug(TAG, "From: " + message.From);

                // Pull message body out of the template
                var messageBody = message.Data["message"];
                if (string.IsNullOrWhiteSpace(messageBody))
                    return;

                Log.Debug(TAG, "Notification message body: " + messageBody);
                SendNotification(messageBody);
            }

            void SendNotification(string messageBody)
            {
                var intent = new Intent(this, typeof(MainActivity));
                intent.AddFlags(ActivityFlags.ClearTop);
                var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

                var notificationBuilder = new NotificationCompat.Builder(this)
                    .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                    .SetContentTitle("New Todo Item")
                    .SetContentText(messageBody)
                    .SetContentIntent(pendingIntent)
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                    .SetAutoCancel(true);

                var notificationManager = NotificationManager.FromContext(this);
                notificationManager.Notify(0, notificationBuilder.Build());
            }
        }

    La méthode `OnMessageReceived`, qui est appelée lorsqu’une application reçoit une notification de FCM, extrait le contenu du message et appelle la méthode `SendNotification`. Cette méthode convertit le contenu du message en une notification locale qui est lancée alors que l’application est en cours d’exécution, cette notification apparaissant dans la zone de notification.

Vous êtes maintenant prêt à tester les notifications Push dans l’application exécutée sur un appareil Android ou sur l’émulateur.

### <a name="test-push-notifications-in-your-android-app"></a>Tester les notifications Push dans votre application Android
Les deux premières étapes sont requises uniquement lorsque vous testez sur un émulateur.

1. Assurez-vous que vous déployez ou déboguez sur un périphérique ou un émulateur configuré avec Google Play Services. Cela peut être vérifié en contrôlant que les applications **Play** sont installées sur le périphérique ou l’émulateur.
2. Ajoutez un compte Google à l’appareil Android en cliquant sur **Applications** > **Paramètres** > **Ajouter un compte**. Puis suivez les invites pour ajouter un compte Google existant sur l’appareil ou pour en créer un nouveau.
3. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **Droid**, puis cliquez sur **Définir comme projet de démarrage**.
4. Cliquez sur **Exécuter** pour générer le projet et lancer l’application sur votre appareil ou émulateur Android.
5. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).
6. Vérifiez qu’une notification est reçue lorsqu’un élément est ajouté.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurer et exécuter le projet iOS (facultatif)
Cette section est dédiée à l’exécution du projet Xamarin iOS pour les appareils iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configurer le Notification Hub pour APNS
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Ensuite, vous allez configurer le paramètre de projet iOS dans Xamarin Studio ou Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Ajout de notifications Push à votre application iOS
1. Dans le projet **iOS**, ouvrez AppDelegate.cs et ajoutez l’instruction suivante en haut du fichier de code.

        using Newtonsoft.Json.Linq;
2. Dans la classe **AppDelegate**, ajoutez également un remplacement pour l’événement **RegisteredForRemoteNotifications** afin de vous inscrire pour les notifications :

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. Dans **AppDelegate**, ajoutez également la substitution suivante pour le Gestionnaire d’événements **DidReceiveRemoteNotification** :

        public override void DidReceiveRemoteNotification(UIApplication application,
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Cette méthode gère les notifications entrantes pendant que l’application est en cours d’exécution.
4. Dans la classe **AppDelegate**, ajoutez le code suivant à la méthode **FinishedLaunching** :

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Cela permet la prise en charge de l’enregistrement Push des notifications et des demandes à distance.

L'application est mise à jour et prend en charge les notifications Push.

#### <a name="test-push-notifications-in-your-ios-app"></a>Tester les notifications Push dans votre application iOS
1. Cliquez avec le bouton droit sur le projet iOS et cliquez sur **Définir comme projet de démarrage**.
2. Cliquez sur le bouton **Exécuter** ou sur **F5** dans Visual Studio pour générer le projet et démarrer l’application sur un appareil iOS. Ensuite, cliquez sur **OK** pour accepter les notifications Push.

   > [!NOTE]
   > Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.
   >
   >
3. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).
4. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

## <a name="configure-and-run-windows-projects-optional"></a>Configurer et exécuter des projets Windows (facultatif)
Cette section s’applique à l’exécution des projets Xamarin.Forms WinApp et WinPhone81 pour les appareils Windows. Ces étapes prennent également en charge les projets de plateforme Windows universelle (UWP). Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Inscrire votre application Windows aux notifications Push avec le service de notification Windows (Windows Notification Service, WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurer le Notification Hub pour WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Ajouter des notifications Push à votre application Windows
1. Dans Visual Studio, ouvrez le fichier **App.xaml.cs** dans un projet Windows et ajoutez les instructions suivantes.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Remplacez `<your_TodoItemManager_portable_class_namespace>` par l’espace de noms de votre projet portable qui contient la classe `TodoItemManager`.
2. Dans le fichier App.xaml.cs, ajoutez la méthode **InitNotificationsAsync** suivante :

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS =
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Cette méthode récupère le canal des notifications Push et inscrit un modèle pour recevoir les notifications de modèle à partir de votre Notification Hub. Un modèle de notification prenant en charge *messageParam* sera transmis à ce client.
3. Dans le fichier App.xaml.cs, mettez à jour la définition de méthode du gestionnaire d’événements **OnLaunched** en ajoutant le modificateur `async`. Puis, ajoutez la ligne de code suivante à la fin de la méthode :

        await InitNotificationsAsync();

    Cela permet de s’assurer que l’inscription aux notifications Push est créée ou actualisée à chaque lancement de l’application. Il est important d’effectuer cette opération pour vous assurer que le canal Push WNS est toujours actif.  
4. Dans l’Explorateur de solutions pour Visual Studio, ouvrez le fichier **Package.appxmanifest**, puis définissez **Compatible toast** sur **Oui** sous **Notifications**.
5. Générez l’application et vérifiez l’absence d’erreurs. Votre application cliente doit désormais s’inscrire pour les notifications de modèle du serveur principal Mobile Apps. Répétez cette section pour chaque projet Windows dans votre solution.

#### <a name="test-push-notifications-in-your-windows-app"></a>Tester les notifications Push dans votre application Windows
1. Dans Visual Studio, cliquez avec le bouton droit sur un projet Windows, puis cliquez sur **Définir comme projet de démarrage**.
2. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application.
3. Dans l’application, tapez un nom pour un nouvel élément todoitem, puis cliquez sur l’icône de signe plus (**+**) pour l’ajouter.
4. Vérifiez qu’une notification est reçue lorsque l’élément est ajouté.

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en plus sur les notifications Push :

* [Envoi de notifications Push à partir d’Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Notifications à distance avec Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnostiquer les problèmes de notification Push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Il existe différentes raisons pour lesquelles les notifications peuvent être perdues ou n’arrivent pas sur les appareils. Cette rubrique vous explique comment analyser et déterminer la cause première des défaillances de notification Push.

Vous pouvez poursuivre avec l’un des didacticiels suivants :

* [Ajouter l’authentification à votre application ](app-service-mobile-xamarin-forms-get-started-users.md)  
  Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Découvrez comment ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal Mobile Apps. La synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
