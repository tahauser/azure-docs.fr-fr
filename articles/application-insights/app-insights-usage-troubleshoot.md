---
title: "Résolution des problèmes : analyse de l’utilisation dans Azure Application Insights"
description: "Guide de résolution des problèmes : analyse de l’utilisation des sites et des applications avec Application Insights."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/16/2018
ms.author: mbullwin
ms.openlocfilehash: cb5f3052301b23eb10cd6b84ab6fae98bcc7ea18
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-usage-analytics-in-application-insights"></a>Dépannage de l’analyse de l’utilisation dans Application Insights
Vous avez des questions concernant les [outils d’analyse de l’utilisation dans Application Insights](app-insights-usage-overview.md) : [Utilisateurs, Sessions, Événements](app-insights-usage-segmentation.md), [Entonnoirs](usage-funnels.md), [Flux d’utilisateurs](app-insights-usage-flows.md), [Rétention](app-insights-usage-retention.md) ou Cohortes ? Voici quelques réponses.

## <a name="counting-users"></a>Comptage d'utilisateurs
**Les outils d’analyse de l’utilisation indiquent que mon application compte un utilisateur ou une session, mais je sais que mon application compte de nombreux utilisateurs ou sessions. Comment puis-je corriger ces erreurs de comptage ?**

Tous les événements de télémétrie dans Application Insights ont un [ID d’utilisateur anonyme](application-insights-data-model-context.md) et un [ID de session](application-insights-data-model-context.md) définis en tant que deux de leurs propriétés standard. Par défaut, tous les outils d’analyse de l’utilisation comptent les utilisateurs et les sessions en fonction de ces ID. Si ces propriétés standard ne sont pas renseignées par des ID uniques pour chaque utilisateur et chaque session de votre application, le nombre d’utilisateurs et de sessions affiché dans les outils d’analyse de l’utilisation seront incorrects.

Si vous effectuez le suivi d’une application web, la solution la plus simple consiste à ajouter le [Kit SDK JavaScript Application Insights ](app-insights-javascript.md) à votre application, puis à vérifier que l’extrait de code de script est correctement chargé sur chaque page dont vous souhaitez effectuer le suivi. Le Kit SDK JavaScript génère automatiquement les ID d’utilisateur anonyme et de session, puis renseigne les événements de télémétrie à l’aide de ces ID tels qu’ils sont envoyés à partir de votre application.

Si vous effectuez le suivi d’un service web (sans interface utilisateur), [créez un initialiseur de télémétrie qui renseigne les propriétés des ID d’utilisateur anonyme et des ID de session](app-insights-usage-send-user-context.md) selon les notions d’utilisateurs et de sessions uniques de votre service.

Si votre application envoie des [ID d’utilisateur authentifié](app-insights-api-custom-events-metrics.md#authenticated-users), vous pouvez effectuer le comptage en fonction des ID d’utilisateur authentifié dans l’outil Utilisateurs. Dans la liste déroulante « Afficher », sélectionnez l’option « Utilisateurs authentifiés ».

Les outils d’analyse de l’utilisation ne prennent actuellement pas en charge le comptage d’utilisateurs ou de sessions à partir de propriétés autres que les ID d’utilisateur anonyme, les ID d’utilisateur authentifié et les ID de session.

## <a name="naming-events"></a>Nommage d’événements
**Mon application a des milliers de noms d’affichages de page et d’événements personnalisés différents. Il est difficile de les différencier, et les outils d’analyse de l’utilisation s’avèrent souvent inutiles dans ce sens. Comment puis-je résoudre ces problèmes de nommage ?**

Les noms d’affichages de page et d’événements personnalisés sont utilisés par les outils d’analyse de l’utilisation. C’est pourquoi il est crucial de nommer correctement les événements pour obtenir des valeurs exploitables de ces outils. L’objectif est de rechercher un juste milieu entre utiliser trop peu de noms très génériques (« bouton sur lequel un clic a été effectué ») et utiliser trop de noms très spécifiques (« bouton d’édition sur lequel un clic a été effectué sur http://www.contoso.com/index »).

Pour modifier les noms d’affichages de page ou d’événements personnalisés envoyés par votre application, vous devez modifier le code source de l’application, puis redéployer cette dernière. **Toutes les données de télémétrie dans Application Insights sont stockées pendant 90 jours, et elles ne peuvent pas être supprimées**. Par conséquent, le renommage d’événements ne prendra entièrement effet qu’après 90 jours. Durant les 90 jours suivant le renommage, les anciens et les nouveaux noms d’événements apparaîtront dans vos données de télémétrie. Veillez donc à gérer correctement les requêtes et à en informer vos équipes en conséquence.

Si votre application envoie un trop grand nombre de noms d’affichages de page, vérifiez si ces noms sont spécifiés manuellement dans le code ou s’ils sont envoyés automatiquement par le Kit SDK JavaScript Application Insights :

* Si les noms d’affichages de page sont spécifiés manuellement dans le code à l’aide de l’[API `trackPageView`](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), attribuez des noms moins spécifiques. Évitez les erreurs courantes, comme insérer l’URL dans le nom de l’affichage de page. Utilisez à la place le paramètre d’URL de l’API `trackPageView`. Déplacez les autres détails du nom de l’affichage de page dans les propriétés personnalisées.

* Si le Kit SDK JavaScript Application Insights envoie automatiquement les noms d’affichages de page, vous pouvez modifier les titres de vos pages ou configurer l’envoi manuel des noms d’affichages de page. Le Kit SDK envoie par défaut le [titre](https://developer.mozilla.org/docs/Web/HTML/Element/title) de chaque page en tant que nom d’affichage de page. Vous pouvez modifier vos titres de façon à ce qu’ils soient plus généraux, mais tenez compte du possible impact sur les performances SEO et des autres impacts que pourrait avoir cette modification. La spécification manuelle des noms d’affichages de page à l’aide de l’API `trackPageView` remplace automatiquement les noms recueillis, ce qui vous permet d’envoyer un plus grand nombre de noms plus généraux vers les données de télémétrie sans modifier les titres des pages.   

Si votre application envoie un trop grand nombre de noms d’événements personnalisés, modifiez les noms dans le code de manière à ce qu’ils soient moins spécifiques. Une fois encore, évitez d’insérer des URL et autres informations dynamiques ou par page directement dans les noms d’événements personnalisés. Déplacez plutôt ces détails dans les propriétés personnalisées de l’événement personnalisé à l’aide de l’API `trackEvent`. Par exemple, au lieu de `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, nous vous suggérons une solution comme `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de l’analyse de l'utilisation](app-insights-usage-overview.md)

## <a name="get-help"></a>Obtenir de l’aide
* [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/ms-application-insights)

