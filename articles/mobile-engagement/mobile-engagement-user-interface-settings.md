---
title: "Interface utilisateur d'Azure Mobile Engagement : Paramètres"
description: Découvrez comment gérer les paramètres globaux de votre application grâce à Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 31df9f151febcb7e5f0f2cd4a83053b3aac416a3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>Comment gérer les paramètres globaux de votre application
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Les options de menu **Paramètres** disponibles pour une application dépendent de la plateforme de l’application et des autorisations qui vous ont été accordées pour cette application. Les paramètres suivants sont inclus : Détails, Projets, Native Push, Push Speed, Balise (Information sur l’application) et Pression commerciale. L’option de menu Balise (Informations sur l’application) peut être gérée par votre application (à l’aide du Kit de développement logiciel (SDK)) ou par votre backend (à l’aide de l’API d’appareil). 

> [!NOTE]
> De nombreuses sections de l’interface utilisateur du portail **Mobile Engagement** contiennent un bouton **AFFICHER L’AIDE**. Appuyez sur ce bouton pour obtenir des informations contextuelles supplémentaires sur une section.
> 
> 

## <a name="details"></a>Détails
Permet de modifier le nom et la description de votre application, d’afficher le propriétaire de votre application et vos autorisations de rôle. 

La configuration de l’analytique permet d’afficher ou de modifier le jour du début de semaine et la durée de rétention en jour(s).

  ![settings1][46]

## <a name="projects"></a>Projets
Vous pouvez sélectionner tous les projets que vous souhaitez voir apparaître dans votre application. 

Vous pouvez également rechercher un projet et afficher le nom, la description, le propriétaire et vos autorisations de rôle pour n'importe quel projet dont votre application fait partie.

Pour plus d’informations, consultez [Documentation sur l’interface utilisateur - Accueil][Link 13]

  ![settings3][48]

## <a name="native-push"></a>Native Push
Vous pouvez inscrire un nouveau certificat ou supprimer un certificat existant pour une utilisation avec Native Push. Native Push permet à Azure Mobile Engagement de transmettre des opérations push à votre application à tout moment, même lorsqu'elle n'est pas en cours d'exécution. 

Après avoir fourni des informations d'identification ou des certificats pour au moins un service Native Push, vous pouvez sélectionner « Tout le temps » lors de la création de campagnes Reach et également utiliser le paramètre « Notification » dans l'API de PUSH.

### <a name="apple-push-notification-service-apns"></a>Service de notifications Push Apple (APNS)
Pour activer Native Push à l'aide du Service de notifications Push Apple (APNS), vous devez enregistrer votre certificat. Vous devrez spécifier le type de certificat, soit de développement (DEV), soit de production (PROD). Vous allez ensuite devoir charger votre certificat et définir son mot de passe.

Pour plus d’informations, consultez [Documentation du Kit de développement logiciel (SDK) - iOS - Préparation de votre application pour les notifications Push Apple][Link 5]

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>Service de notifications Push Windows (WPNS)
Pour activer Native Push à l'aide du Service de notifications Push Windows (WPNS), vous devez fournir les informations d'identification de votre application. Vous aurez besoin de votre ID de sécurité (SID) de Package et la clé secrète.

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging pour Android (GCM)
Pour activer le Push Native à l'aide de GCM, vous devez suivre les instructions fournies par Google. Puis vous devez coller une clé d'API simple de serveur, configurée sans restriction d'adresse IP. Requiert l'intégration avec le Kit de développement logiciel (SDK) pour Android v1.12.0 +.

Pour plus d'informations, consultez les pages suivantes : 

* [Documentation du Kit de développement logiciel (SDK) Android - Intégrer GCM][Link 5]
* [Guide de développement Google GCM](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Amazon Device Messaging pour Android (ADM)
Pour activer Native Push avec Amazon Device Messaging (ADM), vous devez fournir des <OAuth credentials> Amazon qui consistent en un ID Client et Secret Client (requiert l'intégration avec le Kit de développement logiciel (SDK) pour Android v2.1.0 +).

Pour plus d'informations, consultez les pages suivantes : 

* [Documentation du Kit de développement logiciel (SDK) Android - Intégrer ADM][Link 5]
* [Documentation pour les développeurs Amazon ADM](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>Push Speed
Vous pouvez afficher la vitesse de transmission actuelle de votre application et définir la vitesse de transmission de votre application.

  ![settings7][52]

## <a name="tag-app-info"></a>Balise (Informations sur l’application)
![settings11][56]

## <a name="commercial-pressure"></a>Pression commerciale
![settings12][57]

## <a name="see-also"></a>Voir aussi
* [Concepts][Link 6]
* [Service de guide de résolution des problèmes][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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

