---
title: "Intégration du Kit de développement logiciel du module Engagement des applications universelles Windows"
description: "Intégration du module Azure Mobile Engagement avec des applications universelles Windows"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 56a382a348609df1d1d308aeac39f47ca82ac4c8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Intégration du Kit de développement logiciel du module Engagement des applications universelles Windows
> [!div class="op_single_selector"]
> * [Windows universel](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Cette procédure décrit comment activer les fonctions d'analyse et de surveillance d'Engagement dans votre application universelle Windows de la manière la plus simple possible.

Les étapes suivantes permettent d'activer la génération des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques. Pour calculer d’autres statistiques qui dépendent de l’application, comme les événements, les erreurs et les travaux, vous devez générer manuellement les journaux correspondants à l’aide de l’API Engagement. Consultez [Comment utiliser l’API de balisage avancée Mobile Engagement dans votre application universelle Windows](mobile-engagement-windows-store-use-engagement-api.md).

## <a name="supported-versions"></a>Versions prises en charge
Le Kit de développement logiciel (SDK) Engagement Mobile pour les applications universelles Windows peut uniquement être intégré aux applications Windows Runtime et Universal Windows Platform ciblant :

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10 (gammes mobiles et de bureau)

> [!NOTE]
> Si vous ciblez Windows Phone Silverlight, consultez la [procédure d'intégration Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Installation du Kit de développement d'applications universelles Mobile Engagement
### <a name="all-platforms"></a>Toutes les plateformes
Le Kit de développement Mobile Engagement pour l’application universelle Windows est disponible comme package Nuget appelé *MicrosoftAzure.MobileEngagement*. Vous pouvez l'installer à partir du gestionnaire de package Nuget Visual Studio.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x et Windows Phone 8.1
NuGet déploie automatiquement les ressources du Kit de développement logiciel (SDK) dans le dossier `Resources` à la racine de votre projet d'application.

### <a name="windows-10-universal-windows-platform-applications"></a>Applications de la plateforme Windows universelle Windows 10
NuGet ne déploie pas encore automatiquement les ressources du Kit de développement logiciel (SDK) dans votre application UWP. Vous devez le faire manuellement jusqu'à ce que le déploiement de ressources soit réintroduit dans NuGet :

1. Ouvrez votre Explorateur de fichiers.
2. Accédez à l’emplacement suivant (**x.x.x** est la version d’Engagement que vous installez) : *%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3. Faites glisser et déposez le dossier **Ressources** à partir de l'Explorateur de fichiers vers la racine de votre projet dans Visual Studio.
4. Dans Visual Studio, sélectionnez votre projet et activez l’icône **Afficher tous les fichiers** dans la partie supérieure de **l’Explorateur de solutions**.
5. Certains fichiers ne sont pas inclus dans le projet. Pour tous les importer à la fois, cliquez avec le bouton droit sur le dossier **Ressources**, sélectionnez **Exclure du projet**, puis cliquez de nouveau avec le bouton droit sur le dossier **Ressources**, sélectionnez **Inclure dans le projet** pour inclure à nouveau la totalité du dossier. Tous les fichiers du dossier **Ressources** sont désormais inclus dans votre projet.

## <a name="add-the-capabilities"></a>Ajouter les fonctionnalités
Pour fonctionner correctement, le SDK Engagement fait appel à certaines fonctionnalités du SDK Windows.

Ouvrez votre fichier `Package.appxmanifest` et vérifiez que les fonctionnalités suivantes sont déclarées :

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Initialiser le SDK Engagement
### <a name="engagement-configuration"></a>Configuration d'Engagement
La configuration d'Engagement est centralisée dans le fichier `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier :

* Votre chaîne de connexion d'application entre les balises `<connectionString>` and `<\connectionString>`.

Si vous souhaitez plutôt la spécifier au moment de l'exécution, vous pouvez appeler la méthode suivante avant l'initialisation de l'agent Engagement :

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

La chaîne de connexion de votre application est affichée sur le portail Azure.

### <a name="engagement-initialization"></a>Initialisation d'Engagement
Quand vous créez un projet, un fichier `App.xaml.cs` est généré. Cette classe hérite de `Application` et contient de nombreuses méthodes importantes. Son rôle consiste également à initialiser le SDK Engagement.

Modifiez le `App.xaml.cs`:

* Ajoutez à vos instructions `using` :
  
      using Microsoft.Azure.Engagement;
* Définissez une méthode pour partager l'initialisation Engagement une fois pour tous les appels :
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* Appelez `InitEngagement` dans la méthode `OnLaunched` :
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* Si votre application est lancée à partir d'un schéma personnalisé, d'une autre application ou de la ligne de commande, la méthode `OnActivated` est appelée. Vous devez également initier le Kit de développement logiciel (SDK) Engagement quand votre application est activée. Pour cela, remplacez la méthode `OnActivated` :
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> Nous vous déconseillons fortement d'ajouter l'initialisation d'Engagement à un autre endroit de votre application.
> 
> 

## <a name="basic-reporting"></a>Génération de rapports de base
### <a name="recommended-method-overload-your-page-classes"></a>Méthode recommandée : surchargez vos classes `Page`
Pour activer la génération de tous les journaux requis par Engagement pour calculer les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques, vous pouvez simplement configurer toutes vos sous-classes `Page` de manière à ce qu'elles héritent des classes `EngagementPage`.

Voici un exemple qui montre comment procéder pour une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

#### <a name="c-source-file"></a>Fichier source C#
Modifiez le fichier `.xaml.cs` de votre page :

* Ajoutez à vos instructions `using` :
  
      using Microsoft.Azure.Engagement;
* Remplacez `Page` par `EngagementPage` :

**Sans Engagement :**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Avec Engagement :**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!IMPORTANT]
> Si votre page remplace la méthode `OnNavigatedTo`, veillez à appeler `base.OnNavigatedTo(e)`. Sinon, l’activité ne sera pas signalée (la `EngagementPage` appelle `StartActivity` à l’intérieur de sa méthode `OnNavigatedTo`).
> 
> 

#### <a name="xaml-file"></a>Fichier XAML
Modifiez le fichier `.xaml` de votre page :

* Ajoutez une déclaration d'espace de noms :
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Remplacez `Page` par `engagement:EngagementPage` :

**Sans Engagement :**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Avec Engagement :**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Remplacer le comportement par défaut
Par défaut, le nom de classe de la page est signalé comme le nom de l'activité, sans informations supplémentaires. Si la classe utilise le suffixe « Page », Engagement le supprime également.

Si vous souhaitez remplacer le comportement par défaut associé au nom, ajoutez simplement ceci à votre code :

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Si vous souhaitez signaler des informations supplémentaires avec votre activité, vous pouvez ajouter ceci à votre code :

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Ces méthodes sont appelées depuis la méthode `OnNavigatedTo` de votre page.

### <a name="alternate-method-call-startactivity-manually"></a>Autre méthode : appeler `StartActivity()` manuellement
Si vous ne pouvez pas ou ne souhaitez pas surcharger vos classes `Page`, vous pouvez démarrer vos activités en appelant directement les méthodes `EngagementAgent`.

Nous vous recommandons d'appeler `StartActivity` à l'intérieur de la méthode `OnNavigatedTo` de votre Page.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Assurez-vous de terminer votre session correctement.
> 
> Le Kit de développement logiciel Windows Universal appelle automatiquement la méthode `EndActivity` quand l'application est fermée. Par conséquent, il est **FORTEMENT** recommandé d’appeler la méthode `StartActivity` chaque fois que l’activité de l’utilisateur change et de ne **JAMAIS** appeler la méthode `EndActivity`. Cette dernière envoie au serveur Engagement une notification indiquant que l’utilisateur actuel doit quitter l’application, ce qui aura un impact sur tous les journaux d’application.
> 
> 

## <a name="advanced-reporting"></a>Génération de rapports avancés
Vous pouvez éventuellement signaler les événements, erreurs et tâches spécifiques à l'application. Pour cela, utilisez les autres méthodes disponibles dans la classe `EngagementAgent`. L'API Engagement permet d'utiliser toutes les fonctionnalités avancées d'Engagement.

Pour plus d'informations, consultez [Utilisation de l'API de marquage avancée Mobile Engagement dans votre application Windows Phone](mobile-engagement-windows-store-use-engagement-api.md).

