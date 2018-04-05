---
title: Procédures de mise à niveau du Kit de développement logiciel (SDK) des applications Windows Universal
description: Procédures de mise à niveau du Kit de développement logiciel (SDK) des applications Windows Universal pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a9d6cbcdf353f7eea991c344c3efe65378abe336
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procédures de mise à niveau du Kit de développement logiciel (SDK) des applications Windows Universal
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Vous devrez peut-être suivre quelques procédures si vous avez manqué plusieurs versions du kit SDK. Par exemple, si vous migrez de la version 0.10.1 vers 0.11.0, vous devez tout d'abord suivre la procédure « Migration de 0.9.0 vers 0.10.1 », puis la procédure « Migration de 0.10.1 vers 0.11.0 ».

## <a name="from-330-to-340"></a>Migration de 3.3.0 vers 3.4.0
### <a name="test-logs"></a>Journaux des tests
Les journaux de console produits par le Kit de développement logiciel (SDK) peuvent maintenant être activés/désactivés/filtrés. Pour personnaliser ce résultat, mettez à jour la propriété `EngagementAgent.Instance.TestLogEnabled` avec une des valeurs disponibles à partir de l'énumération `EngagementTestLogLevel`, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Ressources
La superposition Reach a été améliorée. Elle fait partie des ressources du package NuGet du Kit de développement logiciel (SDK).

Lors de la mise à niveau vers la nouvelle version du Kit de développement logiciel (SDK), vous pouvez choisir de conserver ou non les fichiers existants dans le dossier de superposition de vos ressources :

