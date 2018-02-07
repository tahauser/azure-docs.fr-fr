---
title: "Alertes Métrique en temps quasi réel dans Azure Monitor | Microsoft Docs"
description: "Les alertes Métrique en temps quasi réel permettent de surveiller des métriques de ressources Azure jusqu’à une fréquence de 1 minute."
author: snehithm
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
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertes Métrique en temps quasi réel (préversion)
Azure Monitor prend désormais en charge un nouveau type d’alertes Métrique appelées alertes Métrique en temps quasi réel (préversion). Cette fonctionnalité est actuellement disponible en préversion publique.
Ces alertes diffèrent des alertes Métrique régulières à plusieurs égards :

- **Latence améliorée** : les alertes Métrique en temps quasi réel permettent de surveiller les modifications des valeurs de métriques jusqu’à des intervalles de 1 minute.
- **Contrôle renforcé des conditions de métrique** : les alertes Métrique en temps quasi réel permettent aux utilisateurs de définir des règles d’alerte plus riches. Les alertes prennent désormais en charge la surveillance des valeurs maximales, minimales, moyennes et totales des métriques.
- **Surveillance combinée de plusieurs métriques** : les alertes Métrique en temps quasi réel peuvent surveiller plusieurs métriques (actuellement deux) avec une seule règle. Une alerte est déclenchée si les deux les métriques violent leurs seuils respectifs durant la période spécifiée.
- **Système de notification modulaire** : les alertes Métrique en temps quasi réel utilisent des [groupes d’actions](monitoring-action-groups.md). Cette fonctionnalité permet aux utilisateurs de créer des actions sous forme modulaire, et de les réutiliser pour plusieurs règles d’alerte.

> [!NOTE]
> Les alertes Métrique en temps quasi réel sont actuellement disponible en préversion publique. Leur fonctionnalité et l’expérience utilisateur sont susceptibles de changer.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Pour quelles ressources puis-je créer près des alertes Métrique en temps quasi réel ?
Voici la liste complète des types de ressources pris en charge par les alertes Métrique en temps quasi réel :

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>Alertes Métrique en temps quasi réel sur les métriques associées à des dimensions
Les alertes Métrique en temps quasi réel prennent en charge la génération d’alertes sur les métriques associées à des dimensions. Les dimensions permettent de filtrer votre métrique au niveau approprié. Les alertes Métrique en temps quasi réel sur les métriques associées à des dimensions sont prises en charge pour les types de ressources suivants :

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (uniquement pour les comptes de stockage dans les régions des États-Unis)
* Microsoft.Storage/storageAccounts/services (uniquement pour les comptes de stockage dans les régions des États-Unis)


## <a name="create-a-near-real-time-metric-alert"></a>Créer une alerte Métrique en temps quasi réel
Actuellement, il n’est possible de créer des alertes Métrique en temps quasi réel que via le portail Azure. La configuration d’alertes Métrique en temps quasi réel via PowerShell, Azure CLI et l’API REST Azure Monitor sera bientôt prise en charge.

L’expérience de création pour l’alerte Métrique en temps quasi réel a été déplacée vers la nouvelle expérience **Alerts (préversion)**. Même si la page actuelle des alertes comporte une option pour **ajouter une alerte Métrique en temps quasi réel**, vous êtes redirigé vers la nouvelle expérience.

Vous pouvez créer une alerte Métrique en temps quasi réel à l’aide de la procédure décrite [ici](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Gestion des alertes Métrique en temps quasi réel
Une fois que vous avez créé une **alerte Métrique en temps quasi réel**, vous pouvez la gérer en suivant la procédure décrite [ici](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Schéma de la charge utile

L’opération POST contient le schéma et la charge utile JSON ci-après pour la plupart des alertes basées sur des métriques.

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>étapes suivantes

* [En savoir plus sur la nouvelle expérience Alerts (préversion)](monitoring-overview-unified-alerts.md)
* [En savoir plus sur les alertes de journal dans Azure Alerts (préversion)](monitor-alerts-unified-log.md)
* [En savoir plus sur les alertes dans Azure](monitoring-overview-alerts.md)