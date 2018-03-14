---
title: Prise en main de Notification Hubs pour les applications Xamarin.Android | Microsoft Docs
description: "Ce didacticiel vous apprend à utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin Android."
author: jwhitedev
manager: kpiteira
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 1cb6fbc82c493e17815dc60ddcff183a47513bc6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Prise en main de Notification Hubs pour les applications Xamarin.Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin.Android. Vous allez créer une application Xamarin.Android vide qui reçoit des notifications Push à l’aide de Firebase Cloud Messaging (FCM). Une fois l’opération terminée, vous pouvez utiliser votre hub de notification pour diffuser des notifications Push sur tous les appareils exécutant votre application. Le code finalisé est disponible dans l’exemple [Application NotificationHubs][GitHub].

Ce didacticiel présente un scénario de diffusion simple utilisant les Notification Hubs.

## <a name="before-you-begin"></a>Avant de commencer
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Le code complet de ce didacticiel est disponible sur GitHub [ici][GitHub].

## <a name="prerequisites"></a>Prérequis
Ce didacticiel requiert les éléments suivants :

* [Visual Studio avec Xamarin] pour Windows ou [Visual Studio pour Mac] sur OS X.
* Un compte Google actif

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels de Notification Hubs pour les applications Xamarin.Android.

> [!IMPORTANT]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Activation de Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Configuration de votre hub de notification
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Sélectionnez l’onglet <b>Configurer</b> dans la partie supérieure, saisissez la valeur de <b>clé API</b> obtenue à la section précédente, puis cliquez sur <b>Enregistrer</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Votre concentrateur de notification est configuré pour FCM, et vous disposez des chaînes de connexion vous permettant d’inscrire votre application pour la réception de notifications et l’envoi de notifications Push.

## <a name="connect-your-app-to-the-notification-hub"></a>Connexion de votre application au hub de notification
Créez d’abord un projet. 

1. Dans Visual Studio, sélectionnez **Nouvelle solution** > **Application Android**, puis **Suivant**.
   
      ![Visual Studio : Créer un projet Android][22]

2. Saisissez le **Nom** et l’**Identificateur** de l’application. Sélectionnez les **plateformes cibles** que vous souhaitez prendre en charge, puis cliquez sur **Suivant** et sur **Créer**.
   
      ![Visual Studio : Configuration de l’application Android][23]

    Un projet Android est créé.

3. Ouvrez les propriétés du projet en cliquant avec le bouton droit sur votre nouveau projet dans la vue Solution et en choisissant **Options**. Sélectionnez l'élément **Android Application** dans la section **Build**.
   
    Assurez-vous que la première lettre de votre nom de package ( **Package name** ) est en minuscule.
   
   > [!IMPORTANT]
   > La première lettre du nom du package doit être une minuscule. Sinon, vous recevrez des erreurs du manifeste lors de l’inscription de votre application pour les notifications Push ci-dessous.
   > 
   > 
   
      ![Visual Studio : Options de projet Android][24]
4. Définissez optionnellement la **version minimale d’Android** sur un autre niveau d’API.
5. Le cas échéant, définissez la **version Android cible** sur une autre version d’API (API de niveau 8 ou supérieur).
6. Sélectionnez **OK** et fermez la boîte de dialogue Options du projet.

### <a name="add-the-required-packages-to-your-project"></a>Ajouter les packages requis à votre projet

1. Cliquez avec le bouton droit de la souris sur votre projet et sélectionnez **Ajouter** > **Ajouter les packages NuGet**.
2. Recherchez **Xamarin.Azure.NotificationHubs.Android** et ajoutez-le au projet.
3. Recherchez **Xamarin.Firebase.Messaging** et ajoutez-le au projet.

