---
title: Prendre en main Azure Notification Hubs pour les applications iOS | Microsoft Docs
description: "Dans ce didacticiel, vous découvrirez comment utiliser Azure Notification Hubs pour envoyer des notifications Push à une application iOS."
services: notification-hubs
documentationcenter: ios
keywords: notification push,notifications push,notifications push ios
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0e9e7ab196eef790b74074be319cd8122cf3ff5c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-ios-apps"></a>Prendre en main Azure Notification Hubs pour les applications iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).
> 
> 

Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS. Vous allez créer une application iOS vide qui reçoit des notifications push à l’aide du [service de notification Push Apple](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)(APNs, Apple Push Notification service). 

Une fois l’opération terminée, vous pouvez utiliser votre hub de notification pour diffuser des notifications Push sur tous les appareils exécutant votre application.

## <a name="before-you-begin"></a>Avant de commencer
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Le code complet de ce didacticiel est disponible [sur GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>configuration requise
Ce didacticiel requiert les éléments suivants :

* [Windows Azure Messaging Framework]
* Version la plus récente de [Xcode]
* Un appareil compatible avec iOS 10 (ou version ultérieure)
* [programme pour développeurs Apple](https://developer.apple.com/programs/)
  
  > [!NOTE]
  > En raison des exigences de configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil iOS physique (iPhone ou iPad) au lieu du simulateur iOS.
  > 
  > 

Vous devez terminer ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurer votre hub de notification pour les notifications Push iOS
Cette section vous guide dans la création d’un hub de notification et la configuration de l’authentification avec APNS à l’aide du certificat Push **.p12** que vous venez de créer. Si vous souhaitez utiliser un hub de notification que vous avez déjà créé, vous pouvez passer directement à l’étape 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Sous <b>Notification Services</b>, sélectionnez <b>Apple (APNS)</b>. N’oubliez pas de sélectionner <b>Certificat</b>, puis cliquez sur l’icône du fichier et sélectionnez le fichier <b>.p12</b> que vous avez exporté au préalable. Assurez-vous que le mot de passe spécifié est correct.</p>

<p>Comme il s’agit de développement, veillez à sélectionner le mode <b>Sandbox</b>. Utilisez uniquement <b>Production</b> si vous souhaitez envoyer des notifications Push aux utilisateurs ayant acheté votre application dans Windows Store.</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![Configurer la certification APNS dans le portail Azure][6]

&emsp;&emsp;&emsp;&emsp;![Configurer la certification APNS dans le portail Azure][7]

Vous avez maintenant configuré votre hub de notification avec APNS et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications Push.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connexion de votre application iOS à Notification Hubs
1. Dans Xcode, créez un projet iOS et sélectionnez le modèle **Single View Application** .
   
    ![Xcode - Application à vue unique][8]
    
2. Au moment de définir les options de votre nouveau projet, veillez à utiliser le même **nom de produit** et le même **identificateur d’organisation** que ceux utilisés lorsque vous avez défini l’identifiant d’offre groupée dans le portail des développeurs Apple.
   
    ![Xcode - options de projet][11]
    
3. Dans le navigateur de projet, cliquez sur le nom de votre projet et sur l’onglet **Général**, puis recherchez **Signature**. Vérifiez que vous sélectionnez l’équipe correspondant à votre compte de développeur Apple. XCode doit extraire automatiquement le profil d’approvisionnement que vous avez créé précédemment en fonction de l’identificateur d’offre groupée.
   
    Si vous ne voyez pas le nouveau profil d’approvisionnement que vous avez créé dans Xcode, essayez d’actualiser les profils pour votre identité de signature. Cliquez sur **Xcode** dans la barre de menus, sur **Preferences**, sur l’onglet **Account**, sur le bouton **View Details**, sur votre identité de signature, puis cliquez sur le bouton d’actualisation dans le coin inférieur droit.

    ![Xcode - profil d’approvisionnement][9]

4. Sélectionnez l’onglet **Fonctionnalités** sans oublier d’activer les notifications Push.

    ![Xcode : Fonctionnalités push][12]
   
5. Téléchargez [Windows Azure Messaging Framework], puis dézippez le fichier. Dans Xcode, cliquez avec le bouton droit sur votre projet et sélectionnez l’option **Add Files to** pour ajouter le dossier **WindowsAzureMessaging.framework** à votre projet Xcode. Sélectionnez **Options**, assurez-vous que l’option **Copy items if needed** (Copier les éléments si nécessaire) est cochée, puis cliquez sur **Add** (Ajouter).

    ![Décompresser le SDK Azure][10]

6. Ajoutez un nouveau fichier d’en-tête au projet appelé **HubInfo.h**. Ce fichier contiendra les constantes de votre hub de notification. Ajoutez les définitions suivantes et remplacez les espaces réservés des littéraux de chaîne par le *nom de votre hub* et la chaîne *DefaultListenSharedAccessSignature* notée précédemment.

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. Ouvrez le fichier **AppDelegate.h** et ajoutez les instructions d’importation suivantes :

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. Dans le **fichier AppDelegate.m**, ajoutez le code suivant dans la méthode **didFinishLaunchingWithOptions** en fonction de votre version d’iOS. Ce code enregistre le handle de votre appareil avec APNs :

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. Dans ce même fichier, ajoutez les méthodes suivantes :

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    Ce code se connecte au hub de notification utilisant les informations de connexion que vous avez spécifiées dans HubInfo.h. Il transmet ensuite le jeton de l’appareil au hub de notification de sorte que ce dernier puisse envoyer des notifications.

10. Dans le même fichier, ajoutez la méthode suivante pour afficher une **UIAlert** si la notification est reçue alors que l’application est active :

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. Pour vérifier l’absence d’échecs, générez et exécutez l’application sur votre appareil.

## <a name="send-test-push-notifications"></a>Envoi de notifications Push de test
Vous pouvez tester la réception de notifications dans votre application avec l’option *Test Send* dans le [portail Azure]. Elle envoie une notification Push de test à votre appareil.

![Portail Azure : Option Test Send][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="checking-if-your-app-can-receive-push-notifications"></a>Vérifier si votre application peut recevoir des notifications Push
Pour tester les notifications Push sur iOS, vous devez déployer l’application sur un appareil iOS physique. Vous ne pouvez pas envoyer de notifications push Apple en utilisant le simulateur iOS.

1. Exécutez l’application, vérifiez que l’inscription est effectuée, puis appuyez sur **OK**.
   
    ![Test d’inscription de notifications Push pour applications iOS][33]
2. Vous pouvez ensuite envoyer une notification Push de test depuis le [portail Azure], comme indiqué ci-dessus. 

3. La notification Push est envoyée à tous les appareils qui sont inscrits pour recevoir les notifications depuis le hub de notification concerné.
   
    ![Test de réception de notifications Push pour applications iOS][35]

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple simple, vous avez envoyé des notifications Push à tous vos appareils iOS inscrits. Dans le cadre de cette formation, nous vous recommandons de poursuivre avec le didacticiel [Azure Notification Hubs notifie les utilisateurs iOS avec le backend .NET]. Ce guide vous montre comment créer un backend pour envoyer des notifications Push avec des balises. 

Si vous souhaitez segmenter vos utilisateurs par groupes d’intérêt, vous pouvez passer au didacticiel [Utilisation de Notification Hubs pour diffuser les dernières nouvelles] . 

Pour obtenir des informations générales sur Notification Hubs, consultez [Recommandations relatives à Notification Hubs].

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs notifie les utilisateurs iOS avec le backend .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Utilisation de Notification Hubs pour diffuser les dernières nouvelles]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[portail Azure]: https://portal.azure.com
