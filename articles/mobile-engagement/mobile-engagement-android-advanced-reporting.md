---
title: Options de génération de rapports avancés pour le SDK Azure Mobile Engagement pour Android
description: Décrit comment générer des rapports avancés visant à capturer des analyses pour le SDK Azure Mobile Engagement pour Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 419b65250d2b65e184e6e36349b17b5ac9e7a6ba
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Génération de rapports avancés avec Engagement sur Android
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!div class="op_single_selector"]
> * [Windows universel](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Cette rubrique décrit des scénarios de génération de rapports supplémentaires dans votre application Android. Vous pouvez appliquer ces options à l’application créée dans le didacticiel [Prise en main](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Le didacticiel que vous avez suivi était délibérément direct et simple, mais vous disposez également d’options avancées.

## <a name="modifying-your-activity-classes"></a>Modification de vos classes `Activity`
Dans le didacticiel de [Prise en main](mobile-engagement-android-get-started.md), il vous suffisait de configurer vos sous-classes `*Activity` pour qu’elles héritent des classes `Engagement*Activity` correspondantes. Par exemple, si votre activité héritée étendait `ListActivity`, vous deviez faire en sorte qu’elle étende `EngagementListActivity`.

> [!IMPORTANT]
> Lorsque vous utilisez `EngagementListActivity` ou `EngagementExpandableListActivity`, assurez-vous que tout appel à `requestWindowFeature(...);` est effectué avant l'appel à `super.onCreate(...);`. Dans le cas contraire, un incident se produira.
> 
> 

Vous trouverez ces classes dans le dossier `src`. Vous pourrez les copier dans votre projet. Les classes se trouvent également dans **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Méthode alternative : appelez `startActivity()` et `endActivity()` manuellement
Si vous ne pouvez pas ou ne voulez pas surcharger vos classes `Activity`, vous pouvez démarrer et terminer vos activités en appelant les méthodes d’`EngagementAgent` directement.

> [!IMPORTANT]
> Le Kit de développement logiciel (SDK) Android n'appelle jamais la méthode `endActivity()`, même quand l'application est fermée (sur Android, les applications ne sont jamais fermées). Il est donc *FORTEMENT* recommandé d’appeler la méthode `startActivity()` dans le rappel `onResume` de *TOUTES* vos activités, et la méthode `endActivity()` dans le rappel `onPause()` de *TOUTES* vos activités. Il s'agit de la seule façon d'empêcher la fuite de sessions. Dans le cas d'une fuite de session, le service Engagement n'est jamais déconnecté du serveur principal d'Engagement (étant donné que le service reste connecté tant qu'une session est en cours).
> 
> 

Voici un exemple : 

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

Cet exemple est similaire à la classe `EngagementActivity` et à ses variantes, dont le code source est fourni dans le dossier `src`.

## <a name="using-applicationoncreate"></a>Utilisation de Application.onCreate()
Tout code que vous placez dans `Application.onCreate()` et dans les autres rappels d’application sera exécuté pour l’ensemble des processus de votre application, notamment le service Engagement. Cela peut avoir des effets indésirables, tels que des allocations de mémoire et des threads inutiles dans le processus Engagement, ou des récepteurs ou services de diffusion en double.

Si vous remplacez `Application.onCreate()`, nous vous recommandons d’ajouter l’extrait de code suivant au début de la fonction `Application.onCreate()` :

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Vous pouvez faire la même chose pour `Application.onTerminate()`, `Application.onLowMemory()` et `Application.onConfigurationChanged(...)`.

Vous pouvez également étendre `EngagementApplication` au lieu d'étendre `Application` : le rappel `Application.onCreate()` effectue la vérification du processus et appelle `Application.onApplicationProcessCreate()` uniquement si le processus actuel n'est pas celui qui héberge le service Engagement. Les mêmes règles s'appliquent pour les autres rappels.

## <a name="tags-in-the-androidmanifestxml-file"></a>Balises dans le fichier AndroidManifest.xml
Dans la balise service du fichier AndroidManifest.xml, l’attribut `android:label` vous permet de choisir le nom du service Engagement tel qu’il apparaîtra aux utilisateurs finaux dans l’écran Services en cours d’exécution de leur téléphone. Nous vous recommandons d’affecter la valeur `"<Your application name>Service"` à cet attribut (par exemple `"AcmeFunGameService"`).

La spécification de l'attribut `android:process` permet au service Engagement d'être exécuté dans le cadre de son propre processus (l'exécution d'Engagement dans le même processus que votre application peut rendre votre thread principal/d'interface utilisateur moins réactif).

## <a name="building-with-proguard"></a>Génération avec ProGuard
Si vous générez votre package d'application avec ProGuard, vous devez conserver certaines classes. Vous pouvez utiliser l'extrait de configuration suivant :

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
