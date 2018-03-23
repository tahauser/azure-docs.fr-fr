---
title: Surveiller l’utilisation de la base de données avec Intelligent Insights - Azure SQL Database | Microsoft Docs
description: Azure SQL Database Intelligent Insights utilise l’intelligence intégrée pour surveiller en permanence l’utilisation de la base de données et détecter les événements perturbateurs entraînant des performances médiocres.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 8654c35bb9c6e46a47f52dee6ab7969f996b42f4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="intelligent-insights"></a>Intelligent Insights

Azure SQL Database Intelligent Insights vous permet de suivre les performances de votre base de données.

Intelligent Insights utilise l’intelligence intégrée pour surveiller en permanence l’utilisation de la base de données et détecter les événements perturbateurs entraînant des performances médiocres. Une fois un tel événement détecté, une analyse détaillée est effectuée, qui génère un journal de diagnostic avec une évaluation intelligente du problème. Cette évaluation se compose d’une analyse de cause racine du problème de performances de la base de données et, dans la mesure du possible, de recommandations pour une amélioration des performances. 

## <a name="what-can-intelligent-insights-do-for-you"></a>Qu’est-ce qu’Intelligent Insights peut faire pour vous ?

Intelligent Insights est une fonctionnalité unique de l’intelligence intégrée Azure, qui apporte les avantages suivants :

- Surveillance proactive
- Analyse des performances personnalisée
- Détection anticipée de la dégradation des performances de la base de données
- Analyse de la cause racine des problèmes détectés
- Recommandations pour l’amélioration des performances
- Capacité de montée en charge sur des centaines de milliers de bases de données
- Impact positif sur les ressources de DevOps et le coût total de possession

## <a name="how-does-intelligent-insights-work"></a>Comment fonctionne Intelligent Insights ?

Intelligents Insights analyse les performances de SQL Database en comparant la charge de travail de la base de données au cours de la dernière heure à la ligne de base de la charge de travail sur les sept derniers jours. La charge de travail de la base de données est composée de requêtes déterminées comme étant les plus significatives pour les performances de la base de données, à savoir par exemple les requêtes les plus répétées et les plus grandes. Chaque base de données étant unique sur le plan de la structure, des données, de l’utilisation et de l’application, chaque ligne de base de la charge de travail générée est unique et propre à une instance individuelle. Intelligent Insights, indépendamment de la ligne de base de la charge de travail, analyse aussi des seuils opérationnels absolus et détecte des problèmes de temps d’attente excessifs, des exceptions critiques et des problèmes de paramétrages des requêtes susceptibles d’affecter les performances.

Une fois qu’un problème de détérioration des performances est détecté par plusieurs mesures observées en utilisant l’intelligence artificielle, l’analyse est effectuée. Un journal de diagnostic est généré avec une information intelligente sur ce qui se passe pour votre base de données. Intelligent Insights facilite le suivi du problème de performances de la base de données de son apparition initiale jusqu’à sa résolution. Chaque problème détecté est suivi tout au long de son cycle de vie depuis sa détection initiale et la vérification de l’amélioration des performances jusqu’à sa fin. Des mises à jour sont fournies dans le journal de diagnostic toutes les 15 minutes. 

![Flux de travail de l’analyse des performances de la base de données](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Les métriques utilisées pour mesurer et détecter les problèmes de performances de la base de données reposent sur la durée des requêtes, les demandes de délai d’expiration, les temps d’attente excessifs et les demandes erronées. Pour plus d’informations sur les métriques, consultez la section [Métriques de détection](sql-database-intelligent-insights.md#detection-metrics) de ce document.

Les détériorations des performances de SQL Database identifiées sont enregistrées dans le journal de diagnostic avec des entrées intelligentes comprenant les propriétés suivantes :

| Propriété             | Détails              |
| :------------------- | ------------------- |
| les informations de base de données | Métadonnées relatives à une base de données sur laquelle une information a été détectée, telles qu’un URI de ressource. |
| Période observée | Heures de début et de fin de la période des informations détectées. |
| Métriques impactées | Métriques qui ont provoqué la génération d’une information : <ul><li>Augmentation de la durée de la requête [secondes].</li><li>Attente excessive [secondes].</li><li>Demandes ayant expiré [pourcentage].</li><li>Demandes erronées [pourcentage].</li></ul>|
| Valeur d’impact | Valeur d’une métrique mesurée. |
| Requêtes impactées et codes d’erreur | Hachage de requête ou code d’erreur. Ces éléments peuvent être utilisés pour établir facilement une corrélation avec les requêtes affectées. Des métriques ayant trait à une augmentation de durée de requête, à un temps d’attente, à des nombres d’expiration ou à des codes d’erreur sont fournies. |
| Détections | Détection identifiée au niveau de la base de données pendant la durée d’un événement. Il existe 15 modèles de détection. Pour plus d’informations, consultez [Résoudre les problèmes de performances liés à Azure SQL Database avec Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analyse de la cause racine | Analyse de la cause racine du problème identifié dans un format lisible. Certaines informations peuvent contenir une recommandation d’amélioration des performances lorsque cela est possible. |
|||

Les problèmes de performances enregistrés dans le journal de diagnostic sont marqués de l’un des trois états du cycle de vie d’un problème, à savoir « Actif », « Vérification » et « Terminé ». Une fois qu’un problème de performances est détecté, et aussi longtemps qu’il est jugé comme présent par l’intelligence intégrée de SQL Database, le problème est marqué de l’état « Actif ». Quand le problème est considéré comme atténué, il est vérifié et son état passe à « Vérification ». Une fois que l’intelligence intégrée de SQL Database considère que le problème est résolu, l’état de celui-ci passe à « Terminé ».

## <a name="use-intelligent-insights"></a>Utiliser Intelligent Insights

Intelligent Insights est un journal intelligent de diagnostic des performances. Il peut être intégré à d'autres produits pour la consommation et des applications spécifiques comme Azure Log Analytics, Azure Event Hubs et Stockage Azure, ou des produits tiers. 

Associé à Azure Log Analytics, Intelligent Insights est généralement utilisé pour afficher des informations via un navigateur Web. Mais l'un des moyens les plus faciles de le prendre en main consiste à tout simplement à utiliser le produit. Associé à Azure Event Hubs, Intelligent Insights est généralement utilisé pour configurer des scénarios de surveillance et d'alerte personnalisés. Associé à Stockage Azure, Intelligent Insights est généralement utilisé pour le développement d'applications personnalisées, par exemple la création de rapports personnalisés ou l'archivage et l'extraction de données.

Pour intégrer Intelligent Insights dans les autres produits Azure Log Analytics, Azure Event Hub, Stockage Azure, ou des produits tiers pour la consommation, vous devez d'abord journaliser Intelligent Insights (journal SQLInsights) puis configurer les données du journal Intelligent Insights afin de les diffuser en continu dans un de ces produits. Pour plus d'informations sur l'activation de la journalisation Intelligent Insights et la configuration des données du journal afin de les diffuser en continu dans un produit de consommation, consultez [Journalisation des métriques et diagnostics d’Azure SQL Database](sql-database-metrics-diag-logging.md). 

Pour une présentation pratique sur l'utilisation d'Intelligent Insights avec Azure Log Analytics et des scénarios d'utilisation standard, regardez la vidéo incorporée :


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights excelle dans la découverte et la résolution des problèmes affectant les performances liés à SQL Database. Pour résoudre les problèmes de performances liés à SQL Database à l’aide d’Intelligent Insights, voir [Résoudre les problèmes de performances liés à Azure SQL Database avec Intelligence Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="set-up-intelligent-insights-with-log-analytics"></a>Configurer Intelligent Insights avec Log Analytics 

Une solution Log Analytics offre des fonctionnalités de création de rapports et d’alertes en plus des données du journal de diagnostic d’Intelligent Insights.

Pour utiliser Intelligent Insights avec Log Analytics et configurer les données du journal Intelligent Insights afin de les diffuser en continu dans Log Analytics, voir [Journalisation des métriques et diagnostics d’Azure SQL Database](sql-database-metrics-diag-logging.md). 

L’exemple suivant montre un rapport Intelligent Insights dans Azure SQL Analytics :

![Rapport Intelligent Insights](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Une fois que le journal de diagnostic Intelligent Insights est configuré pour transmettre en continu des données à SQL Analytics, vous pouvez [surveiller la base de données SQL à l’aide de SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="set-up-intelligent-insights-with-event-hubs"></a>Configurer Intelligent Insights avec Event Hubs

Pour utiliser Intelligent Insights avec Event Hubs et configurer les données du journal Intelligent Insights afin de les diffuser en continu dans Event Hubs, consultez [Diffuser en continu des journaux de diagnostic Azure à Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).

Pour utiliser Event Hubs afin de personnaliser la surveillance et les alertes, consultez [Que faire des journaux de métriques et diagnostics dans Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs). 

## <a name="set-up-intelligent-insights-with-storage"></a>Configurer Intelligent Insights avec le stockage

Pour utiliser Intelligent Insights avec Stockage Azure et configurer les données du journal Intelligent Insights afin de les diffuser en continu dans Stockage Azure, consultez [Diffuser en continu dans Stockage Azure](sql-database-metrics-diag-logging.md#stream-into-storage).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Intégrations personnalisées du journal d’Intelligent Insights

Pour utiliser Intelligent Insights avec des outils tiers ou pour le développement d'une surveillance ou d'alertes personnalisées, consultez [Utiliser le journal de diagnostic du niveau de performance d’Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Métriques de détection

Les métriques utilisées pour les modèles de détection qui génèrent des informations d’Intelligent Insights sont basées sur la surveillance des éléments suivants :

- Durée de la requête
- Demandes de délai d’expiration
- Délais d’attente excessifs
- Demandes erronées

Le durée de la requête et les demandes de délai d’expiration sont utilisées comme modèles principaux dans la détection des problèmes de performances de la charge de travail de la base de données. Ces métriques sont utilisées parce qu’elles mesurent directement ce qui se passe avec la charge de travail. Pour détecter tous les cas possibles de détérioration des performances de la charge de travail, des délais d’attente excessifs et des demandes erronées sont utilisés en tant que modèles supplémentaires pour indiquer des problèmes affectant les performances de la charge de travail.

Le système considère automatiquement les changements de charge de travail et les modifications du nombre de demandes de requête adressées à la base de données afin de déterminer de façon dynamique les seuils normaux et anormaux de performances de la base de données.

Toutes les métriques sont considérées dans différentes relations via un modèle de données élaboré scientifiquement, qui classe par catégories les problèmes de performances détectés. Les informations intelligentes fournies incluent les suivantes :

* Détails du problème de performances détecté. 
* Analyse de la cause racine du problème détecté. 
* Recommandations, dans la mesure du possible, sur la façon d’améliorer les performances de la base de données SQL surveillée.

## <a name="query-duration"></a>Durée de la requête

Un modèle de détérioration de la durée de requête analyse des requêtes individuelles et détecte l’augmentation du temps nécessaire pour compiler et exécuter une requête par rapport à la ligne de base des performances.

Si l’intelligence intégrée de SQL Database détecte une augmentation significative du temps de compilation ou d’exécution des requêtes qui impacte les performances de la charge de travail, ces requêtes sont marquées comme présentant un problème de détérioration des performances de durée. 

Le journal de diagnostic Intelligent Insights génère le hachage de la requête dont les performances sont détériorées. Le hachage de requête indique si la détérioration des performances était liée à l’augmentation du délai de compilation ou d’exécution de la requête, laquelle a elle-même augmenté la durée de la requête.

## <a name="timeout-requests"></a>Demandes de délai d’expiration

Le modèle de dégradation des demandes de délai d’expiration analyse les requêtes individuelles et détecte toute augmentation des délais d’expiration au niveau de l’exécution de la requête, ainsi que les demandes de délai d’expiration globales au niveau de la base de données comparées à la période de référence des performances.

Certaines des requêtes peuvent expirer avant même d’atteindre l’étape d’exécution. Sur la base des workers abandonnés par rapport aux demandes effectuées, l’intelligence intégrée de SQL Database mesure et analyse toutes les requêtes qui ont atteint la base de données, qu’elles soient parvenues à l’étape d’exécution ou non. 

Une fois que le nombre de délais d’expiration pour les requêtes exécutées ou le nombre de workers de demande abandonnés franchissent le seuil géré par le système, un journal de diagnostic est rempli d’informations intelligentes.

Les informations générées contiennent le nombre de demandes de délai d’expiration et le nombre de requêtes ayant expiré. L’indication de la détérioration des performances est liée à l’augmentation du délai d’expiration à l’étape d’exécution, ou bien le niveau global de la base de données est fourni. Quand l’augmentation des délais d’expiration est considérée comme significative pour les performances de la base de données, ces requêtes sont marquées comme présentant un problème de détérioration des performances de délai d’expiration. 

## <a name="excessive-wait-times"></a>Délais d’attente excessifs

Le modèle des délais d’attente excessifs surveille des requêtes de base de données individuelles. Il détecte les statistiques d’attente anormalement élevées qui ont franchi les seuils absolus gérés par le système. Les métriques de délai d’attente excessif de requête sont observées à l’aide de la nouvelle fonctionnalité SQL Server de statistiques d’attente du magasin de données des requêtes (sys.query_store_wait_stats) :

- Atteinte des limites de ressources
- Atteinte des limites de ressources de pool élastique
- Nombre excessif de threads de travail ou de session
- Verrouillage de base de données excessif
- Sollicitation de la mémoire
- Autres statistiques d’attente

L’atteinte des limites des ressources ou des limites des ressources de pool élastique indiquent que la consommation des ressources disponibles sur un abonnement ou dans le pool élastique a franchi les seuils absolus. Ces statistiques indiquent une détérioration des performances de la charge de travail. Un nombre excessif de threads de travail ou de session indique une condition dans laquelle le nombre de threads de travail ou de session lancés a franchi les seuils absolus. Ces statistiques indiquent une détérioration des performances de la charge de travail.

Un verrouillage de base de données excessif indique une condition dans laquelle le nombre de verrous sur une base de données a franchi les seuils absolus. Cette statistique indique une détérioration des performances de la charge de travail. La sollicitation de la mémoire est une condition dans laquelle le nombre de threads qui demandent des allocations de mémoire a franchi un seuil absolu. Cette statistique indique une détérioration des performances de la charge de travail.

La détection d’autres statistiques d’attente indique une condition dans laquelle diverses métriques mesurées par l’intermédiaire des statistiques d’attente du magasin de données des requêtes ont franchi un seuil absolu. Ces statistiques indiquent une détérioration des performances de la charge de travail.

Quand des délais d’attente excessifs sont détectés, le journal de diagnostic Intelligent Insights produit, selon les données disponibles, des résultats variables des hachages des requêtes affectant et affectées dont les performances sont détériorées, des détails des métriques entraînant un délai d’attente d’exécution des requêtes et d’un délai d’attente mesuré.

## <a name="errored-requests"></a>Demandes erronées

Le modèle de dégradation des demandes erronées surveille les requêtes individuelles et détecte toute augmentation du nombre de requêtes erronées par rapport à la période de référence. Ce modèle surveille également les exceptions critiques qui ont franchi les seuils absolus gérés par l’intelligence intégrée de SQL Database. Le système considère automatiquement le nombre de demandes de requête adressées à la base de données, et rend compte de toutes les modifications de la charge de travail survenues durant la période analysée.

Lorsque l’augmentation mesurée des demandes erronées liée au nombre total de demandes effectuées est considéré comme significatif pour les performances de la charge de travail, les requêtes concernées sont marquées comme présentant des problèmes de détérioration des performances des demandes erronées.

Le journal Intelligent Insights génère le nombre de demandes erronées. Il indique si la détérioration des performances était liée à une augmentation des demandes erronées ou au franchissement d’un seuil d’exception critique surveillée et de la durée mesurée de la détérioration des performances. 

Si l’une des exceptions critiques surveillées franchit les seuil absolus gérés par le système, une information intelligente est générée avec des détails sur l’exception critique.

## <a name="next-steps"></a>Étapes suivantes
* Apprendre à [résoudre les problèmes de performances liés à SQL Database avec Intelligence Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
* Utiliser le [journal de diagnostic des performances d’Intelligent Insights dans SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
* Apprendre à [surveiller SQL Database à l’aide de SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
* Apprendre à [collecter et utiliser les données de journaux de vos ressources Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


