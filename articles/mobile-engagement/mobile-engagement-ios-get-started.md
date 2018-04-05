---
title: Bien démarrer avec Azure Mobile Engagement pour iOS en Objective C | Microsoft Docs
description: Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications push pour les applications iOS.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 7cf493e3a4878c64b0ea6a74b6e16329ce3d27b5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Prise en main d’Azure Mobile Engagement pour les applications iOS dans Objective C
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique indique comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications push à un segment d’utilisateurs d’une application iOS.
Dans ce didacticiel, vous créez une application iOS vide qui collecte des données de base et reçoit des notifications push à l'aide du service APN (Apple Push Notification Service).

Ce didacticiel requiert les éléments suivants :

* XCode 8, que vous pouvez installer depuis l’App Store de votre MAC
* Le [kit de développement logiciel (SDK) iOS Mobile Engagement]

Vous devez suivre ce didacticiel pour avoir accès à tous les autres didacticiels Mobile Engagement pour applications iOS.

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).
>
>

## <a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement
Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. Vous trouverez la documentation complète sur l’intégration dans [Intégration du Kit de développement logiciel (SDK) iOS Mobile Engagement](mobile-engagement-ios-sdk-overview.md)

Nous allons créer une application de base à l’aide de XCode afin d’illustrer l’intégration :

### <a name="create-a-new-ios-project"></a>Création d’un projet iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connexion de votre application au serveur principal Mobile Engagement
1. Téléchargez le [kit de développement logiciel (SDK) iOS Mobile Engagement]
2. Décompressez le fichier .tar.gz dans un dossier de votre ordinateur.
3. Cliquez avec le bouton droit sur le projet, puis sélectionnez **Add files to**.

    ![][1]

4. Accédez au dossier dans lequel vous avez extrait le kit de développement logiciel (SDK), sélectionnez le dossier `EngagementSDK`, cliquez sur **Options** dans le coin inférieur gauche et assurez-vous que la case **Copier les éléments si besoin** et la case correspondant à votre cible sont cochées, puis appuyez sur **OK**.

    ![][2]

5. Ouvrez l’onglet **Build Phases** (Générer des phases), puis, dans le menu **Liaisons binaires à des bibliothèques**, ajoutez les infrastructures comme indiqué ci-dessous :

    ![][3]

6. De retour sur le portail Azure, dans la page **Informations de connexion** de votre application, copiez la chaîne de connexion.

    ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)
7. Dans le fichier **AppDelegate.m** , ajoutez la ligne de code suivante.

    ```obj-c
    #import "EngagementAgent.h"
    ```
8. Collez la chaîne de connexion dans le délégué `didFinishLaunchingWithOptions`

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
    }
    ```

9. `setTestLogEnabled` est une instruction facultative qui active les journaux du Kit de développement logiciel (SDK) pour vous permettre d’identifier des problèmes.

## <a id="monitor"></a>Activation de l’analyse en temps réel
Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

1. Ouvrez le fichier **ViewController.h** et importez l’élément **EngagementViewController.h** :

    ```obj-c
    #import "EngagementViewController.h"
    ```

2. Remplacez maintenant la super classe de l’interface **ViewController** par `EngagementViewController` :
 
    ```obj-c
   @interface ViewController : EngagementViewController
   ```

## <a id="monitor"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Activation des notifications push et de la messagerie in-app
Mobile Engagement vous permet d’interagir et d’atteindre vos utilisateurs et REACH à l’aide de notifications push et de la messagerie in-app, dans le cadre d’une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement.
Les sections suivantes vous permettent de configurer votre application pour la réception.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Activer votre application pour recevoir des notifications push Silent
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Ajoutez la bibliothèque du module Couverture à votre projet
1. Cliquez avec le bouton droit sur votre projet.
2. Sélectionnez **Add file to**.
3. Accédez au dossier dans lequel vous avez extrait le SDK.
4. Sélectionnez le dossier `EngagementReach` .
5. Cliquez sur **Add**.

### <a name="modify-your-application-delegate"></a>Modifier votre délégué d'Application
1. Dans le fichier **AppDeletegate.m** , importez le module Couverture d'Engagement.

    ```obj-c
    #import "AEReachModule.h"
    #import <UserNotifications/UserNotifications.h>
    ```

2. Dans la méthode `application:didFinishLaunchingWithOptions` , créez un module Couverture et passez-le à votre ligne d’initialisation Engagement existante :

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]
        return YES;
    }
    ```

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Activez votre application pour recevoir des notifications push du service APN
1. Ajoutez la ligne suivante à la méthode `application:didFinishLaunchingWithOptions` :

    ```obj-c
    if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
    {
        if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
        {
            [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
        }else
        {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
        }
        [application registerForRemoteNotifications];
    }
    else
    {
        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }
    ```
2. Ajoutez la méthode `application:didRegisterForRemoteNotificationsWithDeviceToken` comme suit :

    ```obj-c
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
        NSLog(@"Registered Token: %@", deviceToken);
    }
    ```

3. Ajoutez la méthode `didFailToRegisterForRemoteNotificationsWithError` comme suit :

    ```obj-c
    - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
    {
        NSLog(@"Failed to get token, error: %@", error);
    }
    ```

4. Ajoutez la méthode `didReceiveRemoteNotification:fetchCompletionHandler` comme suit :

    ```obj-c
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }
    ```
    
[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[kit de développement logiciel (SDK) iOS Mobile Engagement]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
