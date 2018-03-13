---
title: "Analyse des performances et de l’utilisation pour les applications de bureau Windows"
description: "Analysez l’utilisation et les performances de votre application de bureau Windows avec HockeyApp et Application Insights."
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: mbullwin
ms.openlocfilehash: 3b91996c12a27847befcacf6daf14457a0e066aa
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Analyse des performances et de l’utilisation dans les applications de bureau Windows


[Azure Application Insights](app-insights-overview.md) et [HockeyApp](https://hockeyapp.net) vous permettent d’analyser vos applications déployées en termes d’utilisation et de performances.

> [!IMPORTANT]
> Nous vous recommandons [HockeyApp](https://hockeyapp.net) pour distribuer et surveiller des applications de bureau et pour périphérique. Avec HockeyApp, vous pouvez gérer la distribution, le test direct et les commentaires des utilisateurs, de même que surveiller l’utilisation et les rapports d’incident. Vous pouvez également [exporter et interroger vos données de télémétrie avec Analytics](app-insights-hockeyapp-bridge-app.md).
> 
> Bien que les données de télémétrie puissent être envoyées à Application Insights à partir d’une application de bureau, ceci est principalement utile à des fins de débogage et d’expérimentation.
> 
> 

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Pour envoyer la télémétrie à Application Insights à partir d’une application Windows
1. Dans le [portail Azure](https://portal.azure.com), [créez une ressource Application Insights](app-insights-create-new-resource.md). Choisissez ASP.NET comme type d’application.
2. Copiez la clé d'instrumentation. Recherchez la clé dans la liste déroulante Essentials de la nouvelle ressource que vous venez de créer. 
3. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application et ajoutez Microsoft.ApplicationInsights.WindowsServer. (Ou choisissez Microsoft.ApplicationInsights si vous souhaitez simplement l’API nue, sans modules de collecte de télémétrie standard.)
4. Définissez la clé d’instrumentation dans votre code :
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*votre clé*`";` 
   
    ou dans ApplicationInsights.config (si vous avez installé l’un des packages de télémétrie standard) :
   
    `<InstrumentationKey>`*votre clé*`</InstrumentationKey>` 
   
    Si vous utilisez ApplicationInsights.config, assurez-vous que ses propriétés dans l’Explorateur de solutions sont définies sur **Build Action = Content, Copy to Output Directory = Copy**.
5. [Utilisez l’API](app-insights-api-custom-events-metrics.md) pour envoyer les données de télémétrie.
6. Exécutez votre application et consultez la télémétrie dans la ressource que vous avez créée dans le portail Azure.

## <a name="telemetry"></a>Exemple de code
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Étapes suivantes
* [Création d’un tableau de bord](app-insights-dashboards.md)
* [Recherche de diagnostic](app-insights-diagnostic-search.md)
* [Exploration des mesures](app-insights-metrics-explorer.md)
* [Écriture de requêtes Analytics](app-insights-analytics.md)

