---
title: "Modèle de données de télémétrie d’Azure Application Insights : télémétrie des mesures | Microsoft Docs"
description: "Modèle de données Application Insights pour la télémétrie des mesures"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: bd09e2a21c25097fa4b378cb2dbe2787edbb1967
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="metric-telemetry-application-insights-data-model"></a>Télémétrie des mesure : modèle de données Application Insights

Deux types de télémétrie des mesures sont prises en charge par [Application Insights](app-insights-overview.md) : la mesure unique et la mesure pré-agrégée. La mesure unique consiste simplement dans un nom et une valeur. La mesure pré-agrégée spécifie les valeurs minimale et maximale de la mesure dans l’intervalle d’agrégation, ainsi que son écart standard.

La télémétrie des mesures pré-agrégées suppose que cette période d’agrégation est d’une minute.

Plusieurs noms de mesure connus sont pris en charge par Application Insights. Ces métriques sont placées dans la table performanceCounters.

Système de représentation des mesures et compteurs de processus :

| **Nom .NET**             | **Nom sans plateforme spécifiée** | **Nom d’API REST** | **Description**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Travail en cours... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | nombre total de processeurs de l’ordinateur
| `\Memory\Available Bytes`                 | Travail en cours... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | mémoire disponible sur le disque
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Travail en cours... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | processeur du processus hébergeant l’application
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Travail en cours... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | mémoire utilisée par le processus hébergeant l’application
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Travail en cours... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | fréquence des opérations d’E/S exécutées par le processus hébergeant l’application
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Travail en cours... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | fréquence des requêtes traitées par application 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Travail en cours... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | fréquence des exceptions levées par application
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Travail en cours... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | durée d’exécution moyenne des requêtes
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Travail en cours... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | nombre de requêtes en attente de traitement dans une file d’attente

## <a name="name"></a>Nom

Nom de la mesure que vous aimeriez voir dans le portail Application Insights et dans l’interface utilisateur. 

## <a name="value"></a>Valeur

Valeur unique pour la mesure. Somme des mesures individuelles de l’agrégation.

## <a name="count"></a>Nombre

Poids de mesure de la mesure agrégée. Ne doit pas être défini pour une mesure.

## <a name="min"></a>Min

Valeur minimale de la mesure agrégée. Ne doit pas être défini pour une mesure.

## <a name="max"></a>max

Valeur maximale de la mesure agrégée. Ne doit pas être défini pour une mesure.

## <a name="standard-deviation"></a>Écart standard

Écart standard de la mesure agrégée. Ne doit pas être défini pour une mesure.

## <a name="custom-properties"></a>Propriétés personnalisées

Si la propriété personnalisée `CustomPerfCounter` d’une métrique est définie sur `true`, celle-ci représente le compteur de performances Windows. Ces métriques sont placées dans la table performanceCounters, pas dans customMetrics. De plus, le nom de cette métrique est analysé pour extraire la catégorie, le compteur et les noms d’instance.

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser [l’API Application Insights pour les événements et les mesures personnalisés](app-insights-api-custom-events-metrics.md#trackmetric).
- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](application-insights-data-model.md).
- Découvrez quelles [plateformes](app-insights-platforms.md) sont prises en charge par Application Insights.
