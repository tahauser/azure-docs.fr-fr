---
title: "Collecter et analyser les journaux d’activités Azure dans Log Analytics | Microsoft Docs"
description: "Vous pouvez utiliser la solution Journaux d’activité Azure pour analyser et rechercher le journal d’activité Azure parmi tous vos abonnements Azure."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: c13890862c058701268c07d032d6d990c659287a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics"></a>Collecter et analyser les journaux d’activités Azure dans Log Analytics

![Symbole des journaux d’activité Azure](./media/log-analytics-activity/activity-log-analytics.png)

La solution Activity Log Analytics vous aide à analyser et rechercher le [journal d’activité Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) parmi tous vos abonnements Azure. Le journal d’activité Azure est un journal qui fournit des informations sur les opérations réalisées sur les ressources de vos abonnements. Le journal d’activité était précédemment appelé *journal d’audit* ou *journal des opérations*, car il indique les événements pour vos abonnements.

Avec le journal d’activité, vous pouvez déterminer *qui*, *quand* et *quoi* pour toutes les opérations d’écriture (PUT, POST, DELETE) réalisées sur des ressources dans votre abonnement. Vous pouvez également comprendre l’état des opérations et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas d’opérations de lecture (GET) ni d’opérations pour les ressources qui utilisent le modèle de déploiement Classic.

Lorsque vous connectez vos journaux d’activité Azure à Log Analytics, vous pouvez :

- Analyser les journaux d’activité avec des affichages prédéfinis
- Analyser et rechercher des journaux d’activité parmi plusieurs abonnements Azure
- Conserver les journaux d’activité pendant plus de 90 jours<sup>1</sup>
- Mettre en corrélation des journaux d’activité avec d’autres données d’application et de plateforme Azure
- Consulter les activités opérationnelles regroupées par état
- Voir les tendances des activités se produisant sur chacun de vos services Azure
- Signaler les modifications d’autorisation sur toutes vos ressources Azure
- Identifier les problèmes d’intégrité de service ou de panne qui ont un impact sur vos ressources
- Utiliser la fonction Recherche de journal pour mettre en corrélation les activités d’utilisateur, les opérations de mise à l’échelle automatique, les modifications d’autorisation et l’intégrité du service avec d’autres journaux ou métriques de votre environnement

<sup>1</sup>Par défaut, Log Analytics conserve vos journaux d’activité Azure pendant 90 jours, même si vous êtes sur le niveau Gratuit. Ou si votre espace de travail est réglé sur une rétention de moins de 90 jours. Si votre espace de travail a une rétention de plus de 90 jours, les journaux d’activité sont conservés pendant la période de rétention de votre espace de travail.

Log Analytics collecte gratuitement les journaux d’activité et les conserve gratuitement pendant 90 jours. Si vous conservez des journaux pendant plus de 90 jours, des frais de rétention vous seront facturés pour les données stockées pendant plus de 90 jours.

Si vous êtes sur le niveau de tarification Gratuit, les journaux d’activité ne s’appliquent pas à votre consommation de données quotidienne.

## <a name="connected-sources"></a>Sources connectées

Contrairement à la plupart des autres solutions Log Analytics, les données ne sont pas collectées pour les journaux d’activité par des agents. Toutes les données utilisées par la solution proviennent directement d’Azure.

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| [Agents Windows](log-analytics-windows-agent.md) | Non  | La solution ne collecte aucune information à partir d’agents Windows. |
| [Agents Linux](log-analytics-linux-agents.md) | Non  | La solution ne collecte aucune information à partir d’agents Linux. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Non  | La solution ne collecte aucune information à partir d’agents dans un groupe d’administration SCOM connecté. |
| [Compte Azure Storage](log-analytics-azure-storage.md) | Non  | La solution ne collecte aucune information à partir de stockage Azure. |

## <a name="prerequisites"></a>configuration requise

- Pour accéder aux informations des journaux d’activité Azure, vous devez posséder un abonnement Azure.

## <a name="configuration"></a>Configuration

Procédez comme suit pour configurer la solution Activity Log Analytics pour vos espaces de travail.

