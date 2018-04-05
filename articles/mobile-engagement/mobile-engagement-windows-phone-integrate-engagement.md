---
title: Intégration du Kit de développement logiciel (SDK) d’Engagement Windows Phone Silverlight
description: Intégration du module Azure Mobile Engagement avec des applications Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 447fea8d-f4e3-4ad4-8ec0-8e3cf1ad3ab0
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f041f19ceff366a34b1761bfdca12242f4bdf9b8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-engagement-sdk-integration"></a>Intégration du Kit de développement logiciel (SDK) d’Engagement Windows Phone Silverlight
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!div class="op_single_selector"]
> * [Windows Universel](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Cette procédure décrit la méthode la plus simple pour activer les fonctions d’analyse et de surveillance d’Azure Mobile Engagement dans votre application Windows Phone Silverlight.

Les étapes suivantes permettent d'activer la génération des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques. La génération des journaux nécessaires au calcul d’autres statistiques, telles que les événements, les erreurs et les travaux, doit être effectuée manuellement à l’aide de l’API Engagement (consultez [Utilisation avancée de l’API de marquage Mobile Engagement dans votre application Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) ci-dessous), dans la mesure où ces statistiques dépendent de l’application.

## <a name="supported-versions"></a>Versions prises en charge
Le Kit de développement logiciel Mobile Engagement pour Windows Silverlight peut uniquement être intégré dans les applications ciblant :

* Windows Phone 8.0
* Windows Phone 8.1 Silverlight

> [!NOTE]
> Si vous ciblez Windows Phone 8.1 (non-Silverlight), consultez la rubrique [Procédure d’intégration de Windows Universal](mobile-engagement-windows-store-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-silverlight-sdk"></a>Installer le Kit de développement logiciel Mobile Engagement Silverlight
Le Kit de développement Mobile Engagement pour Windows Silverlight est disponible comme package Nuget appelé *MicrosoftAzure.MobileEngagement*. Vous pouvez l'installer à partir du gestionnaire de package Nuget Visual Studio. 

## <a name="add-the-capabilities"></a>Ajouter les fonctionnalités
Le SDK Engagement a besoin de certaines fonctionnalités du SDK Windows Phone Silverlight pour pouvoir fonctionner correctement.

Ouvrez votre fichier `WMAppManifest.xml` et assurez-vous que les fonctionnalités suivantes sont indiquées dans le volet `Capabilities` :

* `ID_CAP_NETWORKING`
* `ID_CAP_IDENTITY_DEVICE`

## <a name="initialize-the-engagement-sdk"></a>Initialiser le SDK Engagement
### <a name="engagement-configuration"></a>Configuration d'Engagement
La configuration d'Engagement est centralisée dans le fichier `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier :

* Votre chaîne de connexion d'application entre les balises `<connectionString>` and `<\connectionString>`.

Si vous souhaitez plutôt la spécifier au moment de l'exécution, vous pouvez appeler la méthode suivante avant l'initialisation de l'agent Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

La chaîne de connexion de votre application s’affiche sur le Portail Azure.

### <a name="engagement-initialization"></a>Initialisation d'Engagement
Quand vous créez un projet, un fichier `App.xaml.cs` est généré. Cette classe hérite de `Application` et contient de nombreuses méthodes importantes. Son rôle consiste également à initialiser le SDK Engagement.

Modifiez le fichier `App.xaml.cs`:

* Ajoutez à vos instructions `using` :
  
      using Microsoft.Azure.Engagement;
* Insérez `EngagementAgent.Instance.Init` dans la méthode `Application_Launching` :
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
        EngagementAgent.Instance.Init();
      }
* Insérez `EngagementAgent.Instance.OnActivated` dans la méthode `Application_Activated` :
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
      }

> [!WARNING]
> Nous vous déconseillons fortement d'ajouter l'initialisation d'Engagement à un autre endroit de votre application. Toutefois, sachez que la méthode `EngagementAgent.Instance.Init` s'exécute sur un thread dédié et non sur le thread d'interface utilisateur.
> 
> 

## <a name="basic-reporting"></a>Génération de rapports de base
### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Méthode recommandée : surchargez vos classes `PhoneApplicationPage`
Pour activer la génération de tous les journaux requis par Engagement pour calculer les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques, vous pouvez simplement configurer toutes vos sous-classes `PhoneApplicationPage` de manière à ce qu'elles héritent des classes `EngagementPage`.

Voici un exemple qui montre comment procéder pour une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

#### <a name="c-source-file"></a>Fichier source C#
Modifiez le fichier `.xaml.cs` de votre page :

* Ajoutez à vos instructions `using` :
  
      using Microsoft.Azure.Engagement;
* Remplacez `PhoneApplicationPage` par `EngagementPage` :

