---
title: Application de démonstration Azure Mobile Engagement | Microsoft Docs
description: Explique où télécharger, et comment utiliser et tirer parti des avantages de l’application de démonstration Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: b74afcadc52973a4d824afddc0abff6ac9f71d17
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-demo-app"></a>Application de démonstration d’Azure Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Nous avons publié une application de démonstration d’Azure Mobile Engagement pour les plateformes **iOS**, **Android** et **Windows** pour vous aider à trouver des ressources utiles et à en savoir plus sur Mobile Engagement.

Grâce à cette application, vous pouvez :

* Trouver facilement des liens utiles vers des ressources pour Mobile Engagement, telles que des vidéos, de la documentation, le forum d’assistance et le centre d’envoi de demandes de fonctionnalités.
* Tester des exemples de notifications prises en charge par Mobile Engagement pour trouver des idées pour vos propres applications mobiles.
* Utiliser une implémentation de référence pour étudier comment implémenter Mobile Engagement dans votre propre application. Vous découvrirez comment :
  
  * Recueillir des données d’analyse.
  * Implémenter des scénarios de notification avancés de type *Plein écran interstitiel* ou *Fenêtre contextuelle*.
  * Implémenter des enquêtes et des sondages.
  * Implémenter des scénarios push et push données sans assistance.   

## <a name="app-installation"></a>Installation de l’application
Cette application est disponible dans les magasins d’applications suivants :

