---
title: "Générer une solution de gestion dans Azure | Microsoft Docs"
description: "Les solutions de gestion incluent les scénarios de gestion empaquetée dans Azure que les clients peuvent ajouter à leur espace de travail Log Analytics.  Cet article fournit des informations sur la manière dont vous pouvez créer des solutions de gestion à utiliser dans votre propre environnement ou à mettre à la disposition de vos clients."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0f5d42292c8e885491aed55ada129f05cb3bcd35
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Concevoir et générer une solution de gestion dans Azure (préversion)
> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans Azure qui sont actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.

Les [solutions de gestion](operations-management-suite-solutions.md) fournissent des scénarios de gestion empaquetée que les clients peuvent ajouter à leur espace de travail Log Analytics.  Cet article présente un processus de base pour concevoir et générer une solution de gestion adaptée aux exigences les plus courantes.  Si vous débutez avec la création de solutions de gestion, vous pouvez utiliser ce processus comme point de départ et exploiter les concepts des solutions plus complexes à mesure que vos besoins évoluent.

## <a name="what-is-a-management-solution"></a>Qu’est-ce qu’une solution de gestion ?

Les solutions de gestion contiennent des ressources Azure qui fonctionnent ensemble pour réaliser un scénario de gestion spécifique.  Elles sont implémentées en tant que [modèles de gestion des ressources](../azure-resource-manager/resource-manager-template-walkthrough.md) qui contiennent des détails sur la procédure d’installation et de configuration des ressources qu’elles contiennent une fois la solution installée.

La stratégie de base consiste à démarrer votre solution de gestion en créant les composants individuels dans votre environnement Azure.  Une fois que la fonctionnalité fonctionne correctement, vous pouvez commencer à les empaqueter dans un [fichier de solution de gestion](operations-management-suite-solutions-solution-file.md). 


## <a name="design-your-solution"></a>Conception de votre solution
Le modèle le plus courant pour une solution de gestion est illustré dans le diagramme suivant.  Les différents composants de ce modèle sont décrits ci-dessous.

![Vue d’ensemble des solutions de gestion](media/operations-management-suite-solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Sources de données
La première étape de la conception d’une solution consiste à déterminer les données dont vous avez besoin à partir du référentiel Log Analytics.  Ces données peuvent être collectées par une [source de données](../log-analytics/log-analytics-data-sources.md) ou [une autre solution](operations-management-suite-solutions.md), ou votre solution peut avoir besoin de fournir le processus de collecte.

Il existe plusieurs façons de collecter des sources de données dans le référentiel Log Analytics, comme décrit dans [Sources de données dans Log Analytics](../log-analytics/log-analytics-data-sources.md).  Cela inclut les événements du journal des événements Windows ou ceux générés par Syslog en plus des compteurs de performances pour les clients Windows et Linux.  Vous pouvez également collecter des données à partir des ressources Azure collectées par Azure Monitor.  

Si vous avez besoin de données qui ne sont accessibles via aucune des sources de données disponibles, vous pouvez utiliser [l’API du collecteur de données HTTP](../log-analytics/log-analytics-data-collector-api.md) qui vous permet d’écrire des données dans le référentiel Log Analytics à partir de n’importe quel client pouvant appeler une API REST.  L’approche la plus courante de la collecte de données personnalisées dans une solution de gestion consiste à créer un [runbook dans Azure Automation](../automation/automation-runbook-types.md) qui collecte les données requises à partir des ressources Azure ou externes et utilise l’API du collecteur de données afin d’écrire dans le référentiel.  

### <a name="log-searches"></a>Recherches dans les journaux
Les [recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) sont utilisées pour extraire et analyser des données dans le référentiel Log Analytics.  Elles sont utilisées par les affichages et les alertes et permettent à l’utilisateur d’effectuer une analyse ad hoc des données dans le référentiel.  

Définissez les requêtes qui peuvent être utiles à l’utilisateur même si elles ne sont pas utilisées par des vues ou des alertes.  Elles seront disponibles dans le portail en tant que recherches enregistrées, et vous pouvez également les inclure dans une [Liste de requêtes de composants de visualisation](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) dans votre vue personnalisée.

### <a name="alerts"></a>Alertes
[Les alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md) identifient les problèmes via des [recherches dans les journaux](#log-searches) sur les données dans le référentiel.  Elles informent l’utilisateur ou exécutent automatiquement une action en réponse. Vous devez identifier différentes conditions d’alerte pour votre application et inclure des règles d’alerte correspondantes dans votre fichier solution.

Si le problème peut potentiellement être corrigé avec un processus automatisé, vous devrez généralement créer un runbook dans Azure Automation pour effectuer cette correction.  La plupart des services Azure peuvent être gérés avec des [cmdlets](/powershell/azure/overview) que le runbook peut exploiter pour exécuter ce type de fonctionnalité.

Si votre solution nécessite une fonctionnalité externe en réponse à une alerte, alors vous pouvez utiliser une [réponse webhook](../log-analytics/log-analytics-alerts-actions.md).  Cela vous permet d’appeler un service web externe envoyant des informations à partir de l’alerte.

### <a name="views"></a>Views
Les vues dans Log Analytics permettent de visualiser les données à partir du référentiel de Log Analytics.  Chaque solution contient en général une vue unique avec une [mosaïque](../log-analytics/log-analytics-view-designer-tiles.md) qui s’affiche sur le tableau de bord principal de l’utilisateur.  La vue peut contenir n’importe quel nombre de [composants de visualisation](../log-analytics/log-analytics-view-designer-parts.md) pour fournir différentes visualisations des données collectées à l’utilisateur.

Vous [créez des vues personnalisées à l’aide du Concepteur de vue](../log-analytics/log-analytics-view-designer.md) que vous pouvez ultérieurement exporter pour l’inclure dans votre fichier solution.  


## <a name="create-solution-file"></a>Créer le fichier solution
Une fois que vous avez configuré et testé les composants qui feront partie de votre solution, vous pouvez [créer votre fichier solution](operations-management-suite-solutions-solution-file.md).  Vous allez implémenter les composants de la solution dans un [modèle Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) qui inclut une [ressource de solution](operations-management-suite-solutions-solution-file.md#solution-resource) avec des relations aux autres ressources dans le fichier.  


## <a name="test-your-solution"></a>Tester votre solution
Lorsque vous développez votre solution, vous devez l’installer et la tester dans votre espace de travail.  Vous pouvez faire cela en utilisant l’une des méthodes disponibles pour [tester et installer des modèles Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publier votre solution
Une fois que vous avez terminé et testé votre solution, vous pouvez la rendre disponible aux clients via les sources suivantes.

- **Modèles de démarrage rapide Azure**.  [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) est un ensemble de modèles Resource Manager fournis par la communauté via GitHub.  Vous pouvez rendre votre solution disponible en suivant les informations dans le [guide de contribution](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Place de marché Azure**.  La [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/) vous permet de distribuer et de vendre votre solution à d’autres développeurs, ISV et professionnels de l’informatique.  Pour apprendre à publier votre solution dans la Place de marché Azure, consultez la section [Comment publier et gérer une offre dans la Place de marché Azure](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [créer un fichier de solution](operations-management-suite-solutions-solution-file.md) pour votre solution de gestion.
* Découvrez comment [créer des modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Dans [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates), recherchez des exemples de modèles Resource Manager.