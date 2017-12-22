---
title: "Notifications Push sécurisées avec Azure Notification Hubs"
description: "Découvrez comment envoyer des notifications Push sécurisées à une application iOS depuis Azure. Code samples written in Objective-C and C#."
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: e5f09fb3716303bb21fe7442aa6fa8832174838e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="azure-notification-hubs-secure-push"></a>Notifications Push sécurisées avec Azure Notification Hubs
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
La prise en charge des notifications Push dans Microsoft Azure vous permet d’accéder à une infrastructure Push conviviale, multiplateforme avec montée en charge qui simplifie fortement l’implémentation des notifications Push pour les applications grand public et d’entreprise destinées aux plateformes mobiles.

En raison de contraintes liées à la réglementation ou à la sécurité, une application peut avoir besoin d'inclure dans la notification des informations qui ne peuvent pas être transmises via l'infrastructure de notification Push standard. Ce didacticiel montre comment procéder en envoyant des informations sensibles par l'intermédiaire d'une connexion authentifiée sécurisée entre l'appareil client et le serveur principal de l'application.

Globalement, le processus est le suivant :

1. Le serveur principal de l'application :
   * stocke la charge utile sécurisée dans la base de données principale ;
   * envoie l'ID de cette notification à l'appareil (aucune information sécurisée n'est envoyée).
2. L'application qui se trouve sur l'appareil, lorsqu'elle reçoit la notification :
   * L'appareil contacte le serveur principal en demandant la charge utile sécurisée.
   * L'application peut afficher la charge utile sous la forme d'une notification sur l'appareil.

Veuillez noter que dans le flux précédent (et dans ce didacticiel), nous partons du principe que l’appareil stocke un jeton d’authentification dans un stockage local, une fois l’utilisateur connecté. Cela simplifie nettement l’expérience, car l’appareil peut récupérer la charge utile sécurisée en utilisant ce jeton. Si votre application ne stocke pas les jetons d'authentification sur l'appareil, ou si ces jetons sont susceptibles d'expirer, lorsque l'application sur l'appareil reçoit la notification, elle doit afficher une notification générique demandant à l'utilisateur de lancer l'application. L'application authentifie alors l'utilisateur et affiche la charge utile de la notification.

Ce didacticiel sur les notifications Push sécurisées montre comment envoyer une notification Push en toute sécurité. Il s’appuie sur le didacticiel [Envoi de notifications à des utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md). Vous devez donc suivre ce dernier au préalable.

> [!NOTE]
> Ce didacticiel repose sur l'hypothèse que vous avez créé et configuré votre hub de notification comme décrit dans [Prise en main de Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modification du projet iOS
Maintenant que vous avez modifié le serveur principal de votre application pour qu'il n'envoie que l' *id* des notifications, vous devez modifier votre application iOS pour gérer cette notification et rappeler votre serveur pour récupérer le message sécurisé à afficher.

Pour cela, nous devons écrire la logique permettant de récupérer le contenu sécurisé auprès du serveur principal de l'application.

1. Dans **AppDelegate.m**, assurez-vous que l’application s’inscrit aux notifications sans assistance afin qu’elle traite l’ID de notification envoyé à partir du serveur principal. Ajoutez l’option **UIRemoteNotificationTypeNewsstandContentAvailability** dans didFinishLaunchingWithOptions :
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. Dans **AppDelegate.m** , ajoutez une section d'implémentation au début, avec la déclaration qui suit :
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. Ensuite, ajoutez le code qui suit dans la section d’implémentation, en remplaçant l’espace réservé `{back-end endpoint}` par le point de terminaison de votre serveur principal que vous avez obtenu précédemment :

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

1. Maintenant, nous devons gérer la notification entrante et utiliser la méthode ci-dessus pour récupérer le contenu à afficher. Nous devons tout d'abord permettre à l'application iOS de s'exécuter en arrière-plan lorsqu'elle reçoit une notification Push. Dans **XCode**, sélectionnez votre projet d’application dans le volet gauche, puis cliquez sur votre cible d’application principale dans la section **Cibles** du volet central.
2. Cliquez ensuite sur l’onglet **Capacités** en haut du volet central et cochez la case **Notifications distantes**.
   
    ![][IOS1]
3. Dans **AppDelegate.m** , ajoutez la méthode suivante pour gérer les notifications Push :
   
        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);
   
            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];
   
        }
   
    Notez qu'il est préférable de gérer les cas de refus ou les cas concernant les propriétés d'en-tête d'authentification manquantes au niveau du serveur. La gestion spécifique de ces cas dépend surtout de l'expérience utilisateur souhaitée. Une possibilité est d'afficher une notification avec une invite générique demandant à l'utilisateur de s'authentifier afin de récupérer la notification elle-même.

## <a name="run-the-application"></a>Exécution de l'application
Pour exécuter l'application, procédez comme suit :

1. Dans XCode, exécutez l’application sur un appareil iOS physique (les notifications Push ne fonctionnent pas dans le simulateur).
2. Dans l'interface utilisateur de l'application iOS, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux.
3. Dans l'interface utilisateur de l'application iOS, cliquez sur **Log in**. Cliquez ensuite sur **Send push**. Vous devriez voir la notification sécurisée affichée dans le centre de notifications.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
