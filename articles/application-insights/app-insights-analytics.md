---
title: "Analytics, le puissant outil de recherche et de requête d’Azure Application Insights | Microsoft Docs"
description: "Présentation d’Analytics, le puissant outil de recherche d’Application Insights. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 5f324051a2eeedd35a22f77c771793af9c90c434
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="analytics-in-application-insights"></a>Analytics dans Application Insights
Analytics est le puissant outil de recherche et de requête d[’Application Insights](app-insights-overview.md). Analytics étant un outil web, aucune installation n’est nécessaire. Si vous avez déjà configuré Application Insights pour l’une de vos applications, vous pouvez alors analyser les données de votre application en ouvrant Analytics à partir du [panneau Vue d’ensemble](app-insights-dashboards.md) de votre application.

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-insights-analytics/001.png)

Vous pouvez également utiliser l[’interface Analytics](https://go.microsoft.com/fwlink/?linkid=859557), un environnement de démonstration gratuit incluant de nombreux exemples de données.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Interrogation de données dans Analytics
Une requête classique commence par un nom de table suivi d’une série *d’opérateurs* séparés par des `|`.
Par exemple, nous allons rechercher le nombre de demandes que notre application a reçu en provenance de différents pays, au cours des 3 dernières heures :
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Commençons par le nom de la table, *requests*, et ajoutons les éléments redirigés nécessaires.  Tout d’abord, nous définissons un filtre de temps pour consulter uniquement les enregistrements des 3 dernières heures.
Nous comptons ensuite le nombre d’enregistrements par pays (ces données se trouvent dans la colonne *client_CountryOrRegion*). Pour finir, nous affichons les résultats dans un graphique à secteurs.
<br>

![Résultats de la requête](./media/app-insights-analytics/030.png)

Le langage possède de nombreuses fonctionnalités attrayantes :

* [Filtrer](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) vos données de télémétrie d’application brutes sur tous les champs, y compris les propriétés et métriques personnalisées.
* [Joindre](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/join-operator) plusieurs tables ; mettez en corrélation les demandes avec les affichages de page, les appels de dépendance, les exceptions et les suivis du journal.
* [Agrégations](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions)statistiques puissantes.
* Visualisations immédiates et puissantes.
* Il existe une [API REST](https://dev.applicationinsights.io/) que vous pouvez utiliser pour exécuter des requêtes par programme, par exemple à partir de PowerShell.

La [référence complète du langage](https://go.microsoft.com/fwlink/?linkid=856079) détaille chaque commande prise en charge et se met à jour régulièrement.

## <a name="next-steps"></a>Étapes suivantes
* Prise en main du [portail Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* Prise en main de l[’écriture de requêtes](https://go.microsoft.com/fwlink/?linkid=856078)
* Consultez l[’aide-mémoire des utilisateurs de SQL](https://aka.ms/sql-analytics) pour obtenir la traduction des idiomes courants.
* Testez la version d’évaluation d’Analytics sur votre [interface](https://analytics.applicationinsights.io/demo) si votre application n’envoie pas encore de données à Application Insights.
* Regardez la [vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).