---
title: "Utilisation d’Analytics, le puissant outil de recherche d’Azure Application Insights | Microsoft Docs"
description: "Utilisation d’Analytics, le puissant outil de recherche de diagnostic d’Application Insights. "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: danha; mbullwin
ms.openlocfilehash: 0ca5c8b19f4699548a8551ec673e4a067d4e5fad
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="using-analytics-in-application-insights"></a>Utilisation d’Analytics dans Application Insights
[Analytics](app-insights-analytics.md) est la fonctionnalité de recherche performante [d’Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Log Analytics.

* **[Regardez la vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testez la version d’évaluation d’Analytics sur nos données simulées](https://analytics.applicationinsights.io/demo)** si votre application n’envoie pas encore de données à Application Insights.

## <a name="open-analytics"></a>Ouverture de la fonctionnalité Analytics
À partir de la ressource de base de votre application dans Application Insights, cliquez sur Analytics.

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-insights-analytics-using/001.png)

Le didacticiel en ligne vous donne quelques idées sur les possibilités qui vous sont offertes.

Vous pouvez cependant consulter [ici une présentation approfondie](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Interrogation de votre télémétrie
### <a name="write-a-query"></a>Écrivez votre requête.
![Affichage du schéma](./media/app-insights-analytics-using/150.png)

Commencez par les noms des tables répertoriées sur la gauche (ou les opérateurs [range](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) ou [union](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator)). Utilisez `|` pour créer un pipeline [d’opérateurs](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

IntelliSense vous indique les opérateurs et les éléments d’expression que vous pouvez utiliser. Cliquez sur l’icône d’informations (ou appuyez sur CTRL + espace) pour obtenir une description plus détaillée et des exemples d’utilisation de chaque élément.

Consultez la [visite guidée du langage d’Analytics](app-insights-analytics-tour.md) et les [informations de référence sur le langage](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Exécution d’une requête
![Exécution d’une requête](./media/app-insights-analytics-using/130.png)

1. Vous pouvez utiliser des sauts de ligne uniques dans une requête.
2. Placez le curseur à l’intérieur ou à la fin de la requête à exécuter.
3. Vérifiez l'intervalle de temps de votre requête. (Vous pouvez le modifier ou le remplacer en incluant votre propre clause [`where...timestamp...`](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) dans la requête.)
3. Cliquez sur Accéder pour exécuter la requête.
4. N’insérez pas de lignes vides dans votre requête. Vous pouvez conserver plusieurs requêtes séparées dans un onglet de requête en les séparant par des lignes vides. Seule la requête sur laquelle le curseur est placé s’exécute.

### <a name="save-a-query"></a>Enregistrement d’une requête
![Enregistrement d’une requête](./media/app-insights-analytics-using/140.png)

1. Enregistrez le fichier de requête actuel.
2. Ouvrez un fichier de requête enregistré.
3. Créez un fichier de requête.

## <a name="see-the-details"></a>Voir des informations détaillées
Développez des lignes dans les résultats pour afficher la liste complète de leurs propriétés. Vous pouvez développer davantage n’importe quelle propriété qui est une valeur structurée. Par exemple, des dimensions personnalisées ou la liste d’empilement dans une exception.

![Extension d’une ligne](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Réorganisation des résultats
Vous pouvez trier, filtrer, paginer et regrouper les résultats retournés par votre requête.

> [!NOTE]
> Le tri, le regroupement et le filtrage dans le navigateur ne réexécutent pas votre requête. Ils réorganisent uniquement les résultats retournés par votre dernière requête. 
> 
> Pour exécuter ces tâches sur le serveur avant que les résultats ne soient retournés, écrivez votre requête avec les opérateurs [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) et [where](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator).
> 
> 

Choisissez les colonnes que vous souhaitez voir, faites glisser les en-têtes de colonne pour les réorganiser et redimensionnez les colonnes en faisant glisser ses bordures.

![Réorganisation de colonnes](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Tri et filtrage d’éléments
Triez les résultats en cliquant sur l’en-tête d’une colonne. Cliquez à nouveau pour trier dans l’ordre inverse, puis cliquez sur une troisième fois pour rétablir l’ordre d’origine retourné par la requête.

Utilisez l’icône de filtre pour affiner votre recherche.

![Tri et filtrage de colonnes](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Regroupement d’éléments
Pour trier par plusieurs colonnes, utilisez le regroupement. Tout d’abord, activez le regroupement, puis faites glisser les en-têtes de colonne dans l’espace au-dessus de la table.

![Groupe](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Certains résultats manquent ?

Si vous pensez que vous ne voyez pas tous les résultats escomptés, il existe deux raisons possibles.

* **Filtre d'intervalle de temps**. Par défaut, vous verrez seulement les résultats des dernières 24 heures. Un filtre automatique limite l'intervalle des résultats récupérés à partir des tables source. 

    Toutefois, vous pouvez modifier ce filtre d'intervalle à l’aide du menu déroulant.

    Ou vous pouvez remplacer l'intervalle automatique en incluant votre propre [clause `where  ... timestamp ...`](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) dans la requête. Par exemple : 

    `requests | where timestamp > ago('2d')`

* **Limite de résultats**. Il existe une limite d’environ 10 000 lignes pour les résultats retournés à partir du portail. Un avertissement s’affiche si vous dépassez la limite. Dans ce cas, le tri des résultats de la table ne vous indique pas toujours tous les premiers ou derniers résultats réels. 

    Il est recommandé d’éviter d’atteindre la limite. Utilisez le filtre d'intervalle de temps, ou utiliser des opérateurs tels que :

  * [top 100 by timestamp](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [take 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [summarize ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [where timestamp &gt; ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Vous voulez plus de 10 000 lignes ? Utilisez plutôt l'[exportation continue](app-insights-export-telemetry.md). Analytics est conçu pour l'analyse plutôt que pour la récupération de données brutes.)

## <a name="diagrams"></a>Diagrammes
Sélectionnez le type de diagramme de votre choix :

![Sélectionnez un type de diagramme](./media/app-insights-analytics-using/230.png)

Si vous disposez de plusieurs colonnes de types appropriés, vous pouvez choisir les axes x et y et spécifier une colonne de dimensions avec laquelle fractionner les résultats.

Par défaut, les résultats sont initialement affichés sous forme de table. Vous pouvez ensuite sélectionner le diagramme manuellement. Mais vous pouvez également utiliser la [directive render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) à la fin d’une requête pour sélectionner un diagramme.

### <a name="analytics-diagnostics"></a>Diagnostic analytique


Sur un graphique temporel, s’il existe un pic ou une chute brutal dans vos données, un point peut être mis en évidence sur la ligne. Cela indique que le diagnostic analytique a identifié une combinaison de propriétés qui filtrent le changement soudain. Cliquez sur le point pour obtenir plus de détails sur le filtre, et pour afficher la version filtrée. Cela peut vous aider à identifier l’origine de la modification. 

[En savoir plus sur le diagnostic analytique](app-insights-analytics-diagnostics.md)


![Diagnostic analytique](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Épingler au tableau de bord
Vous pouvez épingler un diagramme ou une table à l’un de vos [tableaux de bord partagés](app-insights-dashboards.md) : cliquez simplement sur l’épingle. 

![Cliquez sur l’épingle](./media/app-insights-analytics-using/pin-01.png)

Cela signifie que, lorsque vous créez un tableau de bord pour vous aider à surveiller les performances ou l’utilisation de vos services web, vous pouvez inclure des analyses très complexes en parallèle des autres mesures. 

Vous pouvez épingler une table au tableau de bord, si elle comporte au maximum quatre colonnes. Seules les sept premières lignes sont affichées.

### <a name="dashboard-refresh"></a>Actualisation du tableau de bord
Le graphique épinglé au tableau de bord est actualisé automatiquement lorsque vous réexécutez la requête environ toutes les heures. Vous pouvez également cliquer sur le bouton Actualiser.

### <a name="automatic-simplifications"></a>Simplifications automatiques

Certaines simplifications sont appliquées à un graphique lorsque vous l’épinglez à un tableau de bord.

**Restriction de temps :** les requêtes sont automatiquement limitées aux 14 derniers jours. L’effet est le même que si votre requête inclut `where timestamp > ago(14d)`.

**Restriction du nombre d'emplacements :** si vous utilisez un graphique comportant un grand nombre d’emplacements discrets (généralement un graphique à barres), les emplacements les moins remplis sont automatiquement regroupés en un seul emplacement « autres ». Par exemple, cette requête :

    requests | summarize count_search = count() by client_CountryOrRegion

se présente sous la forme suivante dans Analytics :

![Graphique à longue traîne](./media/app-insights-analytics-using/pin-07.png)

mais lorsque vous l’épinglez à un tableau de bord, elle ressemble à ceci :

![Graphique comportant des emplacements limités](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exporter vers Excel
Une fois votre requête exécutée, vous pouvez télécharger un fichier .csv. Cliquez sur **Exporter, Excel**.

## <a name="export-to-power-bi"></a>Exporter vers Power BI
Placez le curseur dans une requête et choisissez **Exporter, Power BI**.

![Exportation depuis Log Analytics vers Power BI](./media/app-insights-analytics-using/240.png)

Vous exécutez la requête dans Power BI. Vous pouvez la définir pour qu’elle s’actualise selon une planification.

Avec Power BI, vous pouvez créer des tableaux de bord qui rassemblent les données issues d’un large éventail de sources.

[En savoir plus sur l’exportation vers Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Lien ciblé

Obtenez un lien sous **Exporter, Partager un lien** que vous pouvez envoyer à un autre utilisateur. À condition que l’utilisateur ait [accès à votre groupe de ressources](app-insights-resources-roles-access-control.md), la requête s’ouvre dans l’interface utilisateur Analytics.

(Dans le lien, le texte de la requête s’affiche après « ? q = », il est compressé avec gzip et codé en base64. Vous pouvez écrire du code pour générer des liens ciblés que vous fournissez aux utilisateurs. Toutefois, la méthode recommandée pour exécuter Analytics à partir du code est d’utiliser l’[API REST](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automatisation

Utilisez l’[API REST d’accès aux données](https://dev.applicationinsights.io/) pour exécuter des requêtes Analytics. [Par exemple](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (à l’aide de PowerShell) :

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Contrairement à l’interface utilisateur Analytics, l’API REST n’ajoute pas automatiquement de limitation d’horodatage à vos requêtes. Pensez à ajouter votre propre clause where pour éviter d’obtenir des réponses volumineuses.



## <a name="import-data"></a>Importer des données

Vous pouvez importer des données à partir d’un fichier CSV. Une utilisation typique consiste à importer des données statiques que vous pouvez joindre à des tables à partir de vos données de télémétrie. 

Par exemple, si des utilisateurs authentifiés sont identifiés dans vos données de télémétrie par un alias ou un ID masqué, vous pouvez importer une table qui mappe les alias aux noms réels. En associant les données de télémétrie de la requête, vous pouvez identifier les utilisateurs par leurs noms réels dans les rapports Analytics.

### <a name="define-your-data-schema"></a>Définir votre schéma de données

1. Cliquez sur **Paramètres** (en haut à gauche), puis sur **Sources de données**. 
2. Ajoutez une source de données, en suivant les instructions. Vous êtes invité à fournir un échantillon de données contenant au moins dix lignes. Vous corrigez ensuite le schéma.

Cette opération définit une source de données, que pouvez ensuite utiliser pour importer des tables individuelles.

### <a name="import-a-table"></a>Importer une table

1. Ouvrez votre définition de source de données à partir de la liste.
2. Cliquez sur « Télécharger » et suivez les instructions pour télécharger la table. Cette opération implique un appel à une API REST, et par conséquent, il est facile de l'automatiser. 

Votre table est désormais disponible pour une utilisation dans des requêtes Analytics. Elle apparaîtra dans Analytics 

### <a name="use-the-table"></a>Utiliser le tableau

Supposons que votre définition de source de données s'appelle `usermap` et comporte deux champs, `realName` et `user_AuthenticatedId`. La table `requests` possède également un champ nommé `user_AuthenticatedId`, et il est donc facile de les joindre :

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
La table de requêtes ainsi obtenue comporte une colonne supplémentaire, `realName`.

### <a name="import-from-logstash"></a>Importer à partir de LogStash

Si vous utilisez [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), vous pouvez utiliser Analytics pour interroger vos journaux. Utilisez le [plug-in qui dirige les données vers Analytics](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

