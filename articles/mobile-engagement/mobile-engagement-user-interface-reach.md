---
title: Interface utilisateur d'Azure Mobile Engagement - Reach
description: Découvrez comment atteindre les utilisateurs de votre application avec des notifications Push grâce à Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d999b83df7d9d467f08ce8ec72468c738e8acfa5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Comment atteindre les utilisateurs de votre application avec des notifications Push
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Cet article décrit l’onglet **REACH** du portail **Mobile Engagement**. Le portail **Mobile Engagement** sert à surveiller et à gérer vos applications mobiles. Notez que pour utiliser le portail, vous devez tout d’abord créer un compte **Azure Mobile Engagement** . Pour plus d'informations, consultez [Create an Azure Mobile Engagement account](mobile-engagement-create.md).

La section Reach de l'interface utilisateur est l'outil de gestion des campagnes Push qui vous permet de créer, modifier, activer, terminer ou surveiller et obtenir des statistiques relatives aux campagnes de notifications Push et des fonctionnalités. Vous pouvez accéder à celles-ci via l'API Reach (ainsi que certains éléments de l'API Push de bas niveau). Que vous utilisiez ces API ou l'interface utilisateur, il vous faudra intégrer Azure Mobile Engagement et Reach à votre application pour chaque plateforme à l'aide du Kit de développement logiciel (SDK) avant de pouvoir utiliser les campagnes Reach.

> [!NOTE]
> De nombreuses sections de l’interface utilisateur du portail **Mobile Engagement** contiennent un bouton **AFFICHER L’AIDE**. Appuyez sur ce bouton pour obtenir des informations contextuelles supplémentaires sur une section.
> 
> 

## <a name="four-types-of-push-notifications"></a>Quatre types de notifications Push
1. Annonces : elles vous permettent d'envoyer aux utilisateurs des messages publicitaires qui les redirigent vers un autre emplacement dans votre application ou les envoient vers une page Web ou un Store en dehors de votre application. 
2. Sondages : ces notifications vous permettent de regrouper des informations sur les utilisateurs finaux en leur posant des questions.
3. Push de données : ces notifications vous permettent d'envoyer un fichier de données binaire ou base 64. Les informations contenues dans un Push de données sont envoyées à votre application pour modifier l'expérience actuelle des utilisateurs de votre application. Votre application doit être en mesure de traiter les données dans un Push de données.

## <a name="campaign-details"></a>Informations relatives à la campagne
Vous pouvez modifier, cloner, supprimer ou activer les campagnes qui n'ont pas encore été activées en plaçant le curseur sur leurs noms ou en cliquant pour les ouvrir. Vous pouvez cloner les campagnes qui ont déjà été activées en plaçant le curseur sur leurs noms ou en cliquant pour les ouvrir. Toutefois, vous ne pouvez pas modifier une campagne une fois celle-ci activée.

![Reach1][18]

## <a name="reach-feedback"></a>Commentaire Reach
Cliquez sur **Statistiques** pour afficher les détails d'une campagne Reach. L'affichage **Simple** fournit une représentation visuelle sous la forme d'un graphique à barres en colonnes sur ce qui s'est passé après l'activation d'une campagne. L'affichage **Avancé** fournit des détails plus précis sur la campagne Push. Ces détails ne sont pas disponibles si vous envoyez une campagne de test, c'est-à-dire une notification Push envoyée à un appareil de test. Voici comment interpréter ces détails :

1. **Push** : indique le nombre de messages envoyés aux appareils. Ce nombre dépend du public cible spécifié lors de la création de la campagne Push. Si vous ne spécifiez aucun public cible, cette notification Push sera envoyée à tous les appareils inscrits. Comme tous les autres services Push, nous n'envoyons pas les notifications directement aux appareils. Nous les envoyons aux services de notification Push (PNS - APNS/GCM/WNS) spécifiques à la plateforme respective afin qu'ils transfèrent les notifications aux appareils. 
2. **Remis** : indique le nombre de messages remis avec succès par le PNS à l'appareil et reconnus comme reçus par le Kit de développement logiciel (SDK) Mobile Engagement. 
   
   *Raisons pour lesquelles le nombre de messages remis est inférieur au nombre de notifications Push :*
   
   1. Si l'utilisateur a désinstallé l'application de l'appareil mais que le service PNS ne le sait pas au moment où nous envoyons la notification Push au service PNS, alors le message est supprimé.
   2. Si l'appareil dispose de l'application mais que les appareils eux-mêmes étaient hors ligne pendant de longues périodes, alors la remise du message à l'appareil par le service PNS échoue. 
   3. Si le message est remis à l'appareil mais que le Kit de développement logiciel (SDK) Mobile Engagement dans l'application ne reconnaît pas le contenu du message, alors le message est supprimé. Cela peut se produire si la personnalisation de la notification dans l'application génère une exception que nous relevons dans le Kit de développement logiciel (SDK) et que nous supprimons le message. Cela peut également se produire si l'application sur l'appareil utilise une version du Kit de développement logiciel (SDK) Engagement Mobile qui n'est pas en mesure de comprendre la version la plus récente du message Push envoyé à partir de la plateforme. Cela ne s'applique que lorsque l'application a été mise à niveau après la distribution de la notification par la plateforme de services. L’onglet **Avancé** indique le nombre de messages supprimés. 
   4. Sur les appareils iOS, la remise des messages peut parfois échouer si la batterie de l’appareil est faible ou que l’application consomme trop d’énergie pendant le traitement des notifications distantes. Il s’agit d’une limitation des appareils iOS.   