* Si la superposition précédente vous convient ou que vous intégrez manuellement les éléments `WebView` , vous pouvez choisir de conserver vos fichiers existants car ils resteront opérationnels. 
* Si vous souhaitez effectuer la mise à jour vers la nouvelle superposition, remplacez simplement l’ensemble du dossier `overlay` contenant vos ressources par le nouveau dossier contenant le package du Kit de développement logiciel (SDK) (applications UWP : après la mise à niveau, vous trouverez le nouveau dossier de superposition sous %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> L’utilisation de la nouvelle superposition remplacera toutes les personnalisations apportées à la version précédente.
> 
> 

## <a name="from-320-to-330"></a>De 3.2.0 à 3.3.0
### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement logiciel (HTML, images, superposition), vous devez ensuite les sauvegarder avant de les mettre à niveau et réappliquer votre personnalisation sur les ressources mises à niveau.

## <a name="from-310-to-320"></a>De 3.1.0 à 3.2.0
### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement logiciel (HTML, images, superposition), vous devez ensuite les sauvegarder avant de les mettre à niveau et réappliquer votre personnalisation sur les ressources mises à niveau.

### <a name="webview-integration"></a>l'intégration de vue web
Certaines améliorations pour la correspondance des facteurs de forme d'appareil ont été introduites dans cette version. Assurez-vous que votre intégration de la vue Web correspond à ce qui suit :

Dans votre page XAML () :

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Et dans votre fichier .cs associé :

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>Migration de 2.0.0 vers 3.0.0
### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement logiciel (HTML, images, superposition), vous devez ensuite les sauvegarder avant de les mettre à niveau et réappliquer votre personnalisation sur les ressources mises à niveau.

## <a name="from-111-to-200"></a>Migration de 1.1.1 vers 2.0.0
La section qui suit décrit comment migrer une intégration du SDK à partir du service Capptain offert par Capptain SAS dans une application reposant sur Azure Mobile Engagement. 

> [!IMPORTANT]
> Capptain et Engagement Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous explique uniquement comment migrer l'application cliente. La migration du SDK dans l'application ne migre PAS vos données des serveurs Capptain vers les serveurs Engagement Mobile.
> 
> 

Si vous migrez à partir d'une version antérieure, consultez le site web de Capptain pour migrer tout d'abord vers 1.1.1, puis appliquez la procédure suivante.

### <a name="nuget-package"></a>Package NuGet
Remplacez **Capptain.WindowsPhone** par le package NuGet **MicrosoftAzure.MobileEngagement**.

### <a name="applying-mobile-engagement"></a>Application d'Engagement Mobile
Le SDK utilise le terme `Engagement`. Vous devez mettre à jour votre projet pour qu'il corresponde à cette modification.

Vous devez désinstaller votre package nuget Capptain actuel. Considérez que toutes vos modifications dans le dossier de ressources Capptain seront supprimées. Si vous souhaitez conserver ces fichiers, effectuez-en une copie.

Après cela, installez le nouveau package nuget Microsoft Azure Engagement sur votre projet. Vous le trouverez directement sur le [site web NuGet]. ou ici. Cette action remplace tous les fichiers de ressources utilisés par Engagement et ajoute la nouvelle DLL Engagement à vos références de projet.

Vous devez nettoyer vos références de projet en supprimant les références à la DLL Capptain. Si vous ne le faites pas, la version de Capptain génère un conflit et une erreur se produit.

Si vous avez personnalisé des ressources Capptain, copiez le contenu de vos anciens fichiers et collez-le dans les nouveaux fichiers Engagement. Notez que les fichiers xaml et cs doivent être mis à jour.

Une fois ces étapes terminées, il vous suffit de remplacer les anciennes références Capptain par les nouvelles références Engagement.

1. Tous les espaces de noms Capptain doivent être mis à jour.
   
    Avant la migration :
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Après la migration :
   
        using Microsoft.Azure.Engagement;
2. Toutes les classes Capptain qui contiennent « Capptain » doivent contenir « Engagement ».
   
    Avant la migration :
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Après la migration :
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Pour les fichiers xaml, les attributs et les espaces de noms Capptain changent également.
   
    Avant la migration :
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Après la migration :
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Modifications des pages de superposition
   
   > [!IMPORTANT]
   > La superposition change également. Son nouvel espace de noms est `Microsoft.Azure.Engagement.Overlay`. Il doit être utilisé dans les fichiers xaml et cs. En outre, `CapptainGrid` doit être nommé `EngagementGrid`, `capptain_notification_content` et `capptain_announcement_content` se nomment `engagement_notification_content` et `engagement_announcement_content`.
   > 
   > 
   
    Pour la superposition :
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Cela devient :
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Quant aux autres ressources telles que les images Capptain et les fichiers HTML, notez qu'elles ont également été renommées de façon à utiliser « Engagement ».

### <a name="project-declaration"></a>Déclaration de projet
Sur Package.appxmanifest, `File Type Associations` a été mis à jour à partir de :

* capptain\_reach\_content to engagement\_reach\_content
* capptain\_log\_file to engagement\_log\_file

### <a name="application-id--sdk-key"></a>ID de l'application / clé SDK
Engagement utilise une chaîne de connexion. Il est inutile de spécifier un ID d'application et une clé SDK avec Mobile Engagement. Il suffit de spécifier une chaîne de connexion. Vous pouvez la configurer dans votre fichier EngagementConfiguration.

La configuration d'Engagement peut être définie dans le fichier `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier :

* Votre chaîne de connexion d'application entre les balises `<connectionString>` and `<\connectionString>`.

Si vous souhaitez plutôt la spécifier au moment de l'exécution, vous pouvez appeler la méthode suivante avant l'initialisation de l'agent Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

La chaîne de connexion de votre application s’affiche sur le Portail Azure.

### <a name="items-name-change"></a>Changement de noms d'éléments
Tous les éléments nommés *capptain* ont été renommés *engagement*. De même pour *Capptain* (renommés *Engagement*).

Exemples d'éléments Capptain couramment utilisés :

* CapptainConfiguration se nomme maintenant EngagementConfiguration
* CapptainAgent se nomme maintenant EngagementAgent
* CapptainReach se nomme maintenant EngagementReach
* CapptainHttpConfig se nomme maintenant EngagementHttpConfig
* GetCapptainPageName se nomme maintenant GetEngagementPageName

Notez que ce changement affecte également les méthodes substituées.

