---
title: "Qu’est-ce qu’Azure Application Insights ? | Microsoft Docs"
description: "Gestion des performances et suivi de l’utilisation de votre application web en ligne.  Détectez, triez et diagnostiquez les problèmes et découvrez comment votre application est utilisée."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 05/14/2017
ms.author: mbullwin
ms.openlocfilehash: 2e2a9e8491ad56bcbc42be64729715016f7ed17b
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-application-insights"></a>Présentation d’Application Insights
Application Insights est un service extensible de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes. Utilisez-le pour analyser votre application web en direct. Ce service détecte automatiquement les problèmes de performances. Il intègre de puissants outils d’analyse pour vous aider à diagnostiquer les problèmes et à comprendre ce que font les utilisateurs avec votre application.  Il a été conçu pour vous permettre d’améliorer continuellement les performances et la convivialité. Il fonctionne avec des applications sur un large éventail de plates-formes, notamment .NET, Node.js et J2EE, hébergées sur site ou dans le cloud. Il s’intègre à votre processus DevOps et offre des points de connexion à un large éventail d’outils de développement. Il peut surveiller et analyser les données de télémétrie des applications mobiles en s’intégrant à Visual Studio App Center et à HockeyApp.

![Créez un graphique des statistiques d'activité de l’utilisateur ou explorez des événements spécifiques.](./media/app-insights-overview/00-sample.png)

[Consultez l’animation de présentation](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## <a name="how-does-application-insights-work"></a>Comment fonctionne Application Insights ?
Vous installez un petit package d’instrumentation dans votre application puis configurez une ressource Application Insights dans le portail Microsoft Azure. L’instrumentation surveille votre application et envoie les données de télémétrie au portail. (L’application peut s’exécuter partout, elle n’a pas besoin d’être hébergée dans Azure.)

Vous pouvez instrumenter non seulement l’application de service web, mais aussi tous les composants d’arrière-plan et le code JavaScript des pages web elles-mêmes. 

![L’instrumentation Application Insights dans votre application envoie la télémétrie à votre ressource Application Insights.](./media/app-insights-overview/01-scheme.png)


En outre, vous pouvez extraire des données de télémétrie des environnements hôtes, notamment des compteurs de performances, des diagnostics Azure ou des journaux Docker. Vous pouvez également configurer des tests web qui envoient régulièrement des demandes synthétiques à votre service web.

Tous ces flux de télémétrie sont intégrés dans le Portail Azure, où vous pouvez appliquer des outils puissants d’analyse et de recherche aux données brutes.


### <a name="whats-the-overhead"></a>Quelle est la surcharge ?
L'impact sur les performances de votre application est très faible. Le suivi des appels n’entraîne aucun blocage, et les appels sont regroupés par lots et envoyés dans un thread séparé.

## <a name="what-does-application-insights-monitor"></a>Que surveille Application Insights ?

Application Insights est destiné à l’équipe de développement et permet de comprendre les performances et l’utilisation de votre application. Il analyse les éléments suivants :

* **Taux de demandes, temps de réponse et taux d’échec** : identifiez les pages les plus consultées, à quel moment de la journée, et déterminez où se trouvent vos utilisateurs. Identifiez les pages qui offrent les meilleures performances. Si vos temps de réponse et votre taux d’échec augmentent lorsqu’il y a plus de requêtes, vous avez peut-être un problème de ressources. 
* **Taux de dépendance, temps de réponse et taux d’échec** : déterminez si des services externes vous ralentissent.
* **Exceptions** : analysez les statistiques agrégées, ou choisissez des instances en particulier et explorez l’arborescence des appels de procédure et les requêtes connexes. Les exceptions de serveur et de navigateur sont signalées.
* **Consultations de pages et performances de chargement** : indiquées par le navigateur de vos utilisateurs.
* **Appels AJAX** à partir de pages web : taux, temps de réponse et taux d’échec.
* **Nombre de sessions et d’utilisateurs**.
* **Compteurs de performances** de vos ordinateurs serveurs Windows ou Linux, par exemple le processeur, la mémoire et l’utilisation du réseau. 
* **Diagnostics d’hébergement** de Docker ou Azure. 
* **Journaux de suivi des diagnostics** de votre application : pour pouvoir mettre en corrélation les événements de suivi avec les demandes.
* **Mesures et événements personnalisés** que vous écrivez vous-même dans le code client ou serveur, pour effectuer le suivi des événements commerciaux tels que les articles vendus ou les matchs gagnés.

## <a name="where-do-i-see-my-telemetry"></a>Où trouver mes données de télémétrie ?

Il existe de nombreuses manières d’explorer vos données. Consultez les articles suivants :

|  |  |
| --- | --- |
| [**Détection intelligente et alertes manuelles**](app-insights-proactive-diagnostics.md)<br/>Les alertes automatiques s’adaptent aux habitudes télémétriques normales de votre application et se déclenchent lorsqu’un comportement inhabituel est détecté. Vous pouvez également [définir des alertes](app-insights-alerts.md) sur des niveaux particuliers de mesures personnalisées ou standard. |![Exemple d’alerte](./media/app-insights-overview/alerts-tn.png) |
| [**Mise en correspondance d’applications**](app-insights-app-map.md)<br/>Les composants de votre application, avec des alertes et des mesures clés. |![Mise en correspondance d’applications](./media/app-insights-overview/appmap-tn.png)  |
| [**Profileur**](app-insights-profiler.md)<br/>Examinez les profils d’exécution des requêtes échantillonnées. |![Profileur](./media/app-insights-overview/profiler.png) |
| [**Analyse de l’utilisation**](app-insights-usage-overview.md)<br/>Analysez la rétention et la segmentation ds utilisateurs.|![Outil de rétention](./media/app-insights-overview/retention.png) |
| [**Recherche de diagnostic pour les données d’instance**](app-insights-diagnostic-search.md)<br/>Cherchez et filtrez les événements, comme les requêtes, les exceptions, les appels de dépendance, les suivis de journaux et les affichages de pages.  |![Télémétrie de recherche](./media/app-insights-overview/search-tn.png) |
| [**Metrics Explorer pour les données agrégées**](app-insights-metrics-explorer.md)<br/>Explorez, filtrez et segmentez des données agrégées, comme les taux de demandes, d’échecs et d’exceptions, les temps de réponse et les durées de chargement des pages. |![Mesures](./media/app-insights-overview/metrics-tn.png) |
| [**Tableaux de bord**](app-insights-dashboards.md#dashboards)<br/>Combinez des données de plusieurs sources et partagez-les avec d’autres. Idéal pour les applications à composants multiples et pour un affichage en continu dans la salle de l’équipe. |![Exemple de tableaux de bord](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](app-insights-live-stream.md)<br/>Lorsque vous déployez une nouvelle version, observez ces indicateurs de performance quasiment en temps réel pour vous assurer que tout fonctionne comme prévu. |![Exemple de métriques temps réel](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analytics**](app-insights-analytics.md)<br/>Répondez à des questions difficiles sur les performances et l’utilisation de votre application avec ce langage de requêtes puissant. |![Exemple d’analyse](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](app-insights-visual-studio.md)<br/>Consultez les données de performances dans le code. Accédez au code à partir de l’arborescence des appels de procédure.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Débogueur de capture instantanée**](app-insights-snapshot-debugger.md)<br/>Déboguez les captures instantanées échantillonnées à partir des opérations en direct, avec des valeurs de paramètre.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](app-insights-export-power-bi.md)<br/>Intégrez des mesures d’utilisation à d’autres données décisionnelles.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**API REST**](https://dev.applicationinsights.io/)<br/>Écrivez du code pour exécuter des requêtes sur vos propres données brutes et mesures.| ![API REST](./media/app-insights-overview/rest-tn.png) |
| [**Exportation continue**](app-insights-export-telemetry.md)<br/>Exportation en bloc de données brutes vers le système de stockage dès leur arrivée. |![Exportation](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Comment utiliser Application Insights ?

### <a name="monitor"></a>Surveiller
Installez Application Insights dans votre application, configurez les [tests web de disponibilité](app-insights-monitor-web-app-availability.md), puis :

* Configurez un [tableau de bord](app-insights-dashboards.md) pour la salle de l’équipe afin de garder un œil sur la charge, la réactivité et la performance de vos dépendances, chargements de pages et appels AJAX.
* Découvrez quelles demandes sont les plus lentes et se terminent le plus par un échec.
* Surveillez le [flux temps réel](app-insights-live-stream.md) lorsque vous déployez une nouvelle version, afin d’être immédiatement informé en cas de dégradation.

### <a name="detect-diagnose"></a>Détecter, diagnostiquer
Lorsque vous recevez une alerte ou découvrez un problème :

* Évaluez le nombre d’utilisateurs affectés.
* Mettez en corrélation les échecs et les exceptions, les appels de dépendance et les traces.
* Examinez le profileur, les captures instantanées, les vidages de pile et les journaux de suivi.

### <a name="build-measure-learn"></a>Créer, mesurer, apprendre
[Mesurez l’efficacité](app-insights-usage-overview.md) de chaque nouvelle fonctionnalité que vous déployez.

* Planifiez l’évaluation de la manière dont les clients utilisent de nouvelles fonctionnalités d’entreprise ou utilisateur.
* Écrivez des données de télémétrie personnalisées dans votre code.
* Fondez le prochain cycle de développement sur des preuves tangibles provenant de votre télémétrie.

## <a name="get-started"></a>Prise en main
Application Insights est un des nombreux services hébergés dans Microsoft Azure, et les données de télémétrie sont envoyées à des fins d’analyse et de présentation. Ainsi, avant d’effectuer une autre opération, vous devez souscrire un abonnement [Microsoft Azure](http://azure.com). L’inscription est gratuite ; de plus, si vous choisissez le [plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/) de base d’Application Insights, rien n’est facturé tant que votre application n’est pas utilisée de façon substantielle. Si votre organisation possède déjà un abonnement, votre compte Microsoft peut y être ajouté.

Il existe plusieurs façons de démarrer avec Application Insights. Commencez par la méthode qui vous convient le mieux. Vous pourrez ajouter les autres par la suite.

* **Au moment de l’exécution : instrumentez votre application web sur le serveur.** Permet d’éviter toute mise à jour du code. Vous avez besoin de l’accès administrateur à votre serveur.
  * [**IIS local ou sur une machine virtuelle**](app-insights-monitor-performance-live-website-now.md)
  * [**Application web ou machine virtuelle Azure**](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **Au moment du développement : ajoutez Application Insights à votre code.** Vous permet d’écrire des données de télémétrie personnalisées et d’instrumenter le serveur principal et les applications de bureau.
  * [Visual Studio](app-insights-asp-net.md) 2013 Update 2 ou version ultérieure.
  * Java dans [Eclipse](app-insights-java-eclipse.md) ou [autres outils](app-insights-java-get-started.md)
  * [Node.JS](app-insights-nodejs.md)
  * [Autres plateformes](app-insights-platforms.md)
* **[Instrumentez vos pages web](app-insights-javascript.md)** pour l’affichage de page, AJAX et d’autres données de télémétrie côté client.
* **[Analysez l’utilisation des applications mobiles](app-insights-mobile-center-quickstart.md)** en effectuant l’intégration avec Visual Studio App Center.
* **[Tests de disponibilité](app-insights-monitor-web-app-availability.md)** : effectuez régulièrement un test ping sur votre site web à partir de nos serveurs.


## <a name="next-steps"></a>Étapes suivantes
Prise en main lors de l’exécution avec :

* [Serveur IIS](app-insights-monitor-performance-live-website-now.md)
* [Serveur J2EE](app-insights-java-live.md)

Prise en main pendant le développement avec :

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>Support et commentaires
* Questions et problèmes :
  * [Résolution des problèmes][qna]
  * [Forum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Vos suggestions :
  * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Blog :
  * [Blog Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Vidéos

[![Présentation animée](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