**Sans Engagement :**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Avec Engagement :**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!WARNING]
> Si votre page hérite de la méthode `OnNavigatedTo`, veillez à laisser l'appel `base.OnNavigatedTo(e)`. Sinon, l'activité ne sera pas signalée. En effet, l'appel `EngagementPage` invoque `StartActivity` à l'intérieur de la méthode `OnNavigatedTo`.
> 
> 

#### <a name="xaml-file"></a>Fichier XAML
Modifiez le fichier `.xaml` de votre page :

* Ajoutez à vos déclarations d'espaces de noms :
  
      xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
* Remplacez `phone:PhoneApplicationPage` par `engagement:EngagementPage` :

**Sans Engagement :**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Avec Engagement :**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">

            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Remplacement du comportement par défaut
Par défaut, le nom de classe de la page est signalé comme le nom de l'activité, sans informations supplémentaires. Si la classe utilise le suffixe « Page », Engagement le supprime également.

Si vous souhaitez remplacer le comportement par défaut pour le nom, ajoutez simplement ceci à votre code :

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
Si vous ne pouvez pas ou ne souhaitez pas surcharger vos classes `PhoneApplicationPage`, vous pouvez démarrer vos activités en appelant directement les méthodes `EngagementAgent`.

Nous vous recommandons d’appeler `StartActivity` à l’intérieur de la méthode `OnNavigatedTo` de votre PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [!IMPORTANT]
> Assurez-vous de terminer votre session correctement.
> 
> Le Kit de développement logiciel (SDK) appelle automatiquement la méthode `EndActivity` à la fermeture de l'application. Par conséquent, il est **FORTEMENT** recommandé d’appeler la méthode `StartActivity` chaque fois que l’activité de l’utilisateur change et de ne **JAMAIS** appeler la méthode `EndActivity`. Cette méthode envoie un message au serveur Engagement indiquant que l'utilisateur actuel a quitté l'application et cela affecte tous les journaux d'application.
> 
> 

## <a name="advanced-reporting"></a>Génération de rapports avancés
Vous pouvez éventuellement signaler les événements, erreurs et tâches spécifiques à l'application. Pour cela, utilisez les autres méthodes disponibles dans la classe `EngagementAgent`. L'API Engagement permet d'utiliser toutes les fonctionnalités avancées d'Engagement.

Pour plus d'informations, consultez la rubrique [Utilisation de l'API de marquage avancée Mobile Engagement dans votre application Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md).

## <a name="advanced-configuration"></a>Configuration avancée
### <a name="disable-automatic-crash-reporting"></a>Désactiver le signalement automatique des incidents
Vous pouvez désactiver la fonctionnalité de signalement automatique des incidents d'Engagement. Dans ce cas, si une exception non gérée se produit, Engagement ne fait rien.

> [!WARNING]
> Si vous envisagez de désactiver cette fonctionnalité, sachez que si un incident non pris en charge se produit dans votre application, Engagement n'enverra pas l'incident **ET** ne fermera ni la session ni les tâches.
> 
> 

Pour désactiver le signalement automatique d'incident, il suffit de personnaliser votre configuration en fonction de la façon dont vous l'avez déclaré :

#### <a name="from-engagementconfigurationxml-file"></a>Dans le fichier `EngagementConfiguration.xml`
Affectez au signalement des incidents la valeur `false` entre les balises `<reportCrash>` et `</reportCrash>`.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Dans l'objet `EngagementConfiguration` au moment l'exécution
Affectez au signalement des incidents la valeur false à l'aide de votre objet EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Mode rafale
Par défaut, le service Engagement génère des journaux en temps réel. Si votre application génère très fréquemment des journaux, il est préférable de les mettre en mémoire tampon et de les générer tous en même temps à intervalles réguliers (« mode rafale »).

Pour cela, appelez la méthode :

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L'argument est une valeur en **millisecondes**. Si vous souhaitez réactiver la génération de journaux en temps réel, vous pouvez appeler à tout moment la méthode sans aucun paramètre ou avec la valeur 0.

Le mode rafale accroît légèrement l'autonomie de la batterie, mais il affecte aussi Engagement Monitor. En effet, la durée des sessions et des tâches est arrondie au seuil de rafale (les sessions et les tâches plus courtes que le seuil de rafale ne sont donc pas visibles). Il est recommandé d'utiliser un seuil de rafale inférieur à 30 000 (30 s). Vous devez savoir que les journaux enregistrés sont limités à 300 entrées. Si l'envoi est trop long, vous risquez de perdre certains journaux.

> [!WARNING]
> Il n'est pas possible de configurer un seuil de rafale inférieur à une seconde. Si vous essayez de le faire, le SDK affichera une erreur et réinitialisera automatiquement la valeur par défaut, c'est-à-dire zéro seconde. Le SDK génère alors les journaux en temps réel.
> 
> 

