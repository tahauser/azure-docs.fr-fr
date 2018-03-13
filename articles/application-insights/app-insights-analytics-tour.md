---
title: "Visite guidée d’Analytics dans Azure Application Insights | Microsoft Docs"
description: "Courts exemples de toutes les requêtes principales dans Analytics, outil de recherche puissant d’Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: mbullwin
ms.openlocfilehash: 271ccc126eeb9411646b68b32fd30ce32b5eef5c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Visite guidée d’Analytics dans Application Insights
[Analytics](app-insights-analytics.md) est la fonctionnalité de recherche performante [d’Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Log Analytics.

* **[Regardez la vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testez la version d’évaluation d’Analytics sur nos données simulées](https://analytics.applicationinsights.io/demo)** si votre application n’envoie pas encore de données à Application Insights.
* **[L’aide-mémoire des utilisateurs de SQL](https://aka.ms/sql-analytics)** traduit les idiomes courants.

Pour vous aider à démarrer, examinons certaines requêtes de base.

## <a name="connect-to-your-application-insights-data"></a>Connectez-vous à vos données Application Insights
Ouvrez Analytics à partir du [panneau Vue d’ensemble](app-insights-dashboards.md) de votre application dans Application Insights :

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsioquerylanguagequerylanguagetakeoperatorhtml-show-me-n-rows"></a>[Take](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html): afficher n lignes
Les points de données qui consignent les opérations des utilisateurs (généralement des requêtes HTTP reçues par votre application web) sont stockés dans une table appelée `requests`. Chaque ligne est un point de données de télémétrie provenant du Kit de développement logiciel (SDK) Application Insights dans votre application.

Commençons par examiner quelques exemples de lignes de la table :

![results](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Placez le curseur quelque part dans l’instruction avant de cliquer sur OK. Vous pouvez répartir une instruction sur plusieurs lignes, mais ne placez pas de lignes vides dans une instruction. Pour conserver plusieurs requêtes dans la fenêtre, séparez-les par des lignes vides.
>
>

Choisissez les colonnes, faites-les glisser, groupez-les par colonnes et filtrez :

![Cliquez sur le sélecteur de colonnes en haut à droite des résultats](./media/app-insights-analytics-tour/030.png)

Développez un élément pour afficher les détails :

![Choisissez la vue de table et utilisez Configurer les colonnes](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Cliquez sur l’en-tête d’une colonne pour trier à nouveau les résultats disponibles dans le navigateur web. Sachez toutefois que, pour un jeu de résultats volumineux, le système limite le nombre de lignes téléchargées vers le navigateur. L’utilisation de cette méthode de tri, qui trie simplement le jeu de résultats retourné, ne vous permet pas toujours d’obtenir effectivement les éléments dans l’ordre croissant ou décroissant. Pour trier les éléments de manière fiable, utilisez l’opérateur `top` ou `sort`.
>
>

## <a name="query-across-applications"></a>Requête dans plusieurs applications
Si vous souhaitez combiner des données provenant de plusieurs applications Application Insights, utilisez le mot clé **application** pour spécifier l’application et le nom de la table.  Cette requête combine les requêtes de deux applications différentes à l’aide de la commande **union**.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsioquerylanguagequerylanguagetopoperatorhtml-and-sorthttpsdocsloganalyticsioquerylanguagequerylanguagesortoperatorhtml"></a>[Top](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) et [sort](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)
`take` est utile pour obtenir un exemple rapide d’un résultat, mais il n’affiche pas les lignes de la table dans un ordre particulier. Pour obtenir un affichage ordonné, utilisez `top` (pour un échantillon) ou `sort` (qui porte sur la table entière).

Afficher les n premières lignes, classées par une colonne particulière :

```AIQL

    requests | top 10 by timestamp desc
```

* *Syntaxe :* la plupart des opérateurs ont des paramètres de mot clé comme `by`.
* `desc` = ordre décroissant, `asc` = ordre croissant.

![](./media/app-insights-analytics-tour/260.png)

`top...` est une manière plus performante d’exprimer `sort ... | take...`. Nous aurions pu écrire :

```AIQL

    requests | sort by timestamp desc | take 10
```

Le résultat serait le même, mais l’exécution de la requête serait un peu plus lente. (Vous pouvez également écrire `order`, qui est un alias de `sort`.)

Les en-têtes de colonne dans la vue de table peuvent également servir à trier les résultats sur l’écran. Mais, bien sûr, si vous avez utilisé `take` ou `top` pour récupérer une partie seulement d’une table, cliquer sur l’en-tête de colonne ne fait que retrier les enregistrements que vous avez récupérés.

## <a name="wherehttpsdocsloganalyticsioquerylanguagequerylanguagewhereoperatorhtml-filtering-on-a-condition"></a>[Where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html): filtrage sur une condition

Concentrons-nous sur les requêtes qui ont renvoyé un code de résultat particulier :

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

L’opérateur `where` utilise une expression booléenne. Voici quelques points clés les concernant :

* `and`, `or` : opérateurs booléens
* `==`, `<>`, `!=` : égal à et différent de
* `=~`, `!~` : chaîne ne respectant pas la casse (égal à et différent de). Il existe de nombreux autres opérateurs de comparaison de chaîne.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Rechercher les requêtes ayant échoué

Convertir une valeur de chaîne en un entier pour utiliser une comparaison de supériorité :

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Temps

Par défaut, vos requêtes sont limitées aux dernières 24 heures. Mais vous pouvez modifier cette plage :

![](./media/app-insights-analytics-tour/change-time-range.png)

Remplacez l’intervalle de temps en écrivant une requête qui mentionne `timestamp` dans une clause where. Par exemple : 

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

La fonctionnalité d’intervalle de temps est équivalente à une clause « where » insérée après chaque mention d’une des tables sources.

`ago(3d)` signifie « il y a trois jours ». Il existe d’autres unités de temps, par exemple les heures (`2h`, `2.5h`), les minutes (`25m`) et les secondes (`10s`).

Autres exemples :

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[Référence sur les dates et les heures](https://docs.loganalytics.io/concepts/concepts_datatypes_datetime.html).


## <a name="projecthttpsdocsloganalyticsioquerylanguagequerylanguageprojectoperatorhtml-select-rename-and-compute-columns"></a>[Projet](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) : sélectionnez, renommez et calculez des colonnes
Utilisez [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) pour choisir uniquement les colonnes qui vous intéressent :

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Vous pouvez également renommer des colonnes et en définir de nouvelles :

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* Les noms de colonnes peuvent contenir des espaces ou des symboles s’ils sont placés entre crochets comme suit : `['...']` ou `["..."]`
* `%` est l’opérateur modulo habituel.
* `1d` (le chiffre un, suivi de la lettre d) est un littéral d’intervalle de temps qui signifie un jour. Voici d’autres littéraux d’intervalle de temps : `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) arrondit une valeur au multiple inférieur le plus proche de la valeur de base que vous fournissez. Ainsi, `floor(aTime, 1s)` arrondit une heure vers le bas à la seconde la plus proche.

Les expressions peuvent inclure tous les opérateurs habituels (`+`, `-`, etc.), et il existe une gamme de fonctions utiles.

## <a name="extend"></a>Extend
Si vous souhaitez simplement ajouter des colonnes à des colonnes existantes, utilisez [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Utiliser [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html) est plus concis que [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) si vous souhaitez conserver toutes les colonnes existantes.

### <a name="convert-to-local-time"></a>Convertir en heure locale

Les timestamps sont toujours exprimés en UTC. Par conséquent, si vous vous trouvez sur la côte Pacifique des États-Unis et que c’est l’hiver, ceci pourrait vous intéresser :

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizehttpsdocsloganalyticsioquerylanguagequerylanguagesummarizeoperatorhtml-aggregate-groups-of-rows"></a>[Summarize](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html): agréger des groupes de lignes
`Summarize` applique une *fonction d’agrégation* spécifiée sur des groupes de lignes.

Par exemple, le temps que met votre application web à répondre à une requête est reporté dans le champ `duration`. Observons le temps de réponse moyen pour toutes les demandes :

![](./media/app-insights-analytics-tour/410.png)

Nous pouvons également séparer les résultats en requêtes de noms différents :

![](./media/app-insights-analytics-tour/420.png)

`Summarize` regroupe les points de données du flux que la clause `by` évalue équitablement. Chaque valeur de l’expression `by` (chaque nom d’opération unique dans l’exemple ci-dessus) génère une ligne dans la table des résultats.

Nous pouvons également regrouper les résultats par heure :

![](./media/app-insights-analytics-tour/430.png)

Notez que nous utilisons la fonction `bin` (également appelée `floor`). Si nous avions simplement utilisé `by timestamp`, chaque ligne d’entrée apparaîtrait dans un groupe individuel. Pour des valeurs scalaires continues telles que des heures ou des nombres, nous devons diviser la plage continue en un nombre gérable de valeurs discrètes. `bin` (qui représente simplement la fonction `floor` habituelle d’arrondi à la valeur inférieure) est le plus simple moyen d’y parvenir.

Nous pouvons utiliser la même technique pour réduire les plages de chaînes :

![](./media/app-insights-analytics-tour/440.png)

Notez que vous pouvez utiliser `name=` pour définir le nom d’une colonne de résultat, dans les expressions d’agrégation ou dans la clause by.

## <a name="counting-sampled-data"></a>Décompte des données échantillonnées
`sum(itemCount)` est l’agrégation recommandée pour le comptage des événements. Dans de nombreux cas, étant donné que itemCount==1, la fonction compte simplement le nombre de lignes dans le groupe. En revanche, quand [l’échantillonnage](app-insights-sampling.md) est en cours, seule une fraction des événements d’origine est conservée comme point de données dans Application Insights. Ainsi, pour chaque point de données que vous voyez, il existe `itemCount` événements.

Par exemple, si l’échantillonnage ignore 75 % des événements d’origine, itemCount == 4 dans les enregistrements conservés. Autrement dit, pour chaque enregistrement conservé, il existe quatre enregistrements d’origine.

L’échantillonnage adaptatif rend la valeur itemCount supérieure lors des périodes pendant lesquelles votre application est utilisée de façon intensive.

Par conséquent, le fait de résumer itemCount donne une bonne estimation du nombre d’événements d’origine.

![](./media/app-insights-analytics-tour/510.png)

Il existe également une agrégation `count()` (et une opération de comptage), pour les cas où vous souhaitez réellement compter le nombre de lignes dans un groupe.

Il existe toute une gamme de [fonctions d’agrégation](https://docs.loganalytics.io/learn/tutorials/aggregations.html).

## <a name="charting-the-results"></a>Affichage des résultats dans un graphique
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Par défaut, les résultats sont affichés sous forme de table :

![](./media/app-insights-analytics-tour/225.png)

Nous pouvons aller au-delà de la vue de table. Examinons les résultats dans la vue graphique avec l’option barres verticales :

![Cliquez sur la vue graphique, puis choisissez le graphique à barres verticales et affectez les axes x et y](./media/app-insights-analytics-tour/230.png)

Bien que nous n’ayons pas trié les résultats par heure (comme le montre l’affichage de table), le graphique affiche toujours les dates dans l’ordre approprié.


## <a name="timecharts"></a>Graphiques temporels
Afficher le nombre d’événements qui se produisent chaque heure :

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Sélectionnez l’option d’affichage graphique :

![Graphique temporel](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Séries multiples
La présence de plusieurs expressions dans la clause `summarize` crée plusieurs colonnes.

Plusieurs expressions de la clause `by` créent plusieurs lignes, une pour chaque combinaison de valeurs.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tableau de demandes par heure et par emplacement](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Segmentez un graphique par dimensions
Si vous tracez un tableau comportant une colonne de type chaîne et une colonne numérique, la chaîne peut être utilisée pour fractionner les données numériques en séries de points distinctes. S’il existe plusieurs colonnes de type chaîne, vous pouvez choisir la colonne à utiliser comme discriminant.

![Segmenter un tableau d’analyse](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Taux de rebond

Convertir un booléen en une chaîne à utiliser comme discriminant :

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Afficher plusieurs mesures
Si vous tracez un tableau comportant plusieurs colonnes numériques, en plus du timestamp, vous pouvez afficher n’importe quelle combinaison de celles-ci.

![Segmenter un tableau d’analyse](./media/app-insights-analytics-tour/110.png)

Vous devez sélectionner **Ne pas fractionner** avant de pouvoir sélectionner plusieurs colonnes numériques. Vous ne pouvez fractionner par colonne de chaîne tout en affichant plusieurs colonnes numériques.

## <a name="daily-average-cycle"></a>Cycle moyen quotidien
Dans quelle mesure l’utilisation varie-t-elle dans un jour moyen ?

Compter les requêtes en prenant un jour comme modulo de temps et en fractionnant le résultat par heure :

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Graphique en courbes des heures dans une journée moyenne](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Remarquez que nous devons convertir les durées en dates pour afficher les résultats sur un graphique en courbes.
>
>

## <a name="compare-multiple-daily-series"></a>Comparer plusieurs séries quotidiennes
Dans quelle mesure l’utilisation varie-t-elle au fil de la journée dans différents pays ?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![Fractionner par client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Tracer une distribution
Combien existe-t-il de sessions de différentes longueurs ?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

La dernière ligne est nécessaire pour la conversion en datetime. L’axe X d’un graphique est affiché sous forme scalaire uniquement s’il s’agit d’une valeur datetime.

La clause `where` exclut les sessions à déclenchement unique (sessionDuration==0) et définit la longueur de l’axe X.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsioquerylanguagequerylanguagepercentilesaggfunctionhtml"></a>[Centiles](https://docs.loganalytics.io/queryLanguage/query_language_percentiles_aggfunction.html)
Quelles sont les plages de durées qui couvrent différents pourcentages de sessions ?

Utilisez la requête ci-dessus, mais remplacez la dernière ligne :

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Nous avons également supprimé la limite supérieure dans la clause where, afin d’obtenir des chiffres corrects englobant toutes les sessions avec plusieurs requêtes :

![result](./media/app-insights-analytics-tour/180.png)

Nous pouvons voir que :

* 5 % des sessions durent moins de 3 minutes 34 s ;
* 50 % des sessions durent moins de 36 minutes ;
* 5 % des sessions durent plus de 7 jours.

Pour obtenir une répartition distincte pour chaque pays, il suffit simplement d’intégrer la colonne client_CountryOrRegion séparément par le biais des deux opérateurs summarize :

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Join
Nous avons accès à plusieurs tables, y compris les demandes et les exceptions.

Pour rechercher les exceptions liées à une requête qui a renvoyé une réponse d’échec, nous pouvons joindre les tables sur `session_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Avant d’effectuer la jointure, nous pouvons utiliser `project` pour sélectionner uniquement les colonnes dont nous avons besoin.
Dans les mêmes clauses, nous renommons la colonne timestamp.

## <a name="lethttpsdocsloganalyticsioquerylanguagequerylanguageletstatementhtml-assign-a-result-to-a-variable"></a>[Let](https://docs.loganalytics.io/queryLanguage/query_language_letstatement.html): affecter un résultat à une variable

Utilisez `let` pour séparer les parties de l’expression précédente. Les résultats sont identiques :

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> Dans le client Analytics, ne placez pas de lignes vides entre les différentes parties de la requête. Exécutez-la en totalité.
>

Utilisez `toscalar` pour convertir une cellule de tableau en une valeur :

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Functions

Utilisez *Let* pour définir une fonction :

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Accès à des objets imbriqués
Les objets imbriqués sont faciles d’accès. Par exemple, dans le flux d’exceptions, vous pouvez voir des objets structurés comme suit :

![result](./media/app-insights-analytics-tour/520.png)

Vous pouvez les aplatir en choisissant les propriétés qui vous intéressent :

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Vous devez effectuer une conversion de type vers le type approprié.


## <a name="custom-properties-and-measurements"></a>Mesures et propriétés personnalisées
Si votre application attache [des dimensions (propriétés) personnalisées et des mesures personnalisées](app-insights-api-custom-events-metrics.md#properties) à des événements, elles seront visibles dans les objets `customDimensions` et `customMeasurements`.

Par exemple, si votre application inclut :

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Pour extraire ces valeurs dans Analytics :

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type

```

Pour vérifier si une dimension personnalisée est d’un type particulier :

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>Tableaux de bord
Vous pouvez épingler vos résultats à un tableau de bord afin de rassembler tous vos tableaux et graphiques les plus importants.

* [Tableau de bord partagé Azure](app-insights-dashboards.md#share-dashboards) : cliquez sur l’icône d’épingle. Avant cela, vous devez disposer d’un tableau de bord partagé. Dans le portail Azure, ouvrez ou créez un tableau de bord et cliquez sur Partager.
* [Tableau de bord Power BI](app-insights-export-power-bi.md) : cliquez sur Exporter, Requête Power BI. L’avantage de cette solution est que vous pouvez afficher votre requête avec d’autres résultats issus d’un large éventail de sources.

## <a name="combine-with-imported-data"></a>Combiner avec des données importées

Les rapports d’analyse sont du plus bel effet sur le tableau de bord mais il peut arriver que vous souhaitiez convertir les données dans un format plus digeste. Par exemple, supposons que vos utilisateurs authentifiés sont identifiés dans les données de télémétrie par un alias. Vous souhaitez afficher leurs vrais noms dans vos résultats. Pour cela, vous avez besoin d’un fichier CSV qui fait correspondre les alias aux véritables noms.

Vous pouvez importer un fichier de données et l’utiliser comme une table standard (requêtes, exceptions et ainsi de suite). Interrogez-la individuellement ou joignez-la à d’autres tables. Par exemple, si vous disposez d’une table nommée usermap et qu’elle contient des colonnes `realName` et `userId`, vous pouvez l’utiliser pour traduire le champ `user_AuthenticatedId` dans les données de télémétrie de la requête :

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Pour importer une table, dans le panneau Schéma, sous **Autres sources de données**, suivez les instructions pour ajouter une nouvelle source de données en téléchargeant un échantillon de vos données. Vous pouvez utiliser cette définition permet de charger des tables.

La fonctionnalité d’importation est actuellement en version préliminaire, vous verrez donc initialement un lien « Nous contacter » sous « Autres sources de données ». Utilisez-le pour vous inscrire au programme d’évaluation, et le lien sera alors remplacé par un bouton « Ajouter une nouvelle source de données ».


## <a name="tables"></a>Tables
Le flux de données de télémétrie provenant de votre application est accessible par le biais de plusieurs tables. Le schéma des propriétés disponibles pour chaque table est visible à la gauche de la fenêtre.

### <a name="requests-table"></a>Table de requêtes
Compter le nombre de requêtes HTTP envoyées à votre application web et les segmenter par nom de page :

![Compter les requêtes segmentées par nom](./media/app-insights-analytics-tour/analytics-count-requests.png)

Rechercher les requêtes qui échouent le plus :

![Compter les requêtes segmentées par nom](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Table des événements personnalisés
Si vous utilisez [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) pour envoyer vos propres événements, vous pouvez les lire depuis cette table.

Prenons un exemple dans lequel le code de votre application contient les lignes suivantes :

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Afficher la fréquence de ces événements :

![Afficher la fréquence des événements personnalisés](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Extraire des mesures et des dimensions de ces événements :

![Afficher la fréquence des événements personnalisés](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Table des mesures personnalisées
Si vous utilisez [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) pour envoyer vos propres valeurs métriques, vous trouverez ses résultats dans le flux **customMetrics**. Par exemple :   

![Mesures personnalisées dans Application Insights Analytics](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> Dans [Metrics Explorer](app-insights-metrics-explorer.md), toutes les mesures personnalisées attachées à une télémétrie, quel que soit son type, apparaissent dans le panneau de métriques avec les métriques envoyées à l’aide de `TrackMetric()`. Dans Analytics, en revanche, les mesures personnalisées restent attachées à leur type de télémétrie d’origine (événements, requêtes, etc.) alors que les métriques envoyées par TrackMetric apparaissent dans leur propre flux.
>
>

### <a name="performance-counters-table"></a>Table des compteurs de performances
[Les compteurs de performances](app-insights-performance-counters.md) vous indiquent les métriques système de base pour votre application, notamment le processeur, la mémoire et l’utilisation du réseau. Vous pouvez configurer le Kit de développement logiciel (SDK) pour l’envoi de compteurs supplémentaires, notamment vos propres compteurs personnalisés.

Le schéma **compteur de performances** expose les noms `category`, `counter` et `instance` nom de chaque compteur de performance. Les noms d’instance de compteur sont uniquement applicables à certains compteurs de performance et indiquent généralement le nom du processus auquel le comptage se rapporte. Dans les données de télémétrie pour chaque application, vous ne verrez que les compteurs pour cette application. Par exemple, pour voir les compteurs disponibles :

![Compteurs de performances dans Application Insights Analytics](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Pour obtenir un graphique présentant la mémoire disponible sur la période sélectionnée :

![Graphique temporel dans Application Insights Analytics](./media/app-insights-analytics-tour/analytics-available-memory.png)

Comme les autres données de télémétrie, **performanceCounters** possède également une colonne `cloud_RoleInstance` qui indique l’identité de l’ordinateur hôte sur lequel votre application est en cours d’exécution. Par exemple, pour comparer les performances de votre application sur des ordinateurs différents :

![Performances segmentées par instances de rôle d’Application Insights Analytics](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Table des exceptions
[Les exceptions signalées par votre application](app-insights-asp-net-exceptions.md) sont disponibles dans cette table.

Pour rechercher la requête HTTP que votre application traitait lorsque l’exception a été levée, ajoutez l’operation_Id :

![Ajoutez des exceptions avec des requêtes operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Table des délais de chargement dans le navigateur
`browserTimings` affiche les données de chargement de pages collectées dans les navigateurs de vos utilisateurs.

[Configurez votre application pour la télémétrie côté client](app-insights-javascript.md) afin de voir ces métriques.

Le schéma inclut [les métriques indiquant les longueurs des différentes étapes du processus de chargement de pages](app-insights-javascript.md#page-load-performance) (elles n’indiquent pas la durée de fois vos utilisateurs lisent une page).  

Afficher les popularités de différentes pages et les délais de chargement pour chaque page :

![Délai de chargement de page dans Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Tableau de résultats de la disponibilité
`availabilityResults` affiche les résultats de votre [tests web](app-insights-monitor-web-app-availability.md). Chaque exécution de vos tests à partir de chaque emplacement est déclarée séparément.

![Délai de chargement de page dans Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Table des dépendances
Contient les résultats d’appels que votre application effectue vers des bases de données et des API REST et d’autres appels vers TrackDependency(). Inclut également les appels AJAX effectués à partir du navigateur.

Appels AJAX à partir du navigateur :

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Appels de dépendance à partir du serveur :

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Les résultats de la dépendance côté serveur indiquent toujours `success==False` si l’agent Application Insights n’est pas installé. Toutefois, les autres données sont correctes.

### <a name="traces-table"></a>Table des traces
Contient les données de télémétrie envoyées par votre application à l’aide de TrackTrace(), ou [d’autres frameworks de journalisation](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Vidéo 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Requêtes avancées :

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Étapes suivantes
* [Référence sur le langage Analytics](app-insights-analytics-reference.md)
* [L’aide-mémoire des utilisateurs de SQL](https://aka.ms/sql-analytics) traduit les idiomes courants.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
