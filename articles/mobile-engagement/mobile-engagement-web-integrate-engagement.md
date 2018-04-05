---
title: Intégration du SDK web Azure Mobile Engagement | Microsoft Docs
description: Dernières mises à jour et procédures du Kit de développement logiciel (SDK) web Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: bccfbdfe9d99900f58d5dbfa44183146c79b9c88
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Intégration d’Azure Mobile Engagement dans une application web
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

Les procédures de cet article décrivent la méthode la plus simple pour activer les fonctions d’analyse et de surveillance d’Azure Mobile Engagement dans votre application web.

Suivez ces étapes afin d’activer la génération des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les informations techniques. Pour les statistiques liées à l’application, telles que les événements, erreurs et tâches, vous devez activer manuellement les journaux à l’aide de l’API Azure Mobile Engagement. Pour plus d’informations, consultez [Utilisation de l’API avancée de balisage de Mobile Engagement dans votre application web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introduction
[Téléchargez le Kit de développement logiciel (SDK) web Azure Mobile Engagement](http://aka.ms/P7b453).
Le Kit de développement logiciel (SDK) web Mobile Engagement est fourni sous la forme d’un seul fichier JavaScript, azure-engagement.js, à inclure dans chaque page de votre site ou de votre application web.

> [!IMPORTANT]
> Avant d’exécuter ce script, vous devez exécuter un script ou un extrait de code écrit par vous pour configurer Mobile Engagement pour votre application.
> 
> 

## <a name="browser-compatibility"></a>Compatibilité des navigateurs
Le Kit de développement logiciel (SDK) web Mobile Engagement utilise un codage/décodage JSON natif en complément de requêtes AJAX interdomaines (reposant sur la spécification W3C CORS). Il est compatible avec les navigateurs suivants :

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## <a name="configure-mobile-engagement"></a>Configuration de Mobile Engagement
Écrivez un script qui crée un objet JavaScript `azureEngagement` global comme dans l’exemple suivant. Étant donné que votre site peut comporter plusieurs pages, cet exemple suppose que ce script est inclus dans chaque page. Dans cet exemple, l’objet JavaScript est nommé `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

La valeur `connectionString` de votre application s’affiche sur le portail Azure.

> [!NOTE]
> Les valeurs `appVersionName` et `appVersionCode` sont facultatives. Toutefois, nous vous recommandons de les configurer pour que les analyses puissent traiter les informations de version.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Inclure des scripts Mobile Engagement dans vos pages
Ajoutez des scripts Mobile Engagement à vos pages de l’une des manières suivantes :

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Ou cela :

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
Une fois le script du Kit de développement (SDK) web Mobile Engagement chargé, il crée l’alias **engagement** pour accéder aux API du Kit de développement logiciel (SDK). Vous ne pouvez pas utiliser cet alias pour définir la configuration du Kit de développement logiciel (SDK). Cet alias est utilisé comme référence dans cette documentation.

Notez que si l’alias par défaut est en conflit avec une autre variable globale de votre page, vous pouvez le redéfinir comme suit dans la configuration avant de charger le Kit de développement logiciel (SDK) web Mobile Engagement :

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Génération de rapports de base
La section Génération de rapports de base de Mobile Engagement présente les statistiques au niveau de la session, notamment les statistiques sur les utilisateurs, les sessions, les activités et les incidents.

### <a name="session-tracking"></a>Suivi de session
Une session Mobile Engagement est divisée en une séquence d’activités, chacune identifiée par un nom.

Dans un site web classique, nous vous recommandons de déclarer une autre activité sur chaque page de votre site. Pour une site ou une application web dans lesquels la page actuelle ne change jamais, vous pouvez suivre les activités à une plus petite échelle, par exemple au niveau de la page.

Dans les deux cas, pour démarrer ou modifier l’activité utilisateur en cours, appelez la fonction `engagement.agent.startActivity` . Par exemple : 

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Le serveur Mobile Engagement termine automatiquement une session ouverte dans un délai de trois minutes après la fermeture de la page de l’application.

Ou vous pouvez terminer une session manuellement en appelant `engagement.agent.endActivity`. Cette opération définit l’activité de l’utilisateur en cours sur « Idle » (inactive).  La session se terminera 10 secondes plus tard, sauf si un nouvel appel à `engagement.agent.startActivity` reprend la session.

Vous pouvez configurer le délai de 10 secondes dans l’objet Engagement global, comme suit :

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> Vous ne pouvez pas utiliser `engagement.agent.endActivity` dans le rappel `onunload` car vous ne pouvez pas effectuer d’appels AJAX à ce stade.
> 
> 

## <a name="advanced-reporting"></a>Génération de rapports avancés
Éventuellement, si vous voulez signaler des événements, des erreurs et des tâches de l'application spécifiques, vous devez utiliser l'API Mobile Engagement. Vous accédez à l’API Mobile Engagement sur mobile via l’objet `engagement.agent` .

Vous pouvez accéder à toutes les fonctionnalités avancées d’Engagement Mobile dans l’API Engagement Mobile. L’API est détaillée dans l’article [Utilisation de l’API avancée de balisage de Mobile Engagement dans votre application web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personnaliser les URL utilisées pour les appels AJAX
Vous pouvez personnaliser les URL utilisées par le Kit de développement logiciel (SDK) web Mobile Engagement. Par exemple, pour redéfinir l’URL de connexion (le point de terminaison du kit de développement logiciel pour l’enregistrement), vous pouvez remplacer la configuration comme suit :

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Si vos fonctions URL renvoient une chaîne commençant par `/`, `//`, `http://` ou `https://`, le schéma par défaut n’est pas utilisé. Par défaut, le schéma `https://` est utilisé pour ces URL. Si vous voulez personnaliser le schéma par défaut, remplacez la configuration par ceci :

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
