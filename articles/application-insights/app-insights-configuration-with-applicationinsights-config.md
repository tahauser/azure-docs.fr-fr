---
title: Référence sur ApplicationInsights.config - Azure | Microsoft Docs
description: Activez ou désactivez les modules de collecte de données et ajoutez des compteurs de performances et d’autres paramètres.
services: application-insights
documentationcenter: ''
author: OlegAnaniev-MSFT
editor: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: a35da5c84e4e79d7bc6f2167ec7e172970992612
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configuration du kit de développement logiciel (SDK) Application Insights à l’aide du fichier ApplicationInsights.config ou .xml
Le kit de développement logiciel (SDK) .NET Application Insights se compose d’un certain nombre de packages NuGet. Le [package principal](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fournit l'API pour l'envoi des données télémétriques à Application Insights. Des [packages supplémentaires](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fournissent les *modules* et les *initialiseurs* de télémétrie pour le suivi télémétrique automatique de votre application et de son contexte. La modification du fichier de configuration permet d’activer ou de désactiver les modules et initialiseurs de télémétrie, et de définir les paramètres pour certains d’entre eux.

Le fichier de configuration est nommé `ApplicationInsights.config` ou `ApplicationInsights.xml`, selon le type de votre application. Il est automatiquement ajouté à votre projet lorsque vous [installez la plupart des versions du Kit de développement logiciel (SDK)][start]. Il est également ajouté à une application web par [Status Monitor sur un serveur IIS][redfield] ou lorsque vous sélectionnez [l’extension Appplication Insights pour un site web ou une machine virtuelle Azure](app-insights-azure-web-apps.md).

Il n’existe aucun fichier équivalent permettant de contrôler le [kit de développement logiciel (SDK) dans une page web][client].

Ce document décrit les sections du fichier de configuration, la façon dont ils contrôlent les composants du Kit de développement logiciel (SDK) et les packages NuGet qui chargent ces composants.

> [!NOTE]
> Les instructions ApplicationInsights.config et ApplicationInsights.xml ne s’appliquent pas au kit SDK .NET Core. Pour apporter des modifications à une application .NET Core, nous utilisons généralement le fichier appsettings.json. Vous trouverez un exemple dans la [documentation du Débogueur de capture instantanée](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications).

## <a name="telemetry-modules-aspnet"></a>Modules de télémétrie (ASP.NET)
Chaque module de télémétrie collecte un type de données précis et utilise l'API de base pour envoyer les données. Les modules sont installés par différents packages NuGet, qui ajoutent également les lignes requises dans le fichier .config.

Il existe un nœud dans le fichier de configuration pour chaque module. Pour désactiver un module, supprimez le nœud ou commentez-le.

### <a name="dependency-tracking"></a>Suivi des dépendances
[Dependency tracking](app-insights-asp-net-dependencies.md) collecte la télémétrie des appels de votre application aux bases de données et aux services et bases de données externes. Pour permettre à ce module de fonctionner dans un serveur IIS, vous devez [installer Status Monitor][redfield]. Pour l’utiliser dans des applications web ou des machines virtuelles Azure, [sélectionnez l’extension Application Insights](app-insights-azure-web-apps.md).

Vous pouvez également écrire votre propre code de suivi des dépendances à l'aide de l’ [API TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

### <a name="performance-collector"></a>Collecteur de performances
[Collecte les compteurs de performances système](app-insights-performance-counters.md), notamment l’UC, la mémoire et la charge réseau, à partir des installations IIS. Vous pouvez spécifier les compteurs à collecter, y compris les compteurs de performances que vous avez configurés vous-même.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .

### <a name="application-insights-diagnostics-telemetry"></a>Télémétrie des diagnostics Application Insights
Le `DiagnosticsTelemetryModule` répertorie les erreurs dans le code d'instrumentation Application Insights lui-même. Par exemple, si le code ne peut pas accéder aux compteurs de performances ou si un `ITelemetryInitializer` renvoie une exception. La télémétrie de trace suivie par ce module s’affiche dans [Diagnostic Search (Recherche de diagnostic)][diagnostic]. Envoie des données de diagnostic à dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Si vous installez simplement ce package, le fichier ApplicationInsights.config n'est pas créé automatiquement.

### <a name="developer-mode"></a>Mode développeur
`DeveloperModeWithDebuggerAttachedTelemetryModule` force Application Insights `TelemetryChannel` à envoyer des données immédiatement, un élément de télémétrie à la fois, lorsqu’un débogueur est associé au processus d'application. Cela réduit le délai entre le moment où votre application effectue le suivi de télémétrie et celui où les données apparaissent sur le portail Application Insights. Cela cause une surcharge importante au niveau de l'UC et de la bande passante réseau.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)

### <a name="web-request-tracking"></a>Suivi des requêtes web
Indique le [temps de réponse et le code résultant](app-insights-asp-net.md) des requêtes HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Suivi des exceptions
`ExceptionTrackingTelemetryModule` comptabilise le nombre d’exceptions non traitées dans votre application web. Consultez [Échecs et exceptions][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - suit [les exceptions de tâche non prises en charge](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - suit les exceptions non gérées pour les rôles de travail, les services Windows et les applications de console.
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) .

### <a name="eventsource-tracking"></a>Suivi EventSource
`EventSourceTelemetryModule` vous permet de configurer les événements EventSource à envoyer à Application Insights en tant que traces. Pour plus d’informations sur le suivi des événements EventSource, consultez [Utilisation d’événements EventSource](app-insights-asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Suivi des événements ETW
`EtwCollectorTelemetryModule` vous permet de configurer les événements provenant des fournisseurs ETW à envoyer à Application Insights en tant que traces. Pour plus d’informations sur le suivi des événements ETW, consultez [Utilisation d’événements ETW](app-insights-asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Le package Microsoft.ApplicationInsights fournit l’[API de base](https://msdn.microsoft.com/library/mt420197.aspx) du Kit de développement logiciel (SDK). Les autres modules de télémétrie l’utilisent. Vous pouvez également [l’utiliser pour définir votre propre télémétrie](app-insights-api-custom-events-metrics.md).

* Aucune entrée dans ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Si vous installez simplement ce package NuGet, aucun fichier .config n'est créé.

## <a name="telemetry-channel"></a>Canal de télémétrie
Le canal de télémétrie gère la mise en mémoire tampon et la transmission de la télémétrie au service Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` est le canal par défaut des services. Il met les données en mémoire.
* `Microsoft.ApplicationInsights.PersistenceChannel` est une alternative pour les applications de console. Il peut enregistrer les données non vidées dans un stockage permanent à la fermeture de votre application et les envoyer au redémarrage de l'application.

## <a name="telemetry-initializers-aspnet"></a>Initialiseurs de télémétrie (ASP.NET)
Les initialiseurs de télémétrie définissent les propriétés de contexte envoyées avec chaque élément de télémétrie.

Vous pouvez [écrire vos propres initialiseurs](app-insights-api-filtering-sampling.md#add-properties) pour définir les propriétés de contexte.

Les initialiseurs standard sont tous définis par les packages NuGet web ou WindowsServer :

* `AccountIdTelemetryInitializer` définit la propriété AccountId.
* `AuthenticatedUserIdTelemetryInitializer` définit la propriété AuthenticatedUserId déterminée par le Kit de développement logiciel (SDK) JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` met à jour les propriétés `RoleName` et `RoleInstance` du contexte `Device` pour tous les éléments de télémétrie avec des informations extraites de l'environnement d’exécution Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` met à jour la propriété `Version` du contexte `Component` pour tous les éléments de télémétrie avec la valeur extraite du fichier `BuildInfo.config` produit par MSBuild.
* `ClientIpHeaderTelemetryInitializer` met à jour la propriété `Ip` du contexte `Location` de tous les éléments de télémétrie à partir de l’en-tête HTTP `X-Forwarded-For` de la demande.
* `DeviceTelemetryInitializer` met à jour les propriétés suivantes du contexte `Device` pour tous les éléments de télémétrie.
  * `Type` est défini sur « PC »
  * `Id` est défini sur le nom de domaine de l'ordinateur sur lequel l'application web est exécutée.
  * `OemName` est défini sur la valeur extraite du champ `Win32_ComputerSystem.Manufacturer` à l'aide de WMI.
  * `Model` est défini sur la valeur extraite du champ `Win32_ComputerSystem.Model` à l'aide de WMI.
  * `NetworkType` est défini sur la valeur extraite de l’`NetworkInterface`.
  * `Language` est défini sur le nom de `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` met à jour la propriété `RoleInstance` du contexte `Device` pour tous les éléments de télémétrie avec le nom de domaine de l'ordinateur sur lequel l'application web est exécutée.
* `OperationNameTelemetryInitializer` met à jour la propriété `Name` de `RequestTelemetry` et la propriété `Name` du contexte `Operation` de tous les éléments de télémétrie à partir de la méthode HTTP, ainsi que les noms du contrôleur ASP.NET MVC et de l’action appelée pour traiter la demande.
* `OperationIdTelemetryInitializer` ou `OperationCorrelationTelemetryInitializer` met à jour la propriété de contexte `Operation.Id` de tous les éléments de télémétrie suivis lors du traitement d'une demande avec le `RequestTelemetry.Id` généré automatiquement.
* `SessionTelemetryInitializer` met à jour la propriété `Id` du contexte `Session` pour tous les éléments de télémétrie avec la valeur extraite du cookie `ai_session` généré par le code d’instrumentation JavaScript Application Insights en cours d'exécution dans le navigateur de l'utilisateur.
* `SyntheticTelemetryInitializer` ou `SyntheticUserAgentTelemetryInitializer` met à jour les propriétés de contexte `User`, `Session` et `Operation` de tous les éléments de télémétrie suivis lors du traitement d'une requête émanant d'une source synthétique, comme un test de disponibilité ou un robot de moteur de recherche. Par défaut, [Metrics Explorer](app-insights-metrics-explorer.md) n'affiche pas la télémétrie synthétique.

    Ensemble de `<Filters>` qui identifie les propriétés des requêtes.
* `UserTelemetryInitializer` met à jour les propriétés `Id` et `AcquisitionDate` du contexte `User` pour tous les éléments de télémétrie avec les valeurs extraites du cookie `ai_user` généré par le code d’instrumentation JavaScript Application Insights en cours d'exécution dans le navigateur de l'utilisateur.
* `WebTestTelemetryInitializer` définit l’ID utilisateur, l’ID de session et les propriétés de la source de synthèse pour des requêtes HTTP provenant des [tests de disponibilité](app-insights-monitor-web-app-availability.md).
  Ensemble de `<Filters>` qui identifie les propriétés des requêtes.

Pour les applications .NET en cours d’exécution dans Service Fabric, vous pouvez inclure le package NuGet `Microsoft.ApplicationInsights.ServiceFabric`. Ce package comprend un `FabricTelemetryInitializer`, qui ajoute des propriétés Service Fabric pour les éléments de télémétrie. Pour plus d’informations, consultez la [page GitHub](https://go.microsoft.com/fwlink/?linkid=848457) sur les propriétés ajoutées par ce package NuGet.

## <a name="telemetry-processors-aspnet"></a>Processeurs de télémétrie (ASP.NET)
Les processeurs de télémétrie peuvent filtrer et modifier chaque élément de télémétrie avant son envoi au portail à partir du Kit de développement logiciel (SDK).

Vous pouvez [écrire vos propres processeurs de télémétrie](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processeur de télémétrie d'échantillonnage adaptatif (à partir de 2.0.0-beta3)
Cette option est activée par défaut. Si votre application envoie beaucoup de télémétrie, ce processeur en supprime une partie.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Le paramètre fournit la cible que l'algorithme essaie d'atteindre. Chaque instance du Kit de développement logiciel (SDK) fonctionne de manière indépendante. Ainsi, si votre serveur est un cluster de plusieurs ordinateurs, le volume réel des données de télémétrie sera multiplié en conséquence.

[En savoir plus sur l'échantillonnage](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processeur de télémétrie d'échantillonnage à taux fixe (à partir de 2.0.0-beta1)
Il existe également un [processeur de télémétrie d’échantillonnage](app-insights-api-filtering-sampling.md) standard (à partir de 2.0.1) :

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Paramètres de canal (Java)
Ces paramètres ont un effet sur la manière dont le kit de développement logiciel (SDK) doit stocker et vider les données de télémétrie qu’il collecte.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Nombre d’éléments de télémétrie qui peuvent être stockés dans la mémoire du kit de développement logiciel (SDK). Une fois ce nombre atteint, la mémoire tampon de télémétrie est vidée : les éléments de télémétrie sont envoyés au serveur Application Insights.

* Min : 1
* Max : 1000
* Valeur par défaut : 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Détermine la fréquence à laquelle les données stockées en mémoire doivent être vidées (envoyées à Application Insights).

* Min : 1
* Max : 300
* Valeur par défaut : 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Détermine la taille maximale en Mo allouée au stockage persistant sur le disque local. Ce stockage est utilisé pour les éléments de télémétrie persistants qui n’ont pas pu être transmis au point de terminaison Application Insights. Une fois la capacité de stockage atteinte, les nouveaux éléments de télémétrie sont ignorés.

* Min : 1
* Max : 100
* Valeur par défaut : 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey
Ce paramètre détermine la ressource Application Insights dans laquelle vos données s’affichent. En général, vous créez une ressource séparée, avec une clé distincte, pour chacune de vos applications.

Si vous souhaitez définir la clé de manière dynamique, par exemple si vous souhaitez transmettre des résultats de votre application vers différentes ressources, vous pouvez omettre la clé du fichier de configuration, et la définir plutôt dans le code.

Définissez la clé dans TelemetryConfiguration.Active pour toutes les instances de TelemetryClient, y compris les modules standard de télémétrie. Définissez-la dans une méthode d’initialisation, telle que global.aspx.cs dans un service ASP.NET :

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Si vous souhaitez simplement envoyer un ensemble spécifique d’événements à une autre ressource, vous pouvez définir la clé pour un TelemetryClient spécifique :

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Pour obtenir une nouvelle clé, [créez une ressource dans le portail Application Insights][new].

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur l’API][api].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
