---
title: Surveillance des applications et des ressources Azure | Microsoft Docs
description: "Vue d’ensemble des différents services et fonctionnalités Microsoft qui contribuent à une stratégie de surveillance complète pour vos services et applications Azure."
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: 505e92b5fc63f570bc4d0f8899ae977b93850356
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Surveillance des applications et des ressources Azure

La surveillance consiste à recueillir et à analyser des données afin de déterminer les performances, l’intégrité et la disponibilité de votre application métier et des ressources dont elle dépend. Une stratégie de surveillance efficace vous aidera à comprendre le fonctionnement détaillé des différents composants de votre application et à accroître la durée de fonctionnement en vous signalant de manière proactive les problèmes critiques, afin que vous puissiez les résoudre au plus vite.

Azure comprend plusieurs services qui effectuent chacun une tâche ou tiennent un rôle spécifique dans l’espace de surveillance, et qui ensemble offrent une solution complète pour recueillir, analyser et agir sur les données de télémétrie de votre application et les ressources Azure sous-jacentes correspondantes.  Ces services peuvent aussi surveiller les ressources locales critiques afin de fournir un environnement de surveillance hybride.   Comprendre les outils et les données disponibles est la première étape du développement d’une stratégie de surveillance complète pour votre application. 

Le diagramme suivant présente une vue conceptuelle des différents composants qui collaborent pour fournir une surveillance des ressources Azure.  Chacun d’eux est décrit dans les sections suivantes, avec des liens vers des informations techniques détaillées.

![Vue d’ensemble de la surveillance](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Surveillance de base
La surveillance de base assure la surveillance essentielle et obligatoire des ressources Azure.  Ces services nécessitent une configuration minimale et recueillent des données de télémétrie de base exploitées par les services de surveillance Premium.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) assure la surveillance de base du service Azure en activant la collecte de [métriques](../monitoring-and-diagnostics/monitoring-overview-metrics.md), de [journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) et de [journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).  Par exemple, le journal d’activité indique quand des ressources sont créées ou modifiées.  Les métriques fournissent des statistiques sur les performances pour différentes ressources, et même pour le système d’exploitation à l’intérieur d’une machine virtuelle.  Vous pouvez afficher ces données avec l’un des explorateurs dans le portail Azure, les envoyer à Log Analytics à des fins d’analyse détaillée et d’identification des tendances, ou créer des règles d’alerte afin d’être informé des problèmes critiques.

### <a name="service-health"></a>Service Health
L’intégrité de votre application repose sur les services Azure dont elle dépend.  [Azure Service Health](../service-health/service-health-overview.md) identifie les éventuels problèmes avec les services Azure qui peuvent avoir un impact sur votre application, et vous aide également à planifier la maintenance.

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) surveille en permanence votre télémétrie d’utilisation et de configuration des ressources afin de fournir des recommandations personnalisées basées sur les bonnes pratiques.  Suivre ces recommandations vous aidera à améliorer les performances, la sécurité et la disponibilité des ressources prenant en charge vos applications.


## <a name="premium-monitoring-services"></a>Services de surveillance Premium
Les services Azure suivants offres de puissantes fonctionnalités de collecte et d’analyse des données de surveillance.  Ils reposent sur la surveillance de base, tirent parti de fonctionnalités courantes dans Azure et fournissent une puissante analytique avec les données recueillies, afin de vous communiquer des données précieuses sur vos applications et votre infrastructure.  Ils présentent des données dans le contexte de scénarios particuliers ciblant différents publics.

