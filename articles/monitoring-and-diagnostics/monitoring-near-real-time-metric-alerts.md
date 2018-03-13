---
title: "Alertes de métrique en temps quasi réel dans Azure Monitor | Microsoft Docs"
description: "Découvrez comment utiliser les alertes de métrique en temps quasi réel pour surveiller les métriques des ressources Azure avec une fréquence aussi petite que 1 minute."
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
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertes de métrique en temps quasi réel (préversion)
Azure Monitor prend en charge un nouveau type d’alerte appelé alertes de métrique en temps quasi réel (préversion). Cette fonctionnalité est actuellement disponible en préversion publique.

Les alertes de métrique en temps quasi réel diffèrent des alertes de métrique traditionnelles de plusieurs façons :

- **Latence améliorée** : les alertes de métrique en temps quasi réel permettent de surveiller les modifications des valeurs de métrique avec des intervalles aussi fins que 1 minute.
- **Contrôle renforcé des conditions de métrique** : vous pouvez définir des règles d’alerte de métrique en temps quasi réel plus riches. Les alertes prennent en charge la surveillance des valeurs maximales, minimales, moyennes et totales des métriques.
- **Surveillance combinée de plusieurs métriques**: les alertes de métrique en temps quasi réel peuvent surveiller plusieurs métriques (actuellement jusqu’à deux) avec une seule règle. Une alerte est déclenchée si les deux les métriques violent leurs seuils respectifs durant la période spécifiée.
- **Système de notification modulaire** : Les alertes de métrique en temps quasi réel utilisent des [groupes d’actions](monitoring-action-groups.md). Vous pouvez utiliser des groupes d’actions pour créer des actions modulaires. Vous pouvez réutiliser des groupes d’actions pour plusieurs règles d’alerte.

> [!NOTE]
> Les alertes de métrique en temps quasi réel sont actuellement en préversion publique. La fonctionnalité et l’expérience utilisateur sont susceptibles de changer.
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>Ressources que vous pouvez utiliser avec les alertes de métrique en temps quasi réel
Voici la liste complète des types de ressources pris en charge par les alertes de métrique en temps quasi réel :

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

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>Alertes de métrique en temps quasi réel pour les métriques qui utilisent des dimensions
Les alertes de métrique en temps quasi réel prennent en charge la génération d’alertes pour les métriques qui utilisent des dimensions. Vous pouvez utiliser les dimensions pour filtrer votre métrique au niveau approprié. Les alertes de métrique en temps quasi réel associées à des dimensions sont prises en charge pour les types de ressources suivants :

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (uniquement pour les comptes de stockage dans les régions des États-Unis)
* Microsoft.Storage/storageAccounts/services (uniquement pour les comptes de stockage dans les régions des États-Unis)

## <a name="create-a-near-real-time-metric-alert"></a>Créer une alerte de métrique en temps quasi réel
Actuellement, vous pouvez créer des alertes de métrique en temps quasi réel uniquement dans le portail Azure. La configuration d’alertes de métrique en temps quasi réel via PowerShell, Azure CLI et les API REST Azure Monitor sera bientôt prise en charge.

L’expérience de création pour l’alerte de métrique en temps quasi réel a été déplacée vers la nouvelle page **Alertes (préversion)**. Même si la page des alertes actuelles affiche **Ajouter une alerte de métrique en temps quasi réel**, vous êtes redirigé vers la page **Alertes (préversion)**.

Pour savoir comment créer une alerte de métrique en temps quasi réel, consultez [Créer une règle d’alerte dans le portail Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Gestion des alertes de métrique en temps quasi réel
Après avoir créé une alerte de métrique en temps quasi réel, vous pouvez gérer l’alerte à l’aide de la procédure décrite dans [Gérer vos alertes dans le portail Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Schéma de la charge utile

L’opération POST contient le schéma et la charge utile JSON ci-après pour la plupart des alertes basées sur des métriques :

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

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur la nouvelle [expérience Alerts (préversion)](monitoring-overview-unified-alerts.md).
* En savoir plus sur les [alertes de journal dans Azure Alerts (préversion)](monitor-alerts-unified-log.md).
* En savoir plus sur les [alertes dans Azure](monitoring-overview-alerts.md).