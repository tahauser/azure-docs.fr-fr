---
title: "Actions webhook pour les alertes de journal dans Azure Alerts (préversion) | Microsoft Docs"
description: "Cet article comment une règle d’alerte de journal utilisant une analyse de journal ou des informations d’application envoie des données en tant que webhook HTTP et des détails sur les différentes personnalisations possibles."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/2/2018
ms.author: vinagara
ms.openlocfilehash: 5852eb099f6620656aa69e5085447c2a8b4e0c01
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>Actions webhook pour les règles d’alerte de journal
Quand une [alerte est créée dans Azure (préversion)](monitor-alerts-unified-usage.md), vous avez la possibilité d’effectuer une [configuration à l’aide de groupes d’actions](monitoring-action-groups.md) pour exécuter une ou plusieurs actions.  Cet article décrit les différentes actions webhook disponibles et les détails de la configuration du webhook personnalisé basé sur JSON.


## <a name="webhook-actions"></a>Actions de webhook

Les actions de webhook permettent d’appeler un processus externe par le biais d’une simple requête HTTP POST.  Le service appelé doit prendre en charge les webhooks et déterminer comment il doit utiliser toute charge utile qu’il reçoit.   Les exemples d’utilisation d’un webhook en réponse à une alerte envoient un message dans [Slack](http://slack.com) ou créent un incident dans [PagerDuty](http://pagerduty.com/).  

Les propriétés requises par les actions webhook sont décrites dans le tableau suivant :

| Propriété | Description |
|:--- |:--- |
| URL du webhook |URL du webhook. |
| Charge utile JSON personnalisée |Charge utile personnalisée à envoyer avec le webhook, lorsque vous choisissez cette option au moment de la création de l’alerte. Détails disponibles sur la page [Gérer les alertes à l’aide d’Azure Alerts (préversion)](monitor-alerts-unified-usage.md) |

> [!NOTE]
> L’utilisation du bouton Tester le Webhook avec l’option *Inclure une charge utile Json personnalisée pour webhook* pour Alerte du journal, déclenche un appel fictif pour tester l’URL du webhook. Il ne contient aucune donnée réelle et représentative du schéma JSON utilisé pour les alertes de journal. 

Les webhooks incluent une URL et une charge utile au format JSON qui correspond aux données envoyées au service externe.  Par défaut, la charge utile comprend les valeurs indiquées dans le tableau suivant : vous pouvez choisir de remplacer cette charge utile par des données personnalisées de votre choix.  Dans ce cas, vous pouvez reprendre les variables de chacun des paramètres indiquées dans le tableau pour inclure les valeurs correspondantes dans votre charge utile personnalisée.


| Paramètre | Variable | Description |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nom de la règle d’alerte. |
| Niveau de gravité |#severity |Gravité définie pour l’alerte de journal déclenchée. |
| AlertThresholdOperator |#thresholdoperator |Opérateur de seuil de la règle d’alerte.  *Supérieur à* ou *Inférieur à*. |
| AlertThresholdValue |#thresholdvalue |Valeur de seuil de la règle d’alerte. |
| LinkToSearchResults |#linktosearchresults |Lien vers la recherche dans les journaux Log Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| ResultCount |#searchresultcount |Nombre d’enregistrements dans les résultats de recherche. |
| Heure de fin de l’intervalle de recherche |#searchintervalendtimeutc |Heure de fin de la requête au format UTC. |
| Intervalle de recherche |#searchinterval |Fenêtre de temps de la règle d’alerte. |
| Heure de début de l’intervalle de recherche |#searchintervalstarttimeutc |Heure de début de la requête au format UTC. 
| SearchQuery |#searchquery |Requête de recherche de journal utilisée par la règle d’alerte. |
| SearchResults |"IncludeSearchResults": true|Enregistrements retournés par la requête en tant que tableau JSON, limités aux 1 000 premiers enregistrements ; si "IncludeSearchResults": true est ajouté dans la définition de webhook JSON personnalisé en tant que propriété de niveau supérieur. |
| WorkspaceID |#workspaceid |ID de votre espace de travail Log Analytics (OMS). |
| ID de l'application |#applicationid |ID de votre application Application Insights. |
| Identifiant d’abonnement |#subscriptionid |ID de l’abonnement Azure utilisé avec Application Insights. 


Par exemple, vous pouvez spécifier la charge utile personnalisée suivante qui inclut un paramètre unique appelé *text*.  Le service appelé par ce webhook s’attendrait à recevoir ce paramètre.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Cette charge utile peut donner ce qui suit quand elle est envoyée au webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Pour inclure les résultats de la recherche dans une charge utile personnalisée, vérifiez que **IncudeSearchResults** est défini comme une propriété de niveau supérieur dans la charge utile json. 

## <a name="sample-payloads"></a>Exemples de charges utiles
Cette section présente une exemple de charge utile de webhook pour les alertes de journal, notamment lorsque la charge utile est standard et quand elle est personnalisée.

> [!NOTE]
> Pour garantir la compatibilité descendante, la charge utile de webhook standard pour les alertes utilisant Azure Log Analytics est identique à celle de la [gestion des alertes OMS](../log-analytics/log-analytics-alerts-creating.md). Mais pour les alertes de journal utilisant [Application Insights](../application-insights/app-insights-analytics.md), la charge utile de webhook standard est basée sur le schéma du Groupe d’actions.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard pour les alertes de journal 
Ces deux exemples indiquaient une charge utile fictive ne comprenant que deux colonnes et deux lignes.

#### <a name="log-alert-for-azure-log-analytics"></a>Alerte de journal pour Azure Log Analytics
Voici un exemple de charge utile pour une action standard webhook *sans utiliser d’option Json personnalisée* pour des alertes de journal basées sur une analyse des journaux.

    {
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
    }
    


#### <a name="log-alert-for-azure-application-insights"></a>Alerte de journal pour Azure Application Insights
Voici un exemple de charge utile pour un webhook standard *sans option Json personnalisée* en cas d’utilisation pour des alertes de journal basées sur des informations d’application.
    

    {
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error"
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>Alerte de journal avec charge utile JSON personnalisée
Par exemple, pour créer une charge utile personnalisée qui inclut uniquement le nom de l’alerte et les résultats de recherche, vous pouvez utiliser ce qui suit : 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

Voici un exemple de charge utile pour une action de webhook personnalisée pour une alerte de journal quelconque.
    

    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }




## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [alertes de journal dans Azure Alerts (préversion)](monitor-alerts-unified-log.md)
- Créer et gérer des [groupes d’actions dans Azure](monitoring-action-groups.md)
- En savoir plus sur [Application Insights](../application-insights/app-insights-analytics.md)
- En savoir plus sur [Log Analytics](../log-analytics/log-analytics-overview.md). 