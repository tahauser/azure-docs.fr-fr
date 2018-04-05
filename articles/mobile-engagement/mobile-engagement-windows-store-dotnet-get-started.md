---
title: Prise en main d’Azure Mobile Engagement pour les applications universelles Windows
description: Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications push pour les applications universelles Windows.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4f1de1092458bfc12236802d829a2043dcc74540
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Prise en main d’Azure Mobile Engagement pour les applications universelles Windows
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique explique comment utiliser Azure Mobile Engagement pour analyser l'utilisation de votre application et envoyer des notifications Push à des segments d'utilisateurs d'une application universelle Windows.
Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. Vous allez créer une application universelle Windows vide, qui collecte des données de base sur l’utilisation des applications et reçoit des notifications Push à l’aide du service de notification Windows.

> [!NOTE]
> Le service Azure Mobile Engagement ne sera plus disponible à partir de mars 2018. Actuellement, il reste uniquement disponible pour les clients déjà abonnés à ce service. Pour plus d’informations, consultez la page [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Configuration de Mobile Engagement pour votre application universelle Windows
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement
Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. La documentation complète d’intégration peut être consultée dans [Intégration du Kit de développement logiciel (SDK) universel Mobile Engagement Windows](mobile-engagement-windows-store-sdk-overview.md).

Nous allons créer une application de base à l’aide de Visual Studio pour la démonstration de l’intégration.

### <a name="create-a-windows-universal-app-project"></a>Création d’un projet d’application universelle Windows
Les étapes suivantes supposent l’utilisation de Visual Studio 2015, même si elles sont similaires dans les versions antérieures de Visual Studio.

1. Démarrez Visual Studio, puis, dans l’écran **Accueil**, sélectionnez **Nouveau projet**.
2. Dans le menu contextuel, sélectionnez **Windows** -> **Universelle** -> **Application vide (Windows universel)**. Dans les champs **Nom** et **Nom de la solution**, indiquez les nom et nom de solution de l’application, puis cliquez sur **OK**.

    ![][1]

Vous avez créé un projet d’application universelle Windows, dans lequel vous allez intégrer le Kit de développement logiciel (SDK) Azure Mobile Engagement.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Connexion de votre application au serveur principal Mobile Engagement
1. Installez le package Nuget [MicrosoftAzure.MobileEngagement] dans votre projet. Si vous ciblez les plateformes Windows et Windows Phone, vous devez effectuer cette opération pour les deux projets. Pour Windows 8.x et Windows Phone 8.1, le même package Nuget place les fichiers binaires propres à chaque plateforme, dans chaque projet.
2. Ouvrez **Package.appxmanifest** et assurez-vous que la fonctionnalité suivante y est ajoutée :

        Internet (Client)

    ![][2]
3. Collez maintenant la chaîne de connexion, que vous avez copiée précédemment pour votre application Mobile Engagement, dans le fichier `Resources\EngagementConfiguration.xml`, entre les balises `<connectionString>` et `</connectionString>` :

    ![][3]

    > [!TIP]
    > Si votre application cible les plateformes Windows et Windows Phone, vous devez quand même créer deux applications Mobile Engagement, une par plateforme prise en charge. Cette étape vous permet de segmenter correctement le public et d’envoyer des notifications ciblées de manière appropriée sur chaque plateforme.

    > [!IMPORTANT]
    > NuGet ne copie pas automatiquement les ressources du Kit de développement logiciel (SDK) dans votre application Windows 10 UWP. Vous devez le faire manuellement en suivant les étapes qui s’affichent (readme.txt) lorsque le package Nuget est installé.  

1. Dans le fichier `App.xaml.cs` :

    a. Ajoutez l'instruction `using` :

            using Microsoft.Azure.Engagement;

    b. Ajoutez une méthode qui initialise Engagement :

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Initialisez le kit de développement logiciel (SDK) dans la méthode **OnLaunched** :

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Insérez le code suivant dans la méthode **OnActivated** et ajoutez la méthode si elle n’est pas encore présente :

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Activation de l’analyse en temps réel
Pour commencer à envoyer des données et vérifier que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

1. Dans le fichier **MainPage.xaml.cs**, ajoutez l’instruction `using` suivante :

    using Microsoft.Azure.Engagement.Overlay;
2. Dans **MainPage**, remplacez la classe de base **Page** par **EngagementPageOverlay** :

        class MainPage : EngagementPageOverlay
3. Dans le fichier `MainPage.xaml` :

    a. Ajoutez une déclaration d'espace de noms :

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Remplacez **Page** dans le nom de balise XML par **engagement:EngagementPageOverlay**.

> [!IMPORTANT]
> Si votre page remplace la méthode `OnNavigatedTo`, veillez à appeler `base.OnNavigatedTo(e)`. Sinon, l’activité n’est pas signalée (`EngagementPage` appelle `StartActivity` à l’intérieur de sa méthode `OnNavigatedTo`). Ceci est particulièrement important dans un projet Windows Phone où le modèle par défaut dispose d’une méthode `OnNavigatedTo` .
>
> Pour les **applications universelles Windows 10**, utilisez la méthode recommandée dans la section « Méthode recommandée : surchargez vos classes » de la page [Génération de rapports avancés avec le Kit de développement logiciel (SDK) des applications Windows Universal Engagement](mobile-engagement-windows-store-advanced-reporting.md), plutôt que celle mentionnée ci-dessus.

## <a id="monitor"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Activation des notifications push et de la messagerie in-app
Mobile Engagement vous permet d’interagir et d’atteindre vos utilisateurs à l’aide de notifications push et de la messagerie dans l’application, dans le cadre d’une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement.
Les sections suivantes vous permettent de configurer votre application pour la réception.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>Activer la réception des notifications push des Services de notifications push Windows pour votre application
1. Dans le fichier `Package.appxmanifest`, sous l’onglet **Application**, sous **Notifications**, pour **Compatible toast**, sélectionnez **Oui**.

    ![][5]

### <a name="initialize-the-reach-sdk"></a>Initialiser le SDK du module Couverture
Dans `App.xaml.cs`, appelez **EngagementReach.Instance.Init(e);** dans la fonction **InitEngagement**, juste après initialisation de l’agent :

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Vous êtes prêt à envoyer un toast. Ensuite, nous vérifions que l’intégration de base est correcte.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Accorder l’accès à Mobile Engagement pour l’envoi de notifications push
1. Ouvrez le [Centre de développement Windows Store] dans votre navigateur web, connectez-vous et créez un compte si nécessaire.
2. Cliquez sur **Tableau de bord** en haut à droite, puis cliquez sur **Créer une application** dans le menu du panneau gauche.

    ![][9]
3. Créez votre application en réservant son nom.

    ![][10]
4. Une fois l’application créée, accédez à **Services -> Notifications Push** dans le menu de gauche.

    ![][11]
5. Dans la section Notifications Push, cliquez sur le lien du site **Services Windows Live** .

    ![][12]
6. Vous accédez à la section des informations d’identification Push. Assurez-vous que vous êtes dans la section **Paramètres de l’application** et copiez le **SID du package** et votre **clé secrète client**.

    ![][13]
7. Accédez aux **Paramètres** du portail Mobile Engagement, puis cliquez sur la section**Push natif** à gauche. Ensuite, cliquez sur le bouton **Modifier** pour entrer l’**identificateur de sécurité (SID) du package** et votre **clé secrète**, comme indiqué ci-dessous :

    ![][6]
8. Enfin, assurez-vous que vous avez associé votre application Visual Studio à cette application que vous avez créée dans l’App Store. Cliquez sur **Associer l’application au Windows Store** dans Visual Studio.

    ![][7]

## <a id="send"></a>Envoi d’une notification vers votre application
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Si l’application est en cours d’exécution, vous voyez s’afficher une notification dans l’application. Sinon, si l’application est fermée, vous voyez une notification toast.
Si vous voyez une notification dans l’application, mais pas une notification toast, et que vous exécutez l’application en mode débogage dans Visual Studio, accédez à **Événements de cycle de vie -> Interrompre** dans la barre d’outils pour vérifier que l’application est bien interrompue. Si vous avez cliqué sur le bouton Accueil pendant le débogage de l’application dans Visual Studio, l’application n’est pas systématiquement interrompue. Et même si la notification est affichée comme étant dans l’application, elle ne se présente pas comme une notification toast.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Centre de développement Windows Store]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
