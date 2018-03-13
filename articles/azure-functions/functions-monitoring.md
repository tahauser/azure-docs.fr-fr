---
title: "Surveiller l’exécution des fonctions Azure"
description: "Découvrez comment utiliser Azure Application Insights avec Azure Functions pour surveiller l’exécution des fonctions."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 20b12da7dedb9c5ac76a09785b68fb384789c2d7
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="monitor-azure-functions"></a>Surveiller l’exécution des fonctions Azure

## <a name="overview"></a>Vue d'ensemble 

[Azure Functions](functions-overview.md) est intégré à [Azure Application Insights](../application-insights/app-insights-overview.md) pour la surveillance des fonctions. Cet article explique comment configurer Azure Functions pour envoyer des données de télémétrie à Application Insights.

![Application Insights Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Azure Functions dispose également de fonctionnalités de surveillance qui n’utilisent pas Application Insights. Nous recommandons Application Insights, qui offre plus de données et de meilleurs moyens de les analyser. Pour plus d’informations sur la surveillance intégrée, consultez la [dernière section de cet article](#monitoring-without-application-insights).

## <a name="enable-application-insights-integration"></a>Activer l’intégration à Application Insights

Pour qu’une application de fonction envoie des données à Application Insights, elle a besoin de connaître la clé d’instrumentation d’une instance d’Application Insights. Il existe deux façons d’effectuer cette connexion dans le [portail Azure](https://portal.azure.com) :

* [Créer une instance d’Application Insights connectée quand vous créez l’application de la fonction](#new-function-app).
* [Connecter une instance d’Application Insights à une application de fonction existante](#existing-function-app).

### <a name="new-function-app"></a>Nouvelle application de fonction

Activer Application Insights dans la page Function App **Créer** :

1. Définissez le commutateur **Application Insights** sur **Activé**.

2. Sélectionnez un **Emplacement d’Application Insights**.

   ![Activer Application Insights pendant la création d’une application de fonction](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>Application de fonction existante

Obtenir la clé d’instrumentation et l’enregistrer dans une application de fonction :

1. Créez l’instance d’Application Insights. Définissez le type d’application sur **Général**.

   ![Créer une instance d’Application Insights, type Général](media/functions-monitoring/ai-general.png)

2. Copiez la clé d’instrumentation à partir de la page **Bases** de l’instance d’Application Insights. Placez le curseur au-dessus de la fin de la valeur de clé affichée pour faire apparaître un bouton **Cliquer pour copier**.

   ![Copier la clé d’instrumentation Application Insights](media/functions-monitoring/copy-ai-key.png)

1. Dans la page **Paramètres d’application** de l’application de fonction, [ajoutez un paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings) en cliquant sur **Ajouter un nouveau paramètre**. Nommez le nouveau paramètre APPINSIGHTS_INSTRUMENTATIONKEY et collez la clé d’instrumentation copiée.

   ![Ajouter la clé d’instrumentation aux paramètres de l’application](media/functions-monitoring/add-ai-key.png)

1. Cliquez sur **Enregistrer**.

## <a name="disable-built-in-logging"></a>Désactiver la journalisation intégrée

Si vous activez Application Insights, nous vous conseillons de désactiver la [journalisation intégrée qui utilise le stockage Azure](#logging-to-storage). La journalisation intégrée permet de tester des charges de travail légères, mais n’est pas destinée à une utilisation en production avec des charges importantes. Application Insights est recommandé pour surveiller la production. Si la journalisation intégrée est utilisée en production, l’enregistrement de journal peut être incomplet en raison d’une limitation du stockage Azure.

Pour désactiver la journalisation intégrée, supprimez le paramètre d’application `AzureWebJobsDashboard`. Pour plus d’informations sur la suppression de paramètres d’application dans le portail Azure, consultez la section **Paramètres de l’application** dans [Comment gérer une application de fonction dans le portail Azure](functions-how-to-use-azure-function-app-settings.md#settings).

Lorsque vous activez Application Insights et désactivez la journalisation intégrée, l’onglet **Surveiller** d’une fonction dans le portail Azure vous amène à Application Insights.

## <a name="view-telemetry-data"></a>Afficher les données de télémétrie

Pour accéder à l’instance Application Insights connectée à partir d’une application de fonction dans le portail, sélectionnez le lien **Application Insights** dans la page **Vue d’ensemble** de l’application de fonction.

Pour plus d’informations sur l’utilisation d’Application Insights, consultez la [documentation d’Application Insights](https://docs.microsoft.com/azure/application-insights/). Cette section présente des exemples montrant comment afficher les données dans Application Insights. Si vous êtes déjà familiarisé avec Application Insights, vous pouvez passer directement aux [sections sur la configuration et la personnalisation des données de télémétrie](#configure-categories-and-log-levels).

Dans [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md), vous pouvez créer des graphiques et les alertes basés sur des métriques telles que le nombre d’appels de fonction, l’heure d’exécution et les taux de réussite.

![Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Sous l’onglet [Échecs](../application-insights/app-insights-asp-net-exceptions.md), vous pouvez créer des graphiques et des alertes basées sur les échecs de fonction et les exceptions de serveur. Le **Nom de l’opération** est le nom de la fonction. Les échecs de dépendances ne sont pas affichés, sauf si vous implémentez des [données de télémétrie personnalisées](#custom-telemetry-in-c-functions) pour les dépendances.

![Échecs](media/functions-monitoring/failures.png)

Sous l’onglet [Performances](../application-insights/app-insights-performance-counters.md), vous pouvez analyser les problèmes de performances.

![Performances](media/functions-monitoring/performance.png)

L’onglet **Serveurs** affiche l’utilisation des ressources et le débit par serveur. Ces données peuvent être utiles pour déboguer les scénarios où les fonctions ralentissent vos ressources sous-jacentes. Les serveurs sont appelés **instances de rôle cloud**.

![Serveurs](media/functions-monitoring/servers.png)

L’onglet [Flux de métriques temps réel](../application-insights/app-insights-live-stream.md) affiche les données des métriques en temps réel à mesure qu’elles sont créées.

![Flux temps réel](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Interroger les données de télémétrie

[Application Insights Analytics](../application-insights/app-insights-analytics.md) vous donne accès à toutes les données de télémétrie sous la forme de tables de base de données. Analytics fournit un langage de requête pour l’extraction, la manipulation et la visualisation des données.

![Sélectionner Analytics](media/functions-monitoring/select-analytics.png)

![Exemple Analytics](media/functions-monitoring/analytics-traces.png)

Voici un exemple de requête. Il montre la distribution des demandes par rôle de travail au cours des 30 dernières minutes.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Les tables disponibles sont affichées sous l’onglet **Schéma** du volet gauche. Les données générées par les appels de fonction sont disponibles dans les tables suivantes :

* **traces** : journaux créés par le runtime et par le code de fonction.
* **requests** : une demande par appel de fonction.
* **exceptions** : toutes les exceptions levées par le runtime.
* **customMetrics** : nombre d’appels ayant réussi ou échoué, taux de réussite, durée.
* **customEvents** : événements suivis par le runtime, par exemple : requêtes HTTP qui déclenchent une fonction.
* **performanceCounters** : informations sur le niveau de performance des serveurs sur lesquels les fonctions sont en cours d’exécution.

Les autres tables concernent les tests de disponibilité et les données de télémétrie client/navigateur. Vous pouvez implémenter une télémétrie personnalisée en vue d’y ajouter des données.

Dans chaque table, un champ `customDimensions` contient certaines des données spécifiques à Azure Functions.  Par exemple, la requête suivante récupère toutes les traces dont le niveau de journal est `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Le runtime fournit `customDimensions.LogLevel` et `customDimensions.Category`. Vous pouvez fournir des champs supplémentaires dans les journaux que vous écrivez dans votre code de fonction. Consultez [Journalisation structurée](#structured-logging) plus loin dans cet article.

## <a name="configure-categories-and-log-levels"></a>Configurer les catégories et les niveaux de journal

Vous pouvez utiliser Application Insights sans en personnaliser la configuration, mais la configuration par défaut risque d’engendrer de grands volumes de données. Si vous utilisez un abonnement Azure Visual Studio, vous risquez d’atteindre votre plafond de données pour Application Insights. Le reste de cet article indique comment configurer et personnaliser les données que vos fonctions envoient à Application Insights.

### <a name="categories"></a>Catégories

L’enregistreur d’événements d’Azure Functions inclut une *catégorie* par journal. La catégorie indique quelle partie du code d’exécution ou de votre code de fonction a écrit le journal. 

Les journaux créés par le runtime d’Azure Functions ont une catégorie qui commence par « Host ». Par exemple, les journaux « function started », « function executed », et « function completed » ont la catégorie « Host.Executor ». 

Si vous écrivez des journaux dans votre code de fonction, leur catégorie est « Function ».

### <a name="log-levels"></a>Niveaux de journal

L’enregistreur d’événements d’Azure Functions inclut également un *niveau de journal* avec chaque journal. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) est une énumération, et le code d’entier indique l’importance relative :

|LogLevel    |Code|
|------------|---|
|Trace       | 0 |
|Déboguer       | 1 |
|Information | 2 |
|Avertissement     | 3 |
|Error       | 4 |
|Critique    | 5. |
|Aucun        | 6. |

Le niveau de journal `None` est expliqué dans la section suivante. 

### <a name="configure-logging-in-hostjson"></a>Configurer la journalisation dans host.json

Le fichier *host.json* configure la quantité de journalisation qu’une application de fonction envoie à Application Insights. Pour chaque catégorie, vous indiquez le niveau de journal minimal à envoyer. Voici un exemple :

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Cet exemple montre comment configurer les règles suivantes :

1. Pour les journaux ayant la catégorie « Host.Results » ou « Function », envoyer uniquement les niveaux `Error` et supérieurs à Application Insights. Les journaux pour les niveaux `Warning` et inférieurs sont ignorés.
2. Pour les journaux ayant la catégorie Host. Aggregator, envoyer uniquement les niveaux `Information` et supérieurs à Application Insights. Les journaux pour les niveaux `Debug` et inférieurs sont ignorés.
3. Pour tous les autres journaux, envoyer uniquement les niveaux `Information` et supérieurs à Application Insights.

La valeur de catégorie dans *host.json* contrôle la journalisation de toutes les catégories qui commencent par la même valeur. Par exemple, « Host » dans *host.json* contrôle la journalisation pour « Host.General », « Host.Executor », « Host.Results » et ainsi de suite.

Si *host.json* inclut plusieurs catégories qui commencent par la même chaîne, les plus longues sont traitées en priorité. Par exemple, supposons que vous souhaitez que la totalité du runtime à l’exception de « Host.Aggregator » soit journalisée au niveau `Error` et que « Host.Aggregator » soit journalisé au niveau `Information` :

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Pour supprimer tous les journaux relatifs à une catégorie, vous pouvez utiliser le niveau de journal `None`. Aucun journal n’est écrit avec cette catégorie, et il n’existe aucun niveau de journal au-dessus de celle-ci.

Les sections suivantes décrivent les principales catégories de journaux générées par le runtime. 

### <a name="category-hostresults"></a>Catégorie Host.Results

Ces journaux apparaissent sous la dénomination « requests » dans Application Insights. Ils indiquent la réussite ou l’échec d’une fonction.

![Graphique de demandes](media/functions-monitoring/requests-chart.png)

Tous ces journaux étant écrits au niveau `Information`, si vous filtrez sur le niveau `Warning` ou supérieur, vous ne pouvez pas visualiser ces données.

### <a name="category-hostaggregator"></a>Catégorie Host.Aggregator

Ces journaux fournissent des nombres et des moyennes d’appels de fonction sur une période de temps [configurable](#configure-the-aggregator). La période par défaut est 30 secondes ou 1 000 résultats, selon la première de ces éventualités. 

Les journaux sont disponibles dans la table **customMetrics** dans Application Insights. Le nombre d’exécutions, le taux de réussite et la durée en sont des exemples.

![Requête portant sur customMetrics](media/functions-monitoring/custom-metrics-query.png)

Tous ces journaux étant écrits au niveau `Information`, si vous filtrez sur le niveau `Warning` ou supérieur, vous ne pouvez pas visualiser ces données.

### <a name="other-categories"></a>Autres catégories

Tous les journaux des catégories autres que celles déjà mentionnées apparaissent dans la table **traces** dans Application Insights.

![Requête portant sur traces](media/functions-monitoring/analytics-traces.png)

Tous les journaux ayant des catégories commençant par « Host » sont écrits par le runtime d’Azure Functions. Les journaux « Function started » et « Function completed » ont la catégorie « Host.Executor ». Pour les exécutions ayant réussi, ces journaux sont de niveau `Information` ; les exceptions sont journalisées au niveau `Error`. En outre, le runtime crée des journaux de niveau `Warning`, par exemple, les messages de file d’attente envoyés à la file d’attente de messages incohérents.

Les journaux écrits par votre code de fonction ont la catégorie « Function » et peuvent avoir n’importe quel niveau de journal.

## <a name="configure-the-aggregator"></a>Configurer le paramètre d’agrégation

Comme indiqué dans la section précédente, le runtime agrège les données sur les exécutions de fonction sur une période de temps. La période par défaut est 30 secondes ou 1 000 exécutions, selon la première de ces éventualités. Vous pouvez configurer ce paramètre dans le fichier *host.json*.  Voici un exemple :

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurer l’échantillonnage

Application Insights a une fonctionnalité [d’échantillonnage](../application-insights/app-insights-sampling.md) qui peut vous éviter de produire une quantité excessive de données de télémétrie aux heures de forte activité. Quand le nombre d’éléments de télémétrie dépasse un taux spécifié, Application Insights commence à ignorer aléatoirement certains des éléments entrants. Vous pouvez configurer l’échantillonnage dans *host.json*.  Voici un exemple :

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>Écrire des journaux dans des fonctions C#

Vous pouvez écrire des journaux dans votre code de fonction qui apparaissent sous forme de traces dans Application Insights.

### <a name="ilogger"></a>ILogger

Utilisez un paramètre [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) dans vos fonctions au lieu d’un paramètre `TraceWriter`. Les journaux créés à l’aide de `TraceWriter` sont certes destinés à Application Insights, mais `ILogger` vous permet d’effectuer une [journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Avec un objet `ILogger`, vous appelez des [méthodes d’extension sur ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) `Log<level>` pour créer des journaux. Par exemple, le code suivant écrit des journaux `Information` ayant la catégorie « Function ».

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Journalisation structurée

C’est l’ordre des espaces réservés, pas leurs noms, qui détermine les paramètres utilisés dans le message du journal. Par exemple, supposons que vous avez le code suivant :

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Si vous conservez la même chaîne de message et que vous inversez l’ordre des paramètres, les valeurs dans le texte du message résultant ne sont pas situées à la bonne place.

Ce mode de gestion des espaces réservés vous permet d’effectuer une journalisation structurée. Application Insights stocke les paires nom de paramètre/valeur en plus de la chaîne du message. Ainsi, les arguments du message deviennent des champs pouvant faire l’objet de requêtes.

Par exemple, si votre appel de méthode de l’enregistreur d’événements ressemble à l’exemple précédent, vous pouvez interroger le champ `customDimensions.prop__rowKey`. L’ajout du préfixe `prop__` évite toute collision entre les champs ajoutés par le runtime et les champs ajoutés par votre fonction de code.

Vous pouvez également exécuter une requête portant sur la chaîne de message d’origine en référençant le champ `customDimensions.prop__{OriginalFormat}`.  

Voici un exemple de représentation JSON des données `customDimensions` :

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>Journalisation des métriques personnalisées  

Dans les fonctions de script C#, vous pouvez utiliser la méthode d’extension `LogMetric` sur `ILogger` pour créer des métriques personnalisées dans Application Insights. Voici un exemple d’appel de méthode :

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Ce code est une alternative à l’appel de `TrackMetric` à l’aide de [l’API Application Insights pour .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Écrire des journaux dans les fonctions JavaScript

Dans les fonctions Node.js, utilisez `context.log` pour écrire des journaux. La journalisation structurée n’est pas activée.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Journalisation des métriques personnalisées  

Dans les fonctions Node.js, vous pouvez utiliser la méthode `context.log.metric` pour créer des métriques personnalisées dans Application Insights. Voici un exemple d’appel de méthode :

```javascript
context.log.metric("TestMetric", 1234); 
```

Ce code est une alternative à l’appel de `trackMetric` à l’aide du [SDK Node.js pour Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Données de télémétrie personnalisées dans les fonctions C#

Vous pouvez utiliser le package NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) pour envoyer des données de télémétrie personnalisées à Application Insights.

Voici un exemple de code C# qui utilise [l’API de télémétrie personnalisée](../application-insights/app-insights-api-custom-events-metrics.md). L’exemple concerne une bibliothèque de classes .NET, mais le code Application Insights est le même pour le script C#.

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetry = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, userName);
            telemetry.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, userName);
            telemetry.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, userName);
            telemetry.TrackDependency(dependency);
            
            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
        
        // This correllates all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

N’appelez pas `TrackRequest` ou `StartOperation<RequestTelemetry>`, afin d’éviter les doublons de demandes pour un appel de fonction.  Le runtime d’Azure Functions effectue automatiquement le suivi des demandes.

Définissez `telemetry.Context.Operation.Id` sur l’identificateur d’invocation à chaque démarrage de votre fonction. Ainsi, vous pouvez mettre en corrélation tous les éléments de télémétrie pour un appel de fonction donné.

```cs
telemetry.Context.Operation.Id = context.InvocationId.ToString();
```

## <a name="custom-telemetry-in-javascript-functions"></a>Données de télémétrie personnalisées dans les fonctions JavaScript

Le [SDK Node.js pour Application Insights](https://www.npmjs.com/package/applicationinsights) est en version bêta. Voici un exemple de code qui envoie des données de télémétrie personnalisées à Application Insights :

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

Le paramètre `tagOverrides` définit `operation_Id` sur l’identificateur d’invocation de la fonction. Ce paramètre vous permet de mettre en corrélation toutes les données de télémétrie générées automatiquement et personnalisées pour un appel de fonction donné.

## <a name="known-issues"></a>Problèmes connus

### <a name="dependencies"></a>Les dépendances

Les dépendances de la fonction vis-à-vis d’autres services n’apparaissent pas automatiquement, mais vous pouvez écrire du code personnalisé pour les afficher. L’exemple de code dans la [section relative aux données de télémétrie personnalisées C#](#custom-telemetry-in-c-functions) montre comment faire. L’exemple de code entraîne un *mappage d’application* dans Application Insights, qui ressemble à ceci :

![Mise en correspondance d’applications](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Signaler des problèmes

Pour signaler un problème avec l’intégration d’Application Insights dans Azure Functions, ou pour faire une suggestion ou une demande, [créez un problème dans GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Surveillance sans Application Insights

Nous recommandons Application Insights pour surveiller les fonctions, car il offre plus de données et de meilleurs moyens de les analyser. Toutefois, vous pouvez également trouver des données de télémétrie et de journalisation dans les pages du portail Azure pour une application de fonction.

### <a name="logging-to-storage"></a>Journalisation dans le stockage

La journalisation intégrée utilise le compte de stockage spécifié par la chaîne de connexion dans le paramètre d’application `AzureWebJobsDashboard`. Si ce paramètre d’application est configuré, vous pouvez voir les données de journalisation dans le portail Azure. Dans la page d’une application de fonction, sélectionnez une fonction, puis l’onglet **Surveiller** pour obtenir la liste des exécutions de la fonction. Sélectionnez une exécution de fonction pour consulter la durée, les données d’entrée, les erreurs et les fichiers journaux associés.

Si vous utilisez Application Insights et que vous avez désactivé la [journalisation intégrée](#disable-built-in-logging), l’onglet **Surveiller** vous renvoie à Application Insights.

### <a name="real-time-monitoring"></a>Surveillance en temps réel

Vous pouvez diffuser des fichiers journaux à une session de ligne de commande sur une station de travail locale, à l’aide de l’[interface de ligne de commande Azure (CLI) 2.0](/cli/azure/install-azure-cli) ou d’[Azure PowerShell](/powershell/azure/overview).  

Dans l’interface de ligne de commande Azure CLI 2.0, utilisez les commandes suivantes pour vous connecter, choisir votre abonnement et diffuser les fichiers journaux :

```
az login
az account list
az account set <subscriptionNameOrId>
az appservice web log tail --resource-group <resource group name> --name <function app name>
```

Dans Azure PowerShell, utilisez les commandes suivantes pour ajouter votre compte Azure, choisir votre abonnement et diffuser les fichiers journaux :

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Pour plus d’informations, consultez [Diffusion en continu des journaux](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Consultation locale des fichiers journaux

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Application Insights](/azure/application-insights/)
* [Journalisation ASP.NET Core](/aspnet/core/fundamentals/logging/)
