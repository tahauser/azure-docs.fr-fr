---
title: Interface utilisateur d'Azure Engagement Mobile - Accueil
description: Apprenez à gérer votre application et vos projets existants à l'aide d'Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c0379e51a30927af88367fc687d8f8ce66eb827f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-your-existing-application-and-projects"></a>Gestion de votre application et de vos projets existants
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Cet article décrit la page **d’accueil** du portail **Mobile Engagement**. Le portail **Mobile Engagement** sert à surveiller et à gérer vos applications mobiles. Notez que pour utiliser le portail, vous devez tout d’abord créer un compte **Azure Mobile Engagement** . 

Pour accéder à la page d’accueil, cliquez sur **Accueil** dans la partie supérieure gauche de la page. La page contient la liste de toutes vos applications qui font partie de la collection choisie. Sur cette page, vous bénéficiez d’une rapide vue d’ensemble de vos applications.

La page d’accueil contient également tous vos projets qui peuvent inclure toute application se trouvant dans votre compte. Notez que toute personne peut accéder à la page d’accueil de l’interface utilisateur en créant un compte. Toutefois, vous devez accorder une autorisation aux autres utilisateurs pour qu’ils puissent accéder à vos applications personnalisées dans **Mes projets**.

Vous pouvez également afficher le tableau comparatif pour les applications sélectionnées. Sinon, choisissez d’afficher le tableau comparatif pour les applications sélectionnées dans un projet.

![Accueil 1][0]

## <a name="my-applications"></a>Mes applications
La vue d’ensemble rapide de vos applications vous permet de sélectionner les applications que vous souhaitez ouvrir pour afficher les options détaillées du ruban. Vous pouvez cliquer sur le nom de votre application pour revenir à l'emplacement du ruban que vous avez visité le plus récemment dans votre application, ou cliquer sur l'icône d'engrenage pour accéder directement à la page « Paramètres » de votre application. Vous pouvez rechercher, filtrer ou trier les informations affichées sur les tables d'application. Vous pouvez également glisser-déposer les en-têtes de colonne pour modifier l'ordre.

Entre autres choses, la vue d’ensemble de vos applications inclut :

* **Tendances des nouveaux utilisateurs**: évolution des nouveaux utilisateurs au cours des deux dernières semaines.
* **Utilisateurs actifs**: nombre d’utilisateurs actifs au cours des 30 derniers jours.
* **Tendance des utilisateurs actifs**: évolution des utilisateurs actifs au cours des deux dernières semaines.
* **Sessions**: une session désigne une utilisation de l’application effectuée par un utilisateur, à partir du moment où il commence à l’utiliser jusqu’à ce qu’il arrête.
* **Tendances des sessions** : évolution des sessions au cours des deux dernières semaines.

Une fois que vous cliquez sur une application, vous pouvez démarrer la surveillance et la gestion de vos applications via l’interface utilisateur. Par exemple :     

* [Surveillance des données en temps réel concernant votre application](mobile-engagement-user-interface-monitor.md)
* [Analyse des données historiques de votre application](mobile-engagement-user-interface-analytics.md)
* [Création et gestion des segments d’utilisateurs afin d’identifier les modèles d’utilisation](mobile-engagement-user-interface-segments.md)
* [Interaction avec les utilisateurs de votre application à l’aide de notifications Push](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>Mes projets :
Vous pouvez utiliser des projets pour grouper vos applications et accorder des autorisations d’accès à vos applications à d’autres utilisateurs. Accordez des autorisations à d’autres utilisateurs en indiquant leur adresse de messagerie. Le bouton **Nouveau projet** permet de créer un projet en entrant uniquement le « nom » et la « description » de votre projet. Une fois un projet créé, vous pouvez cliquer sur le nom du projet pour modifier le nom et la description de votre produit et sélectionner toutes les applications que vous souhaitez voir dans ce projet.

![Accueil 6][60]

Les rôles sont les suivants :

* **Observateur**: un observateur est un utilisateur qui peut voir uniquement les applications associées à un projet. Un observateur permet d'accéder aux données de l'analyse et du moniteur et d'examiner les résultats Reach. Un observateur ne peut pas modifier d'informations, ni gérer des applications ou des utilisateurs. Un observateur ne peut pas créer ou modifier le statut de la campagne Reach.
* **Développeur**: un développeur est un utilisateur qui peut faire tout ce qu’un observateur peut effectuer, ainsi que gérer des applications. Un développeur peut activer et désactiver les applications, modifier les informations des applications (telles que le package et la signature) et créer des campagnes Reach. Un développeur ne peut pas gérer les utilisateurs.
* **Administrateur**: un administrateur est un utilisateur qui peut faire tout ce qu’un développeur peut effectuer, ainsi que gérer les utilisateurs. Un administrateur peut inviter des utilisateurs à participer à un projet, modifier les rôles des utilisateurs et les informations du projet. Les autorisations de niveau application peuvent également être définies dans « paramètres ».

Cliquez sur un projet pour afficher toutes les applications qui font partie de ce projet. L’image suivante montre le tableau comparatif pour les applications sélectionnées.

![Accueil 2][3]

## <a name="see-also"></a>Voir aussi
* [Concepts][Link 6]
* [Service de guide de résolution des problèmes][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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
