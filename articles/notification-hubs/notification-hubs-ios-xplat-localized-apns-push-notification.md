---
title: "Didacticiel sur l’utilisation de Notification Hubs pour envoyer les dernières nouvelles localisées pour iOS"
description: "Découvrez comment utiliser Azure Service Bus Notification Hubs pour envoyer des notifications de dernières nouvelles localisées (iOS)."
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: fd2b7d9dfd4f432bbcbaa3ed76f8bec0b9677e17
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Utilisation de Notification Hubs pour envoyer les dernières nouvelles localisées vers des appareils iOS
> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
Cette rubrique montre comment utiliser la fonctionnalité de [modèles](notification-hubs-templates-cross-platform-push-messages.md) d’Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles qui ont été localisées par langue et par appareil. Dans ce didacticiel, vous commencez avec l’application iOS que vous avez créée dans le cadre du didacticiel [Utilisation de Notifications Hubs pour envoyer les dernières nouvelles]. Lorsque vous aurez terminé, vous pourrez vous inscrire aux catégories qui vous intéressent, spécifier une langue dans laquelle recevoir les notifications et recevoir uniquement des notifications Push pour les catégories sélectionnées dans cette langue.

Ce scénario comporte deux parties :

* L'application iOS permet aux appareils clients d'indiquer une langue et de s'abonner à différentes catégories de dernières nouvelles.
* Le serveur principal diffuse les notifications à l’aide des fonctionnalités de **balise** et de **modèle** d’Azure Notification Hubs.

## <a name="prerequisites"></a>Composants requis
Vous devez avoir suivi le didacticiel [Utilisation de Notifications Hubs pour envoyer les dernières nouvelles] et avoir le code à disposition, car le présent didacticiel est basé sur ce code.

Visual Studio 2012 ou une version ultérieure est facultative.

## <a name="template-concepts"></a>Concepts de modèle
Dans le didacticiel [Utilisation de Notifications Hubs pour envoyer les dernières nouvelles] , vous avez créé une application qui se sert de **balises** pour s'abonner aux notifications relatives à différentes catégories de nouvelles.
Cependant, de nombreuses applications sont destinées à plusieurs marchés et doivent donc être localisées. Cela signifie que le contenu des notifications proprement dites doit lui aussi être localisé et envoyé au bon ensemble d’appareils.
Dans cette rubrique, nous allons vous montrer comment utiliser la fonctionnalité de **modèle** de Notification Hubs pour facilement envoyer des notifications de dernières nouvelles localisées.

Remarque : pour envoyer des notifications localisées, vous pouvez notamment créer plusieurs versions de chaque balise. Par exemple, pour prendre en charge l'anglais, le français et le mandarin, nous aurions besoin de trois balises différentes pour les nouvelles internationales : « world_en », « world_fr » et « world_ch ». Il faudrait ensuite que nous envoyions une version localisée des nouvelles internationales à chacune de ces balises. Dans cette rubrique, nous utilisons des modèles afin d'éviter la prolifération de balises et d'éliminer la nécessité d'envoyer plusieurs messages.

À un haut niveau, les modèles permettent de spécifier comment un appareil particulier reçoit une notification. Le modèle spécifie le format de charge utile exact en se référant aux propriétés qui font partie du message envoyé par le serveur principal de votre application. Aux fins de notre exemple, nous allons envoyer un message de paramètres régionaux contenant toutes les langues prises en charge :

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Ensuite, nous allons nous assurer que les appareils s'inscrivent avec un modèle qui se réfère à la bonne propriété. Par exemple, une application iOS qui souhaite s’abonner aux nouvelles françaises inscrira ce qui suit :

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Les modèles sont une fonctionnalité très puissante sur laquelle vous pouvez obtenir plus d’informations en lisant notre article [Modèles](notification-hubs-templates-cross-platform-push-messages.md) .

## <a name="the-app-user-interface"></a>Interface utilisateur de l’application
Nous allons maintenant modifier l’application de dernières nouvelles que vous avez créée à la rubrique [Utilisation de Notifications Hubs pour envoyer les dernières nouvelles] pour envoyer les dernières nouvelles localisées à l’aide de modèles.

Dans votre MainStoryboard_iPhone.storyboard, ajoutez un contrôle segmenté avec les trois langues que nous prendrons en charge : anglais, français et mandarin.

![][13]

Puis, assurez-vous d’ajouter un IBOutlet dans votre ViewController.h comme indiqué ci-dessous :

![][14]

## <a name="building-the-ios-app"></a>Création de l’application iOS
1. Dans Notification.h, ajoutez la méthode *retrieveLocale* , puis modifiez le magasin et les méthodes d'abonnement comme indiqué :
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    Dans Notification.m, modifiez la méthode *storeCategoriesAndSubscribe* en ajoutant les paramètres régionaux et en les stockant dans les valeurs par défaut de l'utilisateur :
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
   
    Puis, modifiez la méthode *subscribe* afin d'inclure les paramètres régionaux :
   
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
   
    Remarquez que nous utilisons à présent la méthode *registerTemplateWithDeviceToken* au lieu de *registerNativeWithDeviceToken*. Lorsque nous nous abonnons à un modèle, nous devons fournir le modèle json ainsi qu'un nom pour le modèle (car notre application peut vouloir s'abonner à différents modèles). Assurez-vous d'abonner vos catégories sous la forme de balises, car nous voulons nous assurer de recevoir les notifications pour ces nouvelles.
   
    Ajoutez une méthode pour extraire les paramètres régionaux des paramètres utilisateur par défaut :
   
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
2. Maintenant que nous avons modifié notre classe Notifications, nous devons nous assurer que notre paramètre ViewController utilise le nouveau paramètre UISegmentControl. Ajoutez la ligne suivante dans la méthode *viewDidLoad* pour garantir l'affichage des paramètres régionaux actuellement sélectionnés :
   
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
   
    Puis, dans votre méthode *subscribe*, modifiez votre appel à *storeCategoriesAndSubscribe* comme suit :
   
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
3. Enfin, vous devez mettre à jour la méthode *didRegisterForRemoteNotificationsWithDeviceToken* dans votre AppDelegate.m afin que vous puissiez correctement actualiser votre abonnement lorsque votre application démarre. Modifiez votre appel sur la méthode *subscribe* de notifications comme suit :
   
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(facultatif) Envoyer des notifications de modèle localisé à partir de l’application console .NET.
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(facultatif) Envoyer des notifications de modèle localisé à partir de l’appareil
Si vous n’avez pas accès à Visual Studio ou si vous souhaitez simplement essayer d’envoyer les notification de modèle localisé directement depuis l’application sur votre appareil.  Vous pouvez simplement ajouter les paramètres de modèle localisé à la méthode `SendNotificationRESTAPI` que vous avez défini précédemment, dans le didacticiel.

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation des modèles, consultez :

* [Notification des utilisateurs avec Notification Hubs : ASP.NET]
* [Notification des utilisateurs avec Notification Hubs : Mobile Services]

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilisation de Notifications Hubs pour envoyer les dernières nouvelles]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification des utilisateurs avec Notification Hubs : ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notification des utilisateurs avec Notification Hubs : Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
