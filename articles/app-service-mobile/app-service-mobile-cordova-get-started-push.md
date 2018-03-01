---
title: "Ajouter des notifications Push à une application Apache Cordova à l’aide de la fonctionnalité Mobile Apps d’Azure App Service | Microsoft Docs"
description: "Découvrez comment utiliser Mobile Apps pour envoyer des notifications Push à votre application Apache Cordova."
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: 
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 6af5fa51f2e6553431b9f0aa2dbb368651e7e209
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Ajout de notifications Push à votre application Apache Cordova.
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d'ensemble
Dans ce didacticiel, vous ajoutez des notifications Push au projet de [démarrage rapide Apache Cordova][5] afin qu’une notification Push soit envoyée à l’appareil chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devrez disposer du package d’extension de notification Push. Pour plus d’informations, consultez l’article [Utiliser le kit SDK du serveur backend .NET pour Azure Mobile Apps][1].

## <a name="prerequisites"></a>Configuration requise
Ce didacticiel repose sur l’hypothèse que vous disposez d’une application Apache Cordova développée avec Visual Studio 2015. Cet appareil doit s’exécuter sur l’Émulateur Android de Google, un appareil Android, un appareil Windows ou un appareil iOS.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* PC équipé de [Visual Studio Community 2015][2] ou d’une version ultérieure ; 
* [Visual Studio Tools pour Apache Cordova][4] ;
* [compte Azure actif][3] ;
* projet de [démarrage rapide Apache Cordova][5] terminé ;
* (Android) [compte Google][6] avec une adresse électronique vérifiée ;
* (iOS) [appartenance au programme pour développeurs Apple][7] et appareil iOS (le simulateur iOS ne prend pas en charge les notifications Push) ;
* (Windows) [compte de développeur Microsoft Store][8] et appareil Windows 10.

## <a name="configure-hub"></a>Configurer un hub de notification
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Visionnez une vidéo illustrant la procédure décrite dans cette section][9].

## <a name="update-the-server-project"></a>Mettre à jour le projet de serveur
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modification de votre application Cordova
Vérifiez que votre projet d’application Apache Cordova est prêt à gérer les notifications Push en installant le plug-in de notifications Push Cordova, ainsi que tous les services Push propres à la plateforme.

#### <a name="update-the-cordova-version-in-your-project"></a>Mise à jour de la version de Cordova dans votre projet.
Si votre projet utilise une version d’Apache Cordova antérieure à la version 6.1.1, mettez à jour le projet client. Pour mettre à jour le projet, procédez comme suit : 

* Pour ouvrir le concepteur de configuration, cliquez avec le bouton droit sur `config.xml`.
* Sélectionnez l’onglet **Plateformes**.
* Dans la zone de texte **Cordova CLI**, sélectionnez **6.1.1**. 
* Pour mettre à jour le projet, sélectionnez **Générer**, puis **Générer la solution**.

#### <a name="install-the-push-plugin"></a>Installation du plug-in Push
En mode natif, les applications Apache Cordova ne gèrent pas les fonctionnalités de réseau ou d’appareils.  Ces fonctionnalités sont fournies par des plug-ins publiés sur [npm][10] ou sur GitHub. Le plug-in `phonegap-plugin-push` gère les notifications Push du réseau.

Vous pouvez installer le plug-in de notifications Push de l’une des façons suivantes :

**À partir de l'invite de commandes :**

Exécutez la commande suivante :

    cordova plugin add phonegap-plugin-push

**À partir de Visual Studio :**

1. Dans l’Explorateur de solutions, ouvrez le fichier `config.xml`. Ensuite, sélectionnez **Plug-ins** > **Personnalisé**. Puis sélectionnez **Git** comme source d’installation. 
    
2. Entrez `https://github.com/phonegap/phonegap-plugin-push` en tant que source.

    ![Ouvrir le fichier config.xml dans l’Explorateur de solutions][img1]

3. Sélectionnez la flèche en regard de la source d’installation.

