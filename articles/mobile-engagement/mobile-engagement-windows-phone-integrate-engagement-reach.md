---
title: Intégration du Kit de développement logiciel (SDK) du module Couverture Windows Phone Silverlight
description: Intégration du module Couverture d’Azure Mobile Engagement avec des applications Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 34e5ce414ebf72fbecef6c921e57128e2658c921
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Intégration du Kit de développement logiciel (SDK) du module Couverture Windows Phone Silverlight
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Vous devez suivre la procédure d'intégration décrite dans la rubrique [Intégration du Kit de développement logiciel d’Engagement Windows Phone Silverlight  Engagement](mobile-engagement-windows-phone-integrate-engagement.md) avant de suivre ce guide.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Intégration du SDK du module Couverture d'Engagement dans votre projet Windows Phone Silverlight
Vous n'avez rien à ajouter. `EngagementReach` se trouvent déjà dans votre projet.

> [!TIP]
> Vous pouvez personnaliser les images situées dans le dossier `Resources` de votre projet, en particulier l'icône de marque (par défaut, il s'agit de l'icône d'Engagement).
> 
> 

## <a name="add-the-capabilities"></a>Ajouter les fonctionnalités
Le Kit de développement logiciel (SDK) du module Couverture d'Engagement nécessite l'ajout de certaines fonctionnalités.

Ouvrez votre fichier `WMAppManifest.xml` et vérifiez que les fonctionnalités suivantes sont déclarées :

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

Le premier est utilisé par le service MPNS pour permettre l'affichage de notification toast. L'autre est utilisé pour intégrer une tâche du navigateur dans le Kit de développement logiciel.

Modifiez le fichier `WMAppManifest.xml` et ajoutez-le à l'intérieur de la balise `<Capabilities />` :

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Activation du service de notifications Push Microsoft
Pour utiliser le **service de notifications Push Microsoft** (appelé MPNS), votre fichier `WMAppManifest.xml` doit contenir une balise `<App />` avec un attribut `Publisher` défini sur le nom de votre projet.

## <a name="initialize-the-engagement-reach-sdk"></a>Initialiser le SDK du module Couverture d'Engagement
### <a name="engagement-configuration"></a>Configuration d'Engagement
La configuration d'Engagement est centralisée dans le fichier `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier la configuration de couverture :

* *Facultatif*, indiquez si l’installation du service Push natif (MPNS) est activée ou non entre les balises `<enableNativePush>` et `</enableNativePush>` (`true` par défaut).
* *Facultatif*, indiquez le nom du canal de transmission Push entre les balises `<channelName>` et `</channelName>`. Indiquez celui utilisé par votre application ou laissez-le vide.

Si vous souhaitez plutôt le spécifier lors de l'exécution, vous pouvez appeler la méthode suivante avant l'initialisation de l'agent Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> Vous pouvez spécifier le nom du canal de transmission MPNS de votre application. Par défaut, Engagement crée un nom basé sur l'ID de l'application. Il n'est pas nécessaire de spécifier le nom vous-même, sauf si vous prévoyez d'utiliser le canal Push en dehors d'Engagement.
> 
> 

### <a name="engagement-initialization"></a>Initialisation d'Engagement
Modifiez le fichier `App.xaml.cs`:

* Ajoutez à vos instructions `using` :
  
      using Microsoft.Azure.Engagement;
* Insérez `EngagementReach.Instance.Init` juste après `EngagementAgent.Instance.Init` dans `Application_Launching` :
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Insérez `EngagementReach.Instance.OnActivated` dans la méthode `Application_Activated` :
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> `EngagementReach.Instance.Init` est exécuté dans un thread dédié. Vous n'avez pas à le faire vous-même.
> 
> 

## <a name="app-store-submission-considerations"></a>Considérations relatives à la soumission App store
Microsoft impose certaines règles lors de l'utilisation de notifications Push :

Selon la documentation sur les [politiques d'applications] Microsoft, section 2.9 :

1) vous devez demander à l'utilisateur s'il accepte de recevoir des notifications Push. Ensuite, ajoutez un moyen de désactiver les notifications Push dans vos paramètres.

L'objet EngagementReach fournit deux méthodes pour gérer les autorisations de réception : `EnableNativePush()` et `DisableNativePush()`. Par exemple, vous pouvez créer une option dans les paramètres avec un bouton bascule pour désactiver ou activer le service MPNS.

Vous pouvez également décider de désactiver MPNS via la configuration Engagement \<windows-phone-sdk-reach-configuration\>.

> 2.9.1) L’application doit tout d’abord décrire les notifications envoyées et **obtenir l’autorisation expresse de l’utilisateur**. Elle **doit également proposer un moyen de désactiver la réception de notifications Push**. Toutes les notifications fournies par le biais des services Microsoft Push Notification doivent être cohérentes avec la description fournie à l'utilisateur et respecter toutes les [Stratégies d'application][Content Policies] et [exigences supplémentaires pour les types d'application spécifiques] applicables.
> 
> 

2) Vous ne devez pas utiliser trop de notifications Push. Engagement gérera les notifications pour vous.

