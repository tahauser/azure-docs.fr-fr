---
title: Alertes de métrique en temps quasi réel dans Azure Monitor | Microsoft Docs
description: Découvrez comment utiliser les alertes de métrique en temps quasi réel pour surveiller les métriques des ressources Azure avec une fréquence aussi petite que 1 minute.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertes de métrique en temps quasi réel (préversion)
Azure Monitor prend en charge un nouveau type d’alerte appelé alertes de métrique en temps quasi réel (préversion). Cette fonctionnalité est actuellement disponible en préversion publique.

Les alertes de métrique en temps quasi réel diffèrent des alertes de métrique traditionnelles de plusieurs façons :

- **Latence améliorée** : les alertes de métrique en temps quasi réel permettent de surveiller les modifications des valeurs de métrique à des intervalles d’une minute seulement.
- **Contrôle renforcé des conditions de métrique** : vous pouvez définir des règles d’alerte de métrique en temps quasi réel plus riches. Les alertes prennent en charge la surveillance des valeurs maximales, minimales, moyennes et totales des métriques.
- **Métriques basées sur des journaux** : à partir de données de journaux populaires dans [Log Analytics](../log-analytics/log-analytics-overview.md), des métriques peuvent être extraites dans Azure Monitor et alertées en temps quasi réel
- **Surveillance combinée de plusieurs métriques**: les alertes de métrique en temps quasi réel peuvent surveiller plusieurs métriques (actuellement jusqu’à deux) avec une seule règle. Une alerte est déclenchée si les deux les métriques violent leurs seuils respectifs durant la période spécifiée.
- **Système de notification modulaire** : Les alertes de métrique en temps quasi réel utilisent des [groupes d’actions](monitoring-action-groups.md). Vous pouvez utiliser des groupes d’actions pour créer des actions modulaires. Vous pouvez réutiliser des groupes d’actions pour plusieurs règles d’alerte.

> [!NOTE]
> L’alerte métrique quasiment en temps réel est actuellement en préversion publique. Et les métriques basées sur des fonctionnalités de journaux se trouvent dans la préversion publique *limitée*. La fonctionnalité et l’expérience utilisateur sont susceptibles de changer.
>

## <a name="metrics-and-dimensions-supported"></a>Métriques et dimensions prises en charge
Les alertes de métrique en temps quasi réel prennent en charge la génération d’alertes pour les métriques qui utilisent des dimensions. Vous pouvez utiliser les dimensions pour filtrer votre métrique au niveau approprié. Toutes les métriques prises en charge, ainsi que les dimensions applicables, peuvent être examinées et visualisés à partir d’[Azure Monitor – Metrics Explorer (préversion)](monitoring-metric-charts.md).

Voici la liste complète des sources de métriques basées sur Azure Monitor qui sont prises en charge pour émettre des alertes métriques en temps quasi réel :

|Nom de la métrique/Détails  |Dimensions prises en charge  |
|---------|---------|
|Microsoft.ApiManagement/service     | OUI        |
|Microsoft.Automation/automationAccounts     |     N/A    |
|Microsoft.Automation/automationAccounts     |   N/A      |
|Microsoft.Cache/Redis     |    N/A     |
|Microsoft.Compute/virtualMachines     |    N/A     |
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |
|Microsoft.DataFactory/factories     |   N/A      |
|Microsoft.DBforMySQL/servers     |   N/A      |
|Microsoft.DBforPostgreSQL/servers     |    N/A     |
|Microsoft.EventHub/namespaces     |   N/A      |
|Microsoft.Logic/workflows     |     N/A    |
|Microsoft.Network/applicationGateways     |    N/A     |
|Microsoft.Network/publicipaddresses     |  N/A       |
|Microsoft.Search/searchServices     |   N/A      |
|Microsoft.ServiceBus/namespaces     |  N/A       |
|Microsoft.Storage/storageAccounts     |    OUI     |
|Microsoft.Storage/storageAccounts/services     |     OUI    |
|Microsoft.StreamAnalytics/streamingjobs     |  N/A       |
|Microsoft.CognitiveServices/accounts     |    N/A     |


Les métriques basées sur des journaux prennent actuellement en charge les journaux OMS populaires suivants :
- [Les compteurs de performance](../log-analytics/log-analytics-data-sources-performance-counters.md) pour les machines Windows et Linux
- Enregistrements de pulsations d’inventaire pour les machines
- Enregistrements de la [gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md)

