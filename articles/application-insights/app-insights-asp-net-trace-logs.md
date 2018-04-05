---
title: Exploration des journaux .NET dans Application Insights
description: Effectuez une recherche dans les journaux générés avec Trace, NLog ou Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 574b11f9ba38bda775610f2f9e90fbb2d2b05868
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Exploration des journaux .NET dans Application Insights
Si vous utilisez NLog, log4Net ou System.Diagnostics.Trace pour le suivi de diagnostic dans votre application ASP.NET, les journaux peuvent être envoyés à [Azure Application Insights][start], où vous pouvez les explorer et les rechercher. Les journaux sont fusionnés avec la télémétrie provenant de votre application, afin que vous puissiez identifier les traces associées au traitement des demandes de l’utilisateur et les mettre en corrélation avec d’autres événements et des rapports d’exception.

> [!NOTE]
> Avez-vous besoin du module de collecte de journaux ? Il s’agit d’un adaptateur très utile pour les enregistreurs d’événements tiers. Cependant, si vous n’utilisez pas déjà NLog, log4Net ou System.Diagnostics.Trace, vous pouvez appeler [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) directement.
>
>

## <a name="install-logging-on-your-app"></a>Installation de la journalisation sur votre application
Installez votre infrastructure de journalisation choisie dans votre projet. Ceci devrait générer une entrée dans le fichier app.config ou web.config.

Vous devez ajouter une entrée au fichier web.config si vous utilisez System.Diagnostics.Trace.

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Configuration d’Application Insights pour la collecte des journaux
Si vous ne l’avez pas encore fait, **[ajoutez Application Insights à votre projet](app-insights-asp-net.md)**. Une option permettant d’inclure le collecteur de journaux doit s’afficher.

Ou **configurez Application Insights** en cliquant avec le bouton droit dans l’Explorateur de solutions. Sélectionnez l’option **Configurer la collecte des traces**.

