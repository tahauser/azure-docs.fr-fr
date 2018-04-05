---
title: Intégration du Kit de développement logiciel du module Couverture des applications Windows Universal
description: Intégration du module Couverture d’Azure Mobile Engagement avec des applications Windows Universal
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8472af2ac72a1976871970963851be775b19859b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Intégration du Kit de développement logiciel du module Couverture des applications Windows Universal
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Vous devez suivre la procédure d'intégration décrite dans la rubrique [Intégration du Kit de développement logiciel Windows Universal Engagement](mobile-engagement-windows-store-integrate-engagement.md) avant de suivre ce guide.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Intégration du Kit de développement logiciel (SDK) du module Couverture d’Engagement à votre projet Windows Universal
Vous n'avez rien à ajouter. `EngagementReach` se trouvent déjà dans votre projet.

> [!TIP]
> Vous pouvez personnaliser les images situées dans le dossier `Resources` de votre projet, en particulier l'icône de marque (par défaut, il s'agit de l'icône d'Engagement). Sur les applications Universal, vous pouvez également déplacer le dossier `Resources` sur votre projet partagé pour partager son contenu entre les applications, mais vous devrez conserver le fichier `Resources\EngagementConfiguration.xml` à son emplacement par défaut, car il est dépendant de la plate-forme.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Activer le service de notification Windows
### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x et Windows Phone 8.1 uniquement
Pour utiliser le **service de notification Windows** (WNS) dans votre fichier `Package.appxmanifest` sur `Application UI`, cliquez sur `All Image Assets` dans la zone de gauche. À droite de la zone dans `Notifications`, modifiez `toast capable` en remplaçant `(not set)` par `(Yes)`.