## <a name="advanced-configuration"></a>Configuration avancée
### <a name="disable-automatic-crash-reporting"></a>Désactiver le signalement automatique des incidents
Vous pouvez désactiver la fonctionnalité de signalement automatique des incidents d'Engagement. Dans ce cas, si une exception non gérée se produit, Engagement ne fait rien.

> [!WARNING]
> Si vous désactivez cette fonctionnalité et qu'un incident non géré se produit dans votre application, sachez qu'Engagement n'envoie pas l'incident **ET** qu'il ne ferme ni la session ni les tâches.
> 
> 

Pour désactiver le signalement automatique des incidents, il vous suffit de personnaliser votre configuration en fonction de la façon dont vous l'avez déclarée :

#### <a name="from-engagementconfigurationxml-file"></a>Dans le fichier `EngagementConfiguration.xml`
Affectez au signalement des incidents la valeur `false` entre les balises `<reportCrash>` et `</reportCrash>`.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Dans l'objet `EngagementConfiguration` au moment l'exécution
Affectez au signalement des incidents la valeur false à l'aide de votre objet EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Mode rafale
Par défaut, le service Engagement génère des journaux en temps réel. Si votre application génère très fréquemment des journaux, il est préférable de les mettre en mémoire tampon et de les générer tous en même temps à intervalles réguliers (« mode rafale »).

Pour cela, appelez la méthode :

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argument est une valeur en **millisecondes**. Si vous souhaitez réactiver la génération de journaux en temps réel, vous pouvez appeler à tout moment la méthode sans aucun paramètre ou avec la valeur 0.

Le mode rafale accroît légèrement l'autonomie de la batterie, mais il affecte aussi Engagement Monitor. En effet, la durée des sessions et des tâches est arrondie au seuil de rafale (les sessions et les tâches plus courtes que le seuil de rafale ne sont donc pas visibles). Il est recommandé d'utiliser un seuil de rafale inférieur à 30 000 (30 s). Vous devez savoir que les journaux enregistrés sont limités à 300 entrées. Si l'envoi est trop long, vous risquez de perdre certains journaux.

> [!WARNING]
> Vous ne pouvez pas configurer un seuil de rafale inférieur à 1 s. Sinon, le SDK affiche une trace avec l'erreur et rétablit automatiquement la valeur par défaut, c'est-à-dire 0 s. Le SDK génère alors les journaux en temps réel.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

