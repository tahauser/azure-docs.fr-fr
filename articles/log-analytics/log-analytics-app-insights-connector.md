---
title: Consulter les données d’application d’Azure Application Insights | Microsoft Docs
description: Vous pouvez utiliser la solution Application Insights Connector pour diagnostiquer les problèmes de performances et comprendre ce que les utilisateurs font avec votre application lorsqu’Application Insights les surveille.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: magoedte
ms.openlocfilehash: 854ec70c897b6a561fdec056228f82ccec3ae16c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="application-insights-connector-management-solution-preview"></a>Solution de gestion Application Insights Connector (préversion)

![Symbole Application Insights](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

La solution Application Insights Connector vous aide à diagnostiquer les problèmes de performances et à comprendre ce que les utilisateurs font avec votre application lorsqu’[Application Insights](../application-insights/app-insights-overview.md) les surveille. Dans Log Analytics, vous pouvez voir les mêmes données de télémétrie que les développeurs voient dans Application Insights. Toutefois, lorsque vous intégrez vos applications Application Insights à Log Analytics, la visibilité de vos applications augmente lorsque les données de fonctionnement et d’application se trouvent au même emplacement. Avoir les mêmes vues vous aide à collaborer avec vos développeurs d’applications. Les vues communes peuvent aider à réduire le temps nécessaire à la détection et à la résolution des problèmes d’application et de plateforme.

Lorsque vous utilisez la solution, vous pouvez :

- Consulter toutes vos applications Application Insights en un lieu unique, même si elles se trouvent dans différents abonnements Azure
- Mettre en corrélation les données d’infrastructure et les données d’application
- Visualiser les données d’application avec des perspectives dans la recherche dans les journaux
- Passer des données Log Analytics à votre application Application Insights dans les portails Azure

## <a name="connected-sources"></a>Sources connectées

Contrairement à la plupart des autres solutions Log Analytics, les données ne sont pas collectées pour Application Insights Connector par des agents. Toutes les données utilisées par la solution proviennent directement d’Azure.

| Source connectée | Prise en charge | Description |
| --- | --- | --- |
| [Agents Windows](log-analytics-windows-agent.md) | Non  | La solution ne collecte aucune information à partir d’agents Windows. |
| [Agents Linux](log-analytics-linux-agents.md) | Non  | La solution ne collecte aucune information à partir d’agents Linux. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Non  | La solution ne collecte aucune information à partir d’agents dans un groupe d’administration SCOM connecté. |
| [Compte Azure Storage](log-analytics-azure-storage.md) | Non  | La solution ne collecte aucune information à partir de stockage Azure. |

## <a name="prerequisites"></a>Prérequis


- Pour accéder aux informations d’Application Insights Connector, vous devez posséder un abonnement Azure.
- Vous devez disposer d’au moins une ressource Application Insights configurée.
- Vous devez être le propriétaire ou le contributeur de la ressource Application Insights.

## <a name="configuration"></a>Configuration

1. Activez la solution Azure Web Apps Analytics depuis la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).
2. Dans le portail OMS, cliquez sur **Paramètres** &gt; **Données** &gt; **Application Insights**.
3. Sous **Sélectionner un abonnement**, sélectionnez un abonnement qui contient des ressources Application Insights, puis sous **Nom de l’application**, sélectionnez une ou plusieurs applications.
4. Cliquez sur **Enregistrer**.

Après environ 30 minutes, les données deviennent disponibles et la vignette Application Insights est mise à jour avec les données, comme sur l’image suivante :

![Vignette Application Insights](./media/log-analytics-app-insights-connector/app-insights-tile.png)

Autres points à garder à l’esprit :

