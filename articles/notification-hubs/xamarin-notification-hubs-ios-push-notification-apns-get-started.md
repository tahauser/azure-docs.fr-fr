---
title: Prendre en main Azure Notification Hubs pour les applications Xamarin.iOS | Microsoft Docs
description: "Ce didacticiel vous apprend à utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin iOS."
services: notification-hubs
keywords: notifications push iOS, messages push, notifications push, envoi de messages
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 38ad8a15fcc4077926e735e01f877a4ee66718ef
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Prendre en main Azure Notification Hubs pour les applications Xamarin.iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS. Vous allez créer une application Xamarin.iOS vide qui reçoit des notifications Push à l’aide du [service de notification Push Apple (APN, Apple Push Notification)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

Une fois l’opération terminée, vous pouvez utiliser votre hub de notification pour diffuser des notifications Push sur tous les appareils exécutant votre application. Le code finalisé est disponible dans l’exemple [Application NotificationHubs][GitHub].

Ce didacticiel présente un scénario de simple diffusion de messages Push utilisant les hubs de notification.

## <a name="prerequisites"></a>Prérequis
Ce didacticiel requiert les éléments suivants :

* Version la plus récente de [Xcode][Install Xcode]
* Un appareil compatible iOS 10 (ou version ultérieure)
* [programme pour développeurs Apple](https://developer.apple.com/programs/)
* [Visual Studio pour Mac]
  
  > [!NOTE]
  > En raison des exigences de configuration requise pour les notifications Push iOS, vous devez déployer et tester l’exemple d’application sur un appareil iOS physique (iPhone ou iPad) au lieu d’un simulateur.
  > 
  > 

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels consacrés à Notification Hubs pour applications Xamarin. iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurer votre hub de notification pour les notifications Push iOS
Cette section vous guide dans la création d’un hub de notification et la configuration de l’authentification avec APNS à l’aide du certificat Push **.p12** que vous venez de créer. Si vous souhaitez utiliser un hub de notification que vous avez déjà créé, vous pouvez passer directement à l’étape 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Cliquez sur le bouton <b>Notification Services</b>, puis sélectionnez <b>Apple (APNS)</b>. N’oubliez pas de sélectionner <b>Certificat</b>, de cliquer sur l’icône du fichier, puis de sélectionner le fichier <b>.p12</b> que vous avez exporté au préalable. Assurez-vous que le mot de passe spécifié est correct.</p>

<p>Comme il s’agit de développement, veillez à sélectionner le mode <b>Sandbox</b>. Utilisez uniquement <b>Production</b> si vous souhaitez envoyer des notifications Push aux utilisateurs ayant acheté votre application dans Windows Store.</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Configurer APNS dans le portail Azure][6]

&emsp;&emsp;&emsp;&emsp;![Configurer la certification APNS dans le portail Azure][7]

Votre hub de notification est maintenant configuré pour APNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications Push.

## <a name="connect-your-app-to-the-notification-hub"></a>Connexion de votre application au hub de notification
#### <a name="create-a-new-project"></a>Création d'un projet
1. Dans Visual Studio, créez un projet iOS, sélectionnez le modèle **Application avec affichage unique**, puis cliquez sur **Suivant**.
   
     ![Visual Studio : Sélectionner le type d’application][31]

2. Entrez le nom de l’application et l’identifiant de votre organisation, puis appuyez sur **Suivant** et enfin sur **Créer**.

3. Dans la vue des solutions, double-cliquez sur *Info.plist*. Ensuite, sous **Identité**, assurez-vous que l’identificateur de l’offre groupée correspond à celui utilisé lors de la création du profil d’approvisionnement. Sous **Signature**, vérifiez que votre compte de développeur est sélectionné sous **Team**, que l’option Automatically manage signing (Gérer automatiquement la signature) est activée et que votre certificat de signature et votre profil d’approvisionnement sont automatiquement sélectionnés.

    ![Visual Studio : Configuration des applications iOS][32]

4. Ajoutez le package de messagerie Azure. Dans la vue des solutions, cliquez sur le projet avec le bouton droit de la souris, puis sélectionnez **Ajouter** > **Ajouter des paquets NuGet**. Recherchez le package **Xamarin.Azure.NotificationHubs.iOS** et ajoutez-le à votre projet.

5. Ajoutez un nouveau fichier à votre classe et nommez-le **Constants.cs**. Ensuite, ajoutez les variables suivantes et remplacez les espaces réservés des littéraux de chaînes par le *nom du hub* et la chaîne *DefaultListenSharedAccessSignature* notée précédemment.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. Dans **AppDelegate.cs**, ajoutez l'instruction using suivante :
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. Déclarez une instance de **SBNotificationHub**:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. Dans **AppDelegate.cs**, mettez à jour **FinishedLaunching()** afin qu’il corresponde à ce qui suit :
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. Remplacez la méthode **RegisteredForRemoteNotifications()** dans **AppDelegate.cs** :
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. Remplacez la méthode **ReceivedRemoteNotification()** dans **AppDelegate.cs** :
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. Créez la méthode **ProcessNotification()** dans **AppDelegate.cs** :
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > Vous pouvez choisir de remplacer **FailedToRegisterForRemoteNotifications()** pour gérer les situations, par exemple, l’absence de connexion réseau, etc. Cette opération est particulièrement importante lorsque l’utilisateur cherche à démarrer votre application en mode hors connexion (par exemple, en mode avion) et que vous souhaitez gérer les scénarios de messagerie Push propres à votre application.
  

12. Exécutez l'application sur votre appareil.

## <a name="sending-test-push-notifications"></a>Envoyer des notifications Push de test
Vous pouvez tester la réception de notifications dans votre application avec l’option *Test Send* du [portail Azure]. Cette option envoie une notification Push de test à votre appareil.

![Portail Azure : Option Test Send][30]

Les notifications Push sont normalement envoyées dans un service principal tel que Mobile Apps ou ASP.NET à l’aide d’une bibliothèque compatible. Si aucune bibliothèque n’est disponible pour votre backend, vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification.

Pour continuer, nous vous recommandons de consulter le didacticiel [Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md). Vous y apprendrez à envoyer des notifications à partir d’un backend ASP.NET. Toutefois, les approches suivantes peuvent servir à envoyer des notifications :

La liste ci-dessous répertorie certains autres didacticiels, que vous pouvez consulter pour envoyer des notifications :
* Interface REST : vous pouvez prendre en charge les notifications Push sur n’importe quelle plateforme backend à l’aide de [l’interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **SDK .NET Microsoft Azure Notification Hubs**: dans le Gestionnaire de package Nuget pour Visual Studio, exécutez [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* Node.js : [Utilisation de Notification Hubs à partir de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* Java / PHP** : pour voir un exemple d’envoi de notifications Push au moyen des API REST, consultez « Utilisation de Notification Hubs à partir de Java/PHP » ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple simple, vous avez envoyé des notifications Push à tous vos appareils iOS. Pour cibler certains utilisateurs, reportez-vous au didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]. Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. Pour plus d’informations sur l’utilisation de Notification Hubs, consultez les pages [Vue d’ensemble de Notifications Hubs] et [Procédures Notification Hubs pour iOS].

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Vue d’ensemble de Notifications Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Procédures Notification Hubs pour iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio pour Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /manage/services/notification-hubs/notify-users-aspnet
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Portail Azure]: https://portal.azure.com
