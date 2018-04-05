---
title: Interface utilisateur d'Azure Mobile Engagement - Procédure de Reach
description: Présentation de l'interface utilisateur d'Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0c536d378023ee4a9675bb2d67262fb485c8052b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Procédure de mise en route pour l’utilisation et la gestion des push en vue d’atteindre vos utilisateurs finaux
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Une fois que le Kit de développement logiciel (SDK) est totalement intégré à votre application, vous pouvez commencer à utiliser la section Reach de l'interface utilisateur pour envoyer des notifications Push aux utilisateurs de votre application.  

## <a name="do-your-first-push-notification-campaign"></a>Lancer votre première campagne de notification push
* Vérifiez que votre module Couverture est intégré à votre application avec le SDK. 
* Sélectionnez votre application

![Premier 1][1]

* Accédez à la section « Couverture », puis cliquez sur « Nouvelle annonce »

![Premier 2][2]

* Créez une campagne et donnez-lui un nom
  
![Premier 3][3]

* Sélectionnez la façon dont la notification doit être remise, par exemple, dans l'application uniquement

![Premier 4][4]

* Créez le message à envoyer

![Premier 5][5]

* Vous pouvez donner un titre à la notification (facultatif).
* Écrivez le contenu du message push.
* Vous pouvez télécharger une image. N'oubliez pas que la taille du fichier ne peut pas dépasser 32 768 octets.
* Vous avez également la possibilité de sélectionner d'autres options, mais pour les besoins de ce didacticiel, nous les aborderons plus tard.
* Sélectionnez Notification uniquement comme type de contenu

![Premier 6][6]

* Créez votre campagne push. Elle apparaîtra dans votre liste de campagnes.

![Premier 7][7]

## <a name="test-your-push-notification-campaign"></a>Tester votre campagne de notification push
![Test 1][8]

* Enregistrez votre appareil.
* Cliquez sur la case à cocher de l'appareil auquel vous voulez envoyer la notification push.
* Cliquez sur le bouton « Test » pour envoyer la notification push à l'appareil.

![Test 2][9]

* Activez la campagne

![Test 3][10]

* Maintenant que vous avez créé votre campagne, vous devez l'activer pour que la notification soit envoyée à vos utilisateurs.

## <a name="send-personalized-pushes"></a>Envoyer des notifications push personnalisées
* Cet exemple crée une opération push à l'endroit où un code remise personnalisé est entré dans la notification push.

![Personnalisé 1][11]

La personnalisation consiste à remplacer un marqueur d'une balise d'informations de l'application. Vous devez donc d'abord vous assurer que les informations d'application appropriées sont définies pour l'utilisateur. Dans cet exemple, la balise d'informations de l'application rebate_code est définie pour les utilisateurs ciblés.
Comme vous pouvez le constater ci-dessus, le contenu de la notification push inclut le marqueur ${rebate_code}, qui indique qu'il doit être remplacé par le contenu réel de la balise d'informations de l'application.

> [!WARNING]
> si la balise d'informations de l'application n'est pas définie pour l'utilisateur, ce dernier ne recevra pas la notification push.

* Résultat

