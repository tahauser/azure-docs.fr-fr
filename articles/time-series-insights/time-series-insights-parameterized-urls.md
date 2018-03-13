---
title: "Partager des vues Azure Time Series Insights personnalisées avec des URL paramétrables | Microsoft Docs"
description: "Cet article explique comment développer des URL paramétrables dans Azure Time Series Insights, afin qu’une vue client puisse être facilement partagée."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: MarkMcGeeAtAquent
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: rest-api
ms.topic: get-started-article
ms.workload: big-data
ms.date: 11/21/2017
ms.openlocfilehash: ffa8e96ab9a5344c924400fe55b4d1e6aee95f06
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Partager une vue personnalisée à l’aide d’une URL paramétrable

Pour partager une vue personnalisée dans l’Explorateur Azure Time Series Insights, vous pouvez créer, par programmation, une URL paramétrable de la vue personnalisée.

Cet Explorateur prend en charge les paramètres de requête d’URL pour spécifier des vues dans l’expérience et ce, directement à partir de l’URL.  Par exemple, en utilisant l’URL uniquement, vous pouvez spécifier un environnement cible, un prédicat de recherche et la durée désirée. Lorsqu’un utilisateur clique sur l’URL personnalisée, l’interface fournit directement un lien vers la ressource dans le portail Time Series Insights.  Les stratégies d’accès aux données s’appliquent. 

## <a name="environment-id"></a>ID de l’environnement

Le paramètre `environmentId=<guid>` spécifie l’ID de l’environnement cible.  Il s’agit d’un composant du nom de domaine complet pour l’accès aux données. Vous le trouverez dans l’angle supérieur droit de l’écran présentation de l’environnement, sur le portail Azure.  Cela correspond à tous les éléments précédant `env.timeseries.azure.com`. Exemple de paramètre d’ID de l’environnement : `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Temps

Vous pouvez spécifier des valeurs de temps absolues ou relatives avec une URL paramétrable.

### <a name="absolute-time-values"></a>Valeurs de temps absolues

Pour les valeurs de temps absolues, utilisez les paramètres `from=<integer>` et `to=<integer>`. 

`from=<integer>` est une valeur JavaScript, en millisecondes, qui définit l’heure de début de la période de recherche.

`to=<integer>` est une valeur JavaScript, en millisecondes, qui définit l’heure de fin de la période de recherche. 

Pour identifier le nombre de millisecondes JavaScript d’une date, consultez la section relative au [convertisseur d’horodatage Epoch et Unix](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valeurs de temps relatives

Dans le cas d’une valeur de temps relative, utilisez l’élément `relativeMillis=<value>`, où la *valeur* est exprimée en millisecondes JavaScript à partir des données les plus récentes sur le serveur principal.

Par exemple, l’élément `&relativeMillis=3600000` affiche les dernières 60 minutes de données.

Les valeurs acceptées correspondent au menu **Quick Time** de l’Explorateur Time Series Insights, et insérez les éléments suivants :

- 1 800 000 (30 dernières minutes)
- 3 600 000 (60 dernières minutes)
- 10 800 000 (3 dernières heures)
- 21 600 000 (6 dernières heures)
- 43 200 000 (12 dernières heures)
- 86 400 000 (24 dernières heures)
- 604 800 000 (7 derniers jours)
- 2 592 000 000 (30 dernières heures)

### <a name="optional-parameters"></a>Paramètres facultatifs

Le paramètre `timeSeriesDefinitions=<collection of term objects>` indique les conditions d’une vue Time Series Insights :

- "name":"<string>"
  - nom du *terme* ;
- "splitBy":"<string>"
  - nom de colonne pour le *fractionnement* ;
- "measureName":"<string>"
  - nom de colonne de *mesure* ;
- "predicate":"<string>"
  - clause *where* pour le filtrage côté serveur.
-  "useSum":"true"
  - Il s’agit d’un paramètre facultatif qui spécifie l’utilisation de la somme pour la mesure.  Notez que si "Events" est la mesure sélectionnée, le nombre est sélectionné par défaut.  Si "Events" n’est pas sélectionné, la moyenne est sélectionnée par défaut.  

Le paramètre « multiChartStack=<true/false> » permet l’empilement dans le graphique et le paramètre « multiChartSameScale=<true/false> » permet d’appliquer la même échelle d’axe des ordonnées entre les termes au sein d’un paramètre facultatif.  

- 'multiChartStack=false'
  - « True » est activée par défaut donc passez sur « false » pour empiler.
- 'multiChartStack=false&multiChartSameScale=true' 
  - L’empilement doit être activé pour pouvoir utiliser la même échelle de l’axe des ordonnées entre les termes.  « False » est activé par défaut, donc passer sur « True » pour activer cette fonctionnalité.  
  
« timeBucketUnit=<Unit>&timeBucketSize=<integer> » permet d’ajuster le curseur intervalle pour générer une vue plus granulaire, plus simple, ou plus globale du graphique.  
- 'timeBucketUnit=<Unit>&timeBucketSize=<integer>'
  - Unités = jours, heures, minutes, secondes, millisecondes.  Mettez toujours les unités en majuscules.
  - Définissez le nombre d’unités en passant l’entier souhaité pour timeBucketSize.  Notez que vous simplifiez jusqu'à 7 jours.  
  
Le paramètre « timezoneOffset =<integer> » vous permet de définir le fuseau horaire pour le graphique afin qu’il s’affiche en tant qu’offset de fuseau horaire UTC.  
  - 'timezoneOffset=-<integer>'
    - L’entier est toujours en millisecondes.  
    - Notez que cette fonctionnalité est légèrement différente de celle que nous proposons dans l’Explorateur TSI, dans lequel nous vous permettons de choisir l’heure locale (l’heure du navigateur) ou l’heure UTC.  
 
### <a name="examples"></a>Exemples

Par exemple, pour ajouter des définitions de série de temps en tant que paramètre d’URL, vous pouvez utiliser les éléments suivants :

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

En utilisant ces exemples de définitions Time Series pour 

- l’ID de l’environnement,
- les 60 dernières minutes de données,
- les termes (F1PressureID, F2TempStation et F3VibrationPL) qui composent des paramètres facultatifs,
 
vous pouvez construire l’URL paramétrable suivante pour une vue :

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Si vous avez utilisé l’Explorateur Azure Time Series Insights pour créer la vue décrite par l’URL précédente, cela s’afficherait comme suit :

![Termes de l’Explorateur Time Series Insights](media/parameterized-url/url1.png)

La vue complète (y compris le graphique) s’afficherait comme suit :

![Vue de graphique](media/parameterized-url/url2.png)

## <a name="next-steps"></a>Étapes suivantes
[Interroger des données à l’aide de C#](time-series-insights-query-data-csharp.md)
