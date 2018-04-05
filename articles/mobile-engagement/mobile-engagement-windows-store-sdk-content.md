---
title: Contenu du Kit de développement logiciel (SDK) des applications Windows Universal
description: Découvrez le contenu du Kit de développement logiciel (SDK) des applications Windows Universal pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7b520adcc6af24f7b092580ea82a787a120668bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-content"></a>Contenu du Kit de développement logiciel (SDK) des applications Windows Universal
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Ce document répertorie et décrit le contenu déployé par le Kit de développement dans votre application.

## <a name="the-resources-folder"></a>Le dossier `/Resources`
Ce dossier contient toutes les ressources dont Mobile Engagement a besoin. Vous pouvez également les personnaliser pour les adapter à votre application.

* `EngagementConfiguration.xml` : fichier de configuration de Mobile Engagement. C'est dans ce fichier que vous pouvez personnaliser les paramètres de Mobile Engagement (chaîne de connexion Mobile Engagement, rapport sur les incidents, etc.).

### <a name="html-folder"></a>Dossier /html
* `EngagementNotification.html` : conception html de la vue web `Notification` pour les bannières dans l'application.
* `EngagementAnnouncement.html` : conception html de la vue web `Announcement` pour les bannières dans les vues interstitielles.

### <a name="images-folder"></a>Dossier /images
* `EngagementIconNotification.png` : l’icône de marque affichée à gauche d'une notification. Remplacez celle-ci par l'icône de votre marque.
* `EngagementIconOk.png` : l’icône `Ok` des pages de contenu de Couverture pour le bouton d'action ou de validation.
* `EngagementIconNOK.png` : l’icône `NOK` utilisée quand le bouton de validation des pages de contenu de Couverture est désactivé.
* `EngagementIconClose.png` : l’icône `Close` des notifications et du contenu de couverture pour le bouton Ignorer.

### <a name="overlay-folder"></a>Dossier /overlay
* `EngagementPageOverlay.cs` : page de superposition responsable de l'ajout de l'interface utilisateur Engagement Reach dans l'application à son enfant.

