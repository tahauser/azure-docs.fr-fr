---
title: Interface utilisateur d'Azure Mobile Engagement - Analyses
description: En savoir plus sur l’analyse des données historiques de votre application avec Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fceae1ffff40fc525170121181e21726fe2bd3f7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-analyze-historical-data-about-your-application"></a>Analyse des données historiques de votre application
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Cet article décrit l’onglet **ANALYTICS** du portail **Mobile Engagement**. Le portail **Mobile Engagement** sert à surveiller et à gérer vos applications mobiles. Notez que pour utiliser le portail, vous devez tout d’abord créer un compte **Azure Mobile Engagement** .

La section Analyses de l'interface utilisateur fournit des informations globales sur votre application basées sur des données historiques qui sont mises à jour toutes les 24 heures. Les informations sont affichées sur différents tableaux de bord composés de graphiques linéaires/à barres/en secteurs, de grilles et de cartes. Les données peuvent également être téléchargées sous forme de fichiers .csv. La plupart de ces mêmes informations sont disponibles en temps réel dans la section Écran de l'interface utilisateur, et peuvent également être consultées à partir de l'API Analyses.

> [!NOTE]
> De nombreuses sections de l’interface utilisateur du portail **Mobile Engagement** contiennent un bouton **AFFICHER L’AIDE**. Appuyez sur ce bouton pour obtenir des informations contextuelles supplémentaires sur une section.

## <a name="standard-and-custom-analytics"></a>Analyses standard et personnalisées :
Azure Mobile Engagement fournit un ensemble d'informations d'analyses standard, de base, sur vos applications qui peuvent être représentées graphiquement dès que vous intégrez votre application au Kit de développement logiciel. Azure Mobile Engagement permet également de recueillir des informations supplémentaires d'analyses personnalisées que vous désirez obtenir sur le comportement de vos utilisateurs finaux. Pour ce faire, créez un plan de balises des « Tags (app info) » personnalisées, réalisées à partir des **Paramètres** de manière à ce qu'Azure Mobile Engagement puisse collecter ces données supplémentaires pour vous.

## <a name="analytics"></a>Analyse
* Tableau de bord : affiche des informations générales sur vos utilisateurs nouveaux et actifs et leurs tendances.
* Utilisateurs : les utilisateurs sont identifiés par l'identifiant de leur appareil. Cet identifiant est unique pour chaque appareil (un nouvel utilisateur représente en réalité un nouvel appareil). Un utilisateur est considéré comme nouveau sur une période donnée s'il a effectué sa première session durant cette période. Un utilisateur est considéré comme retenu s'il a effectué au moins une session durant les derniers 7 jours. Les utilisateurs actifs sont ceux qui ont effectué au moins une session durant une période donnée. Vous pouvez les trier par mois, par semaine, par jour ou par heure. Tous les graphiques se ressemblent, mais ils vous permettent de filtrer via différentes fonctionnalités, telles que la version de votre application, puis de trier par période. Les informations standard regroupées lors de l’intégration du Kit de développement logiciel (SDK) incluent les informations suivantes : les utilisateurs actifs, le nouvel utilisateur, le nombre de sessions, la durée de chaque session, les informations techniques sur le pays, les habitants, l'emplacement, la langue de l’opérateur, les appareils, le microprogramme, le réseau (Wi-Fi), les versions de l'application et du Kit de développement logiciel (SDK) utilisées par les clients. Cette information peut être consultée en temps réel depuis la section Écran.

> [!NOTE]
> La période est calculée en fonction de la date indiquée dans les paramètres de l'appareil de l'utilisateur, de manière à ce qu'un utilisateur, disposant d'un téléphone sur lequel la date est mal réglée, puisse apparaître dans la mauvaise période.

