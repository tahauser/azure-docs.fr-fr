---
title: Aide-mémoire sur le langage de requête Azure Log Analytics | Microsoft Docs
description: Cet article a pour but de vous aider à passer au nouveau langage de requête Azure Log Analytics, si vous connaissiez déjà l’ancien.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Transition vers le nouveau langage de requête d’Azure Log Analytics
Log Analytics a récemment implémenté un nouveau langage de requête.  Cet article a pour but de vous aider à passer à ce langage pour Log Analytics si vous connaissez déjà l’ancien.

## <a name="resources"></a>Ressources


## <a name="language-converter"></a>Convertisseur de langage

Si vous connaissez l’ancien langage de requête de Log Analytics, le moyen le plus simple de créer la même requête dans le nouveau langage est d’utiliser le convertisseur de langage, qui se trouve dans le portail Recherche dans les journaux lorsque votre espace de travail est converti.  L’utilisation du convertisseur est simple. Il vous suffit de taper une requête dans l’ancien langage, puis de cliquer sur **Convertir**.  Vous pouvez soit cliquer sur le bouton de recherche pour exécuter la requête, soit copier-coller la requête pour l’utiliser ailleurs.

![Convertisseur de langage](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>Ressources
Le [site de la documentation du langage de requête Log Analytics](https://docs.loganalytics.io) contient toutes les ressources dont vous avez besoin pour vous familiariser avec le nouveau langage,  notamment des didacticiels, des exemples et des informations de référence complètes.


## <a name="cheat-sheet"></a>Aide-mémoire

Le tableau suivant montre des requêtes courantes de l’ancien langage et leurs correspondances dans le nouveau langage de requête Azure Log Analytics.

| Description | Ancien | new |
|:--|:--|:--|
| Rechercher dans toutes les tables      | error | search "error"  (non respect de la casse) |
| Sélectionnez les données d’une table | Type=Event |  Événement |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| Comparaison de chaînes      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso" (non respect de la casse)<br>Event &#124; where Computer contains_cs "Contoso" (respect de la casse) |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| Comparaison de dates        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &amp;#124; where TimeGenerated between (datetime(2017-05-01) . datetime(2017-05-31)) |
| Comparaison booléenne     | Type=Heartbeat IsGatewayInstalled=false  | Heartbeat \| où IsGatewayInstalled == false |
| Trier                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| Différencier               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| Étendre des colonnes         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| Agrégation            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| Agrégation avec limite | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| Union                  | Type=Event or Type=Syslog | union Event, Syslog |
| Join                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Étapes suivantes
- Consultez un [didacticiel sur l’écriture de requêtes](https://go.microsoft.com/fwlink/?linkid=856078) à l’aide du nouveau langage de requête.
- Pour plus d’informations sur les commandes, les opérateurs et les fonctions du nouveau langage de requête, consultez la [Référence sur le langage de requête](https://go.microsoft.com/fwlink/?linkid=856079).  
