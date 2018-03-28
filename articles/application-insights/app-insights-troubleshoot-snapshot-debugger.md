---
title: Guide de résolution des problèmes du débogueur de captures instantanées Azure Application Insights | Microsoft Docs
description: Questions fréquentes sur le débogueur de captures instantanées Azure Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: ''
ms.service: application-insights
ms.workload: ''
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2b4a5f548578b563c92f8d7ff85457b50b02fbd4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Débogueur de captures instantanées : Guide de résolution des problèmes

Le débogueur de captures instantanées Application Insights vous permet de collecter automatiquement un instantané de débogage auprès des applications web dynamiques. Cette capture instantanée indique l’état du code source et des variables au moment où une exception a été levée. Cet article explique comment le débogueur fonctionne et fournit des solutions à des problèmes courants.

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Comment fonctionne le débogueur de captures instantanées Application Insights

Le débogueur de capture instantanée Application Insights fait partie du pipeline de télémétrie Application Insights (qui est une instance de ITelemetryProcessor). Le collecteur de capture instantanée surveille les exceptions levées dans votre code (AppDomain.FirstChanceException) et la télémétrie d’exception signalés à Application Insights via `TelemetryClient.TrackException`. Une fois que vous avez correctement ajouté le collecteur de captures instantanées à votre projet et qu’il a détecté un événement dans le pipeline de télémétrie, un événement personnalisé appelé « AppInsightsSnapshotCollectorLogs » et « SnapshotCollectorEnabled » dans les données personnalisées est envoyé. En même temps, un processus appelé « SnapshotUploader.exe » (version 1.2.0 ou supérieure) ou « MinidumpUploader.exe » (version antérieure à 1.2.0) démarre pour charger les fichiers de données de captures instantanées collectés dans Application Insights.  Lorsque le chargeur démarre, un événement personnalisé appelé « UploaderStart » est envoyé. Ensuite, le collecteur de captures instantanées adopte son comportement normal de surveillance.

Alors que le collecteur de captures instantanées surveille la télémétrie des exceptions Application Insights, il utilise les paramètres (par exemple, ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) définis dans la configuration pour déterminer quand collecter une capture instantanée. Quand toutes les règles sont satisfaites, le collecteur demande une capture instantanée pour l’exception suivante levée au même endroit. Simultanément, un événement personnalisé Application Insights portant le nom « AppInsightsSnapshotCollectorLogs » et « RequestSnapshots » sont envoyés. Étant donné que le compilateur optimise le code « Release », les variables locales peuvent ne pas être visibles dans la capture instantanée collectée. Le collecteur de captures instantanées essaie de désoptimiser la méthode qui a levé l’exception, quand il demande des captures instantanées. Pendant ce temps, un événement personnalisé Application Insights portant le nom « AppInsightsSnapshotCollectorLogs » et « ProductionBreakpointsDeOptimizeMethod » dans les données personnalisées sont envoyés.  Lorsque la capture instantanée de l’exception suivante est collectée, les variables locales sont disponibles. Une fois la capture instantanée collectée, le code est réoptimisé. 

> [!NOTE]
> La désoptimisation nécessite l’installation de l’extension de site Application Insights.

Lorsqu’une capture instantanée est demandée pour une exception spécifique, le collecteur de captures instantanées commence à surveiller le pipeline de gestion des exceptions de votre application (AppDomain.FirstChanceException). Quand l’exception se produit à nouveau, le collecteur démarre une capture instantanée (événement personnalisé Application Insights portant le nom « AppInsightsSnapshotCollectorLogs » et « SnapshotStart » dans les données personnalisées). Ensuite, un cliché instantané du processus en cours d’exécution est effectué (la table des pages est dupliquée). Cette opération prend normalement entre 10 et 20 millisecondes. Ensuite, un événement personnalisé Application Insights portant le nom « AppInsightsSnapshotCollectorLogs » et « SnapshotStop » dans les données personnalisées sont envoyés. Lorsque le processus de duplication (fork) est créé, la mémoire paginée totale augmente de la même quantité que la mémoire paginée de votre application en cours d’exécution (la plage de travail est beaucoup plus petite). Pendant l’exécution normale de votre processus d’application, la mémoire du processus de cliché instantané est vidée sur le disque et chargée vers Application Insights. Une fois la capture instantanée chargée, un événement personnalisé Application Insights portant le nom « UploadSnapshotFinish » est envoyé.

## <a name="is-the-snapshot-collector-working-properly"></a>Le collecteur de captures instantanées fonctionne-t-il correctement ?

### <a name="how-to-find-snapshot-collector-logs"></a>Comment trouver les journaux du collecteur de captures instantanées
Les journaux du collecteur de captures instantanées sont envoyés à votre compte Application Insight si la version du [package NuGet du collecteur de captures instantanées](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) est la version 1.1.0 ou ultérieure. Vérifiez que *ProvideAnonymousTelemetry* n’a pas la valeur false (la valeur est true par défaut).

* Accédez à votre ressource Application Insights dans le portail Azure.
* Cliquez sur *Rechercher* dans la section Vue d’ensemble.
* Entrez la chaîne suivante dans la zone de recherche :
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Remarque : modifiez l’*intervalle de temps* si nécessaire.