### <a name="application-insights"></a>Application Insights
[Application Insights](http://azure.microsoft.com/documentation/services/application-insights) vous permet de surveiller la disponibilité, les performances et l’utilisation de votre application, qu’elle soit hébergée dans le cloud ou localement.  En instrumentant votre application pour qu’elle fonctionne avec Application Insights, vous pouvez obtenir des informations précieuses vous permettant d’identifier et de diagnostiquer rapidement les erreurs sans attendre qu’un utilisateur les signale. Grâce aux informations recueillies, vous pouvez prendre des décisions avisées quant à la maintenance et à l’amélioration de votre application.  En plus des outils étendus permettant d’interagir avec les données recueillies, Application Insights stocke ses données dans un référentiel commun afin de tirer parti de fonctionnalités partagées telles que les alertes, les tableaux de bord et une analyse approfondie avec le langage de requête Log Analytics.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) joue un rôle central dans la surveillance Azure en recueillant des données à partir de diverses ressources et en les plaçant dans un référentiel unique où vous pouvez les analyser avec un langage de requête puissant.  Application Insights et Azure Security Center stockent leurs données dans le magasin de données Log Analytics et tirent parti de son moteur analytique.  Ceci, combiné avec les données recueillies par Azure Monitor, les solutions de gestion et les agents installés sur les machines virtuelles dans le cloud ou localement, vous permet de dresser une image complète de votre environnement. 


### <a name="service-map"></a>Service Map
[Service Map](../operations-management-suite/operations-management-suite-service-map.md) fournit un aperçu de votre environnement IaaS en analysant les machines virtuelles avec leurs différents processus et dépendances envers d’autres ordinateurs et processus externes.  Il intègre des événements, des données de performances et des solutions de gestion dans Log Analytics, ce qui vous permet de visualiser ces données dans le contexte de chaque ordinateur et de sa relation avec le reste de votre environnement.  Service Map est similaire à la [mise en correspondance d’applications dans Application Insights](../application-insights/app-insights-app-map.md), mais se concentre sur les composants d’infrastructure prenant en charge vos applications.

### <a name="network-watcher"></a>Network Watcher
[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) fournit une surveillance et des diagnostics basés sur un scénario pour différents scénarios réseau dans Azure.  Il stocke des données dans des métriques et diagnostics Azure à des fins d’analyse plus approfondie, et opère avec les solutions de surveillance réseau suivantes pour analyser les différents aspects de votre réseau :
* [Network Performance Monitor (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) : solution de surveillance réseau basée sur le cloud, qui surveille la connectivité des clouds publics, des centres de données et des environnements locaux
* [Moniteur ExpressRoute](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/) : fonctionnalité NPM qui surveille la connectivité et les performances de bout en bout sur les circuits ExpressRoute.
* Traffic Analytics : solution cloud qui offre une visibilité de l’activité des utilisateurs et des applications sur votre réseau cloud.
* [DNS Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-dns) : fournit des informations connexes sur les opérations, les performances et la sécurité, en fonction de vos serveurs DNS.

### <a name="management-solutions"></a>Liste des solutions de gestion
Les [solutions de gestion](../log-analytics/log-analytics-add-solutions.md) sont des jeux de logique empaquetés qui fournissent des informations détaillées pour une application ou un service spécifique.  Elles s’appuient sur Log Analytics pour stocker et analyser les données de surveillance recueillies.  Microsoft et ses partenaires proposent des solutions de gestion qui fournissent une surveillance pour divers services Azure et tiers. Parmi les solutions de surveillance disponibles figurent [Container Monitoring](../log-analytics/log-analytics-containers.md), qui vous permet de visualiser et de gérer vos hôtes de conteneur, et [Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md), qui recueille et affiche des métriques de performances pour les bases de données SQL Azure.


## <a name="shared-functionality"></a>Fonctionnalités partagées
Les outils Azure suivants fournissent des fonctionnalités essentielles aux services de surveillance Premium.  Ces fonctionnalités sont partagées par plusieurs services, ce qui vous permet de tirer parti des configurations et des fonctionnalités communes entre plusieurs services.

### <a name="alerts"></a>Alertes
Les [alertes Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) vous avertissent de manière proactive en cas de condition critique, et peuvent prendre des mesures correctives.  Les règles d’alerte peuvent exploiter les données provenant de plusieurs sources, notamment les métriques et les journaux. Elles utilisent des [groupes d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) qui contiennent des ensembles uniques de destinataires et d’actions en réponse à une alerte.  Selon vos besoins, vous pouvez faire en sorte que les alertes lancent des actions externes à l’aide de webhooks et s’intègrent à vos outils ITSM.

### <a name="dashboards"></a>Tableaux de bord
Les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md) vous permettent de combiner différents genres de données dans un même volet dans le portail Azure, et de les partager avec d’autres utilisateurs d’Azure.  Par exemple, vous pouvez créer un tableau de bord qui combine des vignettes affichant un graphique de métriques, un tableau de journaux d’activité, un graphique d’utilisation provenant d’Application Insights et la sortie d’une recherche dans les journaux dans Log Analytics.

Vous pouvez également exporter des données Log Analytics vers [Power BI](https://docs.microsoft.com/power-bi/) afin de tirer parti de visualisations supplémentaires et de mettre les données à disposition d’autres personnes au sein et en dehors de votre organisation.

### <a name="metrics-explorer"></a>Metrics Explorer
Les [métriques](../monitoring-and-diagnostics/monitoring-overview-metrics.md) sont des valeurs numériques générées par des ressources Azure qui vous aident à comprendre le fonctionnement et les performances de la ressource. Vous pouvez envoyer des métriques vers Log Analytics afin de les analyser avec des données provenant d’autres sources.



### <a name="activity-logs"></a>Journaux d’activité
Les [journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) fournissent des données sur les opérations effectuées sur des ressources Azure.  Cela comprend des informations telles que les modifications de configuration de la ressource, les incidents d’intégrité de service, des recommandations visant à mieux utiliser la ressource, et des informations liées aux opérations de mise à l’échelle automatique.  Vous pouvez afficher les journaux pour une ressource particulière dans sa page dans le portail Azure, ou afficher les journaux de plusieurs ressources dans l’explorateur de journaux d’activité.  Vous pouvez également envoyer des journaux d’activité vers Log Analytics, afin de pouvoir les analyser avec des données recueillies par des solutions de gestion, des agents sur des machines virtuelles et d’autres sources.


## <a name="example-scenarios"></a>Exemples de scénarios
Voici des exemples généraux qui illustrent comment utiliser différents outils de surveillance dans Azure pour différents scénarios.

### <a name="monitoring-a-web-application"></a>Surveillance d’une application web
Considérez une application déployée dans Azure à l’aide d’App Services, de Stockage Azure et d’une base de données SQL.  Vous pouvez commencer par accéder aux [métriques](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et aux [journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) de chacune de ces ressources dans leurs pages dans le portail Azure.  Ces pages donnent des informations critiques telles que le nombre de requêtes envoyées à l’application et le temps de réponse moyen, et identifient également les modifications de configuration.

Vous pouvez ensuite accéder à Surveiller dans le portail afin d’afficher simultanément les métriques et les journaux des différentes ressources.  À mesure que vous déterminez les paramètres standard pour les métriques, vous [créez des règles d’alerte](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) afin d’être informé de manière proactive quand par exemple le temps de réponse moyen dépasse un certain seuil.  Pour obtenir un aperçu rapide des performance quotidiennes de votre application, vous créez un tableau de bord Azure pour afficher des graphiques de métriques représentant des indicateurs de performance clés critiques.

Pour effectuer une surveillance plus approfondie de votre application, vous [la configurez pour Application Insights](../application-insights/quick-monitor-portal.md).  Vous pouvez maintenant recueillir des données supplémentaires fournissant des informations encore plus précises sur le fonctionnement et les performances de votre application.  Application Insights détecte les relations sous-jacentes entre les composants de votre application, offrant une représentation visuelle par le biais de la [mise en correspondance d’applications](../application-insights/app-insights-app-map.md) couplée avec le [suivi de bout en bout](../application-insights/app-insights-transaction-diagnostics.md) pour identifier le composant, la dépendance ou l’exception exact(e) où un problème s’est produit.  Vous créez des [tests de disponibilité](../application-insights/app-insights-monitor-web-app-availability.md) afin de tester de manière proactive votre application à partir de différentes régions.  Pour aider vos développeurs, vous [activez le profileur](../application-insights/enable-profiler-compute.md) afin de pouvoir suivre les requêtes et toutes les exceptions jusqu’à une ligne spécifique de code.  

Pour en savoir encore plus sur les services utilisés dans votre application, vous ajoutez la [solution SQL Analytics](../log-analytics/log-analytics-azure-sql.md) afin de recueillir des données supplémentaires dans Log Analytics. Après un certain temps, vous décidez d’effectuer des recherches afin de savoir pourquoi, durant certaines périodes, les performances sur le site sont passées sous le seuil.  Vous écrivez une requête à l’aide de Log Analytics pour mettre en corrélation les données de performances et d’utilisation recueillies par Application Insights avec les données de performances et de configuration parmi les ressources Azure prenant en charge votre application.


### <a name="monitoring-virtual-machines"></a>Surveillance des machines virtuelles
Vous avez une combinaison de machines virtuelles Windows et Linux en cours d’exécution dans Azure.  Vous utilisez Azure Monitor pour afficher les [journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) et les [métriques au niveau de l’hôte](../monitoring-and-diagnostics/monitoring-overview-metrics.md), puis vous ajoutez [l’extension Azure Diagnostics](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) aux machines virtuelles afin de recueillir des métriques à partir du système d’exploitation invité.  Vous créez ensuite des [règles d’alerte](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) pour être informé de manière proactive quand des métriques de base telles que l’utilisation du processeur et la mémoire franchissent des seuils.

Pour recueillir plus de détails sur les machines virtuelles exécutant une application métier, vous [créez un espace de travail Log Analytics et activez l’extension de machine virtuelle](../log-analytics/log-analytics-quick-collect-azurevm.md) sur chaque machine.  Vous configurez la [collecte de différentes sources de données](../log-analytics/log-analytics-data-sources.md) pour votre application et [créez des vues](../log-analytics/log-analytics-view-designer.md) afin d’obtenir des rapports sur ses opérations et ses performances quotidiennes.  Ensuite, vous [créez des règles d’alerte](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) pour être averti lors de la réception d’événements d’erreur particuliers.  Pour surveiller en continu l’intégrité de l’agent installé, vous ajoutez la [solution de gestion Agent Health](../operations-management-suite/oms-solution-agenthealth.md).

Pour obtenir des informations encore plus précises sur l’application, vous [ajoutez l’agent de dépendance](../operations-management-suite/operations-management-suite-service-map-configure.md) aux machines virtuelles afin de les ajouter à [Service Map](../operations-management-suite/operations-management-suite-service-map.md).  Il détecte les processus critiques et identifie les connexions entre les machines et d’autres services.  Après un signalement de panne, vous utilisez Service Map pour identifier les machines spécifiques ayant rencontré le problème.  Vous créez ensuite une [requête sur les données Log Analytics](../log-analytics/log-analytics-log-search-new.md) pour identifier le problème à l’avenir, et vous créez une règle d’alerte pour être averti de manière proactive quand la condition a été détectée.



## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur

* [Azure Monitor dans une vidéo de l’Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Prise en main d’Azure Monitor](monitoring-get-started.md)
* [Azure Diagnostic](../azure-diagnostics.md) si vous tentez de diagnostiquer des problèmes dans votre service cloud, machine virtuelle, jeux de mise à l’échelle de machine virtuelle ou application Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de diagnostiquer des problèmes dans votre application web App Service.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) pour l’analyse des données de surveillance recueillies.
