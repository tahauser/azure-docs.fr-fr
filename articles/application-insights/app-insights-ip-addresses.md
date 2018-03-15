---
title: "Adresses IP utilisées par Application Insights et Log Analytics | Microsoft Docs"
description: Exceptions de pare-feu de serveur requises par Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: mbullwin
ms.openlocfilehash: 9b48b17b214f6ff22c7c68421ba8c89104c8b4b1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Adresses IP utilisées par Application Insights et Log Analytics
Le service [Azure Application Insights](app-insights-overview.md) utilise plusieurs adresses IP. Vous devrez peut-être connaître ces adresses si l’application que vous surveillez est hébergée derrière un pare-feu.

> [!NOTE]
> Bien que ces adresses soient statiques, il est possible que nous devions les modifier de temps à autre.
> 
> 

## <a name="outgoing-ports"></a>Ports sortants
Vous devez ouvrir des ports sortants dans le pare-feu de votre serveur pour autoriser le Kit de développement logiciel (SDK) Application Insights et/ou Status Monitor à envoyer des données sur le portail :

| Objectif | URL | IP | Ports |
| --- | --- | --- | --- |
| Télémétrie |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244 |443 |
| Live Metrics Stream (Flux continu de mesures) |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |
| Télémétrie interne |breeze.aimon.applicationinsights.io |52.161.11.71 |443 |

## <a name="status-monitor"></a>Status Monitor
Configuration de Status Monitor (nécessaire uniquement pour apporter des modifications).

| Objectif | URL | IP | Ports |
| --- | --- | --- | --- |
| Configuration |`management.core.windows.net` | |`443` |
| Configuration |`management.azure.com` | |`443` |
| Configuration |`login.windows.net` | |`443` |
| Configuration |`login.microsoftonline.com` | |`443` |
| Configuration |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Configuration |`auth.gfx.ms` | |`443` |
| Configuration |`login.live.com` | |`443` |
| Installation |`packages.nuget.org` , `nuget.org`, `api.nuget.org` | |`443` |

## <a name="hockeyapp"></a>HockeyApp
| Objectif | URL | IP | Ports |
| --- | --- | --- | --- |
| Données d’incident |gate.hockeyapp.net |104.45.136.42 |80, 443 |

## <a name="availability-tests"></a>Tests de disponibilité
Voici la liste des adresses à partir desquelles les [tests web de disponibilité](app-insights-monitor-web-app-availability.md) sont exécutés. Si vous souhaitez exécuter des tests web sur votre application, mais que votre serveur web est limité au service de clients spécifiques, vous devrez alors autoriser le trafic entrant depuis nos serveurs de test de disponibilité.

Ouvrez les ports 80 (http) et 443 (https) pour le trafic entrant à partir de ces adresses (les adresses IP sont regroupées par emplacement) :