![Capture d’écran d’une recherche des journaux du collecteur de captures instantanées](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Examiner les journaux du collecteur de captures instantanées
Lors de la recherche des journaux du collecteur de captures instantanées, des événements « UploadSnapshotFinish » doivent se trouver dans l’intervalle de temps ciblé. Si vous ne voyez pas le bouton « Ouvrir l’instantané de débogage » pour ouvrir la capture instantanée, envoyez un e-mail à snapshothelp@microsoft.com avec votre clé d’instrumentation Application Insights.

![Capture d’écran de l’examen des journaux du collecteur de captures instantanées](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Je ne trouve pas de capture instantanée à ouvrir
Si les étapes suivantes ne vous aident à résoudre le problème, envoyez un e-mail à snapshothelp@microsoft.com avec votre clé d’instrumentation Application Insights.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Étape 1 : Vérifier que votre application envoie des données de télémétrie et des données d’exception à Application Insights
Accédez à la ressource Application Insights, vérifiez que des données sont envoyées à partir de votre application.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Étape 2 : Vérifier que le collecteur de captures instantanées est correctement ajouté au pipeline Application Insights Telemetry de votre application
Si vous trouvez des journaux à l’étape « Comment trouver les journaux du collecteur de captures instantanées », le collecteur de captures instantanées est correctement ajouté à votre projet et vous pouvez ignorer cette étape.

En l’absence de journaux du collecteur de captures instantanées, vérifiez les éléments suivants :
* Pour les applications ASP.NET classiques, recherchez cette ligne *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* dans le fichier *ApplicationInsights.config*.

* Pour les applications ASP.NET Core, vérifiez que *ITelemetryProcessorFactory* avec *SnapshotCollectorTelemetryProcessor* est ajouté aux services *IServiceCollection*.

* Vérifiez aussi que vous utilisez la clé d’instrumentation correcte dans votre application publiée.

* Le collecteur de captures instantanées ne prend pas en charge plusieurs clés d’instrumentation dans une seule application, il envoie des captures instantanées à la clé d’instrumentation de la première exception qu’il observe.

* Si vous définissez *InstrumentationKey* manuellement dans votre code, mettez à jour l’élément *InstrumentationKey* à partir du fichier *ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Étape 3 : Vérifier que le chargeur minidump a démarré
Dans les journaux du collecteur de captures instantanées, recherchez *UploaderStart* (type UploaderStart dans la zone de texte de recherche). Un événement doit s’y trouver quand le collecteur de captures instantanées a surveillé la première exception. Si cet événement n’existe pas, consultez les autres journaux pour plus d’informations. Une possibilité de résoudre ce problème consiste à redémarrer votre application.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Étape 4 : Vérifier que le collecteur de captures instantanées a exprimé son intention de collecter des captures instantanées
Dans les journaux du collecteur de captures instantanées, recherchez *RequestSnapshots* (type ```RequestSnapshots``` dans la zone de texte de recherche).  En son absence, vérifiez votre configuration. Par exemple, *ThresholdForSnapshotting*, qui indique le numéro d’une exception spécifique qui peut se produire avant de démarrer la collecte de captures instantanées.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Étape 5 : Vérifier que la capture instantanée n’est pas désactivée en raison d’une protection de la mémoire
Pour protéger les performances de votre application, une capture instantanée n’a lieu que lorsque la mémoire tampon est bonne. Dans les journaux du collecteur de captures instantanées, recherchez « CannotSnapshotDueToMemoryUsage ». Dans les données personnalisées de l’événement, une raison précise est donnée. Si votre application s’exécute dans Azure Web App, la restriction peut être stricte. Étant donné qu’Azure Web App redémarre votre application lorsque certaines règles de mémoire sont remplies. Vous pouvez essayer de monter en puissance votre plan de service sur des ordinateurs dotés de plus de mémoire pour résoudre ce problème.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Étape 6 : Vérifier que des captures instantanées ont été capturées
Dans les journaux du collecteur de captures instantanées, recherchez ```RequestSnapshots```.  En son absence, vérifiez votre configuration. Par exemple, *ThresholdForSnapshotting*, qui indique le numéro d’une exception spécifique avant de collecter une capture instantanée.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Étape 7 : Vérifier que les captures instantanées sont correctement chargées
Dans les journaux du collecteur de captures instantanées, recherchez ```UploadSnapshotFinish```.  En son absence, envoyez un e-mail à snapshothelp@microsoft.com avec votre clé d’instrumentation Application Insights. Si cet événement existe, ouvrez un des journaux et copiez la valeur « SnapshotId » dans les données personnalisées. Puis recherchez cette valeur. Vous trouvez ainsi l’exception correspondant à la capture instantanée. Cliquez ensuite sur l’exception et ouvrez la capture instantanée de débogage. (En l’absence d’exception correspondante, la télémétrie des exceptions peut être échantillonnée, en raison d’un volume élevé. Essayez d’un autre ID d’instantané.)

![Capture d’écran de la recherche de SnapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Capture d’écran de l’ouverture de l’exception](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Capture d’écran de l’ouverture de la capture instantanée de débogage](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Capture d’écran de l’affichage des variables locales incomplète

Il manque certaines variables locales. Si votre application exécute du code de version, le compilateur écarte certaines variables à des fins d’optimisation. Par exemple : 

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Si votre cas est différent, cela peut indiquer d’un bogue. Envoyez un e-mail à snapshothelp@microsoft.com avec votre clé d’instrumentation Application Insights, accompagnée de l’extrait de code.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Vue de capture instantanée : Impossible d’obtenir la valeur de la variable locale ou de l’argument
Vérifiez que l’[extension de site Application Insights](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) est installée. Si le problème persiste, envoyez un e-mail à snapshothelp@microsoft.com avec votre clé d’instrumentation Application Insights.
