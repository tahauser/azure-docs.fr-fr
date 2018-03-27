---
title: Qu’est-ce qu’Azure Log Analytics ? | Microsoft Docs
description: Log Analytics est un service d’Azure qui vous permet de collecter et d’analyser les données opérationnelles générées par les ressources de votre cloud et de vos environnements locaux.  Cet article fournit une vue d'ensemble des différents composants de Log Analytics ainsi que des liens vers un contenu détaillé.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: bwren
ms.openlocfilehash: b951d41dab4d349a8d648e7eaa7e23b73ced2ced
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="what-is-azure-log-analytics"></a>Qu’est-ce qu’Azure Log Analytics ?
Log Analytics joue un rôle central dans la gestion Azure : il collecte les données de télémétrie et d’autres données à partir de diverses sources et fournit un langage de requêtes et un moteur d’analyse qui vous donnent des informations sur le fonctionnement de vos applications et de vos ressources.  Vous pouvez interagir directement avec les données Log Analytics par le biais des recherches dans les journaux et des affichages, ou utiliser les outils d’analyse dans d’autres services Azure qui stockent leurs données dans Log Analytics, comme Application Insights ou Azure Security Center.  

Log Analytics nécessite peu de configuration et est déjà intégré à d’autres services Azure.  Vous devez simplement créer un espace de travail qui permettra la collecte.  Vous pouvez ensuite installer des agents sur des machines virtuelles afin de les inclure dans l’espace de travail et d’utiliser des solutions de gestion qui incluent la logique pour fournir des informations supplémentaires sur les différentes applications.  Dans les coulisses, les types de données sont prédéfinis ou créés automatiquement lorsque les données sont collectées.


## <a name="role-in-monitoring"></a>Rôle dans la surveillance

Les différents services de surveillance dans Azure sont décrits dans [Surveillance des applications et des ressources Azure](../monitoring-and-diagnostics/monitoring-overview.md).  Log Analytics joue un rôle central : il consolide les données de surveillance à partir de différentes sources et fournit un langage de requête puissant en vue de la consolidation et de l’analyse.  

Log Analytics ne se contente cependant pas de surveiller les ressources Azure.  Il peut recueillir des données à partir de ressources locales ou dans d’autres clouds afin de créer un environnement d’analyse hybride, et peut se connecter directement à System Center Operations Manager pour collecter les données de télémétrie des agents existants.  Les outils d’analyse de Log Analytics, tels que les recherches dans les journaux, les vues et les solutions de gestion, fonctionnent avec toutes les données collectées. Vous pouvez ainsi analyser l’ensemble de votre environnement de manière centralisée.



## <a name="data-collection"></a>Collecte des données
Log Analytics peut recueillir des données de diverses sources.  Une fois collectées, les données sont organisées dans différents tableaux en fonction du type de données. Toutes les données sont ainsi analysées ensemble, quelle que soit leur source d’origine.

Les méthodes de collecte des données dans Log Analytics incluent :

