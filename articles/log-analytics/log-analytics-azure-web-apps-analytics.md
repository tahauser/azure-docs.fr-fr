---
title: "Afficher les données analytiques d’Azure Web Apps | Microsoft Docs"
description: "Vous pouvez utiliser la solution Azure Web Apps Analytics pour obtenir un aperçu de vos applications Azure Web en collectant différentes mesures pour toutes les ressources de votre application Azure Web."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: magoedte
ms.openlocfilehash: 7c22950c391707cdfe14ca242ea82a317be0e46e
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Afficher des données analytiques pour des métriques sur toutes les ressources de votre application Azure Web

![Symbole Web Apps](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  
La solution Azure Web Apps Analytics (version préliminaire) fournit des informations sur vos [applications Azure Web](../app-service/app-service-web-overview.md) en collectant différentes mesures pour toutes les ressources de votre application Azure Web. Avec la solution, vous pouvez analyser et rechercher des données métriques de ressource Web App.

À l’aide de la solution, vous pouvez afficher :

- Les applications web avec le temps de réponse le plus élevé
- Le nombre de requêtes sur vos applications web, dont celles qui ont réussi et échoué
- Les applications web avec le trafic entrant et sortant le plus conséquent
- Les plans de service avec l’utilisation la plus élevée du processeur et de la mémoire
- Les opérations du journal des activités Azure Web Apps

## <a name="connected-sources"></a>Sources connectées

Contrairement à la plupart des autres solutions Log Analytics, les données ne sont pas collectées pour Azure Web Apps des agents. Toutes les données utilisées par la solution proviennent directement d’Azure.

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| [Agents Windows](log-analytics-windows-agent.md) | Non  | La solution ne collecte aucune information à partir d’agents Windows. |
| [Agents Linux](log-analytics-linux-agents.md) | Non  | La solution ne collecte aucune information à partir d’agents Linux. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Non  | La solution ne collecte aucune information à partir d’agents dans un groupe d’administration SCOM connecté. |
| [Compte Azure Storage](log-analytics-azure-storage.md) | Non  | La solution ne collecte aucune information à partir de stockage Azure. |

## <a name="prerequisites"></a>configuration requise

- Pour accéder aux informations de mesure de ressources Azure Web App, vous devez posséder un abonnement Azure.

## <a name="configuration"></a>Configuration

Procédez comme suit pour configurer la solution Azure Web Apps Analytics pour vos espaces de travail.

1. Activez la solution Azure Web Apps Analytics depuis la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).
2. [Activez la journalisation des mesures de ressources Azure pour OMS à l’aide de PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

La solution Azure Web Apps Analytics collecte deux ensembles de mesures à partir d’Azure :

- Métriques Azure Web Apps
  - Plage de travail moyenne de la mémoire
  - Temps de réponse moyen
  - Octets reçus et envoyés
  - Temps processeur
  - Requests
  - Plage de travail de la mémoire
  - Httpxxx
- Métriques du plan App Service
  - Octets reçus et envoyés
  - Pourcentage UC
  - Longueur de file d'attente de disque
  - Longueur de la file d’attente HTTP
  - Pourcentage de mémoire

Les mesures du plan App Service sont collectées uniquement si vous utilisez un plan de service dédié. Cela ne s’applique pas aux plans App Service gratuits ou partagés.

Si vous ajoutez la solution via le portail OMS, vous verrez la vignette suivante. Vous devez [activer la journalisation des mesures de ressources Azure pour OMS avec PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

![Notification d’exécution de l’évaluation](./media/log-analytics-azure-web-apps-analytics/performing-assessment.png)

Après avoir configuré la solution, les données devraient commencer à circuler vers votre espace de travail dans les 15 minutes.

## <a name="using-the-solution"></a>Utilisation de la solution

Lorsque vous ajoutez la solution Azure Web Apps Analytics à votre espace de travail, la vignette **Azure Web Apps Analytics** est ajoutée à votre tableau de bord de présentation. Cette vignette affiche le nombre d’enregistrements d’applications web Azure auxquels la solution a accès dans votre abonnement Azure.

![Mosaïque Azure Web Apps Analytics](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Afficher les informations d’Azure Web Apps Analytics

Cliquez sur la mosaïque **Azure Web Apps Analytics** afin d’ouvrir le tableau de bord **Azure Web Apps Analytics**. Le tableau de bord comprend les panneaux figurant dans le tableau suivant. Chaque panneau répertorie jusqu'à dix éléments répondant à ses critères en ce qui concerne l’étendue et l’intervalle de temps spécifiés. Vous pouvez exécuter une recherche dans les journaux qui renvoie tous les enregistrements. Pour cela, cliquez sur **Afficher tout** en bas du panneau ou cliquez sur l’en-tête de panneau.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| Colonne | Description |
| --- | --- |
| Azure Webapps |   |
| Tendances de requêtes Web Apps | Affiche un graphique en courbes de l’évolution des requêtes Web Apps pour la plage de dates que vous avez sélectionnée et présente la liste des dix requêtes les plus fréquentes. Cliquez sur le graphique en courbes pour exécuter une recherche dans les journaux de <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* (MetricName=Requests OR MetricName=Http*) &#124; measure avg(Average) by MetricName interval 1HOUR</code> <br>Cliquez sur un élément de requête web pour exécuter une recherche de journal pour la tendance des métriques de requête web. |
| Temps de réponse de Web Apps | Affiche un graphique en courbes du temps de réponse de Web Apps pour la plage de dates que vous avez sélectionnée. Affiche également une liste des dix premiers temps de réponse de Web Apps. Cliquez sur le graphique pour exécuter une recherche dans les journaux de <code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* MetricName="AverageResponseTime" &#124; measure avg(Average) by Resource interval 1HOUR</code>.<br> Cliquez sur une application web pour exécuter une recherche de journal retournant les temps de réponse de cette application. |
| Trafic de Web Apps | Affiche un graphique en courbes pour le trafic Web Apps, en Mo, et répertorie le trafic des principales applications web. Cliquez sur le graphique pour exécuter une recherche dans les journaux de <code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"*  MetricName=BytesSent OR BytesReceived &#124; measure sum(Average) by Resource interval 1HOUR</code>.<br> Toutes les applications web sont présentées avec le trafic pour la dernière minute. Cliquez sur une application web pour exécuter une recherche de journal indiquant les octets reçus et envoyés pour l’application web. |
| Plans Azure App Service |   |
| Plans App Service avec utilisation du processeur &gt; 80 % | Affiche le nombre total de plans App Service avec une utilisation du processeur supérieure à 80 % et répertorie le top 10 des plans App Service par utilisation du processeur. Cliquez sur la zone Total pour exécuter une recherche dans les journaux de <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=CpuPercentage &#124; measure Avg(Average) by Resource</code><br> Cela affiche une liste de vos plans App Service et leur utilisation moyenne du processeur. Cliquez sur un plan App Service pour exécuter une recherche dans les journaux indiquant son utilisation moyenne du processeur. |
| Plans App Service avec utilisation de mémoire &gt; 80 % | Affiche le nombre total de plans App Service avec une utilisation de la mémoire supérieure à 80 % et répertorie le top 10 des plans App Service par utilisation de la mémoire. Cliquez sur la zone Total pour exécuter une recherche dans les journaux de <code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=MemoryPercentage &#124; measure Avg(Average) by Resource</code><br> Cela affiche une liste de vos plans App Service et leur utilisation moyenne de la mémoire. Cliquez sur un plan App Service pour exécuter une recherche dans les journaux indiquant son utilisation moyenne de la mémoire. |
| Journaux d’activité Azure Web Apps |   |
| Audit des activité Azure Web Apps | Affiche le nombre total d’applications web avec [journaux d’activité](log-analytics-activity.md) et répertorie les 10 principales opérations de journal d’activité. Cliquez sur la zone Total pour exécuter une recherche dans les journaux de <code>Type=AzureActivity ResourceProvider= "Azure Web Sites" &#124; measure count() by OperationName</code><br> Cela affiche la liste des opérations de journal d’activité. Cliquez sur une opération de journal d’activité pour exécuter une recherche dans les journaux qui répertorie les enregistrements pour l’opération. |



### <a name="azure-web-apps"></a>Azure Web Apps 

Dans le tableau de bord, vous pouvez effectuer un zoom avant pour obtenir plus d’informations sur les métriques de vos applications web. Ce premier ensemble de panneaux illustre la tendance des requêtes Web Apps, le nombre d’erreurs (par exemple, HTTP 404), le trafic et temps de réponse moyen au fil du temps. Il montre également une répartition de ces mesures pour différentes applications web.

![Panneaux Azure Web Apps](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

Une des principales raisons de l’affichage de ces données est de vous permettre d’identifier les applications web avec un temps de réponse élevé et de déterminer la cause. Une limite de seuil s’applique également pour vous aider à identifier facilement les applications avec des problèmes.

- Les applications web en rouge ont un temps de réponse supérieur à 1 seconde.
- Les applications web en orange ont un temps de réponse supérieur à 0,7 seconde et inférieur à 1 seconde.
- Les applications web en vert ont un temps de réponse inférieur à 0,7 seconde.

Dans l’image d’exemple de recherche dans les journaux suivante, vous pouvez voir que l’application web *anugup3* a un temps de réponse beaucoup plus élevé que les autres applications web.

![Exemple de recherche de journal](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>Plans App Service

Si vous utilisez des plans de service dédiés, vous pouvez également collecter des mesures pour vos plans App Service. Dans cette vue, vous pouvez voir vos plans App Service avec une utilisation élevée du processeur ou de la mémoire (&gt; 80 %). Elle montre également les services d’application avec les utilisations de processeur ou de mémoire les plus élevées. De même, une limite de seuil s’applique pour vous aider à identifier facilement les applications avec des problèmes.

- Les plans App Service en rouge ont une utilisation du processeur/de la mémoire supérieure à 80 %.
- Les plans App Service en orange ont une utilisation du processeur/de la mémoire supérieure à 60 % et inférieure à 80 %.
- Les plans App Service en vert ont une utilisation du processeur/de la mémoire inférieure à 60 %.

![Panneaux de plan Azure App Service](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Recherches dans les journaux Azure Web Apps

La **Liste de requêtes de recherche Azure Web Apps populaires** vous montre tous les journaux d’activité liés aux applications web, qui fournissent des informations sur les opérations qui ont été effectuées sur vos ressources Web Apps. Elle répertorie également toutes les opérations associées et leur nombre d’occurrences.

À l’aide d’une des requêtes de recherche dans les journaux comme point de départ, vous pouvez facilement créer une alerte. Par exemple, vous souhaiterez peut-être créer une alerte lorsque le temps de réponse moyen d’une mesure est supérieur à toutes les 1 seconde.

## <a name="next-steps"></a>Étapes suivantes

- Créez une [alerte](log-analytics-alerts-creating.md) pour une mesure spécifique.
- Utilisez la fonction [Recherche dans les journaux](log-analytics-log-searches.md) pour afficher des informations détaillées provenant de vos journaux d’activité.