![Personnalisé 2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Vous pouvez personnaliser le texte de votre notification
![Personnalisé 3][13]

* Notamment le titre de la notification
* et le contenu du message.
* Choisissez le type d'annonce (vue de texte ou vue web)

![Personnalisé 4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Le corps d'une annonce peut également être personnalisé avec les éléments suivants :
* l'URL de l'action, en cas de personnalisation de la page d'accueil ;
* le titre ;
* le corps du message.

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Différencier votre notification push (dans ou en dehors de l'application)
* Choisissez le type de notification que vous allez envoyer, sélectionnez votre application, accédez à la section « Couverture », sélectionnez ou créez une campagne push et accédez à la section « Notification ».
* Cliquez sur le « mode de remise » souhaité.
* Cochez la case « Restreindre les activités » si vous voulez que la notification se produise pour des activités spécifiques (écrans).

![Différentiation 1][15]

### <a name="out-of-app-only-delivery-mode"></a>Mode de remise « Hors de l'application uniquement »
![Différentiation 2][16]

Le mode de remise « Hors de l'application uniquement » envoie une notification push quand l'application est fermée. Il s'agit de la notification push standard.
Quand vous sélectionnez « Hors de l'application uniquement », vous devez déjà avoir fourni les certificats de la plateforme sur laquelle est créée votre application (service APN ou GCM).

### <a name="see-also"></a>Voir aussi
* [Service de notification Push d’Apple – Certificats](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging – Certificat](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>Mode de remise « Dans l'application uniquement »
![Différentiation 3][17]

Le mode de remise « Dans l'application uniquement » envoie la notification push pendant l'exécution de l'application.
Pour cette notification, il est inutile de passer par le service APN ou le système GCM.
Vous pouvez utiliser le système de remise dans l'application pour toucher vos utilisateurs finaux.
Vous pouvez entièrement personnaliser la notification et décider dans quelle activité (écran) la notification va apparaître.

### <a name="anytime-delivery-mode"></a>Mode de remise « À tout moment »
Vous pouvez choisir le mode de remise « À tout moment », qui vous permet de toucher vos utilisateurs finaux, que l'application soit en cours d'exécution ou non.
Quand vous sélectionnez « À tout moment », vous devez déjà avoir fourni les certificats de la plateforme sur laquelle est créée votre application (service APN ou GCM). 

## <a name="schedule-a-push-campaign"></a>Planifier une campagne push
### <a name="plan-to-start-a-campaign"></a>Planifiez le début de la campagne
![Planification 1][18]

Nous sommes le 21 mars et vous devez diffuser une annonce le 22 mars à minuit. Vous n'êtes pas obligé de rester devant l'interface pour envoyer une notification push ! Vous pouvez planifier à l'avance la minute exacte à laquelle les notifications seront envoyées.

* Décochez la case « Aucune » et sélectionnez une heure de début 
* Choisissez la date et l'heure auxquelles vous voulez démarrer la campagne push.

### <a name="plan-to-end-a-campaign"></a>Planifiez la fin de la campagne
![Planification 2][19]

Vous voulez que votre campagne s'arrête le 25 mars à 15 h 00, mais vous savez que vous ne serez pas là pour le faire.
Vous n'êtes pas obligé de rester devant l'interface pour envoyer une notification push ! Vous pouvez planifier à l'avance la minute exacte à laquelle votre campagne s'arrêtera.

* Cochez la case « Aucune » ou sélectionnez une heure de fin
* Choisissez la date et l'heure auxquelles vous voulez arrêter la campagne push.

### <a name="end-a-campaign-manually"></a>Arrêter manuellement une campagne
![Planification 3][20]

Par défaut, la case « Aucune » est cochée.
Cela signifie que la campagne démarrera ou s'arrêtera dès que l'activerez ou la désactiverez dans la section Couverture.

> [!NOTE]
> Les campagnes marketing créées sans date de fin stockent le push localement sur le périphérique et l'affichent lors de la prochaine ouverture de l'application, même si la campagne est terminée manuellement.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Renforcer une notification push avec une vue de texte
### <a name="what-is-a-text-view"></a>Qu'est-ce que la vue de texte ?
![Vue de texte 1][21]

Une vue de texte est une fenêtre contextuelle contenant du texte. Cette fenêtre contextuelle apparaît une fois que l'utilisateur final a cliqué sur la notification push.
Une vue de texte vous permet de présenter plus de contenu à l'utilisateur final. C'est également une opportunité de présenter un appel à l'action, par exemple, l'accès à une page de votre application, la redirection vers une boutique d'applications, l'ouverture d'une page web, l'envoi d'un message électronique, le lancement d'une recherche géo-localisée, etc.

### <a name="example-text-view"></a>Exemple : vue de texte
* Créez votre campagne de notification push dans la section « Couverture » et donnez un nom à votre campagne

![Vue de texte 2][22]

* Écrivez le message qui s'affichera dans la notification.
* Sélectionnez « Texte » comme type de contenu de l'annonce

![Vue de texte 3][23]

> [!NOTE]
> Quand vous envoyez une vue de texte, elle est toujours précédée d’une notification. 

* Définissez le texte (une fois que vous avez sélectionné l'annonce textuelle, une sous-section apparaît dans laquelle vous pouvez définir le texte à afficher.)

![Vue de texte 4][24]

* Écrivez le titre qui apparaîtra en haut du message.
* Écrivez le contenu principal de la vue de texte.
* Écrivez le contenu qui s'affichera sur le bouton d'action (un bouton d'action permet à l'application d'effectuer une action spécifique, comme l'ouverture d'une page de l'application, la redirection vers une boutique d'applications ou tout type de sources que vous pouvez fournir).
* Écrivez le contenu qui s'affichera sur le bouton Quitter (en cliquant sur le bouton Quitter, la vue de texte disparaît.)
* Créez votre campagne de notification push. Elle apparaîtra dans votre liste de campagnes.

![Vue de texte 5][25]

* Activez votre campagne de notifications push pour envoyer la vue de texte à vos utilisateurs.

![Vue de texte 6][26]

* Résultat

![Vue de texte 7][27]

* L'utilisateur reçoit la notification et clique dessus.
* La vue web apparaît sous forme de fenêtre contextuelle dans laquelle l'utilisateur peut interagir.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Renforcer une notification push avec une vue web
### <a name="what-is-a-web-view"></a>Qu'est-ce qu'une vue web ?
![Vue web 1][28]

Une vue web est une fenêtre contextuelle avec du contenu web. Cette fenêtre contextuelle apparaît une fois que l'utilisateur final a cliqué sur la notification push.
Une vue web favorise l'interaction avec l'utilisateur final.
C'est également une opportunité de présenter un appel à l'action, par exemple, la redirection vers une boutique d'applications, l'ouverture d'une page web, l'envoi d'un message électronique, le lancement d'une recherche géo-localisée, etc.

### <a name="example-web-view"></a>Exemple : vue web
* Créez votre campagne de notification push dans la section « Couverture » et donnez un nom à votre campagne.

![Vue web 2][29]

* Écrivez le message qui s'affichera dans la notification.
* Sélectionnez « Web » comme type de contenu de l'annonce

![Vue web 3][30]

### <a name="about-announcement-types"></a>À propos des types d'annonce :
* Notification uniquement : il s’agit d’une simple notification standard. Cela signifie que si un utilisateur clique dessus, aucun affichage supplémentaire n'apparaîtra. Seule l'action qui y est associée s'affichera.
* Annonce texte : il s’agit d’une notification qui invite l’utilisateur à regarder une vue de texte.
* Annonce web : il s’agit d’une notification qui invite l’utilisateur à regarder une vue web.
  Sélectionnez le contenu « Annonce web ».

> [!NOTE]
> quand vous envoyez une vue web, elle est toujours précédée d'une notification.

* Définissez le contenu web (une fois que vous avez sélectionné le contenu de l'annonce web, une sous-section apparaît dans laquelle vous pouvez définir le contenu de la vue web à afficher.)

![Vue web 4][31]

* Écrivez le titre qui apparaîtra en haut du message (facultatif).
* Écrivez ici votre code HTML.
* Cliquez sur le bouton du mode d'édition de la source pour basculer l'affichage et découvrir à quoi ressemble votre annonce.
* Écrivez le contenu qui s'affichera sur le bouton d'action (un bouton d'action permet à l'application d'effectuer une action spécifique, comme l'ouverture d'une page de l'application, la redirection vers une boutique d'applications ou tout type de sources que vous pouvez fournir).
* Écrivez le contenu qui s'affichera sur le bouton Quitter (en cliquant sur le bouton Quitter, la vue web disparaît.)
* Résultat

![Vue web 5][32]

* L'utilisateur reçoit la notification et clique dessus.
* La vue web apparaît sous forme de fenêtre contextuelle dans laquelle l'utilisateur peut interagir.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

