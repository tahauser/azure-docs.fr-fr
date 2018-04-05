---
title: Prise en main d’Azure Mobile Engagement pour les applications Android
description: Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Android.
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4eda8129e03e4880e168f7c154c7236da028e81e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Prise en main d’Azure Mobile Engagement pour les applications Android
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 


[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d’une application Android.
Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. À cette occasion, vous allez créer une application Android vide qui recueille des données de base et reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android](https://developer.android.com/sdk/index.html), qui incluent l'environnement de développement intégré Android Studio et la dernière plateforme Android.

Le [Kit de développement logiciel (SDK) Mobile Engagement Android](https://aka.ms/vq9mfn)est également requis.

> [!IMPORTANT]
> Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configuration de Mobile Engagement pour votre application Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connexion de votre application au serveur principal Mobile Engagement
Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. Vous allez créer une application de base avec Android Studio afin d’illustrer l’intégration.

Vous trouverez la documentation complète sur l’intégration dans le [Kit de développement logiciel (SDK) Mobile Engagement pour Android](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Création d’une application Android
1. Démarrez **Android Studio** et, dans le menu contextuel, sélectionnez **Démarrer un nouveau projet Android Studio**.

    ![][1]
2. Indiquez un nom d’application et un domaine d’entreprise. Notez les valeurs que vous saisissez, car vous en aurez besoin ultérieurement. Cliquez sur **Suivant**.

    ![][2]
3. Sélectionnez le niveau d’API et le facteur de forme cible, puis cliquez sur **Suivant**.

   > [!NOTE]
   > Mobile Engagement requiert au minimum le niveau d'API 10 (Android 2.3.3).
   >
   >

    ![][3]
4. Sélectionnez ici **Blank Activity** (Activité vide), qui sera le seul écran de cette application, puis cliquez sur **Suivant**.

    ![][4]
5. Enfin, laissez les valeurs par défaut et cliquez sur **Terminer**.

    ![][5]

Android Studio crée l’application de démonstration à laquelle nous allons intégrer Mobile Engagement.

### <a name="include-the-sdk-library-in-your-project"></a>Inclure la bibliothèque du Kit de développement logiciel (SDK) dans votre projet
1. Téléchargez le [Kit SDK Mobile Engagement Android](https://aka.ms/vq9mfn).
2. Extrayez le fichier d'archive dans un dossier sur votre ordinateur.
3. Identifiez la bibliothèque .jar correspondant à la version actuelle de ce Kit SDK et copiez-la dans le Presse-papiers.

      ![][6]
4. Accédez à la section **Projet** (1) et collez le fichier .jar dans le dossier libs (2).

      ![][7]
5. Pour charger la bibliothèque, synchronisez le projet.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Connectez votre application au serveur principal Mobile Engagement à l'aide de la chaîne de connexion.
1. Copiez les lignes de code suivantes dans la création d’activité (cette opération doit être effectuée à un seul emplacement de votre application, généralement dans l’activité principale). Pour cet exemple d’application, ouvrez MainActivity sous src -> main -> dossier java et ajoutez le code suivant :

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. Résolvez les références en appuyant sur Alt + Entrée ou en ajoutant les instructions d’importation suivantes :

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. De retour sur le portail Azure, dans la page **Informations de connexion** de votre application, copiez la **chaîne de connexion**.

      ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)

4. Collez-la dans le paramètre `setConnectionString`, en remplaçant la chaîne entière comme indiqué dans le code suivant :

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Ajouter des autorisations et une déclaration de service
1. Ajoutez ces autorisations au fichier Manifest.xml de votre projet, juste avant ou après la balise `<application>` :

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. Pour déclarer le service de l’agent, ajoutez ce code entre les balises `<application>` et `</application>` :

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. Dans le code que vous avez collé, remplacez `"<Your application name>"` dans l’étiquette qui s’affiche dans le menu **Paramètres** indiquant les services exécutés sur l’appareil. Vous pouvez, par exemple, ajouter le mot « Service » dans cette étiquette.

### <a name="send-a-screen-to-mobile-engagement"></a>Envoyer un écran à Mobile Engagement
Pour commencer à envoyer des données et vérifier que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

Accédez à **MainActivity.java** et ajoutez le code suivant pour remplacer la classe de base **MainActivity** par **EngagementActivity** :

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> Si vous n’utilisez pas la classe de base *Activity*, consultez l’article relatif aux [fonctionnalités de rapports avancées d’Android](mobile-engagement-android-advanced-reporting.md) pour savoir comment hériter de classes différentes.
>
>

Mettez en commentaire la ligne suivante pour cet exemple simple de scénario :

    // setSupportActionBar(toolbar);

Si vous souhaitez conserver le `ActionBar` dans votre application, consultez [Options de génération de rapports avec Engagement sur Android](mobile-engagement-android-advanced-reporting.md).

## <a name="connect-app-with-real-time-monitoring"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Activation des notifications push et de la messagerie in-app
Pendant une campagne, Mobile Engagement vous permet d’interagir avec vos utilisateurs à l’aide de notifications Push et de messages dans l’application. Ce module s'appelle Couverture dans le portail Mobile Engagement.
La section suivante vous permet de configurer votre application pour la réception des notifications.

### <a name="copy-sdk-resources-in-your-project"></a>Copier les ressources du SDK dans votre projet
1. Revenez au contenu de téléchargement de votre Kit de développement logiciel (SDK) et copiez le dossier **res** .

    ![][10]
2. Revenez à Android Studio, sélectionnez le répertoire **main** de vos fichiers de projet, puis collez-le pour ajouter les ressources à votre projet.

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
Accédez au [Kit de développement logiciel (SDK) Android](mobile-engagement-android-sdk-overview.md) pour obtenir des informations détaillées sur l’intégration du kit de développement logiciel.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
