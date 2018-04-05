---
title: Génération de rapports avancés pour des applications Windows Universal avec Mobile Apps Engagement
description: Intégration du module Azure Mobile Engagement avec des applications universelles Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 88d28bbc32179698147ab7516cc414fd23ed4bd6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Génération de rapports avancés avec le Kit de développement logiciel (SDK) des applications Windows Universal Engagement
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!div class="op_single_selector"]
> * [Windows universel](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Cette rubrique décrit des scénarios de génération de rapports supplémentaires dans votre application Windows Universal. Ces scénarios incluent des options que vous pouvez choisir d’intégrer à l’application créée dans le didacticiel [Prise en main](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Avant de commencer ce didacticiel, vous devez suivre le didacticiel [Prise en main](mobile-engagement-windows-store-dotnet-get-started.md) , qui est délibérément direct et simple. Ce didacticiel présente les options supplémentaires vous pouvez choisir.

## <a name="specifying-engagement-configuration-at-runtime"></a>Spécification de la configuration d’Engagement lors de l’exécution
La configuration d’Engagement est centralisée dans le fichier `Resources\EngagementConfiguration.xml` de votre projet, c’est-à-dire l’endroit où il a été spécifié dans la rubrique [Mise en route](mobile-engagement-windows-store-dotnet-get-started.md) .

Mais vous pouvez également le spécifier lors de l'exécution en appelant la méthode suivante avant l'initialisation de l'agent Engagement :

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Méthode recommandée : surchargez vos classes `Page`
Pour activer la génération de tous les journaux requis par Engagement pour calculer les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques, configurez toutes vos sous-classes `Page` de manière à ce qu'elles héritent des classes `EngagementPage`.

Voici un exemple appliqué à une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

### <a name="c-source-file"></a>Fichier source C#
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
> Si votre page remplace la méthode `OnNavigatedTo`, veillez à appeler `base.OnNavigatedTo(e)`. Sinon, l'activité ne sera pas signalée (la `EngagementPage` appelle `StartActivity` à l'intérieur de sa méthode `OnNavigatedTo`).
> 
> 

### <a name="xaml-file"></a>Fichier XAML
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

### <a name="override-the-default-behaviour"></a>Remplacer le comportement par défaut
Par défaut, le nom de classe de la page est signalé comme le nom de l'activité, sans informations supplémentaires. Si la classe utilise le suffixe « Page », Engagement le supprime également.

Pour remplacer le comportement par défaut pour le nom, ajoutez ce code :

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Pour signaler des informations supplémentaires avec votre activité, ajoutez ce code :

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Ces méthodes sont appelées depuis la méthode `OnNavigatedTo` de votre page.

### <a name="alternate-method-call-startactivity-manually"></a>Autre méthode : appeler `StartActivity()` manuellement
Si vous ne pouvez pas ou ne souhaitez pas surcharger vos classes `Page`, vous pouvez démarrer vos activités en appelant directement les méthodes `EngagementAgent`.

Nous vous recommandons d'appeler `StartActivity` à l'intérieur de la méthode `OnNavigatedTo` de votre page.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Assurez-vous de terminer votre session correctement.
> 
> Le Kit de développement logiciel Windows Universal appelle automatiquement la méthode `EndActivity` quand l'application est fermée. Par conséquent, il est **FORTEMENT** recommandé d’appeler la méthode `StartActivity` chaque fois que l’activité de l’utilisateur change et de ne **JAMAIS** appeler la méthode `EndActivity`. Cette méthode informe le serveur Engagement que l'utilisateur actuel a quitté l'application, ce qui affectera tous les journaux d'application.
> 
> 

## <a name="advanced-reporting"></a>Génération de rapports avancés
Vous pouvez éventuellement signaler les événements, erreurs et tâches spécifiques à l'application. Pour cela, utilisez les autres méthodes disponibles dans la classe `EngagementAgent`. L'API Engagement permet d'utiliser toutes les fonctionnalités avancées d'Engagement.

Pour plus d'informations, consultez [Utilisation de l'API de marquage avancée Mobile Engagement dans votre application Windows Phone](mobile-engagement-windows-store-use-engagement-api.md).

