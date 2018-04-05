---
title: Génération de rapports d’emplacement pour le SDK Azure Mobile Engagement pour Android
description: Décrit comment configurer la génération de rapports d’emplacement pour le SDK Azure Mobile Engagement pour Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 7978010bd92af18e3ab7c8ccab8dc682d7ef18df
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Génération de rapports d’emplacement pour le SDK Azure Mobile Engagement pour Android
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Cette rubrique explique comment générer des rapports d’emplacement pour votre application Android.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Rapports d'emplacement
Si vous souhaitez créer des rapports concernant les emplacements, vous devez ajouter quelques lignes de configuration (entre les balises `<application>` et `</application>`).

### <a name="lazy-area-location-reporting"></a>Rapports d'emplacement de zone différé
Le rapport d'emplacement de zone différé permet d'indiquer le pays, la région et la localité associés aux appareils. Ce type de rapport d'emplacement utilise uniquement des emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi). La zone de l'appareil est signalée une fois maximum par session. Le GPS n'est jamais utilisé, ce type de rapport d'emplacement a donc peu d'impact sur la batterie.

Les zones signalées sont utilisées pour calculer des statistiques géographiques relatives aux utilisateurs, aux sessions, aux événements et aux erreurs. Elles peuvent également servir de critère dans les couvertures campagne.

Vous activez le service de localisation de zone différé à l’aide de la configuration précédemment mentionnée dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également spécifier une autorisation d’emplacement. Ce code utilise l’autorisation ``COARSE`` :

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Si votre application l’exige, vous pouvez utiliser ``ACCESS_FINE_LOCATION`` à la place.

### <a name="real-time-location-reporting"></a>Rapports d'emplacement en temps réel
Le rapport d'emplacement en temps réel permet de signaler la latitude et la longitude associées aux appareils. Par défaut, ce type de rapport d'emplacement utilise uniquement des emplacements réseau, basés sur l'ID cellulaire ou le Wi-Fi. Le rapport est uniquement actif quand l'application s'exécute en premier plan (c'est-à-dire pendant une session).

Les emplacements en temps réel ne sont *PAS* utilisés pour calculer des statistiques. Leur seul but est de permettre l’utilisation d’un critère de géorepérage en temps réel \<portée-public-gardiennage virtuel\> dans les campagnes Reach.

Pour activer les rapports d’emplacement en temps réel, ajoutez une ligne de code à l’endroit où vous définissez la chaîne de connexion Engagement dans l’activité du programme de lancement. Le résultat se présente comme suit :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>Rapports GPS
Par défaut, le rapport d'emplacement en temps réel utilise uniquement des emplacements réseau. Pour activer l’utilisation des localisations GPS (beaucoup plus précises), utilisez l’objet de configuration suivant :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l'autorisation suivante si elle manque :

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Rapports en arrière-plan
Par défaut, le rapport d'emplacement en temps réel est uniquement actif quand l'application s'exécute en premier plan (pendant une session par exemple). Pour activer la génération de rapports également en arrière-plan, utilisez l’objet de configuration suivant :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Quand l'application s'exécute en arrière-plan, seuls les emplacements réseau sont signalés, même si vous avez activé le GPS.
> 
> 

Si l’utilisateur redémarre son appareil, le rapport d’emplacement en arrière-plan est arrêté. Pour qu’il redémarre automatiquement au démarrage, ajoutez ce code.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Vous devez également ajouter l'autorisation suivante si elle manque :

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Autorisations Android M
À partir d’Android M, certaines autorisations sont gérées lors de l’exécution et nécessitent une approbation de l’utilisateur.

Si vous ciblez le niveau d’API Android 23, les autorisations d’exécution sont désactivées par défaut pour les nouvelles installations d’application. Sinon, elles sont activées par défaut.

Vous pouvez activer/désactiver ces autorisations dans le menu des paramètres de l’appareil. La désactivation des autorisations à partir du menu système arrête les processus en arrière-plan de l’application. Il s’agit d’un comportement du système qui n’a aucune incidence sur la capacité à recevoir des notifications en arrière-plan.

Dans le cadre de la génération de rapports d’emplacement Mobile Engagement, les autorisations qui requièrent une approbation au moment de l’exécution sont :

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Demandez les autorisations à l’utilisateur via une boîte de dialogue système standard. Si l’utilisateur approuve, indiquez à ``EngagementAgent`` de prendre en compte cette modification en temps réel. Sinon, la modification est traitée la prochaine fois que l’utilisateur lance l’application.

Voici un exemple de code à utiliser dans une activité de votre application pour demander des autorisations et transmettre le résultat, si positif, à ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