> 2.9.2) L'application et son utilisation du service MPNS ne doivent pas utiliser une capacité réseau ou une bande passante MNPS excessive, ni surcharger de notifications un appareil Windows Phone ou autre service ou appareil Microsoft, comme déterminé par Microsoft à sa discrétion, ni endommager ou interférer avec les réseaux ou serveurs Microsoft ou tout serveur ou serveurs tiers connectés au service MNPS.
> 
> 

3) N’utilisez pas le service MPNS pour envoyer des informations critiques. Engagement utilise MPNS, cette règle s'applique donc aussi pour les campagnes créées dans le serveur frontal Engagement.

> 2.9.3) Le service MNPS ne peut pas être utilisé pour envoyer des notifications stratégiques ou pouvant toucher à la vie des utilisateurs, y compris, mais sans s'y limiter, les notifications critiques liées à une affection ou un dispositif médical. MICROSOFT NE GARANTIT AUCUNEMENT LA NON-INTERRUPTION, L'ABSENCE D'ERREUR OU L'EXÉCUTION EN TEMPS RÉEL DE L'UTILISATION DU SERVICE MPNS OU DE LA LIVRAISON DES NOTIFICATIONS MPNS.
> 
> 

**Nous ne pouvons pas garantir que votre application soit validée si vous ne respectez pas ces recommandations.**

## <a name="handle-data-push-optional"></a>Gérer les Push de données (facultatif)
Si vous voulez que votre application puisse recevoir les Push de données du module Couverture, vous devez implémenter deux événements de la classe EngagementReach :

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

Ensuite, définissez le contenu du champ `EngagementReach.Instance.Handler` à l'aide de votre objet personnalisé dans la classe `App.xaml.cs` de la méthode `Application_Launching`.

**Exemple de code :**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Par défaut, Engagement utilise sa propre implémentation de `EngagementReachHandler`. Il n'est pas nécessaire de créer votre propre implémentation. Toutefois, si vous le faites, vous n'aurez pas à remplacer chaque méthode. Le comportement par défaut consiste à sélectionner l'objet de base Engagement.
> 
> 

### <a name="layouts"></a>Mises en forme
Par défaut, le module Couverture utilise les ressources intégrées du DLL pour afficher les notifications et les pages.

Toutefois, vous pouvez décider d'utiliser vos propres ressources pour refléter votre marque dans ces composants.

Vous pouvez remplacer les méthodes `EngagementReachHandler` dans votre sous-classe pour indiquer à Engagement d'utiliser vos mises en forme :

**Exemple de code :**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> La méthode `CreateNotification` peut renvoyer un résultat null. La notification ne s'affichera pas et la couverture campagne est abandonnée.
> 
> 

Pour simplifier l'implémentation de votre mise en forme, nous fournissons également notre propre xaml qui peut servir de base pour votre code. Il se trouve dans l'archive du SDK Engagement (/src/reach/).

> [!WARNING]
> Les sources que nous proposons sont absolument identiques à celles que nous utilisons. Par conséquent, si vous souhaitez les modifier directement, n'oubliez pas de modifier l'espace de noms et le nom.
> 
> 

### <a name="notification-position"></a>Position des notifications
Par défaut, une notification interne à l'application s'affiche dans la partie inférieure gauche de l'application. Vous pouvez modifier ce comportement en remplaçant la méthode `GetNotificationPosition` de l'objet `EngagementReachHandler`.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Actuellement, vous pouvez choisir entre les positions `BOTTOM` (par défaut) et `TOP`.

### <a name="launch-message"></a>Lancer un message
Lorsqu'un utilisateur clique sur une notification du système (un toast), Engagement lance l'application, charge le contenu des messages envoyés et affiche la page de la campagne correspondante.

Il y a un délai entre le lancement de l'application et l'affichage de la page (qui dépend de la vitesse de votre réseau).

Pour indiquer à l'utilisateur qu'un chargement est en cours, vous devez fournir une indication visuelle, telle qu'une barre ou un indicateur de progression. Engagement ne peut pas gérer cela lui-même. Toutefois, il fournit plusieurs gestionnaires à cet effet.

Pour implémenter le rappel, procédez comme suit :

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

Vous pouvez définir le rappel dans la méthode `Application_Launching` de votre fichier `App.xaml.cs`, de préférence avant l'appel `EngagementReach.Instance.Init()`.

> [!TIP]
> Chaque gestionnaire est appelé par le thread d'interface utilisateur. Vous n'avez pas de souci à vous faire quand vous utilisez un MessageBox ou autre objet d'interface utilisateur.
> 
> 

[politiques d'applications]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[exigences supplémentaires pour les types d'application spécifiques]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

