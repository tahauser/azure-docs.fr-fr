---
title: Intégration du SDK Android d'Azure Mobile Engagement
description: Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 77047cb1dc39fa3c05f58550ceea74e78396157f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>Procédures de mise à niveau
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Si vous avez déjà intégré une ancienne version de notre SDK à votre application, tenez compte des points suivants avant de procéder à la mise à niveau du SDK.

Vous devrez peut-être suivre quelques procédures si vous avez manqué plusieurs versions du kit SDK. Par exemple, si vous migrez de la version 1.4.0 vers 1.6.0, vous devez tout d'abord suivre la procédure « Migration de 1.4.0 vers 1.5.0 », puis la procédure « Migration de 1.5.0 vers 1.6.0 ».

Quelle que soit la version que vous mettez à niveau, vous devez remplacer `mobile-engagement-VERSION.jar` .

## <a name="from-420-to-421"></a>Migration de 4.2.0 vers 4.2.1
Vous pouvez effectuer cette étape sur n’importe quelle version du SDK. Il s’agit d’une amélioration de sécurité quand vous intégrez des activités Reach.

Vous devez maintenant ajouter `exported="false"` à toutes les activités Reach.

Les activités Reach doivent maintenant ressembler à ceci sur votre `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

## <a name="from-400-to-410"></a>Migration de 4.0.0 vers 4.1.0
Le SDK gère maintenant un nouveau modèle d’autorisation à partir d’Android M.

Si vous utilisez des fonctionnalités de localisation ou des notifications BigPicture, veuillez lire [cette section](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Outre le nouveau modèle d’autorisation, nous prenons désormais en charge les fonctionnalités de localisation lors de l’exécution.
Nous sommes toujours compatibles avec les paramètres du manifeste pour la localisation, mais ils sont à présent obsolètes. Pour utiliser la configuration lors de l’exécution, supprimez les sections suivantes à partir de votre ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

et lisez [cette procédure mise à jour](mobile-engagement-android-integrate-engagement.md#location-reporting) pour utiliser à la place une configuration lors de l’exécution.

## <a name="from-300-to-400"></a>Migration de 3.0.0 vers 4.0.0
### <a name="native-push"></a>Native Push
Native Push (GCM/ADM) est désormais également utilisé pour les notifications dans l’application. Vous devez donc configurer les informations d’identification Native Push pour tout type de campagne push.

Si ce n’est déjà fait, suivez [cette procédure](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml
L’intégrationReach a été modifiée dans ``AndroidManifest.xml``.

Remplacez ceci :

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

par

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Il se peut désormais qu’un écran de chargement s’affiche lorsque vous cliquez sur une annonce (avec texte/contenu web) ou sur une interrogation.
Vous devez ajouter ceci pour que les campagnes fonctionnent dans la version 4.0.0 :

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Ressources
Intégrez le nouveau fichier `res/layout/engagement_loading.xml` dans votre projet.

## <a name="from-240-to-300"></a>Migration de 2.4.0 vers 3.0.0
La section qui suit décrit comment migrer une intégration du SDK à partir du service Capptain offert par Capptain SAS dans une application reposant sur Azure Mobile Engagement. Si vous migrez à partir d'une version antérieure, consultez le site web de Capptain pour migrer tout d'abord vers 2.4.0, puis appliquez la procédure suivante.

> [!IMPORTANT]
> Capptain et Mobile Engagement ne sont pas les mêmes services et la procédure décrite ci-dessous explique uniquement comment migrer l'application cliente. La migration du SDK dans l'application ne migre PAS vos données des serveurs Capptain vers les serveurs Mobile Engagement .
> 
> 

### <a name="jar-file"></a>Fichier JAR
Remplacez `capptain.jar` par `mobile-engagement-VERSION.jar` dans votre dossier `libs`.

### <a name="resource-files"></a>Fichiers de ressources
Chaque fichier de ressources fourni (précédé de `capptain_`) doit être remplacé par un nouveau (précédé de `engagement_`).

Si vous avez personnalisé ces fichiers, vous devez réappliquer vos modifications aux nouveaux fichiers, **tous les identificateurs dans les fichiers de ressources ont également été renommés**.

### <a name="application-id"></a>ID de l'application
Engagement utilise désormais une chaîne de connexion pour configurer les identificateurs du SDK tels que l'identificateur d'application.

Vous devez utiliser la méthode `EngagementAgent.init` dans l'activité de votre programme de lancement comme suit :

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La chaîne de connexion de votre application est affichée sur le portail Azure.

Supprimez tous les appels vers `CapptainAgent.configure`, car `EngagementAgent.init` remplace cette méthode.

`appId` ne peut plus être configuré avec `AndroidManifest.xml`.

Supprimez cette section de votre `AndroidManifest.xml` si elle existe :

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>API Java
Chaque appel vers n'importe quelle classe Java de notre SDK doit être renommé, par exemple, `CapptainAgent.getInstance(this)` doit être renommé en `EngagementAgent.getInstance(this)`, `extends CapptainActivity` doit être renommé en `extends EngagementActivity`, etc.

Si vous avez effectué l'intégration avec les fichiers de préférences de l'agent par défaut, le nom du fichier par défaut est désormais `engagement.agent`, et la clé est `engagement:agent`.

Quand vous créez des annonces web, le binder Javascript est maintenant `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml
De nombreuses modifications ont été apportées, le service n'est plus partagé et vous ne pouvez plus exporter un grand nombre de destinataires.

La déclaration du service est plus simple, vous supprimez le filtre intent et toutes les métadonnées qu'il contient, et ajoutez `exportable=false`.

En outre, tous les éléments sont renommés pour utiliser Engagement.

Vous devez obtenir quelque chose similaire à ce qui suit :

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Pour l'activation des journaux de tests, les métadonnées ont été déplacées dans la balise application et renommées :

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

Toutes les autres métadonnées ont simplement été renommées, en voici la liste complète (renommez uniquement celles que vous utilisez) :

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Le suivi de Google Play et SmartAd a été supprimé du SDK, il vous suffit le supprimer sans le remplacer :

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Les activités Reach sont maintenant déclarées comme suit :

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

Si vous avez personnalisé des activités Reach, vous devez simplement remplacer les actions intent par `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` ou `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Les récepteurs de diffusion ont été renommés, et nous ajoutons maintenant `exported=false`. Voici la liste complète des récepteurs avec la nouvelle spécification, (renommez uniquement ceux que vous utilisez) :

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Le récepteur de suivi a été supprimé, par conséquent, vous devez supprimer cette section :

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Notez que la déclaration de votre implémentation du récepteur de diffusion **EngagementMessageReceiver** a changé dans `AndroidManifest.xml`. En effet, l'API permettant d'envoyer et de supprimer des messages XMPP arbitraires d'entités XMPP arbitraires, et l'API permettant d'envoyer et de recevoir des messages entre appareils ont été supprimées. Par conséquent, vous devez également supprimer les rappels suivants de votre implémentation de **EngagementMessageReceiver** :

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

and

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

puis supprimez tout appel sur **EngagementAgent** dans :

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

and

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard
La configuration de Proguard peut être affectée par le changement de nom, les règles ressemblent maintenant à ce qui suit :

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }

