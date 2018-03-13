---
title: "Présentation de Log Analytics | Microsoft Docs"
description: "Log Analytics est un service d’Azure qui vous permet de collecter et d’analyser les données opérationnelles générées par les ressources de votre cloud et de vos environnements locaux.  Cet article fournit une vue d'ensemble des différents composants de Log Analytics ainsi que des liens vers un contenu détaillé."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: bwren
ms.openlocfilehash: a95528f5bd259a36ea96c7bc0660ca082c09d6e6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="what-is-log-analytics"></a>Présentation de Log Analytics
Log Analytics est un service d’Azure qui surveille vos environnements cloud et locaux et assure leur disponibilité et leurs performances.  Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources.  Cet article traite brièvement de la valeur apportée par Log Analytics, fournit une vue d’ensemble de son fonctionnement et des liens vers des contenus plus détaillés pour aller plus loin.

## <a name="is-log-analytics-for-you"></a>Log Analytics est-il adapté à vos besoins ?
Si aucune analyse n’est actuellement mise en place pour votre environnement Azure, vous devez commencer avec [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) qui recueille et analyse les données de vos ressources Azure.  Log Analytics peut [collecter des données à partir d’Azure Monitor](log-analytics-azure-storage.md) pour les corréler avec d’autres données et fournir une analyse supplémentaire.

Si vous souhaitez analyser votre environnement local ou si vous disposez déjà d’une analyse existante effectuée à l’aide de services comme Azure Monitor ou System Center Operations Manager, sachez que Log Analytics peut se révéler très utile.  La collecte peut s’effectuer directement à partir de vos agents et également de ces autres outils vers un référentiel unique.  Les outils d’analyse de Log Analytics, tels que les recherches de journaux, les vues et les solutions fonctionnent avec toutes les données collectées. Vous disposez ainsi d’une analyse centralisée de l’ensemble de votre environnement.


## <a name="using-log-analytics"></a>Utilisation de Log Analytics
Vous pouvez accéder à Log Analytics via le portail Azure qui s’exécute dans un navigateur et vous donne accès aux paramètres de configuration et à plusieurs outils pour analyser et agir sur les données collectées.  À partir du portail, vous pouvez utiliser des [recherches dans les journaux](log-analytics-log-searches.md) pour créer des requêtes et analyser les données collectées, des [tableaux de bord](log-analytics-dashboards.md) personnalisables avec des vues graphiques de vos recherches les plus précieuses, et des [solutions](log-analytics-add-solutions.md) intégrant des fonctionnalités et des outils d’analyse supplémentaires.

