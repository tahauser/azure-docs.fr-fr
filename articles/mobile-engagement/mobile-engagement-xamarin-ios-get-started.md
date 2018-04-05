---
title: Prise en main d’Azure Mobile Engagement pour Xamarin.iOS
description: Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Xamarin.iOS.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0448209e-fff6-47bd-985c-2cf074bac12f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 78ba376eb4552468810a1de72d53ceb59ec9d395
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Prise en main d’Azure Mobile Engagement pour les applications Xamarin.iOS
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique indique comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications Push aux utilisateurs segmentés d’une application Xamarin.iOS.
Dans ce didacticiel, vous allez créer une application Xamarin.iOS vide qui collecte des données de base et reçoit des notifications Push à l’aide du service APN (Apple Push Notification).

> [!NOTE]
> Le service Azure Mobile Engagement ne sera plus disponible à partir de mars 2018. Actuellement, il reste uniquement disponible pour les clients déjà abonnés à ce service. Pour plus d’informations, consultez [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Ce didacticiel requiert les éléments suivants :

* [Xamarin Studio](http://xamarin.com/studio). Vous pouvez également utiliser Visual Studio avec Xamarin, mais ce didacticiel utilise Xamarin Studio. Pour obtenir des instructions sur l’installation, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
* [Kit de développement logiciel (SDK) Mobile Engagement pour Xamarin](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement
Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push.

Nous allons créer une application de base à l’aide de Xamarin afin d’illustrer l’intégration :

### <a name="create-a-new-xamarinios-project"></a>Créer un projet Xamarin.iOS
1. Démarrez Xamarin Studio. Accédez à **Fichier** -> **Nouveau** -> **Solution** 
   
    ![][1]
2. Sélectionnez **Single View app** (Application vue unique) et vérifiez que **C#** est le langage sélectionné, puis cliquez sur **Next** (Suivant).
   
    ![][2]
3. Renseignez les champs **App Name** (Nom d’application) et **Organization Identifier** (Identificateur d’organisation), puis cliquez sur **Next** (Suivant). 
   
    ![][3]
   
   > [!IMPORTANT]
   > Assurez-vous que le profil de publication que vous utilisez pour déployer votre application iOS utilise un ID de l’application qui correspond exactement à l’identificateur Bundle Identifier indiqué ici. 
   > 
   > 
4. Mettez à jour les champs **Project Name** (Nom du projet), **Solution Name** (Nom de solution) et **Location** (Emplacement) si nécessaire, puis cliquez sur **Create** (Créer).
   
    ![][4]

Xamarin Studio crée l’application de démonstration dans laquelle nous allons intégrer Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Connexion de votre application au serveur principal Mobile Engagement
1. Dans la fenêtre des solutions, cliquez avec le bouton droit sur le dossier **Packages** (Packages), puis sélectionnez **Add Packages...** (Ajouter des packages...).
   
    ![][5]
2. Recherchez **Microsoft Azure Mobile Engagement Xamarin SDK** et ajoutez-le à votre solution.  
   
    ![][6]
3. Ouvrez **AppDelegate.cs** et ajoutez-y l’instruction using suivante :
   
        using Microsoft.Azure.Engagement.Xamarin;
4. Dans la méthode **FinishedLaunching** , ajoutez ce qui suit pour initialiser la connexion avec le serveur principal Mobile Engagement. Veillez à ajouter la chaîne **ConnectionString**. Ce code utilise également un paramètre **NotificationIcon** factice, qui est ajouté par le Kit de développement logiciel (SDK) Mobile Engagement que vous pouvez remplacer. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a id="monitor"></a>Activation de la surveillance en temps réel
Pour commencer à envoyer des données et à vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran au serveur principal Mobile Engagement.

1. Ouvrez **ViewController.cs** et ajoutez-y l’instruction using suivante :
   
        using Microsoft.Azure.Engagement.Xamarin;
2. Remplacez la classe dans laquelle `ViewController` hérite de `UIViewController` à `EngagementViewController`. 

## <a id="monitor"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Activation des notifications push et de la messagerie in-app
Mobile Engagement vous permet d’interagir et d’atteindre vos utilisateurs et REACH à l’aide de notifications push et de la messagerie in-app, dans le cadre d’une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement.
Les sections suivantes vous permettent de configurer votre application pour la réception.

### <a name="modify-your-application-delegate"></a>Modifier votre délégué d'Application
1. Ouvrez **AppDelegate.cs** et ajoutez-y l’instruction using suivante :
   
        using System; 
2. Maintenant, dans la méthode `FinishedLaunching`, ajoutez ce qui suit pour vous inscrire afin de recevoir les messages de type Push après `EngagementAgent.init(...)`
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. Pour finir, mettez à jour ou ajoutez les méthodes suivantes :
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. Dans le fichier **Info.plist** de la solution, vérifiez que l’identificateur **Bundle Identifier** (Identificateur d’offre groupée) correspond à l’identificateur **App ID** (ID d’application) dont vous disposez dans le profil d’approvisionnement de l’Apple Dev Center. 
   
    ![][7]
5. Dans le même fichier **Info.plist**, assurez-vous d’avoir coché les cases **Enable Background Modes** (Activer les modes en arrière-plan) et **Remote Notifications** (Notifications distantes). 
   
     ![][8]
6. Exécutez l’application sur l’appareil que vous avez associé à ce profil de publication. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
