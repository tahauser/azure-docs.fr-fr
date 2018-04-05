---
title: Intégration du SDK Android pour Azure Mobile Engagement
description: Décrit comment intégrer le SDK Azure Mobile Engagement dans les applications Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: d02bcfc7521df9c18edc534e679e597ad2328e6f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Intégration du SDK Android pour Azure Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

> [!div class="op_single_selector"]
> * [Windows universel](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Ce document décrit toutes les options d’intégration et de configuration disponibles pour le SDK Azure Mobile Engagement pour Android.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Fonctionnalités avancées
### <a name="reporting-features"></a>Fonctionnalités de génération de rapports
Vous pouvez ajouter les fonctionnalités suivantes :

1. [Options de génération de rapports avancés](mobile-engagement-android-advanced-reporting.md)
2. [Options de génération de rapports d’emplacement](mobile-engagement-android-location-reporting.md)
3. [Options de configuration avancées](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Notifications :
[comment intégrer le module Couverture (notifications) à votre application Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM) : [comment intégrer GCM à Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM) : [comment intégrer ADM à Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Implémentation du plan de balise :
[comment utiliser l'API de balisage avancée de Mobile Engagement dans votre application Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Notes de publication

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Résolution d’un incident rare lors de l’appel de `EngagementAgentUtils.isInDedicatedEngagementProcess`, également utilisée par la classe `EngagementApplication`.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Prise en charge Android 8 (les versions précédentes du Kit de développement logiciel ne fonctionnent pas sur Android 8).
* Aucune autre dépendance sur la bibliothèque de prise en charge.
* Supprimez la classe `EngagementFragmentActivity`.
* En raison des [limites d’exécution en arrière-plan](https://developer.android.com/preview/features/background.html) sur Android 8, les journaux en arrière-plan peuvent être retardés jusqu’à ce que l’utilisateur interagisse avec l’appareil. Cela entraîne un retard des statistiques **Remis** et **Notification système (affichée dans la barre de notification)** de la campagne push si l’appareil est en veille (la notification sera toujours affichée, sonnera et vibrera en temps réel sans problème).
* En raison des [limites d’exécution en arrière-plan](https://developer.android.com/preview/features/background-location-limits.html), l’emplacement en arrière-plan en temps réel ne sera pas fréquemment mis à jour sur Android 8.

Pour toutes les versions, consultez les [notes de publication complètes](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procédures de mise à niveau
Si vous avez déjà intégré une version plus ancienne de notre kit de développement logiciel (SDK) dans votre application, consultez [Procédures de mise à niveau](mobile-engagement-android-upgrade-procedure.md).

