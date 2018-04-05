---
title: Intégration du SDK Android d'Azure Mobile Engagement
description: Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: a01bd74649fa28e0d86cea4ed03da80326c06234
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="release-notes"></a>Notes de publication
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 


## <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Résolution d’un incident rare lors de l’appel de `EngagementAgentUtils.isInDedicatedEngagementProcess`, également utilisée par la classe `EngagementApplication`.

## <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Prise en charge Android 8 (les versions précédentes du Kit de développement logiciel ne fonctionnent pas sur Android 8).
* Aucune autre dépendance sur la bibliothèque de prise en charge.
* Supprimez la classe `EngagementFragmentActivity`.
* En raison des [limites d’exécution en arrière-plan](https://developer.android.com/preview/features/background.html) sur Android 8, les journaux en arrière-plan peuvent être retardés jusqu’à ce que l’utilisateur interagisse avec l’appareil. Cela entraîne un retard des statistiques **Remis** et **Notification système (affichée dans la barre de notification)** de la campagne push si l’appareil est en veille (la notification sera toujours affichée, sonnera et vibrera en temps réel sans problème).
* En raison des [limites d’exécution en arrière-plan](https://developer.android.com/preview/features/background-location-limits.html), l’emplacement en arrière-plan en temps réel ne sera pas fréquemment mis à jour sur Android 8.

## <a name="424-03302017"></a>4.2.4 (03/30/2017)
* Résolution du problème des couleurs du texte de notification dans l’application sur Android 7, qui deviennent identiques à celles des versions antérieures d’Android.

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Plus aucun verrou Wi-Fi.
* Résolution d'un blocage lors de l’appel de getDeviceId avant init (bogue introduit dans 4.2.0).

## <a name="422-05172016"></a>4.2.2 (05/17/2016)
* Améliorations de la stabilité.

## <a name="421-05102016"></a>4.2.1 (05/10/2016)
* Sécurité : Désactivation de l’accès aux fichiers locaux dans les vues web.
* Sécurité : Suppression de la classe `EngagementPreferenceActivity` qui étend la classe `PreferenceActivity` obsolète et non sécurisée.
* Sécurité : Les activités Reach sont maintenant documentées pour utiliser `exported="false"`. Vous pouvez aussi utiliser cet indicateur dans les versions précédentes du SDK.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* Le Kit de développement logiciel (SDK) est désormais sous licence MIT.
* Autorise la spécification d’un identificateur d’appareil personnalisé au moment de l’initialisation du Kit de développement logiciel (SDK).

## <a name="415-02012016"></a>4.1.5 (01/02/2016)
* Améliorations de la stabilité.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)
* Améliorations de la stabilité.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)
* Améliorations de la stabilité.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)
* Améliorations de la stabilité.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* Améliorations de la stabilité.

## <a name="410-08252015"></a>4.1.0 (25/08/2015)
* Gestion du nouveau modèle d’autorisation pour Android M.
* Vous pouvez désormais configurer les fonctionnalités de localisation lors de l’exécution au lieu d’utiliser `AndroidManifest.xml`.
* Résolution d’un bogue d’autorisation : si vous utilisez `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` n’est plus nécessaire.
* Améliorations de la stabilité.

## <a name="400-07062015"></a>4.0.0 (06/07/2015)
* Modifications de protocole interne pour rendre les analyses et la diffusion plus fiables.
* Native Push (GCM/ADM) est désormais également utilisé pour les notifications dans l’application. Vous devez donc configurer les informations d’identification Native Push pour tout type de campagne push.
* Correction de la notification de la vue d’ensemble : elle ne s’affichait que 10 s après avoir été transmise.
* Résolution d’un bogue dans l’affichage web : un clic sur un lien exécutait également l’URL de l’action par défaut.
* Résolution d’un blocage rare lié à la gestion du stockage local.
* Correction de la gestion des chaînes de configuration dynamique.
* Mise à jour du CLUF.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)
* Version initiale d'Azure Engagement Mobile
* La configuration d'appId est remplacée par la configuration d'une chaîne de connexion.
* Suppression de l'API pour envoyer et recevoir des messages XMPP arbitraires d'entités XMPP arbitraires.
* Suppression de l'API pour envoyer et recevoir des messages entre appareils.
* Améliorations de sécurité.
* Suppression du suivi de Google Play et SmartAd.

