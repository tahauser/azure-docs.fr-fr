---
title: Intégration du SDK Android d'Azure Mobile Engagement
description: Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fffff6de996b8295639b3d595c5f778de8a0f74f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-android"></a>Comment intégrer Engagement à Android
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

Cette procédure décrit la méthode la plus simple pour activer les fonctions d'analyse et de surveillance dans votre application Android.

> [!IMPORTANT]
> Le niveau minimum de l'API SDK Android doit être égal ou supérieur à 10 (Android 2.3.3 ou version ultérieure).
> 
> 

Les étapes suivantes suffisent à activer la création de journaux nécessaires au calcul de l'ensemble des statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les éléments techniques. Le rapport des journaux nécessaire pour calculer d'autres statistiques telles que les événements, les erreurs et les tâches, doit être généré manuellement à l'aide de l'API Engagement (consultez la rubrique [Utilisation de l'API de balisage Mobile Engagement avancé dans votre Android](mobile-engagement-android-use-engagement-api.md) étant donné que ces statistiques dépendent de l'application.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Intégrer le SDK et le service Engagement à un projet Android
Téléchargez le Kit de développement logiciel (SDK) Android [ici](https://aka.ms/vq9mfn) Récupérez `mobile-engagement-VERSION.jar` et placez-les dans le dossier `libs` de votre projet Android (créez le dossier de la bibliothèque si celui-ci n’existe pas).

> [!IMPORTANT]
> Si vous générez votre package d'application avec ProGuard, vous devez conserver certaines classes. Vous pouvez utiliser l'extrait de configuration suivant :
> 
> -keep public class * extends android.os.IInterface -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

Spécifiez votre chaîne de connexion Engagement en appelant la méthode suivante dans l'activité du programme de lancement :

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La chaîne de connexion de votre application est affichée sur le portail Azure.

* Si ce n'est pas le cas, ajoutez les autorisations Android suivantes (avant la balise `<application>`) :
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* Ajoutez la section suivante (entre les balises `<application>` et `</application>`) :
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* Remplacez `<Your application name>` par le nom de votre application.

> [!TIP]
> La clé `android:label` vous permet de choisir le nom du service Engagement tel qu'il apparaîtra aux utilisateurs finaux dans l'écran Services en cours d'exécution de leur téléphone. Il est recommandé de définir cet attribut sur `"<Your application name>Service"` (par exemple `"AcmeFunGameService"`).
> 
> 

La spécification de l'attribut `android:process` permet au service Engagement d'être exécuté dans le cadre de son propre processus (l'exécution d'Engagement dans le même processus que votre application peut rendre votre thread principal/d'interface utilisateur moins réactif).

> [!NOTE]
> Tout code que vous placez dans `Application.onCreate()` et autres rappels d'application sera exécuté pour l'ensemble des processus de votre application, y compris le service Engagement. Cela peut avoir des effets indésirables (tels que des allocations de mémoire et des threads inutiles dans le processus Engagement, ou des récepteurs ou services de diffusion en double).
> 
> 

Si vous remplacez `Application.onCreate()`, il est recommandé d'ajouter l'extrait de code suivant au début de la fonction `Application.onCreate()` :

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Vous pouvez faire la même chose pour `Application.onTerminate()`, `Application.onLowMemory()` et `Application.onConfigurationChanged(...)`.

Vous pouvez également étendre `EngagementApplication` au lieu d'étendre `Application` : le rappel `Application.onCreate()` effectue la vérification du processus et appelle `Application.onApplicationProcessCreate()` uniquement si le processus actuel n'est pas celui qui héberge le service Engagement. Les mêmes règles s'appliquent pour les autres rappels.

## <a name="basic-reporting"></a>Génération de rapports de base
### <a name="recommended-method-overload-your-activity-classes"></a>Méthode recommandée : surchargez vos classes `Activity`
Pour activer la création d'un rapport à partir de tous les journaux nécessaires à Engagement pour calculer les statistiques relatives aux utilisateurs, aux sessions, aux activités, aux incidents et aux éléments techniques, il vous suffit de faire hériter toutes les sous-classes `*Activity` des classes `Engagement*Activity` correspondantes (par exemple, si votre activité héritée étend `ListActivity`, assurez-vous qu'elle étend `EngagementListActivity`).

**Sans Engagement :**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Avec Engagement :**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> Lorsque vous utilisez `EngagementListActivity` ou `EngagementExpandableListActivity`, assurez-vous que tout appel à `requestWindowFeature(...);` est effectué avant l'appel à `super.onCreate(...);`. Dans le cas contraire, un incident se produira.
> 
> 

Vous trouverez ces classes dans le dossier `src`. Vous pourrez les copier dans votre projet. Les classes se trouvent également dans **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Méthode alternative : appelez `startActivity()` et `endActivity()` manuellement
Si vous ne pouvez pas ou ne voulez pas surcharger vos classes `Activity`, vous pouvez démarrer et terminer vos activités en appelant les méthodes `EngagementAgent` directement.

> [!IMPORTANT]
> Le Kit de développement logiciel (SDK) Android n'appelle jamais la méthode `endActivity()`, même quand l'application est fermée (sur Android, les applications ne sont en fait jamais fermées). Il est donc *FORTEMENT* recommandé d’appeler la méthode `startActivity()` dans le rappel `onResume` de *TOUTES* vos activités, et la méthode `endActivity()` dans le rappel `onPause()` de *TOUTES* vos activités. Il s'agit de la seule façon d'empêcher la fuite de sessions. Dans le cas d'une fuite de session, le service Engagement n'est jamais déconnecté du serveur principal d'Engagement (étant donné que le service reste connecté tant qu'une session est en cours).
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

Cet exemple est très similaire à la classe `EngagementActivity` et à ses variantes, dont le code source est fourni dans le dossier `src`.

## <a name="test"></a>Test
Maintenant, vérifiez votre intégration en exécutant votre application mobile dans un émulateur ou sur un appareil et en vérifiant qu'elle inscrit une session sur l'onglet Surveiller.

Les sections suivantes sont facultatives.

## <a name="location-reporting"></a>Rapports d'emplacement
Si vous souhaitez créer des rapports concernant les emplacements, vous devez ajouter quelques lignes de configuration (entre les balises `<application>` et `</application>`).

### <a name="lazy-area-location-reporting"></a>Rapports d'emplacement de zone différé
Le rapport d'emplacement de zone différé permet d'indiquer le pays, la région et la localité associés aux appareils. Ce type de rapport d'emplacement utilise uniquement des emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi). La zone de l'appareil est signalée une fois maximum par session. Le GPS n'est jamais utilisé, ce type de rapport d'emplacement a donc très peu d'impact (pour ne pas dire aucun) sur la batterie.

Les zones signalées sont utilisées pour calculer des statistiques géographiques relatives aux utilisateurs, aux sessions, aux événements et aux erreurs. Elles peuvent également servir de critère dans les couvertures campagne.

Pour activer le service de localisation de zone différé, vous pouvez utiliser la configuration précédemment mentionnée dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l'autorisation suivante si elle manque :

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou vous pouvez continuer à utiliser ``ACCESS_FINE_LOCATION`` si vous l’utilisez déjà dans votre application.

### <a name="real-time-location-reporting"></a>Rapports d'emplacement en temps réel
Le rapport d'emplacement en temps réel permet de signaler la latitude et la longitude associées aux appareils. Par défaut, ce type de rapport d'emplacement utilise uniquement des emplacements réseau (basés sur l'ID cellulaire ou le Wi-Fi) et le rapport n'est actif que lorsque l'application s'exécute en premier plan (c'est-à-dire pendant une session).

Les emplacements en temps réel ne sont *PAS* utilisés pour calculer des statistiques. Leur seul but est de permettre l’utilisation d’un critère de géorepérage en temps réel \<Reach-Audience-geofencing\> dans les campagnes Reach.

Pour activer le service de localisation de zone en temps réel, vous pouvez utiliser la configuration précédemment mentionnée dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l'autorisation suivante si elle manque :

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou vous pouvez continuer à utiliser ``ACCESS_FINE_LOCATION`` si vous l’utilisez déjà dans votre application.

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
Par défaut, le rapport d'emplacement en temps réel est uniquement actif quand l'application s'exécute en premier plan (c'est-à-dire pendant une session). Pour activer la création de rapports également en arrière-plan, utilisez l’objet de configuration suivant :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Quand l'application s'exécute en arrière-plan, seuls les emplacements réseau sont signalés, même si vous avez activé le GPS.
> 
> 

Le rapport d'emplacement en arrière-plan sera arrêté si l'utilisateur redémarre son appareil. Pour configurer un redémarrage automatique au moment du démarrage, ajoutez ce qui suit :

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Vous devez également ajouter l'autorisation suivante si elle manque :

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Autorisations Android M
À partir d’Android M, certaines autorisations sont gérées lors de l’exécution et nécessitent une approbation de l’utilisateur.

Les autorisations d’exécution seront désactivées par défaut pour les nouvelles installations d’application si vous ciblez le niveau d’API Android 23. Sinon, elles seront activées par défaut.

L’utilisateur peut activer/désactiver ces autorisations dans le menu des paramètres de l’appareil. La désactivation des autorisations à partir du menu système arrête les processus en arrière-plan de l’application. Il s’agit d’un comportement du système qui n’a aucune incidence sur la capacité à recevoir des notifications en arrière-plan.

Dans le cadre de la stratégie Mobile Engagement, les autorisations qui requièrent une approbation au moment de l’exécution sont :

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (uniquement lors du ciblage de niveau d’API Android 23 pour cet élément)

Le stockage externe est utilisé uniquement pour la fonctionnalité Reach BigPicture. Si vous estimez que demander cette autorisation aux utilisateurs est un élément trop perturbateur, vous pouvez le supprimer si vous l’avez utilisé uniquement pour Mobile Engagement, au détriment de la fonctionnalité BigPicture qui sera alors désactivée.

Pour les fonctionnalités de localisation, vous devez demander les autorisations à l’utilisateur via une boîte de dialogue système standard. Si l’utilisateur approuve, vous devez indiquer à ``EngagementAgent`` de prendre enc ompte cette modification en temps réel (sinon, la modification sera traitée au prochain lancement de l’application par l’utilisateur).

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
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>Génération de rapports avancés
Éventuellement, si vous voulez signaler des événements, des erreurs et des tâches de l'application spécifiques, vous devez utiliser l'API Engagement par le biais des méthodes de la classe `EngagementAgent` . Un objet de cette classe peut être récupéré en appelant la méthode statique `EngagementAgent.getInstance()` .

L'API d'Engagement permet d'utiliser toutes les fonctionnalités avancées d'Engagement. Elle est détaillée dans la procédure d'utilisation de l'API d'Engagement sur Android (ainsi que dans la documentation technique de la classe `EngagementAgent`).

## <a name="advanced-configuration-in-androidmanifestxml"></a>Configuration avancée (dans AndroidManifest.xml)
### <a name="wake-locks"></a>Verrous d'activation
Si vous voulez être sûr que les statistiques soient envoyées en temps réel quand vous utilisez le Wi-Fi ou quand l'écran est désactivé, ajoutez l'autorisation facultative suivante :

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Rapport d'incidents
Si vous voulez désactiver les rapports d'incidents, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Seuil du mode rafale
Par défaut, le service Engagement génère des journaux en temps réel. Si votre application crée des journaux très fréquemment, il est préférable de les mettre en mémoire tampon et de les rassembler dans un rapport à intervalle régulier (on parle dans ce cas de « mode rafale »). Pour cela, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Le mode rafale accroît légèrement l'autonomie de la batterie, mais il affecte aussi Engagement Monitor. En effet, la durée des sessions et des tâches est arrondie au seuil de rafale (les sessions et les tâches plus courtes que le seuil de rafale ne sont donc pas visibles). Il est recommandé d'utiliser un seuil de rafale inférieur à 30 000 (30 s).

### <a name="session-timeout"></a>Délai d'expiration de session
Par défaut, une session est terminée dans les 10 s qui suivent la fin de la dernière activité (ce qui se produit généralement quand vous appuyez sur la touche Accueil ou Retour, quand vous mettez le téléphone en mode veille ou quand vous passez à une autre application). Cela permet d'éviter un fractionnement de session chaque fois que l'utilisateur quitte une application puis la rouvre très rapidement (ce qui peut se produire quand il ouvre une image ou une notification, etc.). Il est possible de modifier ce paramètre. Pour cela, ajoutez ce qui suit (entre les balises `<application>` et `</application>`) :

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Désactiver les rapports de suivi
### <a name="using-a-method-call"></a>En appelant une méthode
Si vous voulez qu'Engagement arrête d'envoyer des journaux, vous pouvez appeler la méthode suivante :

            EngagementAgent.getInstance(context).setEnabled(false);

Cet appel est persistant : il utilise un fichier de préférences partagées.

Si le contrat est actif quand vous appelez cette fonction, l'arrêt du service peut prendre une minute. Toutefois, le service ne sera pas lancé la prochaine fois que vous lancerez l'application.

Vous pouvez activer à nouveau la création de rapports de journaux en appelant la même fonction avec `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Intégration dans votre propre `PreferenceActivity`
Au lieu d'appeler cette fonction, vous pouvez également intégrer ce paramètre directement dans votre `PreferenceActivity`.

Vous pouvez configurer Engagement pour qu'il utilise votre fichier de préférences (avec le mode souhaité) dans le fichier `AndroidManifest.xml` avec `application meta-data` :

* La clé `engagement:agent:settings:name` est utilisée pour définir le nom du fichier de préférences partagées.
* La clé `engagement:agent:settings:mode` est utilisée pour définir le mode du fichier de préférences partagées. Vous devez utiliser le même mode que dans votre `PreferenceActivity`. Le mode doit être passé comme un nombre. Si vous utilisez une combinaison d'indicateurs de constante dans votre code, vérifiez la valeur totale.

Engagement utilise toujours la clé booléenne `engagement:key` dans le fichier de préférences pour la gestion de ce paramètre.

L'exemple de suivant de `AndroidManifest.xml` montre les valeurs par défaut :

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Vous pouvez ensuite ajouter un `CheckBoxPreference` dans votre disposition préférée, comme celle qui suit :

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