4. Si vous avez déjà un ID de projet numérique pour le projet de Console de développement Google, ajoutez-le dans **SENDER_ID**. Autrement, entrez une valeur d’espace réservé, par exemple, 777777. Si vous ciblez Android, vous pourrez modifier cette valeur dans le fichier config.xml ultérieurement.

    >[!NOTE]
    >À partir de la version 2.0.0, google-services.json doit être installé dans le dossier racine de votre projet pour la configuration de l’ID de l’expéditeur. Pour plus d’informations, consultez la [documentation d’installation](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md).
5. Sélectionnez **Ajouter**.

Le plug-in de notification Push est maintenant installé.

#### <a name="install-the-device-plugin"></a>Installation du plug-in Appareil
Suivez la même procédure que celle utilisée pour installer le plug-in de notification Push. Ajoutez le plug-in Device à partir de la liste de plug-ins Principal. (Pour le rechercher, sélectionnez **Plug-ins** > **Principal**.) Vous avez besoin de ce plug-in pour obtenir le nom de la plateforme.

#### <a name="register-your-device-when-the-application-starts"></a>Inscription de votre appareil au démarrage de l’application 
Initialement, nous proposons un code minimal pour Android. Par la suite, vous pourrez modifier l’application pour qu’elle s’exécute sur iOS ou sur Windows 10.