- Vous pouvez lier les applications Application Insights à un seul espace de travail Log Analytics.
- Vous pouvez uniquement lier des [ressources Application Insights de base ou d’entreprise](https://azure.microsoft.com/pricing/details/application-insights) à Log Analytics. Cependant, vous pouvez utiliser le niveau Gratuit de Log Analytics.

## <a name="management-packs"></a>Packs d’administration

Cette solution n’installe aucun pack d’administration dans les groupes d’administration connectés.

## <a name="use-the-solution"></a>Utiliser la solution

Les sections suivantes expliquent comment vous pouvez utiliser les panneaux affichés dans le tableau de bord Application Insights pour consulter et interagir avec des données à partir de vos applications.

### <a name="view-application-insights-connector-information"></a>Consulter les informations Application Insights Connector

Cliquez sur la vignette **Application Insights** pour ouvrir le tableau de bord **Application Insights** et afficher les panneaux suivants.

![Tableau de bord Application Insights](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Tableau de bord Application Insights](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

Le tableau de bord comprend les panneaux figurant dans le tableau. Chaque panneau répertorie jusqu'à 10 éléments répondant à ses critères en ce qui concerne l’étendue et l’intervalle de temps spécifiés. Vous pouvez exécuter une recherche dans les journaux qui renvoie tous les enregistrements lorsque vous cliquez sur **Afficher tout** en bas du panneau ou lorsque vous cliquez sur l’en-tête de panneau.

[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| **Colonne** | **Description** |
| --- | --- |
| Applications - Nombre d’applications | Affiche le nombre d’applications dans les ressources Application. Répertorie également le nom des applications et pour chacune, le nombre d’enregistrements de l’application. Cliquez sur le nombre pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Cliquez sur le nom d’une application pour exécuter une recherche dans les journaux de l’application, afin d’afficher les enregistrements d’application par hôte, les enregistrements par type de donnée de télémétrie et toutes les données par type (en fonction du dernier jour). |
| Volume de données – Hôtes envoyant des données | Affiche le nombre d’ordinateurs hôtes qui envoient des données. Répertorie également les ordinateurs hôtes et le nombre d’enregistrements pour chaque hôte. Cliquez sur le nombre pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code>. <br><br> Cliquez sur le nom d’un ordinateur pour exécuter une recherche dans les journaux de l’hôte, afin d’afficher les enregistrements d’application par hôte, les enregistrements par type de donnée de télémétrie et toutes les données par type (en fonction du dernier jour). |
| Disponibilité – Résultats du test web | Affiche un graphique en anneau pour les résultats du test web, spécifiant si le test a réussi ou échoué. Cliquez sur le graphique pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code>. <br><br> Les résultats indiquent le nombre de réussites et d’échecs pour tous les tests. Toutes les applications web sont présentées avec le trafic pour la dernière minute. Cliquez sur le nom d’une application pour afficher une recherche dans les journaux présentant les détails des tests web ayant échoué. |
| Requêtes de serveur – Requêtes par heure | Affiche un graphique en courbes des requêtes de serveur par heure pour différentes applications. Placez le curseur sur une ligne du graphique pour afficher les 3 applications ayant reçu le plus de requêtes à un moment donné. Affiche également une liste des applications recevant des requêtes et le nombre de requêtes pour la période sélectionnée. <br><br>Cliquez sur le graphique pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> et afficher un graphique en courbes plus détaillé des requêtes de serveur par heure pour diverses applications. <br><br> Cliquez sur une application dans la liste pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> et afficher une liste des requêtes, des graphiques pour les requêtes en fonction du temps, la durée des requêtes et une liste des codes de réponse aux requêtes.   |
| Échecs – Requêtes ayant échoué par heure | Affiche un graphique en courbes des requêtes d’application ayant échoué par heure. Placez le curseur sur le graphique pour afficher les 3 applications ayant le plus de requêtes en échec à un moment donné. Affiche également une liste d’applications avec le nombre de requêtes ayant échoué pour chacune. Cliquez sur le graphique pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> et afficher un graphique en courbes plus détaillé des requêtes d’application ayant échoué. <br><br>Cliquez sur un élément dans la liste pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> et afficher les requêtes ayant échoué, des graphiques pour les requêtes ayant échoué en fonction du temps, la durée des requêtes et une liste des codes de réponse aux requêtes ayant échoué. |
| Exceptions – Exceptions par heure | Affiche un graphique en courbes des exceptions par heure. Placez le curseur sur le graphique pour afficher les 3 applications ayant le plus d’exceptions à un moment donné. Affiche également une liste d’applications avec le nombre d’exceptions pour chacune. Cliquez sur le graphique pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> et afficher un graphique en courbes plus détaillé des exceptions. <br><br>Cliquez sur un élément dans la liste pour exécuter une recherche dans les journaux de <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> et afficher la liste des exceptions, des graphiques pour les exceptions en fonction du temps et des demandes ayant échoué, et une liste des types d’exception.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Consulter la perspective d’Application Insights avec la recherche dans les journaux

Lorsque vous cliquez sur un élément dans le tableau de bord, une perspective d’Application Insights s’affiche dans la recherche. La perspective fournit une visualisation étendue, en fonction du type de données de télémétrie sélectionné. Le contenu visualisé change donc en fonction du type de données de télémétrie.

Lorsque vous cliquez n’importe où dans le panneau Applications, vous voyez la perspective **Applications** par défaut.

![Perspective Applications d’Application Insights](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

La perspective montre une vue d’ensemble de l’application que vous avez sélectionnée.

Le panneau **Disponibilité** affiche une autre perspective, qui vous permet de consulter les résultats des tests web et les requêtes connexes ayant échoué.

![Perspective Disponibilité d’Application Insights](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

Lorsque vous cliquez n’importe où dans les panneaux **Requêtes de serveur** ou **Échecs**, les composants de perspective changent afin de vous donner une visualisation associée aux requêtes.

![Panneau Échecs d’Application Insights](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

Lorsque vous cliquez n’importe où dans le panneau **Exceptions**, vous voyez une visualisation spécialement adaptée aux exceptions.

![Panneau Exceptions d’Application Insights](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

Que vous cliquiez ou non sur un élément du tableau de bord **Application Insights Connector**, sur la page **Recherche** proprement dite, toute requête renvoyant des données Application Insights affiche la perspective Application Insights. Par exemple, si vous consultez les données Application Insights, une requête **&#42;** affiche également l’onglet de la perspective comme sur l’image suivante :

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

Les composants de la perspective sont mis à jour en fonction de la requête de recherche. Cela signifie que vous pouvez filtrer les résultats à l’aide de n’importe quel champ de recherche qui vous donne la possibilité d’afficher les données à partir de :

- Toutes vos applications
- Une seule application sélectionnée
- Un groupe d’applications

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Passer à une application dans le portail Azure

Les panneaux Application Insights Connector sont conçus pour vous permettre de passer à l’application Application Insights sélectionnée *lorsque vous utilisez le portail OMS*. Vous pouvez utiliser la solution comme une plateforme de surveillance de haut niveau qui vous permet de dépanner une application. Lorsque vous identifiez un problème potentiel dans l’une de vos applications connectées, vous pouvez soit la rechercher dans Log Analytics, soit accéder directement à l’application Application Insights.

Pour ouvrir l’application, cliquez sur l’ellipse (**...**) qui apparaît à la fin de chaque ligne, puis sélectionnez **Ouvrir dans Application Insights**.

>[!NOTE]
>L’option **Ouvrir dans Application Insights** n’est pas disponible dans le portail Azure.

![Ouvrir dans Application Insights](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Données corrigées par l’exemple

Application Insights fournit une *[correction par échantillonnage](../application-insights/app-insights-sampling.md)* pour aider à réduire le trafic de données de télémétrie. Lorsque vous activez l’échantillonnage sur votre application Application Insights, vous obtenez un nombre limité d’entrées stockées à la fois dans Application Insights et dans Log Analytics. La cohérence des données est conservée sur la page **Application Insights Connector** et dans les perspectives, mais vous devez corriger manuellement les données échantillonnées pour vos requêtes personnalisées.

Voici un exemple de correction par échantillonnage dans une requête de recherche dans les journaux :

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Le champ **Sampled Count** (Nombre d’éléments échantillonnés) est présent dans toutes les entrées et indique le nombre de points de données représentés par l’entrée. Si vous activez l’échantillonnage pour votre application Application Insights, la valeur du champ **Sampled Count** (Nombre d’éléments échantillonnés) est supérieure à 1. Pour compter le nombre d’entrées réellement générées par votre application, additionnez les champs **Sampled Count** (Nombre d’éléments échantillonnés).

L’échantillonnage affecte uniquement le nombre total d’entrées générées par votre application. Vous n’avez pas besoin de corriger l’échantillonnage pour les champs de mesure tels que **RequestDuration** ou **AvailabilityDuration**, car ces champs affichent la moyenne des entrées représentées.

## <a name="input-data"></a>Données d’entrée

La solution reçoit les types de données de télémétrie suivants à partir de vos applications Application Insights connectées :

- Disponibilité
- Exceptions
- Requests
- Affichages de pages – Pour que votre espace de travail reçoive des affichages de pages, vous devez configurer vos applications pour recueillir ces informations. Pour plus d’informations, voir [PageViews](../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Événements personnalisés – Pour que votre espace de travail reçoive des événements personnalisés, vous devez configurer vos applications pour recueillir ces informations. Pour plus d’informations, voir [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Log Analytics reçoit les données en provenance d’Application Insights dès qu’elles sont disponibles.

## <a name="output-data"></a>Données de sortie

Un enregistrement associé au *type* *ApplicationInsights* est créé pour chaque type de données d’entrée. Les enregistrements ApplicationInsights ont les propriétés figurant dans les sections suivantes :

### <a name="generic-fields"></a>Champs génériques

| Propriété | Description |
| --- | --- |
| type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Heure de l’enregistrement |
| ApplicationId | Clé d’instrumentation de l’application Application Insights |
| ApplicationName | Nom de l'application Application Insights |
| RoleInstance | ID de l’hôte du serveur |
| DeviceType | Appareil client |
| ScreenResolution |   |
| Continent | Continent d'où provient la requête |
| Pays | Pays d'où provient la requête |
| Province | Province, état ou lieu d'où provient la requête |
| City | Ville d'où provient la requête |
| isSynthetic | Indique si la requête a été créée par un utilisateur ou par une méthode automatisée. True = générée par l’utilisateur, ou false = méthode automatisée |
| SamplingRate | Pourcentage de télémétrie générée par le Kit SDK qui est envoyé vers le portail. Plage 0.0-100.0. |
| SampledCount | 100/(SamplingRate). Par exemple, 4 =&gt; 25 % |
| IsAuthenticated | True ou false |
| OperationID | Les éléments qui affichent le même ID d’opération sont représentés en tant qu’éléments associés dans le portail. Généralement l’ID de requête |
| ParentOperationID | ID de l’opération parente |
| Nom d'opération |   |
| SessionId | GUID permettant d’identifier de façon unique la session dans laquelle la requête a été créée |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Champs spécifiques à la disponibilité

| Propriété | Description |
| --- | --- |
| TelemetryType | Disponibilité |
| AvailabilityTestName | Nom du test web |
| AvailabilityRunLocation | Source géographique de la requête http |
| AvailabilityResult | Indique si le test web a réussi |
| AvailabilityMessage | Le message joint au test web |
| AvailabilityCount | 100/(Sampling Rate). Par exemple, 4 =&gt; 25 % |
| DataSizeMetricValue | 1.0 ou 0.0 |
| DataSizeMetricCount | 100/(Sampling Rate). Par exemple, 4 =&gt; 25 % |
| AvailabilityDuration | Durée, en millisecondes, du test web |
| AvailabilityDurationCount | 100/(Sampling Rate). Par exemple, 4 =&gt; 25 % |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | GUID unique pour le test web |
| AvailabilityTimestamp | Horodatage précis du test de disponibilité |
| AvailabilityDurationMin | Pour les enregistrements échantillonnés, ce champ affiche la durée minimale du test web (en millisecondes) pour les points de données représentés |
| AvailabilityDurationMax | Pour les enregistrements échantillonnés, ce champ affiche la durée maximale du test web (en millisecondes) pour les points de données représentés |
| AvailabilityDurationStdDev | Pour les enregistrements échantillonnés, ce champ affiche la déviation standard entre toutes les durées de test web (en millisecondes) pour les points de données représentés |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Champs spécifiques à l’exception

| type | ApplicationInsights |
| --- | --- |
| TelemetryType | Exception |
| ExceptionType | Type de l’exception |
| ExceptionMethod | Méthode qui crée l’exception |
| ExceptionAssembly | L’assembly inclut l’infrastructure et la version ainsi que le jeton de clé publique |
| ExceptionGroup | Type de l’exception |
| ExceptionHandledAt | Indique le niveau qui a géré l’exception |
| ExceptionCount | 100/(Sampling Rate). Par exemple, 4 =&gt; 25 % |
| ExceptionMessage | Message de l’exception |
| ExceptionStack | Pile complète de l’exception |
| ExceptionHasStack | True, si l’exception possède une pile |



### <a name="request-specific-fields"></a>Champs spécifiques à la requête

| Propriété | Description |
| --- | --- |
| type | ApplicationInsights |
| TelemetryType | Requête |
| ResponseCode | Réponse HTTP envoyée au client |
| RequestSuccess | Indique la réussite ou l’échec. True ou false. |
| RequestID | ID permettant d’identifier la requête de manière unique |
| RequestName | GET/POST + base d’URL |
| RequestDuration | Durée, en secondes, de la requête |
| URL | URL de la requête sans l’hôte |
| Host | Hôte du serveur web |
| URLBase | URL complète de la requête |
| ApplicationProtocol | Type de protocole utilisé par l’application |
| RequestCount | 100/(Sampling Rate). Par exemple, 4 =&gt; 25 % |
| RequestDurationCount | 100/(Sampling Rate). Par exemple, 4 =&gt; 25 % |
| RequestDurationMin | Pour les enregistrements échantillonnés, ce champ affiche la durée minimale de la requête (en millisecondes) pour les points de données représentés. |
| RequestDurationMax | Pour les enregistrements échantillonnés, ce champ affiche la durée maximale de la requête (en millisecondes) pour les points de données représentés |
| RequestDurationStdDev | Pour les enregistrements échantillonnés, ce champ affiche la déviation standard entre toutes les durées de requête (en millisecondes) pour les points de données représentés |

## <a name="sample-log-searches"></a>Exemples de recherches dans les journaux

Cette solution ne dispose pas d’un ensemble d’exemples de recherches dans les journaux affiché sur le tableau de bord. Toutefois, les requêtes d’exemples de recherches dans les journaux avec les descriptions sont affichées dans la section [Consulter les informations Application Insights Connector](#view-application-insights-connector-information).

## <a name="next-steps"></a>Étapes suivantes

- Utilisez [Recherche dans les journaux](log-analytics-log-searches.md) pour afficher des informations détaillées sur vos applications Application Insights.
