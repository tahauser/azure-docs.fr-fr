---
title: Bien démarrer avec Azure Mobile Engagement pour iOS dans Swift | Microsoft Docs
description: Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications iOS
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
ms.openlocfilehash: 1168e05afa457825c2b982d6d33ea73c62b565dc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Prise en main d’Azure Mobile Engagement pour les applications iOS dans Swift
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique indique comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications push à un segment d’utilisateurs d’une application iOS.
Dans ce didacticiel, vous créez une application iOS vide qui collecte des données de base et reçoit des notifications push à l'aide du service APN (Apple Push Notification Service).

Ce didacticiel requiert les éléments suivants :

* XCode 8, que vous pouvez installer depuis l’App Store de votre MAC
* Le [kit de développement logiciel (SDK) iOS Mobile Engagement]
* Le certificat de notification push (.p12) que vous pouvez obtenir dans votre Centre de développement Apple

> [!NOTE]
> Ce didacticiel utilise Swift version 3.0. 
> 
> 

Vous devez suivre ce didacticiel pour avoir accès à tous les autres didacticiels Mobile Engagement pour applications iOS.

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).
> 
> 

## <a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement
Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. Vous trouverez la documentation complète d’intégration dans [Intégration du Kit de développement logiciel (SDK) iOS Mobile Engagement](mobile-engagement-ios-sdk-overview.md)

Nous allons créer une application de base à l’aide de XCode afin d’illustrer l’intégration :

### <a name="create-a-new-ios-project"></a>Création d’un projet iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Connexion de votre application au serveur principal Mobile Engagement
1. Téléchargez le [kit de développement logiciel (SDK) iOS Mobile Engagement]
2. Extrayez le fichier .tar.gz dans un dossier de votre ordinateur
3. Cliquez avec le bouton droit sur le projet et sélectionnez « Add files to ... »
   
    ![][1]
4. Accédez au dossier dans lequel vous avez extrait le kit de développement logiciel (SDK) et sélectionnez le dossier `EngagementSDK`, puis appuyez sur OK.
   
    ![][2]
5. Ouvrez l’onglet `Build Phases` et dans le menu `Link Binary With Libraries`, ajoutez les infrastructures, comme indiqué ci-dessous :
   
    ![][3]
6. Créez un en-tête de pontage pour pouvoir utiliser les API Objective C du Kit de développement logiciel (SDK) en choisissant Fichier > Nouveau > Fichier > iOS > Source > Fichier d’en-tête.
   
    ![][4]
7. Modifiez le fichier d’en-tête de pontage pour exposer le code Mobile Engagement Objective-C sur votre code Swift, puis ajoutez les importations ci-après :
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. Sous Paramètres de génération, assurez-vous que le paramètre de génération d’en-tête de pontage Objective-C sous Compilateur Swift- Génération de code a un chemin d’accès à cet en-tête. Voici un exemple de chemin d’accès : **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (selon le chemin d’accès)**
   
   ![][6]
9. De retour sur le portail Azure, dans la page *Informations de connexion* de votre application, copiez la chaîne de connexion.
   
   ![][5]
10. Collez la chaîne de connexion dans le délégué `didFinishLaunchingWithOptions`
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a id="monitor"></a>Activation de la surveillance en temps réel
Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

1. Ouvrez le fichier **ViewController.swift** et remplacez la classe de base **ViewController** par **EngagementViewController** :
   
    `class ViewController : EngagementViewController {`

## <a id="monitor"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Activation des notifications Push et de la messagerie intégrée à l’application
Mobile Engagement vous permet d'interagir et d'atteindre vos utilisateurs à l'aide de notifications Push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement.
Les sections suivantes vous permettront de configurer votre application pour la réception des notifications.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Activer votre application pour recevoir des notifications Push Silent
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Ajoutez la bibliothèque du module Couverture à votre projet
1. Cliquez avec le bouton droit sur votre projet
2. Sélectionnez `Add file to ...`
3. Accédez au dossier où vous avez extrait le SDK
4. Sélectionnez le dossier `EngagementReach`
5. Cliquez sur Ajouter.
6. Modifiez le fichier d’en-tête de pontage pour exposer les en-têtes Mobile Engagement Objective-C Reach, puis ajoutez les importations ci-après :
   
        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modifier votre délégué d'Application
1. Dans `didFinishLaunchingWithOptions`, créez un module Couverture et passez-le à la ligne existante d’initialisation d’Engagement :
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Activez votre application pour recevoir des notifications push du service APN
1. Ajoutez la ligne suivante à la méthode `didFinishLaunchingWithOptions` :
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. Ajoutez la méthode `didRegisterForRemoteNotificationsWithDeviceToken` comme suit :
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. Ajoutez la méthode `didReceiveRemoteNotification:fetchCompletionHandler:` comme suit :
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[kit de développement logiciel (SDK) iOS Mobile Engagement]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
