---
title: "Filtrage et prétraitement dans le Kit de développement logiciel (SDK) Azure Application Insights | Microsoft Docs"
description: "Écrivez des processeurs de télémétrie et des initialiseurs de télémétrie que le Kit de développement logiciel (SDK) doit filtrer ou ajoutez des propriétés aux données avant que la télémétrie ne soit envoyée au portail Application Insights."
services: application-insights
documentationcenter: 
author: beckylino
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: borooji;mbullwin
ms.openlocfilehash: 0ed2dbd83b36deacb0f6269dba6f18dc92980fff
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrage et pré-traitement de la télémétrie dans le Kit de développement logiciel (SDK) Application Insights


Vous pouvez écrire et configurer des plug-ins pour le Kit de développement logiciel (SDK) Application Insights afin de personnaliser la capture et le traitement des données de télémétrie avant leur envoi au service Application Insights.

* [échantillonnage](app-insights-sampling.md) réduit le volume des données de télémétrie sans affecter les statistiques. Il maintient ensemble les points de données liés de sorte que vous pouvez naviguer entre eux pour diagnostiquer un problème. Dans le portail, les nombres totaux sont multipliés pour compenser l'échantillonnage.
* Le filtrage avec des processeurs de télémétrie [pour ASP.NET](#filtering) ou [Java](app-insights-java-filter-telemetry.md) vous permet de sélectionner ou de modifier la télémétrie dans le Kit de développement logiciel (SDK) avant l’envoi au serveur. Par exemple, vous pouvez réduire le volume de la télémétrie en excluant les demandes émanant de robots. Mais le filtrage est une approche plus simple que l’échantillonnage pour réduire le trafic. Cela vous permet de mieux contrôler ce qui est transmis, mais vous devez être conscient que cela affecte vos statistiques ; par exemple, si vous filtrez toutes les demandes réussies.
* [Les initialiseurs de télémétrie ajoutent des propriétés](#add-properties) à n’importe quelle télémétrie envoyée à partir de votre application, notamment les données de télémétrie fournies par les modules standard. Par exemple, vous pouvez ajouter des valeurs calculées ou des numéros de version permettant de filtrer les données dans le portail.
* [L'API SDK](app-insights-api-custom-events-metrics.md) est utilisée pour envoyer des événements et des mesures personnalisés.

Avant de commencer :

* Installez le [SDK Application Insights pour ASP.NET](app-insights-asp-net.md) ou le [SDK pour Java](app-insights-java-get-started.md) dans votre application.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrage : ITelemetryProcessor
Cette technique vous offre un contrôle plus direct sur ce qui est inclus ou exclu du flux de télémétrie. Vous pouvez l'utiliser conjointement avec l'échantillonnage, ou séparément.

Pour filtrer la télémétrie, vous écrivez un processeur de télémétrie et l'enregistrez avec le Kit de développement logiciel (SDK). Toute la télémétrie passe par votre processeur et vous pouvez choisir de la supprimer du flux ou d'ajouter des propriétés. Cela inclut les données de télémétrie fournies par les modules standard tels que le collecteur de demandes HTTP et le collecteur de dépendances, ainsi que les données de télémétrie que vous avez écrites vous-même. Vous pouvez, par exemple, exclure la télémétrie concernant les demandes émanant de robots ou les appels de dépendance réussis.

> [!WARNING]
> Filtrer la télémétrie envoyée depuis le Kit de développement logiciel (SDK) à l’aide de processeurs peut fausser les statistiques que vous voyez dans le portail et rendre difficile le suivi des éléments associés.
>
> Envisagez plutôt d'utiliser l' [échantillonnage](app-insights-sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Créer un processeur de télémétrie (C#)
1. Vérifiez que la version du SDK Application Insights dans votre projet est la version 2.0.0 ou ultérieure. Cliquez avec le bouton droit sur votre projet dans l'explorateur de solutions Visual Studio, puis sélectionnez Gérer les packages NuGet. Dans le Gestionnaire de package NuGet, cochez Microsoft.ApplicationInsights.Web.
2. Pour créer un filtre, déployez ITelemetryProcessor. Il s’agit d’un autre point d’extensibilité, à l’instar d’un module de télémétrie, d’un initialiseur de télémétrie et d’un canal de télémétrie.

    Notez que les processeurs de télémétrie construisent une chaîne de traitement. Lorsque vous instanciez un processeur de télémétrie, vous transmettez un lien au processeur suivant dans la chaîne. Lorsqu’un point de données de télémétrie est transmis à la méthode de traitement, il effectue son travail, puis appelle le processeur de télémétrie suivant dans la chaîne.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
1. Insérez-la dans ApplicationInsights.config :

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Il s’agit de la section dans laquelle vous initialisez un filtre d’échantillonnage.)

Vous pouvez transférer des valeurs de chaîne depuis le fichier .config en fournissant des propriétés publiques nommées dans votre classe.

> [!WARNING]
> Veillez à faire correspondre le nom de type et les noms de propriété dans le fichier .config aux noms de classe et de propriété dans le code. Si le fichier .config fait référence à un type ou à une propriété qui n'existe pas, le kit de développement peut échouer lors de l'envoi d'une télémétrie quelconque.
>
>

**également** initialiser le filtre dans le code. Dans une classe d’initialisation appropriée (par exemple, AppStart dans Global.asax.cs), insérez votre processeur dans la chaîne :

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Les clients de télémétrie créés après ce point utiliseront vos processeurs.

Le code suivant montre comment ajouter un initialiseur de télémétrie dans ASP.NET Core.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var initializer = new SuccessfulDependencyFilter();
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();
    configuration.TelemetryInitializers.Add(initializer);
}
```

### <a name="example-filters"></a>Exemples de filtres
#### <a name="synthetic-requests"></a>Demandes synthétiques
Excluez les robots et les tests web. Bien que Metrics Explorer vous donne la possibilité d’exclure les sources synthétiques, cette option réduit le trafic en les filtrant au niveau du Kit de développement (SDK).

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Échec d’authentification
Excluez les demandes avec une réponse de type « 401 ».

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Excluez les appels de dépendance à distance rapides.
Si vous souhaitez uniquement diagnostiquer les appels lents, excluez les appels rapides.

> [!NOTE]
> Cela faussera les statistiques que vous voyez dans le portail. Le graphique de dépendance s’affichera comme si les appels de dépendance avaient tous échoué.
>
>

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnostiquer les problèmes de dépendance
[Ce blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) décrit un projet pour diagnostiquer les problèmes de dépendance en envoyant automatiquement des requêtes ping régulières aux dépendances.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Ajouter des propriétés : ITelemetryInitializer
Utilisez des initialiseurs de télémétrie pour définir des propriétés globales qui sont envoyées avec toute la télémétrie ; et pour substituer le comportement sélectionné des modules standard de télémétrie.

Par exemple, le package Application Insights pour le Web collecte la télémétrie sur les requêtes HTTP. Il indique par défaut l’échec de toute requête à l’aide d’un code de réponse supérieur ou égal à 400. Toutefois, si 400 vous convient, vous pouvez fournir un initialiseur de télémétrie qui définit la propriété Success.

Si vous fournissez un initialiseur de télémétrie, celui-ci est appelé chaque fois qu'une des méthodes Track*() est appelée. Cela inclut les méthodes appelées par les modules de télémétrie standard. Par convention, ces modules ne définissent aucune propriété déjà définie par un initialiseur.

**Définir votre initialiseur**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK
       * behavior of treating response codes >= 400 as failed requests
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Charger votre initialiseur**

Dans ApplicationInsights.config :

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*également* instancier l'initialiseur dans le code, par exemple dans Global.aspx.cs :

```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Voir l’intégralité de cet exemple.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="javascript-telemetry-initializers"></a>Initialiseurs de télémétrie JavaScript
*JavaScript*

Insérer un initialiseur de télémétrie immédiatement après le code d’initialisation obtenu à partir du portail :

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Pour obtenir un résumé des propriétés non personnalisées disponibles dans le telemetryItem, consultez le [modèle d’exportation de données Application Insights](app-insights-export-data-model.md).

Vous pouvez ajouter autant d'initialiseurs que vous le souhaitez.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor et ITelemetryInitializer
Quelle est la différence entre les processeurs de télémétrie et les initialiseurs de télémétrie ?

* Leurs utilisations se recoupent partiellement : les deux peuvent être utilisés pour ajouter des propriétés à la télémétrie.
* Les TelemetryInitializers sont toujours exécutés avant les TelemetryProcessors.
* Les TelemetryProcessors permettent de remplacer ou supprimer complètement un élément de télémétrie.
* Les TelemetryProcessors ne traitent pas la télémétrie du compteur de performances.

## <a name="troubleshooting-applicationinsightsconfig"></a>Résolution des problèmes liés à ApplicationInsights.config
* Vérifiez que le nom de type complet et le nom de l’assembly sont corrects.
* Vérifiez que le fichier applicationinsights.config se trouve dans votre répertoire de sortie et qu’il contient éventuellement des modifications récentes.

## <a name="reference-docs"></a>Documents de référence
* [Présentation de l’API](app-insights-api-custom-events-metrics.md)
* [Référence ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Code du Kit de développement logiciel (SDK)
* [Kit de développement logiciel (SDK) principal ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Kit de développement logiciel (SDK) JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Étapes suivantes
* [Recherche d’événements et de journaux](app-insights-diagnostic-search.md)
* [Échantillonnage](app-insights-sampling.md)
* [Dépannage](app-insights-troubleshoot-faq.md)