*Menu Application Insights non disponible ou aucune option pour le collecteur de journaux ?* Consultez la [Résolution des problèmes](#troubleshooting).

## <a name="manual-installation"></a>Installation manuelle
Utilisez cette méthode si votre type de projet n’est pas pris en charge par le programme d’installation Application Insights (par exemple, un projet de bureau Windows).

1. Si vous prévoyez d'utiliser log4Net ou NLog, installez-le dans votre projet.
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.
3. Recherchez « Application Insights »
4. Sélectionnez le package approprié parmi :

   * Microsoft.ApplicationInsights.TraceListener (pour capturer les appels System.Diagnostics.Trace)
   * Microsoft.ApplicationInsights.EventSourceListener (pour capturer les événements EventSource)
   * Microsoft.ApplicationInsights.EtwListener (pour capturer les événements ETW)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

Le package NuGet installe les assemblys nécessaires et modifie également le fichier web.config ou app.config.

## <a name="insert-diagnostic-log-calls"></a>Insertion d'appels de journaux de diagnostic
Si vous utilisez System.Diagnostics.Trace, un appel standard serait :

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si vous préférez log4net ou NLog :

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Utilisation des événements EventSource
Vous pouvez configurer les événements [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) à envoyer à Application Insights en tant que traces. D’abord, installez le package NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Ensuite, modifiez la section `TelemetryModules` du fichier [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Pour chaque source, vous pouvez définir les paramètres suivants :
 * `Name` spécifie le nom de l’événement EventSource à collecter.
 * `Level` spécifie le niveau de journalisation à collecter. Peut être `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Facultatif) spécifie la valeur entière de combinaisons de mots clés à utiliser.

## <a name="using-diagnosticsource-events"></a>Utilisation des événements DiagnosticSource
Vous pouvez configurer les événements [System.Diagnostics.Tracing.EventSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) de façon à les envoyer à Application Insights en tant que traces. Commencez par installer le package NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Ensuite, modifiez la section `TelemetryModules` du fichier [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Pour chaque DiagnosticSource à tracer, ajoutez une entrée avec l’attribut `Name` défini sur le nom de votre DiagnosticSource.

## <a name="using-etw-events"></a>Utilisation des événements ETW
Vous pouvez configurer les événements ETW à envoyer à Application Insights en tant que traces. D’abord, installez le package NuGet `Microsoft.ApplicationInsights.EtwCollector`. Ensuite, modifiez la section `TelemetryModules` du fichier [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

> [!NOTE] 
> Les événements ETW peuvent uniquement être collectés si le processus hébergeant le Kit de développement logiciel (SDK) s’exécute sous une identité qui membre des « Utilisateurs du journal de performances » ou des administrateurs.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Pour chaque source, vous pouvez définir les paramètres suivants :
 * `ProviderName` est le nom du fournisseur ETW à collecter.
 * `ProviderGuid` spécifie le GUID du fournisseur ETW à collecter, peut être utilisé à la place de `ProviderName`.
 * `Level` définit le niveau de journalisation à collecter. Peut être `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Facultatif) définit la valeur entière de combinaisons de mots clés à utiliser.

## <a name="using-the-trace-api-directly"></a>Utilisation de l’API de suivi directement
Vous pouvez appeler directement l’API de suivi d’Application Insights. Les adaptateurs de journalisation utilisent cette API.

Par exemple : 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

l’un des avantages de TrackTrace est que vous pouvez insérer des données relativement longues dans le message. Par exemple, vous pourriez y encoder des données POST.

Par ailleurs, vous pouvez ajouter un niveau de gravité à votre message. Comme pour les autres données de télémétrie, vous pouvez également ajouter des valeurs de propriété que vous pouvez utiliser pour filtrer ou rechercher différents jeux de traces. Par exemple : 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Cela vous permettrait, dans [Recherche][diagnostic], de filtrer facilement tous les messages d’un niveau de gravité particulier portant sur une certaine base de données.

## <a name="explore-your-logs"></a>Exploration de vos journaux
Exécutez votre application en mode débogage ou déployez-la en direct.

Dans le panneau Vue d’ensemble de votre application du [portail Application Insights][portal], choisissez [Rechercher][diagnostic].

![Dans Application Insights, cliquez sur Recherche.](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![action](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Vous pouvez par exemple :

* Filtrer selon les traces de journal ou les éléments avec des propriétés spécifiques
* Inspecter un élément spécifique en détail
* Rechercher d’autres données de télémétrie relatives à la même demande utilisateur (autrement dit, avec la même OperationId)
* Enregistrer la configuration de cette page en tant que favori

> [!NOTE]
> **Échantillonnage.** Si votre application envoie des données en grand nombre et si vous utilisez le Kit de développement logiciel Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité d’échantillonnage adaptatif peut fonctionner et transmettre uniquement un pourcentage de vos données de télémétrie. [En savoir plus sur l'échantillonnage.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Étapes suivantes
[Diagnostiquer les défaillances et les exceptions dans ASP.NET][exceptions]

[En savoir plus sur Recherche][diagnostic].

## <a name="troubleshooting"></a>Résolution de problèmes
### <a name="how-do-i-do-this-for-java"></a>Comment faire pour Java ?
Utilisez les [adaptateurs de journaux Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Le menu contextuel du projet ne contient aucune option Application Insights
* Vérifiez que les outils Application Insights sont installés sur cet ordinateur de développement. Dans le menu Outils, Extensions et mises à jour de Visual Studio, recherchez les outils Application Insights. S’ils ne sont pas dans l’onglet Installé, ouvrez l’onglet En ligne et installez-les.
* Il peut s’agir d’un type de projet non pris en charge par les outils Application Insights. Utilisez [l’installation manuelle](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Aucune option d’adaptateur dans l’outil de configuration
* Vous devez d’abord installer l’infrastructure de journalisation.
* Si vous utilisez System.Diagnostics.Trace, assurez-vous que vous l’avez [configuré dans `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Disposez-vous de la dernière version d’Application Insights ? Dans le menu **Outils** de Visual Studio, choisissez **Extensions et mises à jour**, puis ouvrez l’onglet **Mises à jour**. Si les outils Developer Analytics sont visibles, cliquez dessus pour les mettre à jour.

### <a name="emptykey"></a>J’obtiens une erreur « La clé d’instrumentation ne peut pas être vide ».
Vous avez peut-être installé le package Nuget de l'enregistreur de données sans installer Application Insights.

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config` , puis sélectionnez **Mettre à jour Application Insights**. Une boîte de dialogue vous invite à vous connecter à Azure et à créer une ressource Application Insights, ou à réutiliser une ressource existante. Ceci devrait résoudre le problème.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Je peux voir des suivis dans la recherche de diagnostic, mais pas les autres événements
Le passage des événements et des demandes dans le pipeline peut prendre un certain temps.

### <a name="limits"></a>Quelle est la quantité de données conservée ?
Plusieurs facteurs affectent la quantité de données conservées. Consultez la section [Limites](app-insights-api-custom-events-metrics.md#limits) de la page sur les métriques d’événement client pour plus d’informations. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Certaines entrées du journal ne sont pas affichées
Si votre application envoie des données en grand nombre et si vous utilisez le Kit de développement logiciel Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité d’échantillonnage adaptatif peut fonctionner et transmettre uniquement un pourcentage de vos données de télémétrie. [En savoir plus sur l'échantillonnage.](app-insights-sampling.md)

## <a name="add"></a>Étapes suivantes
* [Configuration des tests de disponibilité et de réactivité][availability]
* [Résolution des problèmes][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