1. Activez la solution Activity Log Analytics depuis la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).
2. Configurez les journaux d’activité de manière à ce qu’ils accèdent à votre espace de travail Log Analytics.
    1. Dans le portail Azure, sélectionnez votre espace de travail, puis cliquez sur **Journal d’activité Azure**.
    2. Pour chaque abonnement, cliquez sur le nom de l’abonnement.  
        ![ajouter un abonnement](./media/log-analytics-activity/add-subscription.png)
    3. Dans le panneau *SubscriptionName*, cliquez sur **Connecter**.  
        ![connecter un abonnement](./media/log-analytics-activity/subscription-connect.png)

Si vous ajoutez la solution via le portail OMS, vous verrez la vignette suivante. Connectez-vous au portail Azure pour connecter un abonnement Azure à votre espace de travail.  
![exécution de l’évaluation](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>Utilisation de la solution

Lorsque vous ajoutez la solution Activity Log Analytics à votre espace de travail, la vignette **Journal d’activité Azure** est ajoutée à votre tableau de bord de présentation. Cette vignette affiche le nombre d’enregistrements d’activité Azure pour les abonnements Azure auxquels la solution a accès.

![Vignette Journaux d’activité Azure](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Consulter des journaux d’activité Azure

Cliquez sur la vignette **Journaux d’activité Azure** pour ouvrir le tableau de bord **Journaux d’activité Azure**. Le tableau de bord comprend les panneaux figurant dans le tableau suivant. Chaque panneau répertorie jusqu'à 10 éléments répondant à ses critères en ce qui concerne l’étendue et l’intervalle de temps spécifiés. Vous pouvez exécuter une recherche dans les journaux qui renvoie tous les enregistrements. Pour cela, cliquez sur **Afficher tout** en bas du panneau ou cliquez sur l’en-tête de panneau.

Les données de journal d’activité apparaissent uniquement *après* avoir configuré vos journaux d’activité pour qu’ils accèdent à la solution. Sans cela, vous ne pouvez pas les consulter.

| Panneau | Description |
| --- | --- |
| Entrées de journal d’activité Azure | Affiche un graphique à barres du plus grand nombre d’enregistrements d’entrée de journal d’activité Azure pour la période sélectionnée et affiche la liste des 10 premiers appelants d’activité. Cliquez sur le graphique à barres pour exécuter une recherche dans les journaux de <code>AzureActivity</code>. Cliquez sur un élément appelant pour exécuter une recherche dans les journaux qui renvoie toutes les entrées de journal d’activité pour cet élément. |
| Journaux d’activité par état | Affiche un graphique en anneau de l’état des journaux d’activité Azure pour la période sélectionnée. Affiche également la liste des dix premiers enregistrements d’état. Cliquez sur le graphique pour exécuter une recherche dans les journaux de <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Cliquez sur un élément d’état pour exécuter une recherche dans les journaux qui renvoie toutes les entrées de journal d’activité pour cet enregistrement d’état. |
| Journaux d’activité par ressource | Affiche le nombre total de ressources avec les journaux d’activité et répertorie les dix premières ressources avec le nombre d’enregistrements pour chaque ressource. Cliquez sur la zone Total pour exécuter une recherche dans les journaux de <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, qui affiche toutes les ressources Azure disponibles pour la solution. Cliquez sur une ressource pour exécuter une recherche dans les journaux qui renvoie tous les enregistrements d’activité pour cette ressource. |
| Journaux d’activité par fournisseur de ressources | Affiche le nombre total de fournisseurs de ressources qui génèrent des journaux d’activité et répertorie les dix premiers. Cliquez sur la zone Total pour exécuter une recherche dans les journaux de <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, qui affiche tous les fournisseurs de ressources Azure. Cliquez sur un fournisseur de ressources pour exécuter une recherche dans les journaux qui renvoie tous les enregistrements d’activité pour ce fournisseur. |

![Tableau de bord Journaux d’activité Azure](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>Étapes suivantes

- Créez une [alerte](log-analytics-alerts-creating.md) lorsqu’une activité spécifique se produit.
- Utilisez la fonction [Recherche dans les journaux](log-analytics-log-searches.md) pour afficher des informations détaillées provenant de vos journaux d’activité.
