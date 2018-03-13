---
title: Tableaux de bord et navigation dans Azure Application Insights | Microsoft Docs
description: "Créez des vues de votre principaux graphiques et requêtes APM."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 5ec0412dc91a7e5c28ee9ee820df9584ffa80cd8
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigation et tableaux de bord dans le portail Application Insights
Une fois que vous avez [configuré Application Insights sur votre projet](app-insights-overview.md), les données de télémétrie relatives aux performances et à l’utilisation de votre application apparaissent dans les ressources Application Insights de votre projet dans le [portail Azure](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Rechercher vos données de télémétrie
Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à la ressource Application Insights que vous avez créée pour votre application.

![Cliquez sur Parcourir, sélectionnez Application Insights, puis sélectionnez votre application.](./media/app-insights-dashboards/00-start.png)

Le panneau de vue d’ensemble (page) de votre application affiche un résumé des principales mesures de diagnostic de votre application et constitue une passerelle vers les autres fonctionnalités du portail.

![Procédures principales d'affichage de vos données de télémétrie](./media/app-insights-dashboards/010-oview.png)

Vous pouvez personnaliser tous les graphiques et grilles et les intégrer à un tableau de bord. De cette façon, vous pouvez regrouper les données de télémétrie clés issues de différentes applications sur un tableau de bord central.

## <a name="dashboards"></a>Tableaux de bord
La première chose que vous voyez après vous être connecté au [portail Microsoft Azure](https://portal.azure.com) est un tableau de bord. Celui-ci vous permet de rassembler les graphiques les plus importants pour vous pour toutes vos ressources Azure, notamment les données de télémétrie d’[Azure Application Insights](app-insights-overview.md).

![Un tableau de bord personnalisé.](./media/app-insights-dashboards/31.png)

1. **Accédez à des ressources spécifiques** telles que votre application dans Application Insights par le biais de la barre de gauche.
2. **Revenez au tableau de bord actuel** ou basculez vers les autres vues récentes en utilisant le menu déroulant en haut à gauche.
3. **Changez de tableaux de bord** en utilisant le menu déroulant sur le titre du tableau de bord.
4. **Créez, modifiez et partagez des tableaux de bord** à l’aide de la barre d’outils du tableau de bord.
5. **Modifiez le tableau de bord** en pointant sur une vignette, puis en utilisant sa barre supérieure pour déplacer, personnaliser ou supprimer la vignette.

## <a name="add-to-a-dashboard"></a>Ajouter des données à un tableau de bord
Lorsque vous examinez un panneau ou un ensemble de graphiques qui est particulièrement intéressant, vous pouvez en épingler une copie au tableau de bord. Celui-ci sera affiché lors de votre prochain accès au tableau de bord.

![Pour épingler un graphique, passez la souris sur celui-ci, puis cliquez sur « ... » dans l’en-tête.](./media/app-insights-dashboards/33.png)

1. Épinglez un graphique au tableau de bord. Une copie du graphique apparaît sur le tableau de bord.
2. Épinglez la totalité du panneau au tableau de bord. Il apparaît alors sur le tableau de bord sous la forme d’une vignette sur laquelle vous pouvez cliquer.
3. Cliquez sur le coin supérieur gauche pour revenir au tableau de bord actuel. Vous pouvez ensuite utiliser le menu déroulant pour revenir à la vue actuelle.

Notez que les graphiques sont regroupés en vignettes : une vignette peut contenir plusieurs graphiques. Vous épinglez la vignette entière au tableau de bord.

Le graphique est automatiquement actualisé à une fréquence qui varie selon l’intervalle de temps du graphique :

* Intervalle de temps de 1 heure maximum : actualisation toutes les 5 minutes
* Intervalle de temps entre 1 et 24 heures : actualisation toutes les 15 minutes
* Intervalle de temps supérieur à 24 heures : (intervalle de temps)/60.

### <a name="pin-any-query-in-analytics"></a>Épinglez n’importe quelle requête dans Analytics
Vous pouvez également [épingler des graphiques Analytics](app-insights-analytics-using.md#pin-to-dashboard) à un tableau de bord [partagé](#share-dashboards-with-your-team). Ainsi, vous pouvez ajouter les graphiques de toute requête arbitraire en parallèle des mesures standards. 

Les résultats sont recalculés automatiquement toutes les heures. Cliquez sur l’icône Actualiser du graphique pour recalculer immédiatement. (L’actualisation du navigateur ne permet pas de lancer un nouveau calcul.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Ajustement d’une vignette sur le tableau de bord
Lorsqu’une vignette se trouve sur le tableau de bord, vous pouvez l’ajuster.

![Survolez un graphique avec la souris pour le modifier.](./media/app-insights-dashboards/36.png)

1. Ajoutez un graphique à la vignette.
2. Définissez la mesure, la dimension et le style de regroupement (tableau, graphique) d’un graphique.
3. Faites glisser le curseur dans le diagramme pour faire un zoom avant ; cliquez sur le bouton Annuler pour réinitialiser la période ; définissez les propriétés de filtre pour les graphiques sur la vignette.
4. Définissez le titre de la vignette.

Les vignettes épinglées à partir des panneaux de Metrics Explorer ont davantage d’options d’édition que les vignettes épinglées à partir d’un panneau Vue d’ensemble.

La vignette d’origine que vous avez épinglée n’est pas affectée par vos modifications.

## <a name="switch-between-dashboards"></a>Basculement entre les tableaux de bord
Vous pouvez enregistrer plusieurs tableaux de bord et basculer entre ceux-ci. Lorsque vous épinglez un graphique ou un panneau, il est ajouté au tableau de bord actuel.

![Pour basculer entre les tableaux de bord, cliquez sur Tableau de bord et sélectionnez un tableau de bord enregistré. Pour créer et enregistrer un nouveau tableau de bord, cliquez sur Nouveau. Pour les réorganiser, cliquez sur Modifier.](./media/app-insights-dashboards/32.png)

Vous pouvez, par exemple, avoir un tableau de bord pour l’affichage en plein écran dans la salle de réunion et un autre pour le développement général.

Sur le tableau de bord, un panneau s’affiche sous forme de vignette : cliquez dessus pour accéder au panneau. Un graphique réplique le graphique dans son emplacement d’origine.

![Cliquez sur une vignette pour ouvrir le panneau qu’elle représente.](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Partager des tableaux de bord
Lorsque vous avez créé un tableau de bord, vous pouvez le partager avec d’autres utilisateurs.

![Dans l’en-tête du tableau de bord, cliquez sur Partager](./media/app-insights-dashboards/41.png)

Apprenez-en davantage sur [les rôles et le contrôle d’accès](app-insights-resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Créer des tableaux de bord par programmation
Vous pouvez automatiser la création de tableaux de bord à l’aide [d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) et d’un simple éditeur JSON.

## <a name="app-navigation"></a>Navigation au sein d’une application
Le panneau de vue d’ensemble constitue une passerelle permettant d’accéder à des informations supplémentaires sur votre application.

* **N’importe quel graphique ou vignette** : cliquez sur un graphique ou une vignette pour voir plus de détails sur ce qui est affiché.

### <a name="overview-blade-buttons"></a>Boutons du panneau Vue d’ensemble
![Barre de navigation supérieure du panneau Vue d’ensemble](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Metrics Explorer**](app-insights-metrics-explorer.md) : créez vos propres graphiques sur les performances et l’utilisation.
* [**Rechercher**](app-insights-diagnostic-search.md) : analysez des instances spécifiques d’événements telles que les demandes, les exceptions ou les suivis de journal.
* [**Analytics**](app-insights-analytics.md) : pour des requêtes puissantes sur vos données de télémétrie.
* **Période** : ajustez la plage affichée par tous les graphiques dans le panneau.
* **Supprimer** : supprimez la ressource Application Insights correspondant à cette application. Vous devez également supprimer les packages Application Insights à partir de votre code d’application ou modifier la [clé d’instrumentation](app-insights-create-new-resource.md#copy-the-instrumentation-key) dans votre application pour diriger la télémétrie vers une autre ressource Application Insights.

### <a name="essentials-tab"></a>Onglet Bases
* [Clé d’instrumentation](app-insights-create-new-resource.md#copy-the-instrumentation-key) : identifie la ressource de cette application.

### <a name="app-navigation-bar"></a>Barre de navigation au sein d’une application
![Barre de navigation gauche](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Vue d’ensemble** : revenez au panneau de vue d’ensemble de l’application.
* **Journal d’activité** : alertes et événements d’administration Azure.
* [**Contrôle d’accès**](app-insights-resources-roles-access-control.md) : donnez accès aux membres de l’équipe et à d’autres utilisateurs.
* [**Balises**](../azure-resource-manager/resource-group-using-tags.md) : utilisez des balises pour regrouper votre application avec d’autres utilisateurs.

EXAMINER

* [**Plan de l’application**](app-insights-app-map.md) : plan actif présentant les composants de votre application, dérivé des informations de dépendance.
* [**Détection intelligente**](app-insights-proactive-diagnostics.md) : consultez les récentes alertes sur les performances.
* [**Flux en direct**](app-insights-live-stream.md) pour un ensemble de mesures quasi instantanées, ce qui est utile lors du déploiement d’une nouvelle version ou du débogage.
* [**Tests de disponibilité/web**](app-insights-monitor-web-app-availability.md) : envoyez des demandes régulières à votre site à partir de votre application web dans le monde entier.*
* [**Échecs, Performances**](app-insights-web-monitor-performance.md) : exceptions, taux d’échec et temps de réponse des demandes adressées à votre application et des demandes de votre application à destination des [dépendances](app-insights-asp-net-dependencies.md).
* [**Performances**](app-insights-web-monitor-performance.md) : temps de réponse, temps de réponse de dépendance.
* [Serveurs](app-insights-web-monitor-performance.md) : compteurs de performances. Disponible si vous [installez Status Monitor](app-insights-monitor-performance-live-website-now.md).
* **Navigateur** : nombre de pages consultées et performances AJAX. Disponible si vous [instrumentez vos pages web](app-insights-javascript.md).
* **Utilisation** : nombre de pages consultées, d’utilisateurs et de sessions. Disponible si vous [instrumentez vos pages web](app-insights-javascript.md).

CONFIGURER

* **Prise en main** : didacticiel en ligne.
* **Propriétés** : clé d’instrumentation, abonnement et ID de ressource.
* [Alertes](app-insights-alerts.md) : configuration des alertes de métriques.
* [Exportation continue](app-insights-export-telemetry.md) : configurez l’exportation des données de télémétrie vers le stockage Azure.
* [Tests de performances](app-insights-monitor-web-app-availability.md#performance-tests) : configurez une charge synthétique sur votre site web.
* [Quota et tarification](app-insights-pricing.md)et [échantillonnage d’ingestion](app-insights-sampling.md).
* **Accès à l’API** : créez des [annotations de mise en production](app-insights-annotations.md) et pour l’API d’accès aux données.
* [**Éléments de travail**](app-insights-diagnostic-search.md#create-work-item) : connectez-vous à un système de suivi du travail afin de créer des bogues lors de l’inspection de la télémétrie.

PARAMÈTRES

* [**Verrous**](../azure-resource-manager/resource-group-lock-resources.md) : verrouillez les ressources Azure.
* [**Script d’automatisation**](app-insights-powershell.md) : exportez une définition de la ressource Azure afin de l’utiliser comme modèle pour créer d’autres ressources.


## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Étapes suivantes

|  |  |
| --- | --- |
| [Metrics Explorer](app-insights-metrics-explorer.md)<br/>Filtrez et segmentez les mesures. |![Exemple de recherche](./media/app-insights-dashboards/64.png) |
| [Recherche de diagnostic](app-insights-diagnostic-search.md)<br/>Recherchez et examinez des événements, ainsi que les événements associés, et créez des bogues. |![Exemple de recherche](./media/app-insights-dashboards/61.png) |
| [Analytics](app-insights-analytics.md)<br/>Tirez parti d’un puissant langage de requête. |![Exemple de recherche](./media/app-insights-dashboards/63.png) |