* **Application de démonstration universelle Windows**:
  
  * Téléchargez l’application à partir du [Windows Store](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * L’application a été développée comme application universelle Windows 10. Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **Application de démonstration iOS**:
  
  * Téléchargez l’application à partir de l’ [Apple Store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * L’application a été développée dans iOS Swift. Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **Application de démonstration Android**:
  
  * Téléchargez l’application à partir du [Google Play store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Application de démonstration universelle Windows][1]

![Application de démonstration iOS][2]
![Application de démonstration Android][3]

## <a name="usage"></a>Usage
Vous pouvez utiliser cette application des manières suivantes :

**Téléchargez l’application sur votre appareil à partir des liens des boutiques d’applications (fournis ci-dessus) :**

> [!IMPORTANT]
> Vous n’avez pas besoin de compte Azure, ni de connecter l’application à un serveur principal. L’application fonctionne de manière indépendante.
> 
> 

* Une fois l’application téléchargée sur votre appareil, faites défiler les liens dans le menu de gauche pour rechercher les ressources utiles concernant Mobile Engagement.
* Nous avons ajouté le [flux RSS de notre service](https://aka.ms/azmerssfeed) dans cette application pour vous tenir informé des dernières mises à jour de produits.
* Vous pouvez également parcourir les exemples de scénarios de notification pour découvrir les types de notifications prises en charge par Mobile Engagement pour chacune des plateformes. Ces notifications peuvent être testées localement. Vous pouvez par exemple cliquer sur les boutons des écrans pour obtenir une expérience de notifications identique à celle dont vous bénéficiez quand vous envoyez les notifications à partir de la plateforme Mobile Engagement.

![Menu de l’application pour Windows][4]

![Menu de l’application pour iOS][5]
![Menu de l’application pour Android][6]

**Téléchargez le code source à partir des liens Github (fournis ci-dessus) :**

* Une fois que vous avez téléchargé le code source, ouvrez-le dans votre environnement de développement, c’est-à-dire XCode pour iOS, Android Studio pour Android et Visual Studio pour Windows.
* Nous vous invitons ensuite à suivre les [étapes de base de l’intégration du SDK](mobile-engagement-windows-store-dotnet-get-started.md) pour pouvoir connecter cette application à sa propre instance de serveur principal Mobile Engagement.
  * Vous devez configurer une chaîne de connexion dans l’application.
  * Vous devez également configurer la plateforme de notifications push pour votre application.
* Vous remarquerez que l’application proprement dite est instrumentée avec Mobile Engagement. Ainsi, quand vous ouvrez l’application après l’avoir connectée au serveur principal, vous pouvez voir la session utilisateur, les activités, les événements, et ainsi de suite, sous l’onglet **Monitor** (Surveiller).
* Vous pouvez également envoyer des notifications à cette application à partir de votre propre instance Mobile Engagement, au lieu de recourir à des notifications locales.
  
  * Ici vous pouvez ajouter votre appareil comme appareil test à l’aide de l’élément de menu **Get the Device ID** (Obtenir l’ID d’appareil) dans l’application. Vous obtenez alors un ID d’appareil, que vous inscrivez ensuite comme appareil test avec l’instance de serveur principal de votre plateforme.
    
    ![ID d’appareil sur Windows][7]
    
    ![ID d’appareil sur iOS][8]
    ![ID d’appareil sur Android][9]

## <a name="key-features-of-the-demo-app"></a>Principales fonctionnalités de l’application de démonstration
* Comme nous l’avons indiqué plus haut, cette application réunit toutes les ressources essentielles liées à Mobile Engagement. Vous pouvez faire défiler les liens dans le menu de gauche.
* Pour chaque plateforme, vous pouvez tester les notifications hors de l’application. Ces notifications peuvent être fournies sous forme de **notification uniquement**, auquel cas le fait de cliquer sur la notification a simplement pour effet d’ouvrir un écran natif de l’application (à l’aide de **liens profonds**), ou sous forme **d’annonce web**, auquel cas vous pouvez fournir du contenu HTML supplémentaire à partir du serveur principal Mobile Engagement qui sera affiché chaque fois que l’utilisateur cliquera sur la notification.
  
    ![Notifications hors de l’application][29]
* Dans iOS, vous devez fermer l’application pour voir les notifications hors de l’application ou les notifications push système. Vous pouvez examiner ici l’implémentation pour ajouter des **boutons d’action** comme ceux ajoutés à cette notification hors de l’application pour les fonctions *Feedback* (Commentaires) et *Share* (Partager) (pour que l’utilisateur puisse prendre des mesures directement à partir de la notification).
  
    ![Notifications hors de l’application sur iOS][11] ![Affichage de notification hors de l’application sur iOS][14]
* Dans Android, les options prises en charge ajoutent un texte multiligne (**Big Text**) ou une image de notification (**Big Picture**) ajouté(e) à la notification parallèlement aux **boutons d’action** (pris en charge par iOS).
  
    ![Notifications hors de l’application sur Android][12] ![Affichage de notification hors de l’application sur Android][15]
* Dans Windows 10, vous pouvez visualiser les notifications telles qu’elles apparaissent sur le PC. Cette notification s’affiche également dans le **Centre de notifications**Windows 10. L’ajout de **boutons d’action** n’est pour le moment pas pris en charge avec le SDK Windows.
  
    ![Notifications hors de l’application sur Windows][10] ![Affichage de notification hors de l’application sur Windows][13]
* Pour chaque plateforme, vous pouvez tester les notifications « dans l’application ». Il s’agit d’une expérience en deux étapes où une fenêtre **Notification** est affichée en premier. Quand vous cliquez dessus, une **annonce**s’affiche en plein écran, comme illustré dans la capture d’écran suivante. Le contenu de cette annonce provient de votre instance de serveur principal Mobile Engagement. Le SDK contient les modèles pour les notifications et les annonces. Vous pouvez facilement les personnaliser, comme illustré dans cette application de démonstration, avec l’ajout de notre logo et de nos couleurs.  
  
    ![Notifications dans l’application sur Windows][16]
  
    ![Notifications dans l’application sur iOS][17]  ![Notifications dans l’application sur Android][18]
  
    **iOS**, **Android**
* Vous pouvez également utiliser la fonction **Catégorie** de Mobile Engagement pour personnaliser cette expérience par défaut. Dans l’application de démonstration, nous avons présenté deux façons courantes de modifier l’expérience des notifications. Notez que la fonction Catégorie n’est pas encore prise en charge dans le SDK Windows.
  
    **Plein écran interstitiel :**
  
    ![Notification dans l’application - catégorie interstitielle][30]
  
    ![Catégorie interstitielle sur iOS][21]     ![Catégorie interstitielle sur Android][22]
  
    **Notification contextuelle :**
  
    ![Notification dans l’application - catégorie contextuelle][31]
  
    ![Notification contextuelle sur iOS][19]    ![Notification contextuelle sur Android][20]

**iOS**, **Android**

* Mobile Engagement prend également en charge un type spécialisé de notification dans l’application appelée **Sondages**. Vous pouvez envoyer rapidement des enquêtes à vos utilisateurs d’application segmentés. Vous pouvez aussi ajouter des questions et des options pour chaque question, comme illustré dans la capture d’écran suivante. Ces éléments sont ensuite présentés à l’utilisateur de l’application comme notification dans l’application.   
  
    ![Notifications d’interrogation][32]
  
    ![Enquête sur Windows][26]
  
    ![Enquête sur iOS][27]   ![Enquête sur Android][28]

**iOS**, **Android**

* Mobile Engagement prend également en charge l’envoi en mode silencieux de notifications **Push de données** . Avec ces notifications, vous pouvez envoyer des données à partir de votre service (comme les données JSON dans l’exemple suivant), que vous pouvez gérer et exploiter directement dans votre application. La modification du prix d’un article de manière sélective à l’aide des notifications Push de données constitue un bon exemple.
  
    ![Notification Push de données][33]
  
    ![Notification Push de données sur Windows][23]
  
    ![Notification Push de données sur iOS][24]  ![Notification Push de données sur Android][25]

**iOS**, **Android**

> [!NOTE]
> Vous pouvez afficher des instructions détaillées pour chacune de ces notifications en cliquant sur le lien **Cliquez ici pour obtenir des instructions sur l’envoi de ces notifications à partir de la plateforme Mobile Engagement** à partir de n’importe quel écran d’exemple de notification.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
