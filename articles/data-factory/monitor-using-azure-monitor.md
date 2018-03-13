---
title: "Surveiller les fabriques de données à l’aide d’Azure Monitor |Microsoft Docs"
description: "Apprenez à utiliser Azure Monitor pour surveiller les pipelines Data Factory en activant les journaux de diagnostic à partir des informations d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: cae3c797171c3904f100ae3cdec47a31b06d3b31
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Surveiller les fabriques de données à l’aide d’Azure Monitor  
Les applications cloud sont complexes, et se composent de nombreux éléments mobiles. L’analyse fournit des données visant à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Elle vous permet également de parer à des problèmes potentiels ou de résoudre des problèmes déjà survenus. En outre, vous pouvez utiliser les données d’analyse pour obtenir des informations détaillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances ou la facilité de gestion de l’application, ou à automatiser des actions qui exigeraient normalement une intervention manuelle.

Azure Monitor fournit des métriques de niveau de base d’infrastructure et des journaux pour la plupart des services Microsoft Azure. Pour plus d’informations, consultez [Vue d’ensemble de la surveillance](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Les journaux de diagnostic Azure sont des journaux émis par une ressource qui fournissent des informations riches et fréquentes sur le fonctionnement de cette ressource. Data Factory génère les journaux de diagnostic dans Azure Monitor. 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la page [Surveiller et gérer les pipelines dans Data Factory version 1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="diagnostic-logs"></a>Journaux de diagnostic

* Enregistrez-les dans un **compte de stockage** pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) via les paramètres de diagnostic.
* Diffusez-les en continu vers **Event Hubs** pour permettre à un service tiers ou à une solution analytique personnalisée (comme PowerBI) de les ingérer.
* Analysez-les avec **Operations Management Suite (OMS) Log Analytics**.

Vous pouvez utiliser un compte de stockage ou un espace de noms Event Hub qui ne se trouve pas dans le même abonnement que la ressource émettrice des journaux. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC (contrôle d’accès en fonction du rôle) approprié aux deux abonnements.

## <a name="set-up-diagnostic-logs"></a>Configurer les journaux de diagnostic

### <a name="diagnostic-settings"></a>Paramètres de diagnostic
Les journaux de diagnostic pour les ressources non liées au calcul sont configurés à l’aide des paramètres de diagnostic. Les paramètres de diagnostic dans le cas d’un contrôle de ressource sont les suivants :

* Emplacement de destination des journaux de diagnostic (compte de stockage, Event Hubs et/ou OMS Log Analytics)
* Les catégories de journal qui sont envoyées
* La durée pendant laquelle chaque catégorie de journal doit être conservée dans un compte de stockage
* Une durée de rétention de zéro jour signifie que les journaux sont conservés indéfiniment. La valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2147483647.
* Si des stratégies de rétention sont définies, mais que le stockage des journaux dans un compte de stockage est désactivé (par exemple si seules les options Event Hubs ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.
* Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’avant-hier seront supprimés.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Activer les journaux de diagnostic via les API REST

Créez ou mettez à jour un paramètre de diagnostic dans l’API REST Azure Monitor

**Requête**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**En-têtes**
* Remplacez `{api-version}` par `2016-09-01`.
* Remplacez `{resource-id}` par l’ID de la ressource dont vous voulez modifier les paramètres de diagnostic. Pour plus d'informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-portal.md).
* Attribuez à l’en-tête `Content-Type` la valeur `application/json`.
* Définissez l’en-tête d’autorisation sur un jeton web JSON que vous obtenez auprès d’Azure Active Directory. Pour plus d’informations, consultez [Authentification des demandes](../active-directory/develop/active-directory-authentication-scenarios.md).

**Corps**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
} 
```

| Propriété | type | Description |
| --- | --- | --- |
| storageAccountId |Chaîne | ID de ressource du compte de stockage dans lequel vous voulez ajouter les journaux de diagnostic. |
| serviceBusRuleId |Chaîne | ID de règle Service Bus de l’espace de noms Service Bus dans lequel vous voulez que des concentrateurs d’événements soient créés pour la diffusion en continu des journaux de diagnostic. L’ID de règle présente le format suivant : « {ID de ressource Service Bus}/authorizationrules/{nom de la clé} ».|
| workspaceId | Type complexe | Tableau de fragments de temps de métrique et leurs stratégies de rétention. Actuellement, cette propriété est vide. |
|Mesures| Valeurs de paramètre de l’exécution de pipeline à passer au pipeline appelé| Objet JSON mappant des noms de paramètres à des valeurs d’arguments. | 
| journaux| Type complexe| Nom d’une catégorie de journal de diagnostic pour un type de ressource. Pour obtenir la liste des catégories de journal de diagnostic pour une ressource, commencez par effectuer une opération d’obtention (GET) des paramètres de diagnostic. |
| category| Chaîne| Tableau de catégories de journal et leurs stratégies de rétention. |
| timeGrain | Chaîne | Granularité des métriques capturées au format de durée ISO 8601. Elle doit être de PT1M (une minute).|
| Activé| Booléen | Indique si la collection de cette métrique ou catégorie de journal est activée pour cette ressource.|
| retentionPolicy| Type complexe| Décrit la stratégie de rétention pour une métrique ou une catégorie de journal. Utilisé pour l’option de compte de stockage uniquement.|
| days| Int| Nombre de jours durant lesquels les métriques ou les journaux sont conservés. La valeur 0 indique que les journaux sont conservés indéfiniment. Utilisé pour l’option de compte de stockage uniquement. |

**Réponse**

200 OK


```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Obtenez des informations sur le paramètre de diagnostic dans l’API REST Azure Monitor

**Requête**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**En-têtes**
* Remplacez `{api-version}` par `2016-09-01`.
* Remplacez `{resource-id}` par l’ID de la ressource dont vous voulez modifier les paramètres de diagnostic. Pour plus d’informations, consultez Utilisation des groupes de ressources pour gérer vos ressources Azure.
* Attribuez à l’en-tête `Content-Type` la valeur `application/json`.
* Définissez l’en-tête d’autorisation sur un jeton web JSON que vous obtenez auprès d’Azure Active Directory. Pour plus d’informations, consultez Authentification des demandes.

**Réponse**

200 OK

```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/0ee78edb-a0ad-456c-a0a2-901bf542c102/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Plus d’informations ici](https://msdn.microsoft.com/en-us/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>Schéma des journaux et des événements

### <a name="activity-run-logs-attributes"></a>Attributs des journaux d’exécution d’activité

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties:" 
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propriété | type | Description | exemples |
| --- | --- | --- | --- |
| Level |Chaîne | Niveau des journaux de diagnostic. Le niveau 4 est toujours celui associé aux journaux d’exécution d’activité. | `4`  |
| correlationId |Chaîne | ID unique pour le suivi d’une demande particulière de bout en bout | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Chaîne | Heure de l’événement dans l’intervalle de temps, au format UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Chaîne| ID de l’exécution d’activité | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Chaîne| ID de l’exécution de pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| Chaîne | ID de ressource associé pour la ressource de fabrique de données | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Chaîne | Catégorie de journaux de diagnostic. Attribuez à cette propriété la valeur « ActivityRuns » | `ActivityRuns` |
|level| Chaîne | Niveau des journaux de diagnostic. Attribuez à cette propriété la valeur « Informational » | `Informational` |
|operationName| Chaîne |Nom de l’activité avec état. Si l’état est la pulsation de début, il s’agit de `MyActivity -`. Si l’état est la pulsation de fin, il s’agit de `MyActivity - Succeeded` avec l’état final | `MyActivity - Succeeded` |
|pipelineName| Chaîne | Nom du pipeline | `MyPipeline` |
|activityName| Chaîne | Nom de l’activité | `MyActivity` |
|start| Chaîne | Début de l’exécution d’activité dans l’intervalle de temps, au format UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Chaîne | Met fin à l’exécution d’activité dans l’intervalle de temps, au format UTC. Si l’activité n’est pas encore terminée (journal de diagnostic pour une activité qui démarre), une valeur par défaut de `1601-01-01T00:00:00Z` est définie.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Attributs des journaux d’exécution de pipeline

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties": 
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propriété | type | Description | exemples |
| --- | --- | --- | --- |
| Level |Chaîne | Niveau des journaux de diagnostic. Le niveau 4 est celui associé aux journaux d’exécution d’activité. | `4`  |
| correlationId |Chaîne | ID unique pour le suivi d’une demande particulière de bout en bout | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Chaîne | Heure de l’événement dans l’intervalle de temps, au format UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Chaîne| ID de l’exécution de pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| Chaîne | ID de ressource associé pour la ressource de fabrique de données | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Chaîne | Catégorie de journaux de diagnostic. Attribuez à cette propriété la valeur « PipelineRuns » | `PipelineRuns` |
|level| Chaîne | Niveau des journaux de diagnostic. Attribuez à cette propriété la valeur « Informational » | `Informational` |
|operationName| Chaîne |Nom du pipeline avec état. « Pipeline - Succeeded » avec état final quand l’exécution de pipeline arrive à son terme| `MyPipeline - Succeeded` |
|pipelineName| Chaîne | Nom du pipeline | `MyPipeline` |
|start| Chaîne | Début de l’exécution d’activité dans l’intervalle de temps, au format UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Chaîne | Met fin aux exécutions d’activité dans l’intervalle de temps, au format UTC. Si l’activité n’est pas encore terminée (journal de diagnostic pour une activité qui démarre), une valeur par défaut de `1601-01-01T00:00:00Z` est définie.  | `2017-06-26T20:55:29.5007959Z` |
|status| Chaîne | État final de l’exécution de pipeline (Succeeded ou Failed) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Attributs des journaux d’exécution de déclencheur

```json
{ 
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
} 

```

| Propriété | type | Description | exemples |
| --- | --- | --- | --- |
| Level |Chaîne | Niveau des journaux de diagnostic. Défini au niveau 4 pour les journaux d’exécution d’activité. | `4`  |
| correlationId |Chaîne | ID unique pour le suivi d’une demande particulière de bout en bout | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Chaîne | Heure de l’événement dans l’intervalle de temps, au format UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Chaîne| ID de l’exécution du déclencheur | `08587023010602533858661257311` |
|ResourceId| Chaîne | ID de ressource associé pour la ressource de fabrique de données | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Chaîne | Catégorie de journaux de diagnostic. Attribuez à cette propriété la valeur « PipelineRuns » | `PipelineRuns` |
|level| Chaîne | Niveau des journaux de diagnostic. Attribuez à cette propriété la valeur « Informational » | `Informational` |
|operationName| Chaîne |Nom du déclencheur avec état final s’il a été déclenché avec succès. "MyTrigger - Succeeded" si la pulsation a abouti| `MyTrigger - Succeeded` |
|triggerName| Chaîne | Nom du déclencheur | `MyTrigger` |
|triggerType| Chaîne | Type du déclencheur (déclencheur manuel ou déclencheur planifié) | `ScheduleTrigger` |
|triggerEvent| Chaîne | Événement du déclencheur | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Chaîne | Début d’activation du déclencheur dans l’intervalle de temps, au format UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Chaîne | État final indiquant si le déclencheur a été activé avec succès (Succeeded ou Failed) | `Succeeded`|

### <a name="metrics"></a>Mesures

Azure Monitor vous permet d’utiliser la télémétrie pour surveiller les performances et l’intégrité de vos charges de travail sur Azure. Les mesures (aussi appelées compteurs de performances) émises par la plupart des ressources Azure sont le type de données de télémétrie Azure plus important. Azure Monitor propose plusieurs façons de configurer et d’utiliser ces mesures pour l’analyse et le dépannage.

ADFV2 émet les métriques suivantes :

| **Mesure**           | **Nom d’affichage de la métrique**         | **Unité** | **Type d’agrégation** | **Description**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Métriques d’exécutions de pipeline ayant abouti | Count    | Total                | Nombre total d’exécutions de pipeline ayant abouti en une minute |
| PipelineFailedRuns   | Métriques d’exécutions de pipeline ayant échoué    | Count    | Total                | Nombre total d’exécutions de pipeline ayant échoué en une minute    |
| ActiviySucceededRuns | Métriques d’exécutions d’activité ayant abouti | Count    | Total                | Nombre total d’exécutions d’activité ayant abouti en une minute  |
| ActivityFailedRuns   | Métriques d’exécutions d’activité ayant échoué    | Count    | Total                | Nombre total d’exécutions d’activité ayant échoué en une minute     |
| TriggerSucceededRuns | Métriques d’exécutions de déclencheur ayant abouti  | Count    | Total                | Nombre total d’exécutions de déclencheur ayant abouti en une minute   |
| TriggerFailedRuns    | Métriques d’exécutions de déclencheur ayant échoué     | Count    | Total                | Nombre total d’exécutions de déclencheur ayant échoué en une minute      |

Pour accéder aux métriques, suivez les instructions contenues dans l’article suivant : https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics 

## <a name="next-steps"></a>étapes suivantes
Consultez l’article [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md) pour en savoir plus sur la surveillance et la gestion des pipelines en exécutant . 