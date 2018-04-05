---
title: Intégration du SDK Android d'Azure Mobile Engagement
description: Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 21f9c1007c6b7dabc625555c66f267b2b7c579f8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>comment intégrer GCM à Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!IMPORTANT]
> Vous devez suivre la procédure d'intégration décrite dans le document « Comment intégrer Engagement sur Android » avant de suivre ce guide.
> 
> Ce document est utile uniquement si vous avez intégré le module Reach et avez l’intention d’effectuer des transmissions de type Push vers des appareils Google Play. Pour intégrer les couvertures campagnes à votre application, lisez d'abord « Comment intégrer le module de couverture Engagement sur Android ».
> 
> 

## <a name="introduction"></a>Introduction
L’intégration de GCM permet à votre application de recevoir des notifications Push.

Les charges GCM envoyées vers le Kit de développement logiciel (SDK) contiennent toujours la clé `azme` dans l’objet de données. Donc si vous utilisez GCM à d’autres fins dans votre application, vous pouvez filtrer les notifications Push en fonction de cette clé.

> [!IMPORTANT]
> Seuls les appareils disposant d’Android 2.2 ou version ultérieure, de Google Play et d’une connexion d’arrière-plan à Google peuvent faire l’objet d’une notification Push à l’aide de GCM. Toutefois, vous pouvez intégrer ce code en toute sécurité sur les appareils non pris en charge (car il utilise uniquement des intentions).
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>Créer un projet Google Cloud Messaging avec une clé API
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>Intégration du SDK
### <a name="managing-device-registrations"></a>Gestion des inscriptions des appareils
Chaque appareil doit envoyer une commande d'inscription aux serveurs Google, sinon il ne pourra pas recevoir de campagnes.

Un appareil peut également se désinscrire des notifications GCM (l'appareil est automatiquement désinscrit quand l'application est désinstallée).

Si vous n’utilisez pas [le Kit de développement logiciel (SDK) Google Play] ou si vous n’avez pas encore envoyé l’intention d’inscription, vous pouvez demander à Engagement d’inscrire automatiquement l’appareil.

Pour cela, ajoutez le code suivant au fichier `AndroidManifest.xml`, à l'intérieur de la balise `<application/>` :

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Communiquer l'ID d'inscription au service Push d'Engagement et recevoir des notifications
Pour communiquer l'ID d'inscription de l'appareil au service Push d'Engagement et recevoir ses notifications, ajoutez le code suivant au fichier `AndroidManifest.xml`, à l'intérieur de la balise `<application/>` (même si vous gérez vous-même les inscriptions d'appareil) :

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Assurez-vous de disposer des autorisations suivantes dans votre `AndroidManifest.xml` (après la balise `</application>`).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Accorder à Mobile Engagement l’accès à votre clé d’API GCM
Suivez [ce guide](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) pour accorder à Mobile Engagement l’accès à votre clé API GCM

[le Kit de développement logiciel (SDK) Google Play]:https://developers.google.com/cloud-messaging/android/start
