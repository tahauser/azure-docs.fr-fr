---
title: API Application Insights pour les événements et les métriques personnalisés | Microsoft Docs
description: Insérez quelques lignes de code dans votre application de périphérique ou de bureau, votre page web ou votre service pour suivre l'utilisation et diagnostiquer les problèmes.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: ff4b587790872511c7b545233685f5b3ae068291
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API Application Insights pour les événements et les mesures personnalisés

Insérez quelques lignes de code dans votre application pour découvrir ce qu’en font les utilisateurs ou pour faciliter le diagnostic des problèmes. Vous pouvez envoyer la télémétrie depuis des applications de périphérique et de bureau, des clients web et des serveurs web. Utilisez l’API des données de télémétrie principales [Azure Application Insights](app-insights-overview.md) pour envoyer des événements et métriques personnalisés, ainsi que vos propres versions de la télémétrie standard. Cette API est la même que celle utilisée par les collecteurs de données standard d’Application Insights.

## <a name="api-summary"></a>Résumé des API
L’API est uniforme sur toutes les plateformes, à l’exception de quelques petites variations.

| Méthode | Utilisé pour |
| --- | --- |
| [`TrackPageView`](#page-views) |Pages, écrans, panneaux ou formes. |
| [`TrackEvent`](#trackevent) |Actions de l’utilisateur et autres événements. Utilisé pour suivre le comportement de l’utilisateur ou pour analyser les performances. |
| [`TrackMetric`](#trackmetric) |Mesures de performances telles que la longueur des files d’attente non liées à des événements spécifiques. |
| [`TrackException`](#trackexception) |Exceptions de journal pour des diagnostics. Effectuez un suivi lorsqu’ils se produisent par rapport à d’autres événements et examinez les arborescences des appels de procédure. |
| [`TrackRequest`](#trackrequest) |Notez la fréquence et la durée des requêtes du serveur pour l’analyse des performances. |
| [`TrackTrace`](#tracktrace) |Messages du journal de diagnostic Vous pouvez également capturer des journaux tiers. |
| [`TrackDependency`](#trackdependency) |La journalisation de la durée et de la fréquence des appels vers les composants externes dont dépend votre application. |

Vous pouvez [associer des propriétés et des mesures](#properties) à la plupart de ces appels de télémétrie.

## <a name="prep"></a>Avant de commencer
Si vous n’avez pas encore de référence sur le kit SDK Application Insights :

* Ajoutez le Kit de développement logiciel (SDK) Application Insights à votre projet :

  * [Projet ASP.NET](app-insights-asp-net.md)
  * [Projet Java](app-insights-java-get-started.md)
  * [Projet Node.js](app-insights-nodejs.md)
  * [JavaScript dans chaque page web](app-insights-javascript.md) 
* Ajoutez au code de votre périphérique ou de votre serveur web :

    *C# :*`using Microsoft.ApplicationInsights;`

    *Visual Basic :*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`
    
    *Node.js :* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Obtenir une instance de TelemetryClient
Obtenez une instance de `TelemetryClient` (sauf en JavaScript dans les pages web) :

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();
    
*Node.JS*

    var telemetry = applicationInsights.defaultClient;


TelemetryClient est thread-safe.

Dans le cas des projets ASP.NET et Java, les requêtes HTTP entrantes sont automatiquement capturées. Si vous le souhaitez, vous pouvez créer des instances supplémentaires de TelemetryClient pour l’autre module de votre application. Il est par exemple possible d’en avoir une dans une classe d’intergiciels pour signaler les événements de logique métier. Vous pouvez définir des propriétés comme UserId et DeviceId pour identifier l’ordinateur. Cette information est associée à tous les événements envoyés par l'instance. 

*C#*

    TelemetryClient.Context.User.Id = "...";
    TelemetryClient.Context.Device.Id = "...";

*Java*

    telemetry.getContext().getUser().setId("...);
    telemetry.getContext().getDevice().setId("...");

Dans les projets Node.js, vous pouvez utiliser `new applicationInsights.TelemetryClient(instrumentationKey?)` pour créer une instance, mais cette procédure est réservée aux scénarios qui nécessitent une configuration isolée du singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent
Dans Application Insights, un *événement personnalisé* est un point de données que vous pouvez afficher dans [Metrics Explorer](app-insights-metrics-explorer.md) en tant que nombre agrégé et dans [Recherche de diagnostic](app-insights-diagnostic-search.md) en tant qu’occurrences individuelles. (Il n’est pas lié à des « événements » de type MVC ou autres.)

Insérez des appels `TrackEvent` dans votre code pour compter les différents événements. Par exemple, la fréquence à laquelle les utilisateurs choisissent une fonctionnalité particulière, la fréquence à laquelle ils atteignent des objectifs particuliers ou à laquelle ils commettent éventuellement des types d’erreurs particuliers.

Par exemple, dans une application de jeu, envoyez un événement chaque fois qu'un utilisateur gagne le jeu :

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");
    
*Node.JS*

    telemetry.trackEvent({name: "WinGame"});

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>Afficher vos événements sur le portail Microsoft Azure
Pour voir un nombre de vos événements, ouvrez un panneau [Metrics Explorer](app-insights-metrics-explorer.md) , ajoutez un nouveau graphique, puis sélectionnez **Événements**.  

![Afficher un nombre d’événements personnalisés](./media/app-insights-api-custom-events-metrics/01-custom.png)

Pour comparer le nombre d'événements différents, définissez le type de graphique sur **Grille** et groupez par nom d'événement :

![Définition du type de graphique et du regroupement](./media/app-insights-api-custom-events-metrics/07-grid.png)

Dans la grille, cliquez sur un nom d'événement pour voir les occurrences individuelles de cet événement. Pour afficher plus de détails, cliquez sur n’importe quelle occurrence de la liste.

![Extrayez les événements](./media/app-insights-api-custom-events-metrics/03-instances.png)

Pour vous concentrer sur des événements spécifiques dans la recherche ou Metrics Explorer, définissez le filtre du panneau sur les noms d'événements qui vous intéressent :

![Ouvrez Filtres, développez Nom de l'événement et sélectionnez une ou plusieurs valeurs](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Événements personnalisés dans l’analytique

La télémétrie est disponible dans la table `customEvents` dans [Application Insights - Analytique](app-insights-analytics.md). Chaque ligne représente un appel à `trackEvent(..)` dans votre application. 

Si un [échantillonnage](app-insights-sampling.md) est en cours, la propriété itemCount affiche une valeur supérieure à 1. Par exemple, itemCount==10 signifie que sur 10 appels à trackEvent(), le processus d’échantillonnage n’en a transmis qu’un seul. Pour obtenir un nombre correct d’événements personnalisés, vous devez utiliser un code similaire à `customEvent | summarize sum(itemCount)`.


## <a name="trackmetric"></a>TrackMetric

Application Insight peut représenter des mesures qui ne sont pas associées à des événements particuliers. Par exemple, vous pouvez analyser la longueur d’une file d'attente à des intervalles réguliers. Avec les mesures, les mesures individuelles sont moins intéressantes que les variations et tendances, ainsi les graphiques statistiques sont utiles.

Pour pouvoir envoyer des métriques à Application Insights, vous pouvez utiliser l’API `TrackMetric(..)`. Il existe deux façons d’envoyer une métrique : 

* Valeur unique. Chaque fois que vous effectuez une mesure dans votre application, vous envoyez la valeur correspondante à Application Insights. Par exemple, supposons que vous avez une mesure décrivant le nombre d’éléments dans un conteneur. Pendant une période donnée, vous placez d’abord trois éléments dans le conteneur puis vous en supprimez deux. En conséquence, vous appelez `TrackMetric` à deux reprises : d’abord en passant la valeur `3` puis la valeur `-2`. Application Insights stocke les deux valeurs pour votre compte. 

* Agrégation. Quand vous travaillez avec des métriques, chaque mesure individuelle est rarement intéressante. Au lieu de cela, un récapitulatif de ce qui s’est passé au cours d’une période donnée est important. Un tel récapitulatif est appelé _agrégation_. Dans l’exemple ci-dessus, la somme totale des métriques pour cette période est `1` et le nombre de valeurs des métriques est `2`. Quand vous utilisez l’approche par agrégation, vous appelez `TrackMetric` une seule fois par période et vous envoyez les valeurs agrégées. C’est l’approche recommandée, car elle peut réduire considérablement le coût et les problèmes de performances en envoyant moins de points de données à Application Insights, tout en collectant néanmoins toutes les informations pertinentes.

### <a name="examples"></a>Exemples :

#### <a name="single-values"></a>Valeurs uniques

Pour envoyer une seule valeur métrique :

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

*Java*

```Java
    
    telemetry.trackMetric("queueLength", 42.0);

```

*Node.JS*

 ```Javascript
     telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

#### <a name="aggregating-metrics"></a>Agrégation des métriques

Il est recommandé d’agréger les mesures avant de les envoyer à partir de votre application, de façon à réduire la bande passante et le coût, et à améliorer les performances.
Voici un exemple de code d’agrégation :

*C#*

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>Métriques personnalisées dans Metrics Explorer

Pour afficher les résultats, ouvrez Metrics Explorer et ajoutez un nouveau graphique. Modifiez le graphique pour afficher votre mesure.

> [!NOTE]
> L’affichage de votre mesure personnalisée dans la liste des mesures disponibles peut prendre plusieurs minutes.
>

![Ajouter un nouveau graphique ou sélectionnez un graphique et sélectionnez votre métrique sous Personnalisé](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Métriques personnalisées dans Analytics

La télémétrie est disponible dans la table `customMetrics` dans [Application Insights - Analytique](app-insights-analytics.md). Chaque ligne représente un appel à `trackMetric(..)` dans votre application.
* `valueSum` - Il s’agit de la somme des mesures. Pour obtenir la valeur moyenne, divisez par `valueCount`.
* `valueCount` : le nombre de mesures qui ont été agrégées dans cet appel de `trackMetric(..)`.

## <a name="page-views"></a>Affichages de page
Dans un périphérique ou une application de page web, la télémétrie d'affichage de page est envoyée par défaut lorsque chaque écran ou page est chargé. Mais vous pouvez modifier cela pour suivre les affichages de page à différents moments. Par exemple, dans une application qui affiche les onglets ou les panneaux, vous pouvez effectuer le suivi d'une « page » chaque fois que l'utilisateur ouvre un nouveau panneau.

![Filtre d'utilisation dans le panneau Vue d'ensemble](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Les données d’utilisateur et de session sont envoyées en tant que propriétés avec les affichages de page, de façon à ce que les graphiques d’utilisateur et de session soient actifs s’il existe une télémétrie de l’affichage de page.

### <a name="custom-page-views"></a>Affichages de pages personnalisées
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Java*

    telemetry.trackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Si vous avez plusieurs onglets dans différentes pages HTML, vous pouvez aussi spécifier l'URL :

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Affichages de la page de durée
Par défaut, les heures déclarées **Temps de chargement de l’affichage de la page** sont mesurées à partir du moment où le navigateur envoie la demande, jusqu’à ce que l’événement de chargement de la page du navigateur soit appelé.

Au lieu de cela, vous pouvez :

* Définir une durée explicite dans l’appel [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) : `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Utiliser les appels de minutage d’affichage de la page `startTrackPage` et `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

Le nom que vous utilisez comme premier paramètre associe les appels de démarrage et d’arrêt. Le nom de la page actuelle est utilisé par défaut.

Les durées de chargement de la page résultantes affichées dans Metrics Explorer sont dérivées de l’intervalle entre les appels de démarrage et d’arrêt. C’est à vous de décider quel intervalle de temps vous voulez mesurer.

### <a name="page-telemetry-in-analytics"></a>Télémétrie des pages dans Analytique

Dans [Analytique](app-insights-analytics.md), deux tables affichent les données des opérations du navigateur :

* La table `pageViews` contient des données sur l’URL et le titre de la page.
* La table `browserTimings` contient des données sur les performances du client, comme le temps nécessaire pour traiter les données entrantes.

Pour trouver le temps mis par le navigateur pour traiter différentes pages :

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

Pour découvrir la popularité de différents navigateurs :

```
pageViews | summarize count() by client_Browser
```

Pour associer des vues de pages à des appels AJAX, joindre à des dépendances :

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
Le kit de développement logiciel de serveur utilise TrackRequest pour consigner les requêtes HTTP.

Vous pouvez également l'appeler vous-même si vous souhaitez simuler des requêtes dans le cas où le module du service web n’est pas en cours d'exécution.

Toutefois, le moyen recommandé d’envoyer la télémétrie de la demande est là où la demande agit comme un <a href="#operation-context">contexte d’opération</a>.

## <a name="operation-context"></a>Contexte de l’opération
Vous pouvez associer des éléments de télémétrie en les associant à un contexte d’opération. Le module de suivi de requête standard effectue cette opération pour les exceptions et les autres événements envoyés lors du traitement d’une requête HTTP. Dans [Recherche](app-insights-diagnostic-search.md) et [Analytique](app-insights-analytics.md), vous pouvez trouver facilement tous les événements associés à la requête avec son ID d’opération.

Consultez [Corrélation de télémétrie dans Application Insights](application-insights-correlation.md) pour plus d’informations sur la corrélation.

Lors du suivi manuel des données de télémétrie, le moyen le plus simple pour vous assurer de la corrélation des données de télémétrie en utilisant ce modèle :

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...
    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Outre la définition d’un contexte d’opération, `StartOperation` crée un élément de télémétrie du type que vous spécifiez. Il envoie l’élément de télémétrie lorsque vous libérez l’opération, ou si vous appelez explicitement `StopOperation`. Si vous utilisez `RequestTelemetry` comme type de télémétrie, alors sa durée est définie sur l’intervalle compris entre le début et la fin.

Les éléments de télémétrie signalés dans une étendue de l’opération deviennent « enfants » de cette opération. Les contextes de l’opération ne peuvent pas être imbriqués. 

Dans Recherche, le contexte d’opération est utilisé pour créer la liste **Éléments connexes** :

![Éléments connexes](./media/app-insights-api-custom-events-metrics/21.png)

Consultez [Suivi des opérations personnalisées avec le kit SDK .NET d’Application Insights](application-insights-custom-operations-tracking.md) pour plus d’informations sur le suivi des opérations personnalisées.

### <a name="requests-in-analytics"></a>Requêtes dans Analytique 

Dans [Application Insights - Analytique](app-insights-analytics.md), les demandes s’affichent dans la table `requests`.

Si [l’échantillonnage](app-insights-sampling.md) est en cours, la propriété itemCount affiche une valeur supérieure à 1. Par exemple, itemCount==10 signifie que sur 10 appels à trackRequest(), le processus d’échantillonnage n’en a transmis qu’un seul. Pour obtenir un nombre correct de demandes et une durée moyenne segmentée par nom des demandes, utilisez un code similaire à celui-ci :

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
Envoi d’exceptions à Application Insights :

* Pour [les compter](app-insights-metrics-explorer.md), comme une indication de la fréquence d’un problème.
* Pour [inspecter les occurrences individuelles](app-insights-diagnostic-search.md).

Les rapports incluent des arborescences des appels de procédure.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*Java*

    try {
        ...
    } catch (Exception ex) {
        telemetry.trackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }
    
*Node.JS*

    try
    {
       ...
    }
    catch (ex)
    {
       telemetry.trackException({exception: ex});
    }

Les Kits de développement logiciel (SDK) interceptent de nombreuses exceptions automatiquement, ce qui vous évite ainsi d’avoir toujours à appeler TrackException explicitement.

* ASP.NET : [écriture d'un code pour intercepter les exceptions](app-insights-asp-net-exceptions.md).
* J2EE : [les exceptions sont interceptées automatiquement](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript : les exceptions sont interceptées automatiquement. Si vous souhaitez désactiver la collecte automatique, ajoutez une ligne dans l'extrait de code que vous insérez dans vos pages web :

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Exceptions dans Analytique

Dans [Application Insights - Analytique](app-insights-analytics.md), les exceptions s’affichent dans la table `exceptions`.

Si un [échantillonnage](app-insights-sampling.md) est en cours, la propriété `itemCount` affiche une valeur supérieure à 1. Par exemple, itemCount==10 signifie que sur 10 appels à trackException(), le processus d’échantillonnage n’en a transmis qu’un seul. Pour obtenir un nombre correct d’exceptions segmentées par type d’exception, utilisez un code similaire à celui-ci :

```
exceptions | summarize sum(itemCount) by type
```

La plupart des informations importantes sur la pile sont déjà extraites dans des variables distinctes, mais vous pouvez extraire séparément la structure `details` pour en savoir plus. Comme cette structure est dynamique, vous devez effectuer une conversion de type (transtypage) du résultat vers le type attendu. Par exemple : 

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Pour associer des exceptions aux demandes qui s’y rapportent, utilisez une jointure :

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
Utilisez TrackTrace pour diagnostiquer des problèmes en envoyant une « piste de navigation » à Application Insights. Vous pouvez envoyer des blocs de données de diagnostic et les examiner dans la [Recherche de diagnostic](app-insights-diagnostic-search.md).

Dans .NET, les [Adaptateurs de journaux](app-insights-asp-net-trace-logs.md) utilisent cette API pour envoyer des journaux tiers au portail.

En Java, pour les [Enregistreurs d’événements standard, comme Log4J ou Logback](app-insights-java-trace-logs.md), utilisez les appenders Application Insights Log4j ou Logback afin d’envoyer des journaux tiers au portail.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

*Java*

    telemetry.trackTrace(message, SeverityLevel.Warning, properties);
    
*Node.JS*

    telemetry.trackTrace({message: message, severity:applicationInsights.Contracts.SeverityLevel.Warning, properties:properties});


Vous pouvez effectuer une recherche dans le contenu du message, mais (contrairement aux valeurs de propriété), vous ne pouvez pas les filtrer.

La limite de taille sur `message` est plus importante que la limite des propriétés.
l’un des avantages de TrackTrace est que vous pouvez insérer des données relativement longues dans le message. Par exemple, vous pourriez y encoder des données POST.  

Par ailleurs, vous pouvez ajouter un niveau de gravité à votre message. Comme pour les autres données de télémétrie, vous pouvez également ajouter des valeurs de propriété qui permettent de filtrer ou rechercher différents jeux de traces. Par exemple : 

*C#*

```C#
    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```Java

    Map<String, Integer> properties = new HashMap<>();
    properties.put("Database", db.ID);
    telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);

```

Dans [Recherche](app-insights-diagnostic-search.md), vous pouvez filtrer facilement tous les messages d’un niveau de gravité particulier portant sur une certaine base de données.


### <a name="traces-in-analytics"></a>Traces dans Analytique

Dans [Application Insights - Analytique](app-insights-analytics.md), les appels à TrackTrace s’affichent dans la table `traces`.

Si un [échantillonnage](app-insights-sampling.md) est en cours, la propriété itemCount affiche une valeur supérieure à 1. Par exemple, itemCount==10 signifie que sur 10 appels à `trackTrace()`, le processus d’échantillonnage n’en a transmis qu’un seul. Pour obtenir un nombre correct d’appels de trace, vous devez utiliser un code similaire à `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency
Utilisez l’appel à TrackDependency pour suivre les temps de réponse et les taux de réussite des appels vers un bloc de code externe. Les résultats s'affichent dans les graphiques de dépendance sur le portail.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```Java
    boolean success = false;
    long startTime = System.currentTimeMillis();
    try {
        success = dependency.call();
    }
    finally {
        long endTime = System.currentTimeMillis();
        long delta = endTime - startTime;
        RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
        telemetry.setTimeStamp(startTime);
        telemetry.trackDependency(dependencyTelemetry);
    }

```

*JavaScript*

```Javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({dependencyTypeName: "myDependency", name: "myCall", duration: elapsed, success:success});
}
```

N’oubliez pas que les kits SDK de serveur incluent un [module de dépendance](app-insights-asp-net-dependencies.md) qui détecte certains appels de dépendance et en effectue le suivi automatiquement. C’est notamment le cas des bases de données et des API REST. Vous devez installer un agent sur votre serveur pour que le module fonctionne. 

En Java, [l’Agent Java](app-insights-java-agent.md) permet de suivre automatiquement certains appels de dépendance.

Vous utiliserez cet appel si vous souhaitez effectuer le suivi des appels qui ne sont pas interceptés par le système de suivi automatisé, ou si vous ne souhaitez pas installer l'agent.

Pour désactiver le module de suivi des dépendances standard en C#, modifiez [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) et supprimez la référence à `DependencyCollector.DependencyTrackingTelemetryModule`. En Java, n’installez pas l’agent Java si vous ne souhaitez pas collecter automatiquement les dépendances standards.

### <a name="dependencies-in-analytics"></a>Dépendances dans Analytique

Dans [Application Insights - Analytique](app-insights-analytics.md), les appels de trackDependency s’affichent dans la table `dependencies`.

Si un [échantillonnage](app-insights-sampling.md) est en cours, la propriété itemCount affiche une valeur supérieure à 1. Par exemple, itemCount==10 signifie que sur 10 appels à trackDependency(), le processus d’échantillonnage n’en a transmis qu’un seul. Pour obtenir un nombre correct de dépendances segmentées par composant cible, utilisez un code similaire à celui-ci :

```
dependencies | summarize sum(itemCount) by target
```

Pour associer des dépendances aux demandes qui s’y rapportent, utilisez une jointure :

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>Vidage des données
Normalement, le kit SDK envoie des données à des moments choisis pour minimiser l'impact sur l'utilisateur. Toutefois, dans certains cas vous pouvez vider la mémoire tampon - par exemple, si vous utilisez le kit SDK dans une application qui s'arrête.

*C#*
 
 ```C#
    telemetry.Flush();
    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(5000);
```

*Java*

```Java
    telemetry.flush();
    //Allow some time for flushing before shutting down
    Thread.sleep(5000);
```

    
*Node.JS*

    telemetry.flush();

Notez que la fonction est asynchrone pour le [canal de télémétrie du serveur](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Dans l’idéal, il est recommandé d’utiliser la méthode flush() dans l’activité d’arrêt de l’application.

## <a name="authenticated-users"></a>Utilisateurs authentifiés
Dans une application web, les utilisateurs sont identifiés par des cookies par défaut. Un utilisateur peut être compté plusieurs fois s’il accède à votre application à partir d’un autre ordinateur ou navigateur, ou s’il supprime des cookies.

Mais si les utilisateurs se connectent à votre application, vous pouvez obtenir un nombre plus précis en définissant l’ID de l’utilisateur authentifié dans le code du navigateur :

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Dans une application MVC Web ASP.NET, par exemple :

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Il n’est pas nécessaire d’utiliser le nom de connexion réel de l’utilisateur. Il doit uniquement s’agir d’un ID unique pour cet utilisateur. Il ne doit pas inclure d'espaces ni l'un des caractères suivants : `,;=|`.

L’ID d’utilisateur est également défini dans un cookie de session et envoyé au serveur. Si le kit SDK de serveur est installé, l’ID d’utilisateur authentifié est envoyé dans le cadre des propriétés de contexte de télémétrie client et serveur. Vous pouvez ensuite filtrer et rechercher dessus.

Si votre application regroupe les utilisateurs par comptes, vous pouvez également fournir un identificateur pour ce compte (avec les mêmes restrictions de caractères).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

Dans [Metrics Explorer](app-insights-metrics-explorer.md), vous pouvez créer un graphique qui compte les **Utilisateurs authentifiés** et les **Comptes d’utilisateur**.

Vous pouvez également [rechercher](app-insights-diagnostic-search.md) les points de données client avec des comptes et des noms d'utilisateur spécifiques.

## <a name="properties"></a>Filtrage, recherche et segmentation de vos données à l’aide des propriétés
Vous pouvez associer des propriétés et des mesures à vos événements (et également à des mesures, des affichages de page, des exceptions et d'autres données de télémétrie).

*propriétés* sont des valeurs de chaîne que vous pouvez utiliser pour filtrer votre télémétrie dans les rapports d'utilisation. Par exemple, si votre application fournit plusieurs jeux, vous pouvez attacher le nom du jeu à chaque événement pour vous permettre de savoir quels sont les jeux les plus populaires.

Il existe une limite de 8192 sur la longueur de chaîne. (Si vous souhaitez envoyer d’importants blocs de données, utilisez le paramètre de message de [TrackTrace](#track-trace).)

*mesures* sont des valeurs numériques qui peuvent être représentées sous forme graphique. Par exemple, observez s'il existe une augmentation progressive des scores atteints par vos joueurs. Les graphes peuvent être segmentés par les propriétés envoyées avec l'événement pour vous permettre d’obtenir des graphes distincts ou empilés pour différents jeux.

Pour que les valeurs de mesure s’affichent correctement, elles doivent être supérieures ou égales à 0.

Il existe certaines [limites au nombre de propriétés, de valeurs de propriété et de mesures](#limits) que vous pouvez utiliser.

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);

*Node.JS*

    // Set up some properties and metrics:
    var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
    var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

    // Send the event:
    telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});


*Visual Basic*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> Veillez à ne pas journaliser des informations personnelles dans les propriétés.
>
>

*Si vous avez utilisé des mesures*, ouvrez Metrics Explorer et sélectionnez la mesure à partir du groupe **personnalisé** :

![Ouvrez Metrics Explorer, sélectionnez le graphique puis sélectionnez la mesure](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Si votre mesure n'apparaît pas, ou si l'en-tête **personnalisé** n'y figure pas, fermez le panneau de sélection et réessayez ultérieurement. L’agrégation des mesures via le pipeline peut parfois prendre une heure.

*Si vous avez utilisé des propriétés et des mesures*, segmentez la mesure par la propriété :

![Définissez le groupe, puis sélectionnez la propriété sous Grouper par](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

Dans *Recherche de diagnostic*, vous pouvez afficher les propriétés et les mesures des occurrences individuelles d’un événement.

![Sélectionnez une instance, puis sélectionnez « ... »](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Utilisez le champ **Rechercher** pour voir les occurrences de l'événement présentant une valeur de propriété particulière.

![Tapez un terme dans Rechercher](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[En savoir plus sur les expressions de recherche](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>Autre façon de définir des propriétés et des mesures
Si cela est plus pratique, vous pouvez collecter les paramètres d'un événement dans un objet séparé :

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Ne réutilisez pas la même instance d’élément de télémétrie (`event` dans cet exemple) pour appeler Track*() plusieurs fois. Cela peut provoquer un envoi de données de télémétrie configurées de façon incorrecte.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Mesures et propriétés personnalisées dans Analytique

Dans [Analytique](app-insights-analytics.md), les mesures et les propriétés personnalisées s’affichent dans les attributs `customMeasurements` et `customDimensions` de chaque enregistrement de télémétrie.

Par exemple, si vous avez ajouté une propriété nommée « game » à votre télémétrie des demandes, cette requête compte les occurrences des différentes valeurs de « game » et affiche la moyenne de la métrique personnalisée « score » :

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

Notez que :

* Quand vous extrayez une valeur du JSON la customDimensions ou customMeasurements, elle est de type dynamique et par conséquent, vous devez la transtyper en `tostring` ou `todouble`.
* Pour tenir compte de la possibilité [d’échantillonnage](app-insights-sampling.md), vous devez utiliser `sum(itemCount)`, et non pas `count()`.



## <a name="timed"></a> Événements de durée
Vous avez parfois besoin d’obtenir une représentation graphique de la durée nécessaire à la réalisation d’une action. Par exemple, vous souhaitez savoir de combien de temps les utilisateurs ont besoin pour évaluer leurs choix dans un jeu. Vous pouvez utiliser le paramètre de mesure pour cela.

*C#*

```C#
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```Java
    long startTime = System.currentTimeMillis();

    // perform timed action

    long endTime = System.currentTimeMillis();
    Map<String, Double> metrics = new HashMap<>();
    metrics.put("ProcessingTime", endTime-startTime);

    // Setup some propereties
    Map<String, String> properties = new HashMap<>();
    properties.put("signalSource", currentSignalSource.getName());

    //send the event
    telemetry.trackEvent("SignalProcessed", properties, metrics);

```


## <a name="defaults"></a>Propriétés par défaut pour la télémétrie personnalisée
Si vous souhaitez définir des valeurs de propriété par défaut pour certains des événements personnalisés que vous écrivez, vous pouvez les définir dans une instance de TelemetryClient. Ils sont associés à chaque élément de télémétrie envoyée à partir de ce client.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");
    
*Node.JS*

    var gameTelemetry = new applicationInsights.TelemetryClient();
    gameTelemetry.commonProperties["Game"] = currentGame.Name;

    gameTelemetry.TrackEvent({name: "WinGame"});



Les appels de télémétrie individuels peuvent remplacer les valeurs par défaut dans leurs dictionnaires de propriété.

*Pour les clients Web JavaScript*, [utilisez des initialiseurs de télémétrie JavaScript](#js-initializer).

*Pour ajouter des propriétés à toutes les données de télémétrie*, notamment les données des modules de collecte standard, [implémentez `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Échantillonnage, filtrage et traitement de la télémétrie
Vous pouvez écrire du code pour traiter votre télémétrie avant de l’envoyer à partir du Kit de développement logiciel (SDK). Le traitement inclut les données envoyées par les modules de télémétrie standard, telles que la collection de requêtes HTTP et la collection de dépendances.

[Ajoutez des propriétés](app-insights-api-filtering-sampling.md#add-properties) à la télémétrie en implémentant `ITelemetryInitializer`. Par exemple, vous pouvez ajouter des numéros de version ou des valeurs calculées à partir d'autres propriétés.

[Le filtrage](app-insights-api-filtering-sampling.md#filtering) peut modifier ou abandonner des données de télémétrie avant leur envoi depuis le SDK en implémentant `ITelemetryProcesor`. Vous contrôlez ce qui est envoyé ou rejeté, mais vous devez prendre en compte l’impact sur vos mesures. Suivant la façon dont vous ignorez les éléments, vous risquez de ne plus pouvoir naviguer entre des éléments connexes.

[L’échantillonnage](app-insights-api-filtering-sampling.md) est une solution intégrée pour réduire le volume des données envoyées à partir de votre application vers le portail. Cela n’affecte pas les mesures affichées. Et il n’affecte pas votre capacité à diagnostiquer les problèmes en navigant entre des éléments connexes, tels que les exceptions, les requêtes et les affichages de page.

[Plus d’informations](app-insights-api-filtering-sampling.md)

## <a name="disabling-telemetry"></a>Désactivation de la télémétrie
Pour *arrêter et démarrer dynamiquement* la collecte et la transmission de la télémétrie :

*C#*

```csharp

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```Java
    
    telemetry.getConfiguration().setTrackingDisabled(true);

```

Pour *désactiver les collecteurs standard sélectionnés* (par exemple, les compteurs de performances, les requêtes HTTP ou les dépendances), supprimez ou commentez les lignes correspondantes dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Par exemple, vous pouvez faire cela si vous souhaitez envoyer vos propres données TrackRequest.

*Node.JS*

```Javascript

    telemetry.config.disableAppInsights = true;
```

Pour *désactiver les collecteurs standard sélectionnés* (par exemple, les compteurs de performances, les requêtes HTTP ou les dépendances) lors de l’initialisation, enchaînez plusieurs méthodes de configuration dans votre code d’initialisation SDK.

```Javascript

    applicationInsights.setup()
        .setAutoCollectRequests(false)
        .setAutoCollectPerformance(false)
        .setAutoCollectExceptions(false)
        .setAutoCollectDependencies(false)
        .setAutoCollectConsole(false)
        .start();
```

Pour désactiver ces collecteurs après l’initialisation, utilisez l’objet de configuration : `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Mode Développeur :
Pendant le débogage, il est utile d'avoir votre télémétrie envoyée par le pipeline afin que vous puissiez voir immédiatement les résultats. Vous obtenez également des messages supplémentaires qui vous permettent de suivre tout problème relatif à la télémétrie. Désactivez-les lors de la production, car ils peuvent ralentir votre application.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Définition de la touche d’instrumentation pour la télémétrie personnalisée sélectionnée
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Clé d'instrumentation dynamique
Pour éviter de mélanger la télémétrie fournie par les environnements de développement, de test et de production, vous pouvez [créer des ressources Application Insights distinctes](app-insights-create-new-resource.md) et modifier leurs clés en fonction de l’environnement.

Au lieu de récupérer la clé d'instrumentation à partir du fichier de configuration, vous pouvez la définir dans votre code. Définissez la clé dans une méthode d'initialisation, par exemple global.aspx.cs dans un service ASP.NET :

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



Dans les pages web, vous pouvez la définir depuis l'état du serveur web au lieu de la coder littéralement dans le script. Par exemple, dans une page web générée dans une application ASP.NET :

*JavaScript dans Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient a une propriété de contexte contenant les valeurs qui sont envoyées avec toutes les données de télémétrie. Elles sont normalement définies par les modules de télémétrie standard, mais vous pouvez également les définir vous-même. Par exemple : 

    telemetry.Context.Operation.Name = "MyOperationName";

Si vous définissez une de ces valeurs vous-même, supprimez la ligne appropriée dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), pour ne pas mélanger vos valeurs et les valeurs standard.

* **Composant** : l'application et sa version.
* **Appareil** : données concernant l’appareil sur lequel l’application s’exécute. (dans les applications web, il s’agit du serveur ou de l’appareil client depuis lequel la télémétrie est envoyée.)
* **InstrumentationKey** : la ressource Application Insights dans Azure dans laquelle la télémétrie s’affiche. Elle est généralement récupérée dans ApplicationInsights.config.
* **Emplacement** : emplacement géographique de l’appareil.
* **Opération** : dans les applications web, il s’agit de la requête HTTP actuelle. Dans d'autres types d'application, vous pouvez définir celle-ci sur les événements regroupés.
  * **ID**: une valeur générée qui met en relation différents événements de manière à ce que vous trouviez les « Éléments associés » lorsque vous inspectez un événement dans la Recherche de diagnostic.
  * **Nom**: un identificateur, généralement l'URL de la requête HTTP.
  * **SyntheticSource**: si elle est non nulle ou vide, cette chaîne indique que la source de la requête a été identifiée en tant que robot ou test web. Par défaut, elle est exclue des calculs dans Metrics Explorer.
* **Propriétés** : ce sont les propriétés qui sont envoyées avec toutes les données de télémétrie. Elles peuvent être remplacées dans les appels Track* individuels.
* **Session** : la session utilisateur. L'ID est définie sur une valeur générée qui est modifiée lorsque l'utilisateur n'a pas été actif pendant un certain temps.
* **Utilisateur** : Informations utilisateur.

## <a name="limits"></a>limites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Pour éviter d'atteindre la limite de débit de données, utilisez [l’échantillonnage](app-insights-sampling.md).

Pour déterminer la durée de conservation des données, consultez [Rétention des données et confidentialité](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Documents de référence
* [Référence ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Référence Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Référence JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Kit de développement logiciel Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Kit de développement logiciel (SDK) iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Code de Kit de développement logiciel (SDK)
* [Kit de développement logiciel (SDK) principal ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Packages Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Kit SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Kit de développement logiciel (SDK) Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [Kit de développement logiciel (SDK) JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Toutes les plateformes](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Questions
* *Quelles exceptions peuvent être lancées par les appels Track_() ?*

    Aucune. Vous n’avez pas besoin de les inclure dans des clauses try-catch. Si le Kit de développement logiciel (SDK) rencontre des problèmes, il enregistrera des messages dans la sortie de la console de débogage et, si les messages aboutissent, dans la recherche de diagnostic.
* *Existe-t-il une API REST pour obtenir des données à partir du portail ?*

    Oui, [l’API d’accès aux données](https://dev.applicationinsights.io/). Les autres méthodes d’extraction des données sont [l’exportation d’Analytics vers Power BI](app-insights-export-power-bi.md) et [l’exportation continue](app-insights-export-telemetry.md).

## <a name="next"></a>Étapes suivantes
* [Recherche d’événements et de journaux](app-insights-diagnostic-search.md)

* [Résolution des problèmes](app-insights-troubleshoot-faq.md)


