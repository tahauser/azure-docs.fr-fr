---
title: Prise en main d'Azure Mobile Engagement pour Cordova/Phonegap
description: Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications push pour les applications Cordova/Phonegap.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: abe0ad6117cc2feb72ebe788d07b50e8f145174c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Prise en main d'Azure Mobile Engagement pour Cordova/Phonegap
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique explique comment utiliser Azure Mobile Engagement pour analyser l’utilisation de votre application et envoyer des notifications push à des segments d’utilisateurs d’une application développée avec Cordova.

Dans ce didacticiel, nous allons créer une application Cordova vide à l'aide de Mac et intégrer le Kit de développement logiciel (SDK) Mobile Engagement. Elle collectera des données d'analyse de base et recevra des notifications push à l'aide du système de notifications push Apple (APNS) pour iOS et de Google Cloud Messaging (GCM) pour Android. Nous déploierons cette application sur un appareil iOS ou Android pour le test. 

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Ce didacticiel requiert les éléments suivants :

* Xcode, que vous pouvez installer depuis le Mac App Store (pour un déploiement sur iOS)
* [Kit de développement logiciel (SDK) et émulateur Android](http://developer.android.com/sdk/installing/index.html) (pour un déploiement sur Android)
* Certificat de notification Push (.p12) que vous pouvez obtenir dans votre centre de développement Apple pour l’APNS
* Numéro du projet GCM que vous pouvez obtenir à partir de votre Console de développement Google pour GCM
* [Plug-in Mobile Engagement Cordova](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Vous trouverez le code source et le fichier Lisez-moi du plug-in Cordova sur [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova)
> 
> 

## <a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement
Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. 

Nous allons créer une application de base à l’aide de Cordova afin d’illustrer l’intégration :

### <a name="create-a-new-cordova-project"></a>Création d’un nouveau projet Cordova
1. Ouvrez la fenêtre *Terminal* sur votre ordinateur Mac et saisissez les lignes suivantes pour créer un projet Cordova à partir du modèle par défaut. Assurez-vous que le profil de publication que vous utilisez pour déployer votre application iOS utilise « com.mycompany.myapp » comme ID d’application. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Exécutez l’instruction suivante pour configurer votre projet pour **iOS** , puis exécutez celui-ci dans le simulateur iOS :
   
        $ cordova platform add ios 
        $ cordova run ios
3. Exécutez l’instruction suivante pour configurer votre projet pour **Android** et l’exécuter dans l’émulateur Android. Assurez-vous que dans les paramètres de votre émulateur de Kit de développement logiciel Android la cible est définie sur API Google (Google Inc.) et CPU/ABI sur Google APIs ARM.  
   
        $ cordova platform add android
        $ cordova run android
4. Ajout du plug-in Cordova Console. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Connexion de votre application au serveur principal Mobile Engagement
1. Installez le plug-in Azure Mobile Engagement Cordova tout en fournissant les valeurs des variables pour configurer le plug-in :
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Icône Android Reach* : doit être le nom de la ressource sans extension ni préfixe dessinable (ex : mynotificationicon), et le fichier icône doit être copié dans votre projet android (platforms/android/res/drawable)

*Icône iOS Reach* : doit être le nom de la ressource avec son extension (ex : mynotificationicon.png), et le fichier icône doit être ajouté à votre projet iOS avec XCode (en utilisant le menu d’ajout de fichiers)

## <a id="monitor"></a>Activation de la surveillance en temps réel
1. Dans le projet Cordova, modifiez **www/js/index.js** pour ajouter l’appel de Mobile Engagement afin de déclarer une nouvelle activité à la réception de l’événement *deviceReady* .
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Exécutez l'application :
   
   * **Pour iOS**
     
       Dans la fenêtre `Terminal` , exécutez votre application dans une nouvelle instance de simulateur en exécutant le code suivant :
     
           cordova run ios
   * **Pour Android**
     
       Dans la fenêtre `Terminal` , exécutez votre application dans une nouvelle instance d’émulateur en exécutant le code suivant :
     
           cordova run android
3. Les éléments suivants s’affichent dans les journaux de la console :
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Activation des notifications Push et de la messagerie intégrée à l’application
Mobile Engagement vous permet d'interagir avec vos utilisateurs à l'aide de notifications push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement.
Les sections suivantes vous permettront de configurer votre application pour la réception des notifications.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Configuration des informations d'identification Push pour Mobile Engagement
Pour permettre à Mobile Engagement d'envoyer des notifications push en votre nom, vous devez lui accorder l'accès à votre certificat Apple iOS ou à votre clé d’API serveur GCM. 

1. Accédez au portail Mobile Engagement. Vérifiez que vous vous trouvez dans l’application utilisée pour ce projet, puis cliquez sur le bouton **Activer** situé en bas :
   
    ![][1]
2. Vous accédez à la page de paramètres via le portail Engagement. À partir de là, cliquez sur la section **Native Push** :
   
    ![][2]
3. Configuration du certificat iOS/de la clé d’API serveur GCM
   
    **[iOS]**
   
    a. Sélectionnez votre .p12, téléchargez-le et tapez votre mot de passe :
   
    ![][3]
   
    **[Android]**
   
    a. Dans la section Paramètres GCM, cliquez sur l’icône de modification devant **Clé API**. Ensuite, dans le menu contextuel qui s’affiche, collez la clé du serveur GCM, puis cliquez sur **OK**. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Activation des notifications push dans l'application Cordova
Modifiez **www/js/index.js** pour ajouter l’appel de Mobile Engagement afin de demander des notifications push et de déclarer un gestionnaire :

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Exécution de l'application
**[iOS]**

1. Nous allons utiliser XCode pour créer et déployer l'application sur l’appareil afin de tester les notifications push, car iOS autorise uniquement ces notifications sur un appareil réel. Accédez à l’emplacement où votre projet Cordova a été créé, puis accédez à l’emplacement  **...\platforms\ios**. Ouvrez le fichier .xcodeproj natif dans XCode. 
2. Générez et déployez l'application Cordova sur l’appareil iOS en utilisant le compte ayant le profil d’approvisionnement contenant le certificat que vous avez téléchargé vers le portail Mobile Engagement et l'ID d'application correspondant à celui fourni lors de la création de l'application Cordova. Vous pouvez extraire l’*identificateur d’offres groupées* dans votre fichier **Resources\*-info.plist** dans XCode pour déterminer s’il correspond. 
3. La fenêtre contextuelle iOS standard apparaîtra sur votre appareil pour indiquer que l'application demande l'autorisation d'envoyer des notifications. Accordez l'autorisation. 

**[Android]**

Vous pouvez utiliser l'émulateur pour exécuter l'application Android, car les notifications GCM sont prises en charge par l'émulateur Android. 

    cordova run android

## <a id="send"></a>Envoi d’une notification vers votre application
Nous allons maintenant créer une campagne simple de notification Push qui enverra une notification Push à votre application en cours d’exécution sur l’appareil :

1. Accédez à l’onglet **REACH** de votre portail Mobile Engagement.
2. Cliquez sur **Nouvelle annonce** pour créer votre campagne Push.
   
    ![][6]
3. Entrez les informations nécessaires pour créer votre campagne **[Android]**
   
   * Entrez un **nom** pour votre campagne. 
   * Sélectionnez le **Type de distribution** *Notification système* *Simple*.
   * Sélectionnez le **délai de livraison**  *N’importe quand*
   * Entrez un **Titre** pour votre notification, qui constituera la première ligne de la diffusion.
   * Entrez un **Message** pour votre notification, qui constituera le corps du message. 
     
     ![][11]
4. Entrez les informations nécessaires pour créer votre campagne **[iOS]**
   
   * Entrez un **nom** pour votre campagne. 
   * Sélectionnez le **délai de livraison**  *Hors de l’application uniquement*
   * Entrez un **Titre** pour votre notification, qui constituera la première ligne de la diffusion.
   * Entrez un **Message** pour votre notification, qui constituera le corps du message. 
     
     ![][12]
5. Faites défiler l’écran vers le bas et, dans la section Contenu, sélectionnez **Notification uniquement**
   
    ![][8]
6. [Facultatif] Vous pouvez également fournir une URL d’action. Assurez-vous qu’elle utilise un schéma d’URL fourni lors de la configuration de la variable **AZME\_REDIRECT\_URL** du plug-in, par exemple *myapp://test*.  
7. Vous avez terminé de définir la campagne de base la plus simple possible. Faites maintenant défiler vers le bas, puis cliquez sur le bouton **Créer** pour enregistrer votre campagne.
8. Enfin, dernière étape, **activez** votre campagne.
   
    ![][10]
9. Vous devriez maintenant voir une notification Push sur votre appareil ou votre émulateur dans le cadre de cette campagne. 

## <a id="next-steps"></a>Étapes suivantes
[Vue d'ensemble de toutes les méthodes disponibles avec le Kit de développement logiciel (SDK) Cordova Mobile Engagement](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