Voici la liste complète des sources de métriques basées sur le journal OMS qui sont prises en charge pour émettre des alertes métriques en temps quasi réel :

Nom de la métrique/Détails  |Dimensions prises en charge  | Type de journal  |
|---------|---------|---------|
|Average_Avg. Disk sec/Read     |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
| Average_Avg. Disk sec/Write     |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
| Longueur de file d’attente du disque Average_Current   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
| Average_Disk Reads/sec    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
| Average_Disk Transfers/sec    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
|   Average_% Free Space    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
| Average_Available MBytes     |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
| Octets validés en cours d’utilisation Average_%    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
| Average_Bytes Received/sec    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
|  Average_Bytes Sent/sec    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
|  Average_Bytes Total/sec    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
|  Temps processeur Average_%    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
|   Longueur de la file d’attente Average_Processor    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Windows      |
|   Inodes libres Average_%   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Free Space   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Inodes utilisés Average_%  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Inodes utilisés Average_%   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Octets de lecture/s Average_Disk    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Disk Reads/sec |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Disk Transfers/sec |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Octets d’écriture/s Average_Disk   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Disk Writes/sec    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Mégaoctets Average_Free |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Logical Disk Bytes/sec |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Available Memory |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Available Swap Space |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Used Memory  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Used Swap Space  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Available MBytes Memory    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Available MBytes Swap  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Page Reads/sec |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Page Writes/sec    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Pages/sec  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Used MBytes Swap Space |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Used Memory MBytes |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Total Bytes Transmitted    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Total Bytes Received   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Total Bytes    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Total Packets Transmitted  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Total Bytes Received |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Total Rx Errors    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Total Rx Errors    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Total Collisions   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Avg. Disk sec/Read |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Avg. Disk sec/Transfer |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Avg. Disk sec/Write    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Physical Disk Bytes/sec    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Pct Privileged Time    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Pct User Time  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Used Memory MBytes |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Virtual Shared Memory  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% DPC Time |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Idle Time    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Interrupt Time   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% IO Wait Time |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Nice Time    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% Privileged Time  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Temps processeur Average_%   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_% User Time    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Free Physical Memory   |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Free Space in Paging Files |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Free Virtual Memory    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Processes  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Size Stored In Paging Files    |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Uptime |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Average_Users  |     Oui – Computer, ObjectName, InstanceName, CounterPath et SourceSystem    |   Compteur de performances Linux      |
|    Heartbeat  |     Oui – Computer, OSType, Version et SourceComputerId    |   Enregistrements de pulsation |
|    Mettre à jour |     Oui - Computer, Product, Classification, UpdateState, facultatif et approuvée    |   Update Management |

> [!NOTE]
> La métrique et/ou la dimension spécifique ne s’affichera que si des données correspondantes existent pour la période choisie

## <a name="create-a-near-real-time-metric-alert"></a>Créer une alerte de métrique en temps quasi réel
Actuellement, vous pouvez créer des alertes de métrique en temps quasi réel uniquement dans le portail Azure. La configuration d’alertes de métrique en temps quasi réel via PowerShell, Azure CLI et les API REST Azure Monitor sera bientôt prise en charge.

L’expérience de création pour l’alerte de métrique en temps quasi réel a été déplacée vers la nouvelle page **Alertes (préversion)**. Même si la page des alertes actuelles affiche **Ajouter une alerte de métrique en temps quasi réel**, vous êtes redirigé vers la page **Alertes (préversion)**.

Pour savoir comment créer une alerte de métrique en temps quasi réel, consultez [Créer une règle d’alerte dans le portail Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Gestion des alertes de métrique en temps quasi réel
Après avoir créé une alerte de métrique en temps quasi réel, vous pouvez gérer l’alerte à l’aide de la procédure décrite dans [Gérer vos alertes dans le portail Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Schéma de la charge utile

L’opération POST contient le schéma et la charge utile JSON ci-après pour toutes les alertes en temps quasi réel basées sur des métriques lorsqu’un [groupe d’action](monitoring-action-groups.md) configuré de manière appropriée est utilisé :

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur la nouvelle [expérience Alerts (préversion)](monitoring-overview-unified-alerts.md).
* En savoir plus sur les [alertes de journal dans Azure Alerts (préversion)](monitor-alerts-unified-log.md).
* En savoir plus sur les [alertes dans Azure](monitoring-overview-alerts.md).