* Rétention : un utilisateur est considéré comme retenu sur une période donnée s'il a effectué sa première session durant cette période. Vous pouvez modifier les périodes durant lesquelles les utilisateurs retenus (et les nouveaux utilisateurs) sont comptabilisés à l'heure, au jour, à la semaine ou au mois. L'analyse de rétention de l'utilisateur est réalisée sur des cohortes. Une cohorte représente l'ensemble de tous les nouveaux utilisateurs détectés à une période donnée (c'est-à-dire, l'ensemble des utilisateurs qui effectuent leur première session durant cette période). Nous utilisons des cohortes d'un jour, de deux jours, de quatre jours, de sept jours et d'un mois. Azure Mobile Engagement calcule tous les jours, tous les 2 jours, tous les 4 jours, tous les 7 jours ou tous les mois, l'ensemble des utilisateurs qui appartiennent à la cohorte et qui sont toujours actifs (c'est-à-dire l'ensemble des utilisateurs qui ont effectué au moins une session durant la période). Cet ensemble d'utilisateurs est appelé une version de cohorte. (Azure Mobile Engagement peut vous indiquer combien de vos utilisateurs utilisent encore votre application, mais seul le magasin de la plateforme spécifique peut vous signaler combien de vos utilisateurs ont désinstallé votre application (par exemple, GooglePlay, iTunes, Windows Store, etc.)).
* Sessions : une utilisation de l'application par un utilisateur. Des sessions sont générées à partir de la séquence des activités effectuées par les utilisateurs (une activité est généralement associée à l'utilisation d'un écran de l'application, mais cela peut varier en fonction de la manière dont le Kit de développement logiciel a été intégré dans l'application). Un utilisateur ne peut effectuer qu'une activité à la fois : une session commence dès que l'utilisateur démarre sa première activité et elle s'arrête lorsqu'il termine sa dernière. Lorsqu'un utilisateur n'effectue aucune activité durant quelques secondes, sa séquence d'activités est divisée en deux sessions distinctes.
* Activités : le nom de chaque écran de votre application et le temps passé sur chacun d'entre eux. Les activités représentent une option analytique personnalisée qui correspond aux balises d'« informations relatives aux applications » que vous avez mises en place pour votre propre application :
* Chemin d’accès de l’utilisateur : vous indique comment vos utilisateurs naviguent à travers les différentes activités de votre application (écrans). Vous pouvez déplacer le curseur pour ajuster le niveau des détails. Les nœuds bleus représentent les activités de votre application. Leur taille est proportionnelle au temps passé sur chacune d'entre elles. Les nœuds blancs représentent le début et la fin de la session. Les nœuds rouges représentent les incidents. Les liens représentent les transitions existantes entre les différentes activités de votre application (ou entre les activités et les incidents). Cliquez sur un nœud ou sur un lien pour afficher une info-bulle qui vous fournit des informations supplémentaires sur vos données : le temps passé sur un écran spécifique, le nombre de transitions et le pourcentage de transitions de l'activité source à l'activité de destination. (A --- 60 % ---> B signifie qu'une fois l'activité A terminée, les utilisateurs optent, dans 60 % des cas, pour l'activité B). Vous pouvez réorganiser, comme bon vous semble, le graphique pour le rendre plus compréhensible ; sa position est enregistrée chaque fois que vous effectuez une modification. Vous pouvez afficher ou masquer les incidents pour alléger le graphique.
* Événements : des actions spécifiques effectuées par un utilisateur dans l'application. La distribution des événements est représentée par le nombre d'événements effectués par utilisateur et par session. Un événement représente une action instantanée, par exemple, un clic sur un bouton ou la réception d'une notification. (La signification des événements dépend de la manière dont le Kit de développement logiciel a été intégré dans l'application.) Un événement peut survenir durant une session ou bien une tâche,ou peut être autonome.
* Tâches : similaires à des événements, sauf qu'elles se concentrent sur la durée de l'action. Par exemple, des tâches peuvent vous fournir des informations techniques sur la durée de chargement d'un contenu ou d'un appel vers le service Web. Ceci peut également vous indiquer combien de temps un utilisateur a-t-il besoin pour remplir un formulaire, créer un compte, ou réaliser un achat. Une tâche représente la durée d'une tâche, par exemple, la durée d'une tâche de téléchargement ou le temps d'affichage d'une bannière à l'écran. (La signification des tâches dépend de la manière dont le Kit de développement logiciel SDK a été intégré dans l'application.) Les tâches sont généralement associées à des tâches en arrière-plan qui sont effectuées hors de la portée d'une session (c'est-à-dire, sans aucune activité effectuée par l'utilisateur).
* Informations techniques : des informations techniques relatives aux appareils des utilisateurs de votre application que vous pouvez suivre, telles que les paramètres régionaux, l'opérateur, le réseau, l'appareil, le microprogramme, la taille de l'écran des appareils des utilisateurs, la version de votre application et la version du Kit de développement logiciel (SDK) utilisées dans votre application.
* Erreurs : informations relatives aux erreurs techniques survenant dans l'application, qui ne provoquent aucun incident. Une erreur représente un problème instantané, par exemple, une défaillance réseau ou une mauvaise manipulation. (La signification des événements dépend de la manière dont le Kit de développement logiciel a été intégré dans l'application.) Une erreur peut survenir durant une session ou bien une tâche, ou peut être autonome.
* Incidents : informations relatives aux erreurs qui provoquent l'arrêt brutal de votre application. Un incident est un événement inattendu qui survient lorsque certaines fonctionnalités de l'application cessent de fonctionner et que celle-ci doit être arrêtée. Un incident est généralement dû à un bogue se produisant dans l'application.

![Analyse 2][11]

## <a name="accessing-the-retention-overview"></a>Accès à la présentation de la rétention
![Analyse 3][12]

La présentation de la rétention est décomposée en plusieurs cartes, qui affichent chacune la présentation durant une certaine période de rétention. Une période de rétention de 2 jours est utilisée dans cet exemple. Les autres cartes affichent des périodes de rétention de 4 et 7 jours.

## <a name="understanding-the-retention-overview-cards"></a>Explication des cartes de présentation de la rétention
![Analyse 4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Chaque carte est composée de trois éléments principaux :
1. 1 : La cohorte et la période prise en compte
2. 2-4 : La rétention de la période actuelle
3. 5 : Un graphique sparkline de l'historique

### <a name="here-is-detailed-information-about-each-element"></a>Voici des informations détaillées sur chaque élément :
1. Cohorte et période : ce titre indique le type de cohorte. « Période de 2 jours » signifie que nous examinerons ici le comportement des utilisateurs durant 2 jours, l'arrivée de nouveaux utilisateurs durant cette période de 2 jours, et que nous observerons s'ils se connectent durant ces deux jours. L'exemple ci-dessus tient compte de l'activité des utilisateurs observée entre le 21 et le 22 novembre.
2. Il vous indique le taux de rétention, durant le 21 et le 22 novembre, des utilisateurs arrivant le 19 et le 20 novembre. Sur les 3 nouveaux utilisateurs présents entre le 19 le 20 novembre, un seul était actif entre le 21 et le 22 novembre.
3. Cet indicateur visuel fournit les mêmes informations que celles représentées ci-dessus dans ce graphique. (La partie nº 3 de ce cercle indique que cela représente 33 % des utilisateurs.) La couleur donne des informations supplémentaires : la couleur verte vous indique que ce nombre est plus élevé que lors du calcul précédent. La couleur jaune signifie qu'il est identique et la couleur rouge, qu'il est plus faible.
4. Il vous indique les valeurs utilisées pour le calcul.
5. Ceci est un graphique sparkline de l'historique des valeurs de rétention. Il vous permet de découvrir les valeurs précédentes ainsi que leurs évolutions.

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