1. Ajoutez un appel de la méthode **registerForPushNotifications** pendant le rappel pour le processus d’identification. Une autre possibilité consiste à ajouter cet appel au bas de la méthode **onDeviceReady** :

        // Log in to the service.
        client.login('google')
            .then(function () {
                // Create a table reference.
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems.
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item.
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    Cet exemple montre l’appel de **registerForPushNotifications** après une authentification réussie. Vous pouvez appeler `registerForPushNotifications()` aussi souvent que nécessaire.

2. Ajoutez la nouvelle méthode **registerForPushNotifications** comme suit :

        // Register for push notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }
3. (Android) Dans le code précédent, remplacez `Your_Project_ID` par l’ID de projet numérique associé à votre application dans la [Console de développement Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Facultatif) Configurer et exécuter l’application sur Android
Terminez cette section pour activer les notifications Push pour Android.

#### <a name="enable-gcm"></a>Activation de Firebase Cloud Messaging
Dans la mesure où vous ciblez la plateforme Google Android en premier lieu, vous devez activer Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Configuration du serveur principal Mobile Apps pour l’envoi de demandes de notifications Push à l’aide de FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configuration de votre application Cordova pour Android
Dans votre application Cordova, ouvrez le fichier config.xml. Remplacez `Your_Project_ID` par l’ID de projet numérique associé à votre application dans la [Console de développement Google][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Ouvrez le fichier index.js. Puis mettez à jour le code pour qu’il utilise votre ID de projet numérique.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Configuration de votre appareil Android pour le débogage USB
Pour être en mesure de déployer votre application sur votre appareil Android, vous devez activer le débogage USB. Sur votre téléphone Android, procédez comme suit :

1. Accédez à **Paramètres** > **À propos du téléphone**. Appuyez sur **Numéro de build** jusqu’à ce que le mode développeur soit activé (environ sept fois).
2. Au niveau de l’écran **Paramètres** > **Options pour les développeurs**, activez **Débogage USB**. Puis connectez votre téléphone Android à votre PC de développement avec un câble USB.

Nous avons testé cette procédure à l’aide d’un appareil Google Nexus 5 X exécutant Android 6.0 (Marshmallow). Toutefois, les techniques sont communes à l’ensemble des versions Android modernes.

#### <a name="install-google-play-services"></a>Installation des services Google Play
Le plug-in Push sollicite les services Google Play pour les notifications Push.

1. Dans Visual Studio, sélectionnez **Outils** > **Android** > **Android SDK Manager**. Puis développez le dossier **Extras**. Cochez les cases appropriées pour vous assurer que chacun des Kits de développement logiciel (SDK) ci-après est installé :

   * Android 2.3 ou version ultérieure
   * Révision du référentiel Google 27 ou version ultérieure
   * Google Play Services 9.0.2 ou version ultérieure

2. Sélectionnez **Installer les packages**. Attendez que l’installation se termine.

Les bibliothèques actuellement requises figurent dans la [documentation d’installation phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Test des notifications Push dans l’application sur Android
Vous pouvez désormais tester les notifications Push en exécutant l’application et en insérant des éléments dans la table TodoItem. Vous pouvez effectuer ce test à partir du même appareil ou d’un deuxième appareil, à condition d’utiliser le même serveur principal. Testez votre application Cordova sur la plateforme Android de l’une des manières suivantes :

* *Sur un appareil physique :* connectez votre appareil Android à votre ordinateur de développement avec un câble USB.  Au lieu de **l’Émulateur Google Android**, sélectionnez **Appareil**. Visual Studio déploie l’application sur l’appareil et l’exécute. Vous pouvez alors interagir avec l’application sur l’appareil.

  Les applications de partage d’écran telles que [Mobizen][20] peuvent vous aider à développer des applications Android. Mobizen projette votre écran Android sur un navigateur web de votre PC.

* *Sur un émulateur Android :* des étapes de configuration supplémentaires sont requises en cas d’utilisation d’un émulateur.

    Assurez-vous de procéder au déploiement sur un périphérique virtuel sur lequel les API Google sont définis comme cible, comme indiqué dans le Gestionnaire d’appareil virtuel Android (AVD).

    ![Gestionnaire de périphérique virtuel Android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Si vous souhaitez utiliser un émulateur x86 plus rapide, [installez le pilote HAXM][11], puis configurez l’émulateur pour l’utilisation de ce pilote.

    Ajoutez un compte Google à l’appareil Android en sélectionnant **Applications** > **Paramètres** > **Ajouter un compte**. Puis suivez les invites.

    ![Ajout d’un compte Google à l’appareil Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Exécutez normalement l’application todolist et insérez un nouvel élément todo. Cette fois, une icône de notification s’affiche dans la zone de notification. Vous pouvez ouvrir le tiroir de notification pour afficher le texte intégral de la notification.

    ![Afficher une notification](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Facultatif) Configurer et exécuter sur iOS
Cette section est dédiée à l’exécution du projet Cordova sur les appareils iOS. Si vous n’utilisez pas d’appareils iOS, vous pouvez ignorer cette section.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Installer et exécuter l’agent remote build iOS sur un Mac ou un service cloud
Avant de pouvoir exécuter une application Cordova sur iOS à l’aide de Visual Studio, suivez la procédure décrite dans le [Guide d’installation iOS][12] pour installer et exécuter l’agent de build distant.

Assurez-vous que vous pouvez générer l’application pour iOS. La génération de l’application pour iOS à partir de Visual Studio requiert l’exécution de la procédure décrite dans le guide d’installation. Si vous n’avez pas de Mac, vous pouvez générer des applications pour iOS en utilisant l’agent de build distant sur un service tel que MacInCloud. Pour plus d’informations, consultez l’article [Run your iOS app in the cloud (Exécuter votre application iOS dans le cloud)][21].

> [!NOTE]
> XCode 7 ou version ultérieure est requis pour utiliser le plug-in Push sur iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Rechercher l’ID à utiliser en tant qu’ID de l’application
Avant d’inscrire votre application pour les notifications Push, ouvrez le fichier config.xml dans votre application Cordova, recherchez la valeur d’attribut `id` dans l’élément de widget, puis copiez-la pour une utilisation ultérieure. Dans le code XML suivant, l’ID est `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Vous utiliserez cet identifiant ultérieurement lors de la création d’un ID d’application sur le portail des développeurs d’Apple. Si vous créez un autre ID d’application sur le portail des développeurs, vous devrez effectuer quelques étapes supplémentaires plus tard dans ce didacticiel. L’ID de l’élément de widget doit correspondre à l’ID d’application sur le portail des développeurs.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Inscrire l'application pour les notifications push dans le portail de développeurs d'Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Regarder une vidéo montrant des étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour l’envoi de notifications Push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Vérifier que votre ID d’application correspond à votre application Cordova
Si l’ID d’application que vous avez créé dans votre compte de développeur Apple correspond déjà à l’ID de l’élément de widget dans le fichier config.xml, vous pouvez ignorer cette étape. Toutefois, si les ID ne correspondent pas, procédez comme suit :

1. Supprimez le dossier platforms de votre projet.
2. Supprimez le dossier plugins de votre projet.
3. Supprimez le dossier node_modules de votre projet.
4. Mettez à jour l’attribut id de l’élément de widget dans le fichier config.xml pour qu’il utilise l’ID d’application que vous avez créé dans votre compte de développeur Apple.
5. Régénérez votre projet.

##### <a name="test-push-notifications-in-your-ios-app"></a>Tester les notifications Push dans votre application iOS
1. Dans Visual Studio, assurez-vous que **iOS** est sélectionné comme cible de déploiement. Puis sélectionnez **Périphérique** pour exécuter les notifications Push sur votre appareil iOS connecté.

    Vous pouvez exécuter les notifications Push sur un appareil iOS qui est connecté à votre PC avec iTunes. Les notifications Push ne sont pas prises en charge par le simulateur iOS.

2. Sélectionnez le bouton **Exécuter** ou **F5** dans Visual Studio pour générer le projet et démarrer l’application sur un appareil iOS. Ensuite, sélectionnez **OK** pour accepter les notifications Push.

   > [!NOTE]
   > L’application demande confirmation pour les notifications push lors de la première exécution.

3. Dans l’application, tapez une tâche, puis sélectionnez l’icône Plus **(+)**.
4. Vérifiez qu’une notification a été reçue. Puis sélectionnez **OK** pour ignorer la notification.

## <a name="optional-configure-and-run-on-windows"></a>(Facultatif) Configurer et exécuter sur Windows
Cette section décrit la procédure d’exécution du projet d’application Apache Cordova sur les appareils Windows 10 (le plug-in push PhoneGap est pris en charge sur Windows 10). Vous pouvez ignorer cette section si vous n'utilisez pas d'appareils Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Inscrire votre application Windows pour les notifications Push avec WNS
Pour utiliser les options de stockage dans Visual Studio, sélectionnez une cible Windows à partir de la liste Plateformes Solution, par exemple **Windows-x64** ou **Windows-x86**. (Évitez **Windows-AnyCPU** pour les notifications Push.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Regarder une vidéo montrant des étapes similaires][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurer le Notification Hub pour WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurer votre application Cordova pour prendre en charge les notifications Push Windows
Ouvrez le concepteur de configuration en cliquant avec le bouton droit sur **config.xml**. Puis sélectionnez **Concepteur de vues**. Ensuite, sélectionnez l’onglet **Windows**, puis sélectionnez **Windows 10** sous **Version cible de Windows**.

Pour prendre en charge les notifications Push dans vos versions (de débogage) par défaut, ouvrez le fichier build.json. Puis copiez la configuration de « version finale » dans votre configuration de débogage.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Après la mise à jour, le fichier build.json doit contenir le code suivant :

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Générez l’application et vérifiez l’absence d’erreurs. Votre application cliente doit désormais s’inscrire pour les notifications du serveur principal Mobile Apps. Répétez cette section pour chaque projet Windows dans votre solution.

#### <a name="test-push-notifications-in-your-windows-app"></a>Tester les notifications Push dans votre application Windows
Dans Visual Studio, assurez-vous qu’une plateforme Windows est sélectionnée comme cible de déploiement, telle que **Windows-x64** ou **Windows-x86**. Pour exécuter l’application sur un PC Windows 10 hébergeant Visual Studio, choisissez **Ordinateur local**.

1. Sélectionnez le bouton **Exécuter** pour générer le projet et démarrer l’application.

2. Dans l’application, tapez un nom pour un nouvel élément todoitem, puis sélectionnez l’icône Plus **(+)** pour l’ajouter.

Vérifiez qu’une notification est reçue lorsque l’élément est ajouté.

## <a name="next-steps"></a>Étapes suivantes
* Consultez la documentation relative à [Notification Hubs][17] afin d’en découvrir davantage sur les notifications Push.
* Si vous ne l’avez pas encore fait, continuez le didacticiel en [ajoutant une authentification][14] à votre application Apache Cordova.

Découvrez comment utiliser les Kits de développement logiciel (SDK) suivants :

* [Kit de développement logiciel (SDK) Apache Cordova][15]
* [Kit de développement logiciel du serveur ASP.NET][1]
* [Kit de développement logiciel du serveur Node.js][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/
