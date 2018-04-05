---
title: Prise en main d’Azure Mobile Engagement pour Xamarin.Android
description: Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Xamarin.Android.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: 4ac6e85a0143fc083c22a92e99503689f463d813
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Prise en main d’Azure Mobile Engagement pour les applications Xamarin.Android
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d’une application Xamarin.Android.
Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. À cette occasion, vous allez créer une application Xamarin.Android vide qui recueille des données de base et reçoit des notifications Push à l’aide de Google Cloud Messaging (GCM).

> [!NOTE]
> Le service Azure Mobile Engagement ne sera plus disponible à partir de mars 2018. Actuellement, il reste uniquement disponible pour les clients déjà abonnés à ce service. Pour plus d’informations, consultez [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Ce didacticiel requiert les éléments suivants :

* [Xamarin Studio](http://xamarin.com/studio). Vous pouvez également utiliser Visual Studio avec Xamarin, mais ce didacticiel utilise Xamarin Studio. Pour obtenir des instructions sur l’installation, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* [Kit de développement logiciel (SDK) Mobile Engagement pour Xamarin](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Essai gratuit d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).
> 
> 

## <a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement
Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. 

Nous allons créer une application de base à l’aide de Xamarin Studio pour illustrer l’intégration.

### <a name="create-a-new-xamarinandroid-project"></a>Créer un projet Xamarin.Android
1. Démarrez **Xamarin Studio**. Accédez à **File** -> **New** -> **Solution** (Fichier > Nouveau > Solution). 
   
    ![][1]
2. Sélectionnez **Android App** (Application Android) et vérifiez que **C#** est le langage sélectionné, puis cliquez sur **Next** (Suivant).
   
    ![][2]
3. Renseignez les champs **App Name** (Nom de l’application) et **Organization Identifier** (Identifiant de l’organisation). Veillez à cocher la case **Google Play Services** (Services Google Play), puis cliquez sur **Next** (Suivant). 
   
    ![][3]
4. Mettez à jour les champs **Project Name** (Nom du projet), **Solution Name** (Nom de la solution) et **Location** (Emplacement) si nécessaire, puis cliquez sur **Create** (Créer).
   
    ![][4]

Xamarin Studio crée l’application dans laquelle nous allons intégrer Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Connexion de votre application au serveur principal Mobile Engagement
1. Dans la fenêtre des solutions, cliquez avec le bouton droit sur le dossier **Packages** (Packages), puis sélectionnez **Add Packages...** (Ajouter des packages...).
   
    ![][5]
2. Recherchez **Microsoft Azure Mobile Engagement Xamarin SDK** et ajoutez-le à votre solution.  
   
    ![][6]
3. Ouvrez **MainActivity.cs** et ajoutez les instructions using suivantes :
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. Dans la méthode `OnCreate` , ajoutez ce qui suit pour initialiser la connexion avec le serveur principal Mobile Engagement. Veillez à ajouter la chaîne **ConnectionString**. 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>Ajouter des autorisations et une déclaration de service
1. Ouvrez le fichier **Manifest.xml** sous le dossier Properties. Sélectionnez l’onglet Source afin de mettre directement à jour la source XML.
2. Ajoutez ces autorisations au fichier Manifest.xml (qui se trouve sous le dossier **Properties**) de votre projet, juste avant ou après la balise `<application>` :
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. Ajoutez le code suivant entre les balises `<application>`et`</application>` pour déclarer le service de l'agent :
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. Dans le code que vous venez de coller, remplacez `"<Your application name>"` dans l’étiquette. Cet élément s’affiche dans le menu **Paramètres** où les utilisateurs peuvent voir les services qui sont en cours d’exécution sur l’appareil. Vous pouvez, par exemple, ajouter le mot « Service » dans cette étiquette.

### <a name="send-a-screen-to-mobile-engagement"></a>Envoyer un écran à Mobile Engagement
Pour commencer à envoyer des données et à vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran au serveur principal Mobile Engagement. Pour ce faire, vérifiez que `MainActivity` hérite de `EngagementActivity` à la place de `Activity`.

    public class MainActivity : EngagementActivity

Si vous ne pouvez pas hériter de `EngagementActivity`, vous devez alors ajouter les méthodes `.StartActivity` et `.EndActivity` respectivement dans `OnResume` et `OnPause`.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Activation des notifications Push et de la messagerie in-app
Mobile Engagement vous permet d’interagir et d’ATTEINDRE vos utilisateurs à l’aide de notifications Push et de la messagerie in-app dans le cadre de campagnes. Ce module s'appelle Couverture dans le portail Mobile Engagement.
Les sections suivantes vous permettent de configurer votre application pour la réception des notifications.

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
