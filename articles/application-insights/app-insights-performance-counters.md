---
title: "Compteurs de performances dans Application Insights | Microsoft Docs"
description: "Surveillez les compteurs de performances système et .NET personnalisés dans Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: mbullwin
ms.openlocfilehash: 40821d32c5bdfe51bb3cb205660d6f25b2c3fadc
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="system-performance-counters-in-application-insights"></a>Compteurs de performances système dans Application Insights
Windows offre un large éventail de [compteurs de performance](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) tels que le niveau d’occupation du processeur, la mémoire, le disque et l’utilisation du réseau. Vous pouvez également définir les vôtres. [Application Insights](app-insights-overview.md) peut afficher ces compteurs de performance si votre application est exécutée sous IIS sur un hôte ou une machine virtuelle local sur lequel vous disposez d’un accès administratif. Les graphiques indiquent les ressources disponibles pour votre application active, et peuvent vous aider à identifier l’équilibrage de charge entre les instances de serveur.

Les compteurs de performance s’affichent dans le panneau Serveurs, qui inclut une table qui segmente par instance de serveur.

![Compteurs de performances signalés dans Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Les compteurs de performance ne sont pas disponibles pour les applications web Azure. Vous pouvez toutefois [envoyer des diagnostics Azure vers Application Insights](app-insights-azure-diagnostics.md).)

## <a name="view-counters"></a>Afficher des compteurs
Le panneau Serveurs affiche un ensemble de compteurs de performances par défaut. 

Pour afficher d’autres compteurs, modifiez les graphiques dans le panneau Serveurs ou ouvrez un nouveau panneau [Metrics Explorer](app-insights-metrics-explorer.md) et ajoutez de nouveaux graphiques. 

Les compteurs disponibles sont répertoriés en tant que mesures lorsque vous modifiez un graphique.

![Compteurs de performances signalés dans Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Pour afficher tous vos graphiques utiles au même emplacement, créez un [tableau de bord](app-insights-dashboards.md) et épinglez-les à celui-ci.

## <a name="add-counters"></a>Ajouter des compteurs
Si le compteur de performances souhaité n’apparaît pas dans la liste des mesures, c’est parce que le Kit de développement logiciel (SDK) Application Insights ne le collecte pas sur votre serveur web. Vous pouvez le configurer pour qu’il le fasse.

1. Découvrez les compteurs disponibles sur votre serveur à l’aide de la commande PowerShell suivante au niveau du serveur :
   
    `Get-Counter -ListSet *`
   
    (Voir [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Ouvrez ApplicationInsights.config.
   
   * Si vous avez ajouté Application Insights à votre application pendant le développement, modifiez ApplicationInsights.config dans votre projet, puis redéployez-le sur vos serveurs.
   * Si vous avez utilisé Status Monitor pour instrumenter une application web en cours d’exécution, recherchez ApplicationInsights.config dans le répertoire racine de l’application dans IIS. Mettez-le à jour dans chaque instance de serveur.
3. Modifiez la directive du collecteur de performances :
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Vous pouvez capturer les compteurs standard et ceux que vous avez implémentés vous-même. `\Objects\Processes` est un exemple de compteur standard, disponible sur tous les systèmes Windows. `\Sales(photo)\# Items Sold` est un exemple de compteur personnalisé qui peut être implémenté dans un service web. 

Le format est le suivant : `\Category(instance)\Counter"` ou, pour les catégories qui ne présentent aucune instance : `\Category\Counter`, tout simplement.

`ReportAs` est requis pour les noms de compteurs qui ne correspondent pas `[a-zA-Z()/-_ \.]+`, à savoir qu’ils contiennent des caractères ne se trouvant pas dans les ensembles suivants : lettres, crochets, barre oblique, trait d’union, trait de soulignement, espace, point.

Si vous spécifiez une instance, elle est collectée en tant que dimension « CounterInstanceName » de la mesure signalée.

### <a name="collecting-performance-counters-in-code"></a>Collecte des compteurs de performances dans le code
Pour collecter les compteurs de performances système et les envoyer à Application Insights, vous pouvez adapter l’extrait de code ci-dessous :


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Ou vous pouvez effectuer la même opération avec les mesures personnalisées que vous avez créées :

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Compteurs de performances dans Analytics
Vous pouvez rechercher et afficher des rapports de compteur de performances dans [Analytics](app-insights-analytics.md).

Le schéma **compteur de performances** expose les noms `category`, `counter` et `instance` nom de chaque compteur de performance.  Dans les données de télémétrie pour chaque application, vous ne verrez que les compteurs pour cette application. Par exemple, pour voir les compteurs disponibles : 

![Compteurs de performances dans Application Insights Analytics](./media/app-insights-performance-counters/analytics-performance-counters.png)

(« Instance » fait ici référence à l’instance de compteur de performances, pas à l’instance de rôle ou de machine serveur. Le nom d’instance de compteur de performances segmente généralement les compteurs comme le temps processeur par le nom du processus ou de l’application.)

Pour obtenir un graphique présentant la mémoire disponible sur une période récente : 

![Graphique temporel dans Application Insights Analytics](./media/app-insights-performance-counters/analytics-available-memory.png)

Comme les autres données de télémétrie, **performanceCounters** possède également une colonne `cloud_RoleInstance` qui indique l’identité de l’instance de serveur hôte sur lequel votre application est en cours d’exécution. Par exemple, pour comparer les performances de votre application sur des ordinateurs différents : 

![Performances segmentées par instances de rôle d’Application Insights Analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Nombres ASP.NET et Application Insights
*Quelle est la différence entre le taux d’exceptions et les mesures d’exceptions ?*

* *taux d’exceptions* est un compteur de performances système. Le CLR compte l’ensemble des exceptions gérées et non gérées qui sont levées et divise le total d’un intervalle d'échantillonnage par la longueur de cet intervalle. Le Kit de développement logiciel (SDK) Application Insights collecte ce résultat et l’envoie au portail.
* *Exceptions* représente le nombre de rapports TrackException reçus par le portail au cours de l’intervalle d’échantillonnage du graphique. Il comprend uniquement les exceptions gérées pour lesquelles vous avez écrit des appels TrackException dans votre code et n’inclut pas toutes les [exceptions non gérées](app-insights-asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Compteurs de performances dans les applications Asp.Net Core
Les compteurs de performances ne sont pris en charge que si l’application cible le .NET Framework complet. Il est impossible de collecter les compteurs de performances pour les applications .Net Core.

## <a name="alerts"></a>Alertes
Comme d’autres mesures, vous pouvez [définir une alerte](app-insights-alerts.md) pour vous avertir si un compteur de performances dépasse une limite que vous spécifiez. Ouvrez le panneau Alertes et cliquez sur Ajouter une alerte.

## <a name="next"></a>Étapes suivantes
* [Suivi des dépendances](app-insights-asp-net-dependencies.md)
* [Suivi des exceptions](app-insights-asp-net-exceptions.md)