3. **Affichés** : indique le nombre de messages qui sont affichés correctement à l’utilisateur de l’application sur l’appareil sous la forme d’une notification système Push/hors application dans le centre de notification ou une notification dans l’application au sein de l’application mobile.  L’onglet **Avancé** indique le nombre de notifications système et le nombre de notifications dans l’application. 
   
   *Raisons pour lesquelles le nombre d’éléments affichés est inférieur au nombre d’éléments remis (en attente d’être affichés)*
   
   1. Si la campagne de notification avait une date de fin, alors il est possible que la notification ait été remise, mais au moment de l’ouvrir et de l’afficher sur l’application de l’utilisateur, elle avait déjà expiré et donc ne s’est jamais affichée.   
   2. Si la notification est une notification dans l’application, elle est alors uniquement affichée lorsque l’utilisateur de l’application ouvre celle-ci. Dans le cas où l’utilisateur n’aurait pas ouvert l’application, le kit de développement logiciel (SDK) signale que la notification a été remise mais pas encore affichée puisque l’application n’a pas été ouverte. 
   3. S’il s’agit d’une notification dans l’application et qu’elle est configurée pour s’afficher sur un écran spécifique ou lors d’une activité spécifique, alors elle sera également signalée comme remise mais ne sera véritablement remise qu’après l’ouverture par l’utilisateur de l’application sur un écran spécifique. 
4. **Interactions utilisateur** : indique le nombre de messages avec lesquels l’utilisateur de l’application a eu une interaction et inclut les messages qui font l’objet d’une action ou qui sont abandonnés. 
   
   * *L'utilisateur de l'application peut agir sur une notification de l'une des manières suivantes :*
     
     1. Si la notification est une notification système/hors application ou une notification dans l'application envoyée comme notification uniquement, alors l'utilisateur de l'application clique sur la notification.
     2. Si la notification est une notification dans l'application avec un texte ou un affichage web ou des sondages, alors l'utilisateur de l'application clique sur le bouton Action dans la notification.
     3. Si la notification est une notification dans l'application avec un affichage web, alors l'utilisateur de l'application clique sur une URL dans l'affichage web [Android uniquement]
   * *L'utilisateur de l'application peut quitter une notification de l'une des manières suivantes :*
     
     1. En cliquant sur le bouton Fermer directement dans la notification. 
     2. En balayant ou en supprimant la notification. 
     3. Les notifications dans l'application avec du texte/contenu web et les sondages sont généralement affichées à l'utilisateur de l'application dans un processus en deux étapes. L'utilisateur reçoit tout d'abord une notification et lorsqu'il clique dessus, le contenu du texte/web/sondage s'affiche. L'utilisateur de l'application peut quitter une notification dans n'importe laquelle de ces étapes et les détails dans la vue Avancée indiquent cela. 
5. **Actionnés** : indique le nombre de messages qui ont explicitement fait l’objet d’une action par l’utilisateur de l’application. Il s'agit du nombre le plus intéressant car il indique le nombre d'utilisateurs de l'application intéressés par le message transmis dans la notification. 

> [!NOTE]
> Sur les plateformes iOS et Windows, si l'utilisateur dispose de l'application ouverte et que la campagne était du type « À tout moment », il est possible que les notifications hors application et dans l'application s'affichent en même temps. Cela peut entraîner un nombre plus élevé pour le paramètre Affichés que pour le paramètre Remis. Si l'utilisateur interagit avec la notification ou qu'il exécute une action dessus, alors même le nombre Interactions utilisateur/Actionnés peut être supérieur au nombre Remis. 
> 
> 

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