### <a name="set-up-notification-hubs-in-your-project"></a>Configuration de hubs de notification dans votre projet
1. Collectez les informations suivantes pour votre application Android et votre concentrateur de notifications :
   
   * **Chaîne de connexion d’écoute** : dans le tableau de bord du [portail Azure], sélectionnez **Afficher les chaînes de connexion**. Copiez la chaîne de connexion *DefaultListenSharedAccessSignature* pour cette valeur.
   * **Nom du concentrateur** : il s’agit du nom de votre concentrateur sur le [portail Azure]. Par exemple, *mynotificationhub2*.
     
2. Créez une classe **Constants.cs** pour votre projet Xamarin et définissez les valeurs constantes suivantes dans la classe. Remplacez les espaces réservés par vos valeurs.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```

3. Ajoutez les instructions using suivantes à **MainActivity.cs**:
   
    ```csharp
        using Android.Util;
    ```

4. Ajoutez une variable d’instance à la classe **MainActivity.cs** qui sera utilisée pour afficher une boîte de dialogue d’alerte lors de l’exécution de l’application :
   
    ```csharp
        public const string TAG = "MainActivity";
    ```

5. Ajoutez le code suivant à `OnCreate` dans **MainActivity.cs** après `base.OnCreate(savedInstanceState)` :

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```

6. Cliquez avec le bouton droit de la souris sur votre projet, puis ajoutez le fichier `google-services.json` que vous avez au préalable téléchargé à partir de votre projet Firebase. Cliquez avec le bouton droit de la souris sur le fichier ajouté, puis positionnez l’action de génération sur `GoogleServicesJson`.

    ![Visual Studio : Configurer google-services.json][25]

7. Créer une classe appelée **MyFirebaseIIDService**.

8. Ajoutez les instructions using suivantes à **MyFirebaseIIDService.cs** :
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. Dans **MyFirebaseIIDService.cs**, ajoutez le code suivant au-dessus de la déclaration de **classe**, puis faites en sorte que votre classe hérite des paramètres de **FirebaseInstanceIdService** :
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```

10. Dans **MyFirebaseIIDService.cs**, ajoutez le code suivant :
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```

11. Créez une autre classe pour votre projet et nommez-la **MyFirebaseMessagingService**.

12. Ajoutez les instructions using suivantes à la classe **MyFirebaseMessagingService.cs**.
    
    ```csharp
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Firebase.Messaging;
    ```

13. Ajoutez le code suivant au-dessus de votre déclaration de classe, puis faites en sorte que votre classe hérite des paramètres de **FirebaseMessagingService** :
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```
    
14. Ajoutez le code suivant à la classe **MyFirebaseMessagingService.cs** :
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

15. Exécuter l’application sur votre appareil ou un émulateur chargé

## <a name="send-notifications-from-the-portal"></a>Envoyer des notifications à partir du portail
Vous pouvez tester la réception de notifications dans votre application avec l’option *Test Send* du [portail Azure]. Cette option envoie une notification Push de test à votre appareil.

![Portail Azure : Option Test Send][30]

Les notifications Push sont normalement envoyées dans un service backend tel que Mobile Services ou ASP.NET par le biais d’une bibliothèque compatible. Si aucune bibliothèque n’est disponible pour votre backend, vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification.

La liste ci-dessous répertorie certains autres didacticiels que vous pouvez consulter pour envoyer des notifications :

* ASP.NET : consultez [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs].
* Kit de développement logiciel (SDK) Java pour Azure Notification Hubs : consultez [Utilisation de Notification Hubs à partir de Java](notification-hubs-java-push-notification-tutorial.md) pour envoyer des notifications depuis Java. Il a été testé dans Eclipse pour le développement Android.
* PHP : [Utilisation de Notification Hubs à partir de PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple simple, vous avez diffusé des notifications à tous vos appareils Android. Pour cibler certains utilisateurs, reportez-vous au didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]. Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. Pour plus d’informations sur l’utilisation de Notification Hubs, consultez [Recommandations relatives à Notification Hubs] et [Procédures Notification Hubs pour Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio avec Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio pour Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[portail Azure]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Procédures Notification Hubs pour Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /manage/services/notification-hubs/notify-users-aspnet
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
