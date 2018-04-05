---
title: Configuration avancée pour le SDK Android pour Azure Mobile Engagement
description: Décrit les options de configuration avancées, notamment le manifeste Android avec le SDK Android pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 29a7bb7daae59e2034504ce27c9ba66755b11e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configuration avancée pour le SDK Android pour Azure Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!div class="op_single_selector"]
> * [Windows universel](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

Cette procédure explique comment définir différentes options de configuration pour les applications Azure Mobile Engagement pour Android.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Autorisations requises
Certaines options requièrent des autorisations spécifiques, qui sont toutes répertoriées ici pour référence, ainsi qu’en ligne dans la fonctionnalité spécifique. Ajoutez ces autorisations au fichier AndroidManifest.xml de votre projet, juste avant ou après la balise `<application>` .

Le code d’autorisation doit ressembler à ce qui suit, en renseignant l’autorisation appropriée du tableau ci-dessous.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Autorisation | Quand l’utiliser |
| --- | --- |
| INTERNET |Requis. Pour la génération de rapports de base |
| ACCESS_NETWORK_STATE |Requis. Pour la génération de rapports de base |
| RECEIVE_BOOT_COMPLETED |Requis. Pour afficher le centre de notifications après le redémarrage de l’appareil |
| WAKE_LOCK |Recommandé. Active la collecte de données quand vous utilisez le Wi-Fi ou quand l’écran est éteint |
| VIBRATE |facultatif. Active la vibration lors de la réception des notifications |
| DOWNLOAD_WITHOUT_NOTIFICATION |facultatif. Active la Notification de la vue d’ensemble Android |
| WRITE_EXTERNAL_STORAGE |facultatif. Active la Notification de la vue d’ensemble Android |
| ACCESS_COARSE_LOCATION |facultatif. Active la génération de rapports d’emplacement en temps réel |
| ACCESS_FINE_LOCATION |facultatif. Active la génération de rapports sur l’emplacement GPS |

À partir d’Android M, [certaines autorisations sont gérées au moment de l’exécution](mobile-engagement-android-location-reporting.md#android-m-permissions).

Si vous utilisez déjà ``ACCESS_FINE_LOCATION``, vous n’avez pas besoin d’utiliser aussi ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Options de configuration de manifeste Android
### <a name="crash-report"></a>Rapport d'incidents
Pour désactiver les rapports d'incidents, ajoutez ce code entre les balises `<application>` et `</application>` :

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Seuil du mode rafale
Par défaut, le service Engagement génère des journaux en temps réel. Si votre application crée des journaux très fréquemment, il est préférable de les mettre en mémoire tampon et de les rassembler dans un rapport à intervalle régulier (on parle dans ce cas de « mode rafale »). Pour cela, ajoutez ce code entre les balises `<application>` et `</application>` :

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Le mode rafale accroît légèrement l'autonomie de la batterie, mais il affecte aussi Engagement Monitor. En effet, la durée des sessions et des tâches est arrondie au seuil de rafale (les sessions et les tâches plus courtes que le seuil de rafale ne sont donc pas visibles). Votre seuil de rafale ne doit pas dépasser la valeur 30000 (30 s).

### <a name="session-timeout"></a>Délai d'expiration de session
 Vous pouvez terminer une activité en appuyant sur la touche **Accueil** ou **Retour**, en mettant le téléphone en mode veille ou en basculant vers une autre application. Par défaut, une session se termine dix secondes après la fin de la dernière activité. Cela permet d'éviter un fractionnement de session chaque fois que l'utilisateur quitte une application puis la rouvre rapidement, ce qui peut se produire quand il ouvre une image ou une notification, etc. Il est possible de modifier ce paramètre. Pour cela, ajoutez ce code entre les balises `<application>` et `</application>` :

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
* La clé `engagement:agent:settings:mode` est utilisée pour définir le mode du fichier de préférences partagées. Utilisez le même mode que dans votre `PreferenceActivity`. Le mode doit être passé comme un nombre. Si vous utilisez une combinaison d'indicateurs de constante dans votre code, vérifiez la valeur totale.

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