### <a name="all-platforms"></a>Toutes les plateformes
Vous devez synchroniser votre application avec votre compte Microsoft et la plateforme Engagement. Pour ce faire, vous devez créer un compte ou vous connecter au [Centre de développement Windows](https://dev.windows.com). Ensuite, créez une application et recherchez le SID et la clé secrète. Sur le serveur frontal Engagement, accédez au paramètre de votre application dans `native push` , puis collez vos informations d'identification. Ensuite, cliquez avec le bouton droit sur votre projet, puis sélectionnez `store` et `Associate App with the Store...`. Vous devez simplement sélectionner l'application créée avant de la synchroniser.

## <a name="initialize-the-engagement-reach-sdk"></a>Initialiser le SDK du module Couverture d'Engagement
Modifiez le fichier `App.xaml.cs` :

* Insérez `EngagementReach.Instance.Init` juste après `EngagementAgent.Instance.Init` dans votre méthode `InitEngagement` :
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` est exécuté dans un thread dédié. Vous n'avez pas à le faire vous-même.

> [!NOTE]
> Si vous utilisez des notifications push ailleurs dans votre application, vous devez [partager votre canal push](#push-channel-sharing) avec Engagement Reach.
> 
> 

## <a name="integration"></a>Intégration
Engagement fournit deux méthodes pour ajouter des bannières Reach et des vues interstitielles pour les annonces et les sondages dans votre application : l'intégration de superposition et l'intégration manuelle de vues web. Vous ne devriez pas combiner ces deux approches sur la même page.

Le choix entre ces deux intégrations peut se résumer ainsi :

* Vous pouvez choisir l'intégration de superposition si vos pages héritent déjà de l'Agent `EngagementPage` ; il suffit de remplacer `EngagementPage` par `EngagementPageOverlay` et `xmlns:engagement="using:Microsoft.Azure.Engagement"` par `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` dans vos pages.
* Vous pouvez choisir l'intégration manuelle de vues web si vous souhaitez placer avec précision l'interface utilisateur Reach dans vos pages, ou si vous ne voulez pas ajouter un autre niveau d'héritage à vos pages. 

### <a name="overlay-integration"></a>Intégration de superposition
La superposition d'Engagement ajoute dynamiquement les éléments de l'interface utilisateur pour afficher des campagnes Reach dans votre page. Si la superposition ne convient pas à votre disposition, vous devriez opter pour l'intégration manuelle des vues web.

Dans votre fichier .xaml, remplacez la référence `EngagementPage` par `EngagementPageOverlay`

* Ajoutez une déclaration d'espace de noms :
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Remplacez `engagement:EngagementPage` par `engagement:EngagementPageOverlay` :

**Avec EngagementPage :**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**Avec EngagementPageOverlay :**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Puis, dans le fichier .cs, ajoutez à votre page le mot-clé `EngagementPageOverlay` au lieu de `EngagementPage`, puis importez `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Remplacez `EngagementPage` par `EngagementPageOverlay` :

**Avec EngagementPage :**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Avec EngagementPageOverlay :**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


La superposition Engagement ajoute un élément `Grid` en haut de votre page, composé de votre disposition et de des deux éléments `WebView` : un pour la bannière et l'autre pour la vue interstitielle.

Vous pouvez personnaliser les éléments de superposition directement dans le fichier `EngagementPageOverlay.cs`.

### <a name="web-views-manual-integration"></a>Intégration manuelle de vues web
Reach recherchera dans vos pages les deux éléments `WebView` permettant d'afficher la bannière et la vue interstitielle. Il vous suffit donc d'ajouter ces deux éléments `WebView` à vos pages, comme dans cet exemple :

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


Dans cet exemple, les éléments `WebView` sont étirés pour s'ajuster à leur conteneur, qui les redimensionne automatiquement en cas de rotation de l'écran ou de changement de taille de fenêtre.

> [!WARNING]
> Il est important de conserver les mêmes noms `engagement_notification_content` et `engagement_announcement_content` pour les éléments `WebView`. Reach les identifie par leur nom. 
> 
> 

## <a name="handle-datapush-optional"></a>Gérer les Push de données (facultatif)
Si vous voulez que votre application puisse recevoir les Push de données du module Couverture, vous devez implémenter deux événements de la classe EngagementReach :

Dans le constructeur App() du fichier App.xaml.cs, ajoutez :

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Vous pouvez voir que le rappel de chaque méthode renvoie un booléen. Engagement envoie un feedback à son serveur principal après la répartition des Push de données. Si le rappel renvoie la valeur false, le retour `exit` sera envoyé. Dans le cas contraire, il s'agira du retour `action`. Si aucun rappel n'est défini pour les événements, le retour `drop` sera envoyé à Engagement.

> [!WARNING]
> Engagement ne peut pas recevoir plusieurs feedbacks pour un Push de données. Si vous prévoyez de définir plusieurs gestionnaires pour un événement, sachez que le feedback correspondra au dernier envoyé. Dans ce cas, nous vous recommandons de toujours retourner la même valeur afin d'éviter un feedback prêtant à confusion sur le serveur frontal.
> 
> 

## <a name="customize-ui-optional"></a>Personnaliser l'interface utilisateur (facultatif)
### <a name="first-step"></a>Première étape
Il vous est possible de personnaliser l'interface utilisateur du module Couverture.

Pour cela, vous devez créer une sous-classe de la classe `EngagementReachHandler` .

**Exemple de code :**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Ensuite, définissez le contenu du champ `EngagementReach.Instance.Handler` à l'aide de votre objet personnalisé dans la classe `App.xaml.cs` de la méthode `App()`.

**Exemple de code :**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> Par défaut, Engagement utilise sa propre implémentation de `EngagementReachHandler`.
> Il n'est pas nécessaire de créer votre propre implémentation. Toutefois, si vous le faites, vous n'aurez pas à remplacer chaque méthode. Le comportement par défaut consiste à sélectionner l'objet de base Engagement.
> 
> 

### <a name="web-view"></a>Vue web
Par défaut, le module Couverture utilise les ressources intégrées du DLL pour afficher les notifications et les pages.

Pour une personnalisation maximale, nous utilisons uniquement les vues web. Si vous voulez personnaliser les dispositions, remplacez directement les fichiers de ressources `EngagementAnnouncement.html` et `EngagementNotification.html`. Engagement a besoin de tous les codes de `<body></body>` pour être correctement exécuté. Toutefois, vous pouvez ajouter des balises en dehors de `engagement_webview_area`.

Vous pouvez décider d'utiliser vos propres ressources.

Vous pouvez remplacer les méthodes `EngagementReachHandler` de votre sous-classe pour indiquer à Engagement d'utiliser vos dispositions. Veillez, toutefois, à intégrer le mécanisme Engagement :

**Exemple de code :**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Par défaut, AnnouncementHTML est `ms-appx-web:///Resources/EngagementAnnouncement.html`. Il correspond au fichier HTML qui conçoit le contenu du message Push (annonce texte, annonce web et annonce de sondage). AnnouncementName est `engagement_announcement_content`. Il s'agit du nom de la conception de vue web de la page XAML.

NotificationHTML est `ms-appx-web:///Resources/EngagementNotification.html`. Il s'agit du fichier HTML qui conçoit la notification d'un message Push. NotificationName est `engagement_notification_content`. Il s'agit du nom de la conception de vue web de la page XAML.

### <a name="customization"></a>Personnalisation
Vous pouvez personnaliser comme bon vous semble les vues web des notifications et des annonces, du moment que vous conservez l'objet Engagement. Veillez à décrire l'objet webview trois fois. La première fois dans le fichier .xaml, la deuxième fois dans la méthode « setwebview() » du fichier .cs, et la troisième fois dans le fichier .html.

* Dans le fichier .xaml, vous décrivez le composant webview de disposition graphique.
* Dans le fichier .cs, vous pouvez définir "setwebview()" où vous pourrez définir la dimension des deux vues web (notification, annonce). C'est très efficace quand l'application est redimensionnée.
* Dans le fichier .html Engagement, nous décrivons le contenu de la vue web, sa conception et la position des éléments les uns par rapport aux autres.

### <a name="launch-message"></a>Lancer un message
Quand un utilisateur clique sur une notification système (un toast), Engagement lance l'application, charge le contenu des messages Push et affiche la page de la campagne correspondante.

Il y a un délai entre le lancement de l'application et l'affichage de la page (qui dépend de la vitesse de votre réseau).

Pour indiquer à l'utilisateur qu'un chargement est en cours, vous devez fournir une indication visuelle, telle qu'une barre ou un indicateur de progression. Engagement ne peut pas gérer cela lui-même. Toutefois, il fournit plusieurs gestionnaires à cet effet.

Pour implémenter le rappel, dans le fichier App.xaml.cs, dans "Public App(){}", ajoutez ce qui suit :

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Vous pouvez définir le rappel dans la méthode « Public App(){} » du fichier `App.xaml.cs`, de préférence avant l'appel `EngagementReach.Instance.Init()`.

> [!TIP]
> Chaque gestionnaire est appelé par le thread d'interface utilisateur. Vous n'avez pas de souci à vous faire quand vous utilisez un MessageBox ou autre objet d'interface utilisateur.
> 
> 

## <a id="push-channel-sharing"></a> Partage de canal Push
Si vous utilisez des notifications Push à d'autres fins dans votre application, alors vous devez utiliser la fonctionnalité de partage de canal Push du Kit de développement logiciel (SDK) Engagement. Cela évite les notifications Push manquées.

* Vous pouvez fournir votre propre canal Push à l'initialisation d'Engagement Reach. Le Kit de développement logiciel (SDK) l'utilisera au lieu d'en demander un nouveau.

Mettez à jour l'initialisation d'Engagement Reach avec votre canal Push dans la méthode `InitEngagement` à partir du fichier `App.xaml.cs` :

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* Si vous souhaitez simplement utiliser le canal Push après l'initialisation Reach, vous pouvez également définir un rappel sur Engagement Reach pour récupérer le canal Push une fois celui-ci créé par le Kit de développement logiciel (SDK).

Définissez le rappel en tout lieu **après** l'initialisation Reach :

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Astuce concernant les schémas personnalisés
Il est possible d'utiliser des schémas personnalisés. Vous pouvez envoyer un autre type d'URI depuis le serveur frontal Engagement, destiné à être utilisé dans l'application Engagement. Les schémas par défaut comme `http, ftp, ...` sont gérés par Windows. Une fenêtre vous informera si aucune application par défaut n'est installée sur l'appareil. Vous pouvez également créer un schéma personnalisé pour votre application.

Le moyen le plus simple de définir un schéma personnalisé dans votre application est d'ouvrir `Package.appxmanifest` et d'accéder au panneau `Declarations`. Sélectionnez `Protocol` dans la case de défilement Déclarations disponibles, puis ajoutez-le. Remplacez le contenu du champ `Name` par le nom du nouveau protocole.

Pour utiliser ce protocole, modifiez `App.xaml.cs` à l'aide de la méthode `OnActivated`, et n'oubliez pas d'initialiser Engagement ici également :

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