L’image ci-dessous affiche l’écran de Vue d’ensemble présentant des informations résumées pour les [solutions](#add-functionality-with-management-solutions) installées dans l’espace de travail.  Vous pouvez cliquer sur n’importe quelle vignette pour explorer les données de cette solution.

![Portail OMS](media/log-analytics-overview/portal.png)

Log Analytics inclut un langage de requête pour rapidement récupérer et consolider les données dans le référentiel.  Vous pouvez créer et enregistrer des [recherches de journaux](log-analytics-log-searches.md) afin d’analyser les données dans le portail, ou exécuter automatiquement des recherches de journaux pour créer une alerte si les résultats de la requête signalent une condition importante.

![Recherche dans les journaux](media/log-analytics-overview/log-search.png)

Pour analyser des données en dehors de Log Analytics, vous pouvez exporter les données vers des outils tels que [Power BI](log-analytics-powerbi.md) ou Excel.  Vous pouvez également utiliser les [API de recherche de journal](log-analytics-log-search-api.md) pour créer des solutions personnalisées qui exploitent les données Log Analytics, ou pour les intégrer à d'autres systèmes.

## <a name="add-functionality-with-management-solutions"></a>Ajouter des fonctionnalités avec des solutions de gestion
Les [solutions de gestion](log-analytics-add-solutions.md) ajoutent des fonctionnalités à Log Analytics en fournissant des données et des outils d’analyse supplémentaires à Log Analytics.  Elles peuvent également définir de nouveaux types d’enregistrements à collecter qui peuvent être analysés avec des recherches de journaux ou via une interface utilisateur supplémentaire fournie par la solution dans le tableau de bord.  L’image de l’exemple ci-dessous montre la [solution de suivi des modifications](log-analytics-change-tracking.md)

![Solution de suivi des modifications](media/log-analytics-overview/change-tracking.png)

Les solutions sont disponibles pour diverses fonctions et des solutions supplémentaires sont ajoutées régulièrement.  Vous pouvez facilement parcourir les solutions disponibles et [les ajouter à votre espace de travail](log-analytics-add-solutions.md) à partir d’Azure Marketplace.  Nombre d’entre elles sont automatiquement déployées et immédiatement opérationnelles, tandis que d’autres peuvent nécessiter une configuration.

![Galerie de solutions](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Composants de Log Analytics
Au cœur de Log Analytics se trouve un référentiel de données collectées, qui est hébergé dans le cloud Azure.  Les données sont collectées à partir de sources connectées en configurant des sources de données et en ajoutant des solutions à votre abonnement.  Les sources de données et les solutions créeront chacune différents types d'enregistrements avec leur propre jeu de propriétés, mais elles peuvent toujours être analysées ensemble dans des requêtes vers le référentiel.  Vous pouvez ainsi utiliser les mêmes outils et méthodes pour travailler avec différents types de données collectées par différentes sources.

![Composants de Log Analytics](media/log-analytics-overview/overview.png)

Les sources connectées représentent les ordinateurs et autres ressources qui génèrent des données collectées par Log Analytics.  Cela peut inclure des agents installés sur des ordinateurs [Windows](log-analytics-windows-agent.md) et [Linux](log-analytics-linux-agents.md) directement connectés, ou des agents d’un [groupe d’administration System Center Operations Manager connecté](log-analytics-om-agents.md).  Pour les ressources Azure, Log Analytics collecte des données à partir d’[Azure Monitor et Azure Diagnostics](log-analytics-azure-storage.md).

[sources de données](log-analytics-data-sources.md) représentent les différents types de données collectées à partir de chaque source connectée.  Cela inclut les [événements](log-analytics-data-sources-windows-events.md) et les [données de performances](log-analytics-data-sources-performance-counters.md) d’agents [Windows](log-analytics-data-sources-windows-events.md) et Linux en plus des sources telles que les [journaux IIS](log-analytics-data-sources-iis-logs.md) et les [journaux de texte personnalisés](log-analytics-data-sources-custom-logs.md).  Vous configurez chaque source de données que vous souhaitez collecter, et la configuration est automatiquement remise à chaque source connectée.

Si vous avez des besoins spécifiques, vous pouvez utiliser l’[API de collecte de données HTTP](log-analytics-data-collector-api.md) pour écrire des données dans le référentiel à partir d’un client de l’API REST.

## <a name="log-analytics-architecture"></a>Architecture Log Analytics
Les exigences de déploiement de Log Analytics sont minimes car les composants centraux sont hébergés dans le cloud Azure.  Cela inclut le référentiel en plus des services qui vous permettent de mettre en corrélation et d’analyser les données collectées.  Le portail étant accessible depuis n'importe quel navigateur, aucun logiciel client n’est requis.

Vous devez installer les agents sur des ordinateurs [Windows](log-analytics-windows-agent.md) et [Linux](log-analytics-linux-agents.md), mais aucun agent supplémentaire n’est nécessaire pour les ordinateurs déjà membres d’un [groupe d’administration SCOM connecté](log-analytics-om-agents.md).  Les agents SCOM continueront à communiquer avec les serveurs d’administration qui transmettront leurs données à Log Analytics.  Certaines solutions nécessiteront cependant que les agents communiquent directement avec Log Analytics.  La documentation de chaque solution spécifie les besoins de communication.

Lorsque vous vous [inscrivez à Log Analytics](log-analytics-get-started.md), vous créez un espace de travail.  Considérez cet espace de travail comme un environnement Log Analytics unique avec son propre référentiel de données, et ses propres sources de données et solutions. Vous pouvez créer plusieurs espaces de travail dans votre abonnement afin de prendre en charge plusieurs environnements, par exemple de production et de test.

![Architecture Log Analytics](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>Étapes suivantes
* [Inscrivez-vous pour obtenir un compte Log Analytics gratuit](log-analytics-get-started.md) que vous pouvez tester dans votre environnement.
* Affichez les différentes [sources de données](log-analytics-data-sources.md) disponibles pour collecter des données dans Log Analytics.
* [Parcourez les solutions disponibles dans la galerie de solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités à Log Analytics.

