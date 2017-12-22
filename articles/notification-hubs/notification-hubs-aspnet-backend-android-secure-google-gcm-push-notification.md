---
title: "Envoi de notifications push sécurisées avec Azure Notification Hubs"
description: "Découvrez comment envoyer des notifications Push sécurisées à une application Android depuis Azure. Exemples de code écrits en Java et C#."
documentationcenter: android
keywords: notification push,notifications push,messages push,notifications push android
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 29f8c516e611c13fb73c7edc15e7c52708c75bb0
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Envoi de notifications push sécurisées avec Azure Notification Hubs
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
> [!IMPORTANT]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

La prise en charge des notifications Push dans Microsoft Azure vous permet d’accéder à une infrastructure de messages Push conviviale, multiplateforme avec montée en charge qui simplifie fortement l’implémentation des notifications Push pour les applications grand public et d’entreprise destinées aux plateformes mobiles.

En raison de contraintes liées à la réglementation ou à la sécurité, une application peut avoir besoin d'inclure dans la notification des informations qui ne peuvent pas être transmises via l'infrastructure de notification Push standard. Ce didacticiel montre comment procéder en envoyant des informations sensibles par l'intermédiaire d'une connexion authentifiée sécurisée entre l'appareil Android client et le serveur principal de l'application.

Globalement, le processus est le suivant :

1. Le serveur principal de l'application :
   * stocke la charge utile sécurisée dans la base de données principale ;
   * envoie l'ID de cette notification à l'appareil Android (aucune information sécurisée n'est envoyée).
2. L'application qui se trouve sur l'appareil, lorsqu'elle reçoit la notification :
   * L'appareil Android contacte le serveur principal en demandant la charge utile sécurisée.
   * L’application peut afficher la charge utile sous la forme d’une notification sur l’appareil.

Veuillez noter que dans le flux précédent (et dans ce didacticiel), nous partons du principe que l’appareil stocke un jeton d’authentification dans un stockage local, une fois l’utilisateur connecté. Cela simplifie nettement l’expérience, car l’appareil peut récupérer la charge utile sécurisée en utilisant ce jeton. Si votre application ne stocke pas les jetons d’authentification sur l’appareil, ou si ces jetons sont susceptibles d’expirer, lorsque l’application sur l’appareil reçoit la notification push, elle doit afficher une notification générique demandant à l’utilisateur de lancer l’application. L'application authentifie alors l'utilisateur et affiche la charge utile de la notification.

Ce didacticiel vous montre comment envoyer des notifications push sécurisées. Il s’appuie sur le didacticiel [Envoi de notifications à des utilisateurs](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md). Vous devez donc suivre ce dernier au préalable si ce n’est déjà fait.

> [!NOTE]
> Ce didacticiel part du principe que vous avez créé et configuré votre hub de notification, comme décrit dans [Prise en main de Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modification du projet Android
Maintenant que vous avez modifié le serveur principal de votre application pour qu'il n'envoie que l' *ID* d'une notification push, vous devez modifier votre application Android pour gérer cette notification et rappeler votre serveur pour récupérer le message sécurisé à afficher.
Pour atteindre cet objectif, vous devez vous assurer que votre application Android sait comment s’authentifier auprès de votre serveur principal lorsqu’elle reçoit les notifications Push.

Nous allons maintenant modifier le processus de *connexion* afin d'enregistrer la valeur d'en-tête de l'authentification dans les préférences partagées de votre application. D'autres mécanismes de même type peuvent être utilisés pour stocker n'importe quel jeton d'authentification (par exemple des jetons OAuth) que l'application doit utiliser sans demander d'informations d'identification.

1. Dans votre projet d'application Android, ajoutez les constantes suivantes au début de la classe **MainActivity** :
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. Toujours dans la classe **MainActivity**, mettez à jour la méthode `getAuthorizationHeader()` pour qu’elle contienne le code suivant :
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. Ajoutez les instructions `import` suivantes au début du fichier **MainActivity** :
   
        import android.content.SharedPreferences;

Nous allons maintenant changer le gestionnaire qui est appelé lorsque la notification est reçue.

1. Dans la classe **MyHandler**, modifiez la méthode `OnReceive()` afin qu’elle contienne :
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. Puis ajoutez la méthode `retrieveNotification()` en remplaçant l’espace réservé `{back-end endpoint}` par le point de terminaison du serveur principal obtenu lors du déploiement de votre serveur principal :
   
        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);
   
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

Cette méthode appelle le serveur principal de votre application pour récupérer le contenu de la notification avec les informations d'identification stockées dans les préférences partagées et l'affiche comme une notification normale. La notification recherche l'utilisateur de l'application, exactement comme n'importe quelle autre notification Push.

Notez qu'il est préférable de gérer les cas de refus ou les cas concernant les propriétés d'en-tête d'authentification manquantes au niveau du serveur. La gestion spécifique de ces cas dépend surtout de l'expérience utilisateur souhaitée. Une possibilité est d'afficher une notification avec une invite générique demandant à l'utilisateur de s'authentifier afin de récupérer la notification elle-même.

## <a name="run-the-application"></a>Exécution de l'application
Pour exécuter l'application, procédez comme suit :

1. Assurez-vous que **AppBackend** est déployé dans Azure. Si vous utilisez Visual Studio, exécutez l'application API web **AppBackend** . Une page web ASP.NET s’affiche.
2. Dans Eclipse, exécutez l'application sur un appareil Android physique ou dans l'émulateur.
3. Dans l'interface utilisateur de l'application Android, entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux.
4. Dans l'interface utilisateur de l'application Android, cliquez sur **Log in**. Cliquez ensuite sur **Send push**.