```
Australia East
13.70.83.252
13.75.150.96
13.75.153.9
13.75.158.185
Brazil South
191.232.32.122
191.232.172.45
191.232.176.218
191.232.191.225
France South
52.136.140.221
52.136.140.222
52.136.140.223
52.136.140.226
France Central
52.143.140.242
52.143.140.246
52.143.140.247
52.143.140.249
East Asia
13.75.121.122
23.99.115.153
23.99.123.38
23.102.232.186
52.175.38.49
52.175.39.103
North Europe
13.74.184.101
13.74.185.160
40.69.200.198
52.164.224.46
52.169.12.203
52.169.14.11
52.169.237.149
52.178.183.105
Japan East
52.243.33.33
52.243.33.141
52.243.35.253
52.243.41.117
West Europe
52.174.166.113
52.174.178.96
52.174.31.140
52.174.35.14
52.178.104.23
52.178.109.190
52.178.111.139
52.233.166.221
UK South
51.140.79.229
51.140.84.172
51.140.87.211
51.140.105.74
UK West
51.141.25.219
51.141.32.101
51.141.35.167
51.141.54.177
Southeast Asia
52.187.29.7
52.187.179.17
52.187.76.248
52.187.43.24
52.163.57.91
52.187.30.120
West US
104.45.228.236
104.45.237.251
13.64.152.110
13.64.156.54
13.64.232.251
13.64.236.105
13.91.94.59
40.118.131.182
40.83.189.192
40.83.215.122
Central US
52.165.130.58
52.173.142.229
52.173.147.190
52.173.17.41
52.173.204.247
52.173.244.190
52.173.36.222
52.176.1.226
North Central US
23.96.247.139
23.96.249.113
52.162.124.242
52.162.126.139
52.162.241.147
52.162.246.222
52.162.247.136
52.237.153.231
52.237.154.216
52.237.156.14
52.237.157.218
52.237.157.37
South Central US
104.210.145.106
13.84.176.24
13.84.49.16
13.85.11.137
13.85.26.248
13.85.69.145
52.171.136.162
52.171.141.253
52.171.57.172
52.171.58.140
East US
13.82.218.95
13.90.96.71
13.90.98.52
13.92.137.70
40.85.187.235
40.87.61.61
52.168.8.247
52.170.38.79
52.170.80.61
52.179.9.26

```  

## <a name="application-insights-api"></a>API Application Insights
| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |13.82.26.252<br/>40.76.213.73 |80,443 |
| Documents API |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |13.82.24.149<br/>40.114.82.10 |80,443 |
| API interne |aigs.aisvc.visualstudio.com<br/>aigs1.aisvc.visualstudio.com<br/>aigs2.aisvc.visualstudio.com<br/>aigs3.aisvc.visualstudio.com<br/>aigs4.aisvc.visualstudio.com<br/>aigs5.aisvc.visualstudio.com<br/>aigs6.aisvc.visualstudio.com |dynamique|443 |

## <a name="log-analytics-api"></a>API Log Analytics
| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| API |api.loganalytics.io<br/>*.api.loganalytics.io |dynamique |80,443 |
| Documents API |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |dynamique |80,443 |

## <a name="application-insights-analytics"></a>Application Insights Analytics

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Portail Analytics | analytics.applicationinsights.io | dynamique | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamique | 80,443 |
| CDN multimédia | applicationanalyticsmedia.azureedge.net | dynamique | 80,443 |

Remarque : Le domaine *.applicationinsights.io appartient à l’équipe Application Insights.

## <a name="log-analytics-portal"></a>Portail Log Analytics

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Portail | portal.loganalytics.io | dynamique | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamique | 80,443 |

Remarque : le domaine *.loganalytics.io appartient à l’équipe Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Extension du Portail Azure Application Insights

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Extension Application Insights | stamp2.app.insightsportal.visualstudio.com | dynamique | 80,443 |
| CDN de l’extension Application Insights | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamique | 80,443 |

## <a name="application-insights-sdks"></a>SDK Application Insights

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| CDN du SDK JS Application Insights | az416426.vo.msecnd.net | dynamique | 80,443 |
| SDK Java Application Insights | aijavasdk.blob.core.windows.net | dynamique | 80,443 |

## <a name="profiler"></a>Profileur

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 51.143.96.206<br/>51.143.98.157<br/>52.161.8.88<br/>52.161.29.225<br/>52.178.149.106<br/>52.178.147.66<br/>40.68.32.221<br/>104.40.217.71 | 443
| Portail | gateway.azureserviceprofiler.net | dynamique | 443
| Stockage | *.core.windows.net | dynamique | 443

## <a name="snapshot-debugger"></a>Débogueur de capture instantanée

| Objectif | URI | IP | Ports |
| --- | --- | --- | --- |
| Agent | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 23.101.68.84<br/>52.174.44.101<br/>52.250.121.195<br/>51.143.88.187<br/> | 443
| Portail | ppe.gateway.azureserviceprofiler.net | dynamique | 443
| Stockage | *.core.windows.net | dynamique | 443