- la configuration d’Azure Monitor pour copier les métriques et les journaux collectés à partir des ressources Azure ;
- les agents sur les machines virtuelles [Windows](log-analytics-windows-agent.md) et [Linux](log-analytics-linux-agents.md) envoient les données de télémétrie du système d’exploitation invité et des applications à Log Analytics en fonction des [sources de données](log-analytics-data-sources.md) que vous configurez ;  
- la connexion d’un [groupe d’administration System Center Operations Manager](log-analytics-om-agents.md) pour Log Analytics, afin de collecter des données à partir de ses agents ;
- les services Azure tels que [Application Insights](https://docs.microsoft.com/azure/application-insights/) et [Azure Security Center](https://docs.microsoft.com/azure/security-center/) stockent leurs données directement dans Log Analytics, sans aucune configuration ;
- l’écriture des données à partir d’une ligne de commande PowerShell ou d’un [runbook Azure Automation](../automation/automation-runbook-types.md) à l’aide des cmdlets Log Analytics.
- Si vous avez des besoins spécifiques, vous pouvez utiliser l’[API de collecte de données HTTP](log-analytics-data-collector-api.md) pour écrire des données dans Log Analytics à partir de n’importe quel client de l’API REST.


![Composants de Log Analytics](media/log-analytics-overview/collecting-data.png)

## <a name="add-functionality-with-management-solutions"></a>Ajouter des fonctionnalités avec des solutions de gestion
Les [solutions de gestion](log-analytics-add-solutions.md) offrent une logique préconfigurée pour un scénario ou un produit donné.  Elles permettent de recueillir des données supplémentaires dans Log Analytics ou de traiter des données déjà collectées.  Elles incluent généralement une vue vous permettant de mieux analyser ces données supplémentaires.  Les solutions sont disponibles pour diverses fonctions et des solutions supplémentaires sont ajoutées régulièrement.  Vous pouvez facilement parcourir les solutions disponibles et [les ajouter à votre espace de travail](log-analytics-add-solutions.md) à partir d’Azure Marketplace.  

![Marketplace](media/log-analytics-overview/solutions.png)


## <a name="query-language"></a>Langage de requête

Log Analytics inclut un [langage de requête riche](http://docs.loganalytics.io) pour rapidement récupérer, consolider et analyser les données collectées.  Vous pouvez créer et tester des requêtes à l’aide des [portails de recherche dans les journaux ou d’analyse avancée](log-analytics-log-search-portals.md), avant d’analyser directement les données à l’aide de ces outils ou d’enregistrer les requêtes pour les utiliser pour les visualisations, les alertes ou l’exportation vers d’autres outils tels que Power BI ou Excel.

Le langage de requête Log Analytics est adapté aux recherches simples dans les journaux, mais inclut également des fonctionnalités avancées telles que les agrégations, les jointures et les analyses intelligentes. Il existe [plusieurs didacticiels](https://docs.loganalytics.io/docs/Learn/Tutorials) pour vous aider à apprendre le langage de requête.  Des conseils particuliers sont fournis aux utilisateurs qui connaissent déjà [SQL](https://docs.loganalytics.io/docs/Learn/References/SQL-to-Azure-Log-Analytics) et [Splunk](https://docs.loganalytics.io/docs/Learn/References/Splunk-to-Azure-Log-Analytics).

![Recherche dans les journaux](media/log-analytics-overview/analytics-query.png)


## <a name="visualize-log-analytics-data"></a>Visualiser les données Log Analytics

Les [vues dans Log Analytics](log-analytics-view-designer.md) présentent de manière visuelle les données obtenues grâce aux recherches dans les journaux.  Chaque vue inclut plusieurs visualisations, par exemple sous forme de barres et de graphiques en courbes, en plus des listes résumant les données critiques.  Les [solutions de gestion](#add-functionality-with-management-solutions) incluent des vues qui synthétisent les données pour une application donnée. Vous pouvez créer vos propres vues pour présenter les données d’une recherche dans les journaux Log Analytics.

![Vue Log Analytics](media/log-analytics-overview/view.png)

Vous pouvez également épingler les résultats d’une requête Log Analytics sur un [tableau de bord Azure](../azure-portal/azure-portal-dashboards.md). Cela vous permet d’associer les vignettes de différents services Azure.  Vous pouvez même épingler une vue Log Analytics à un tableau de bord.

![Tableaux de bord Azure](media/log-analytics-overview/dashboard.png)

## <a name="creating-alerts-from-log-analytics-data"></a>Création d’alertes à partir des données Log Analytics

Utilisez les [alertes Azure](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) pour être informé de manière proactive de l’état des données Log Analytics qui vous intéressent.  Une requête est automatiquement exécutée à intervalles planifiés et une alerte créée si les résultats répondent à des critères spécifiques.  Vous pouvez ainsi combiner des alertes issues de Log Analytics avec celles d’autres sources, telles que les alertes en quasi-temps réel d’[Azure Monitor](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md) et les exceptions d’application [Application Insights](../application-insights/app-insights-alerts.md), et partager des [groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) en cas de correspondance avec les conditions d’alerte.

![Alerte](media/log-analytics-overview/alerts.png)


## <a name="using-log-analytics-data-in-other-services"></a>Utilisation des données Log Analytics dans d’autres services
Les services tels que Application Insights et Azure Security Center stockent leurs données dans Log Analytics.  Vous allez généralement utiliser les outils d’analyse riches fournis par ces services, mais vous pouvez également utiliser les requêtes Log Analytics pour accéder à leurs données et éventuellement les combiner avec des données provenant d’autres services.  

Par exemple, la vue suivante provient d’Application Insights.  Si vous cliquez sur l’icône située dans le coin supérieur droit, la console d’analyse Log Analytics démarre avec les requêtes utilisées par le graphique.

![Application Insights](media/log-analytics-overview/application-insights.png)


## <a name="exporting-log-analytics-data"></a>Exportation des données Log Analytics

Log Analytics met également ses données à disposition en dehors d’Azure.  Vous pouvez configurer [Power BI](log-analytics-powerbi.md) afin d’importer les résultats d’une requête à intervalles planifiés afin de bénéficier de ses fonctionnalités, telles que la combinaison de données provenant de différentes sources et le partage de rapports sur le Web et sur des appareils mobiles.  Vous pouvez également utiliser les [API de recherche de journal](log-analytics-log-search-api.md) pour créer des solutions personnalisées qui exploitent les données Log Analytics, ou pour les intégrer à d'autres systèmes.

Vous pouvez utiliser [Logic Apps](../logic-apps/logic-apps-overview.md) dans Azure pour créer des flux de travail personnalisés en fonction des données Log Analytics.  Pour une logique plus complexe basée sur PowerShell, vous pouvez utiliser des [runbooks dans Azure Automation](../automation/automation-runbook-types.md).

![Power BI](media/log-analytics-overview/export.png)



## <a name="next-steps"></a>Étapes suivantes
- Commencez avec la [collecte de données à partir de machines virtuelles Azure](log-analytics-quick-collect-azurevm.md).
- Découvrez un [didacticiel sur l’analyse des données Log Analytics à l’aide d’une requête simple](log-analytics-tutorial-viewdata.md).
* [Parcourez les solutions disponibles](log-analytics-add-solutions.md) pour ajouter des fonctionnalités à Log Analytics.

