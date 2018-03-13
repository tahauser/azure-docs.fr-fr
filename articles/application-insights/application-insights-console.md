---
title: Azure Application Insights pour les applications console | Microsoft Docs
description: "Surveiller la disponibilité, les performances et l’utilisation.des applications Web."
services: application-insights
documentationcenter: .net
author: lmolkova
manager: bfung
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 57f5670eec36ff2c4332da592dd2a3eef73fdefc
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights pour les applications console .NET
[Application Insights](app-insights-overview.md) vous permet de surveiller la disponibilité, les performances et l’utilisation de votre application web.

Vous avez besoin d’un abonnement à [Microsoft Azure](http://azure.com). Connectez-vous avec un compte Microsoft, que vous pouvez avoir pour Windows, Xbox Live ou d’autres services cloud de Microsoft. Si votre équipe dispose d’un abonnement d’organisation, demandez à son propriétaire d’y ajouter votre compte Microsoft.

## <a name="getting-started"></a>Prise en main

* Dans le [portail Azure](https://portal.azure.com), [créez une ressource Application Insights](app-insights-create-new-resource.md). Choisissez ASP.NET comme type d’application.
* Copiez la clé d'instrumentation. Recherchez la clé dans la liste déroulante Essentials de la nouvelle ressource que vous avez créée. 
* Installez le package [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) le plus récent.
* Définissez la clé d’instrumentation dans votre code avant d’effectuer le suivi de la télémétrie (ou définissez la variable d’environnement APPINSIGHTS_INSTRUMENTATIONKEY). Après cela, vous devriez pouvoir suivre manuellement les données de télémétrie et les visualiser dans le portail Azure.

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Installez la version la plus récente du package [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) : il effectue automatiquement le suivi des appels SQL, HTTP ou autres appels de dépendance externe.

Vous pouvez initialiser et configurer Application Insights à partir du code ou à l’aide du fichier `ApplicationInsights.config`. Vérifiez que l’initialisation se produit dès que possible.

### <a name="using-config-file"></a>Utilisation du fichier de configuration

Par défaut, le SDK Application Insights recherche le fichier `ApplicationInsights.config` dans le répertoire de travail lors de la création de `TelemetryConfiguration`.

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Read ApplicationInsights.config file if present
```

Vous pouvez également spécifier le chemin du fichier de configuration.

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

Pour plus d’informations, consultez la [référence du fichier de configuration](app-insights-configuration-with-applicationinsights-config.md).

Vous pouvez obtenir un exemple complet du fichier de configuration en installant la version la plus récente du package [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer). Voici la configuration **minimale** pour la collecte de dépendances qui est équivalente à l’exemple de code.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Configuration de la collecte de données de télémétrie à partir du code

* Lors du démarrage de l’application, créez et configurez une instance de `DependencyTrackingTelemetryModule` : il doit s’agir d’un singleton, et elle doit être conservée pendant toute la durée de vie de l’application.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Ajouter des initialiseurs de télémétrie communs

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* Pour une application .NET Framework Windows, vous pouvez également installer et initialiser le module collecteur Compteur de performances comme décrit [ici](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/).

#### <a name="full-example"></a>Exemple complet

```csharp
static void Main(string[] args)
{
    TelemetryConfiguration configuration = TelemetryConfiguration.Active;

    configuration.InstrumentationKey = "removed";
    configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
    configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

    var telemetryClient = new TelemetryClient();
    using (IntitializeDependencyTracking(configuration))
    {
        telemetryClient.TrackTrace("Hello World!");

        using (var httpClient = new HttpClient())
        {
            // Http dependency is automatically tracked!
            httpClient.GetAsync("https://microsoft.com").Wait();
        }
    }

    // run app...

    // when application stops or you are done with dependency tracking, do not forget to dispose the module
    dependencyTrackingModule.Dispose();

    telemetryClient.Flush();
}

static DependencyTrackingTelemetryModule IntitializeDependencyTracking(TelemetryConfiguration configuration)
{
    // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");    
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

    // enable known dependency tracking, note that in future versions, we will extend this list. 
    // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

    // initialize the module
    module.Initialize(configuration);

    return module;
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Surveillez les dépendances](app-insights-asp-net-dependencies.md) pour voir si des ressources REST, SQL ou d’autres ressources externes vous ralentissent.
* [Utilisez l’API](app-insights-api-custom-events-metrics.md) pour envoyer vos propres événements et mesures pour obtenir une vue plus détaillée des performances et de l’utilisation de votre application.
