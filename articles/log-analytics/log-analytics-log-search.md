---
title: Recherches dans les journaux dans Azure Log Analytics | Microsoft Docs
description: "Vous avez besoin d’effectuer une recherche dans les journaux pour récupérer des données à partir de Log Analytics.  Cet article décrit comment sont utilisées les nouvelles recherches dans les journaux dans Log Analytics, puis présente les concepts que vous devez comprendre avant de créer une recherche."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 5f040d1480433ccf4c0b2b22c0cf1e25a7151d74
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2017
---
# <a name="understanding-log-searches-in-log-analytics"></a>Présentation des recherches dans les journaux dans Log Analytics

Vous avez besoin d’effectuer une recherche dans les journaux pour récupérer des données à partir de Log Analytics.  Que vous analysiez des données dans le portail, configuriez une règle d’alerte pour être averti d’une condition particulière ou récupériez des données à l’aide de l’API Log Analytics, vous utiliserez une recherche dans les journaux pour spécifier les données souhaitées.  Cet article décrit comment sont utilisées les recherches dans les journaux dans Log Analytics, et présente les concepts que vous devez comprendre avant de créer une recherche. Pour plus d’informations sur la création et la modification des recherches dans les journaux et pour obtenir des références sur le langage de requête, consultez la section [Étapes suivantes](#next-steps).

## <a name="where-log-searches-are-used"></a>Où les recherches dans les journaux sont-elles utilisées ?

Voici quelques-unes des différentes façons dont vous utiliserez les recherches dans les journaux dans Log Analytics :

- **Portails.** Vous pouvez effectuer une analyse interactive des données dans le dépôt sur le portail Azure ou le [portail Advanced Analytics](https://go.microsoft.com/fwlink/?linkid=856587).  Cela vous permet de modifier votre requête et d’analyser les résultats dans divers formats et visualisations.  La plupart des requêtes que vous créerez démarreront dans l’un des portails, puis seront copiées une fois que vous aurez vérifié qu’elles fonctionnent comme prévu.
- **Règles d’alerte.** Les [règles d’alerte](log-analytics-alerts.md) identifient de façon proactive les problèmes à partir des données dans votre espace de travail.  Chaque règle d’alerte est basée sur une recherche dans les journaux qui est exécutée automatiquement à intervalles réguliers.  Les résultats sont inspectés pour déterminer si une alerte doit être créée.
- **Vues.**  Vous pouvez créer des visualisations de données à inclure dans les tableaux de bord utilisateur avec le [Concepteur de vues](log-analytics-view-designer.md).  Les recherches dans les journaux fournissent les données utilisées par les [vignettes](log-analytics-view-designer-tiles.md) et les [parties de visualisation](log-analytics-view-designer-parts.md) dans chaque vue.  Vous pouvez descendre dans la hiérarchie à partir des parties de visualisation dans la page Recherche dans les journaux afin d’effectuer une analyse approfondie des données.
- **Exportation.**  Quand vous exportez des données de l’espace de travail Log Analytics vers Excel ou [Power BI](log-analytics-powerbi.md), vous créez une recherche dans les journaux pour définir les données à exporter.
- **PowerShell.** Vous pouvez exécuter un script PowerShell à partir d’une ligne de commande ou d’un runbook Azure Automation qui utilise [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) pour récupérer des données à partir de Log Analytics.  Cette applet de commande nécessite une requête pour déterminer les données à récupérer.
- **API Log Analytics.**  L’[API de recherche dans les journaux Log Analytics](log-analytics-log-search-api.md) permet à tout client d’API REST de récupérer des données à partir de l’espace de travail.  La demande d’API comprend une requête qui est exécutée sur Log Analytics pour déterminer les données à récupérer.

![Recherches dans les journaux](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Organisation des données Log Analytics
Quand vous générez une requête, commencez par déterminer les tables où figurent les données que vous recherchez. Chaque [source de données](log-analytics-data-sources.md) et [solution](../operations-management-suite/operations-management-suite-solutions.md) stocke ses données dans des tables dédiées dans l’espace de travail Log Analytics.  La documentation de chaque source de données et solution inclut le nom du type de données qu’elle crée et une description de chacune de ses propriétés.  De nombreuses requêtes nécessitent les données d’une seule table, mais d’autres peuvent utiliser diverses options pour inclure des données provenant de plusieurs tables.

![Tables](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>Écriture d’une requête
Au cœur des recherches dans les journaux dans Log Analytics est [un langage de requête étendu](https://docs.loganalytics.io/) qui vous permet de récupérer et d’analyser des données du dépôt de différentes façons.  Ce même langage de requête est utilisé pour [Application Insights](../application-insights/app-insights-analytics.md).  Apprendre à écrire une requête est essentiel pour la création de recherches dans les journaux dans Log Analytics.  Vous commencerez généralement par des requêtes de base, puis vous passerez à des fonctions plus avancées à mesure que vos exigences deviendront plus complexes.

La structure de base d’une requête est une table source suivie d’une série d’opérateurs séparés par une barre verticale `|`.  Vous pouvez chaîner plusieurs opérateurs pour affiner les données et effectuer des fonctions avancées.

Par exemple, supposez que vous souhaitez rechercher les dix ordinateurs ayant eu le plus d’événements d’erreur durant les dernières 24 heures,

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

ou que vous souhaitez rechercher les ordinateurs qui n’ont pas eu de pulsation durant les dernières 24 heures.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

Et si vous génériez un graphique en courbes avec l’utilisation du processeur pour chaque ordinateur durant la semaine dernière ?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

Grâce à ces exemples rapides, vous pouvez constater que, quel que soit le type de données avec lequel vous travaillez, la structure de la requête est similaire.  Vous pouvez la décomposer en étapes distinctes où les données obtenues à partir d’une commande sont envoyées à la commande suivante par l’intermédiaire du pipeline.

Vous pouvez également interroger les données dans les espaces de travail Log Analytics au sein de votre abonnement.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


Pour obtenir une documentation complète sur le langage de requête Azure Log Analytics, notamment une référence du langage et des didacticiels, consultez la [documentation du langage de requête Azure Log Analytics](https://docs.loganalytics.io/).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [portails que vous utilisez pour créer et modifier des recherches dans les journaux](log-analytics-log-search-portals.md).
- Consultez un [didacticiel sur l’écriture de requêtes](log-analytics-tutorial-viewdata.md) à l’aide du nouveau langage de requête.
