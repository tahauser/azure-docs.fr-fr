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
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>Utiliser les alertes métriques les plus récentes pour les services Azure dans le Portail Azure
Azure Monitor prend en charge un nouveau type d’alerte appelé alertes métriques en temps quasi réel. 

Les alertes métriques en temps quasi réel à partir des [alertes métriques classiques](insights-alerts-portal.md) de plusieurs façons :

- **Latence améliorée** : des alertes métriques en temps quasi réel peuvent être exécutées toutes les minutes. Les alertes métriques les plus anciennes s’exécutent toujours à une fréquence de 5 minutes.
- **Prise en charge de plusieurs métriques multidimensionnelles** : vous pouvez avertir sur des métriques dimensionnelles ce qui vous permet d’analyser un segment intéressant de la métrique.
- **Contrôle renforcé des conditions de métrique** : vous pouvez définir des règles d’alerte de métrique en temps quasi réel plus riches. Les alertes prennent en charge la surveillance des valeurs maximales, minimales, moyennes et totales des métriques.
- **Surveillance combinée de plusieurs métriques**: les alertes de métrique en temps quasi réel peuvent surveiller plusieurs métriques (actuellement jusqu’à deux) avec une seule règle. Une alerte est déclenchée si les deux les métriques violent leurs seuils respectifs durant la période spécifiée.
- **Système de notification modulaire** : Les alertes de métrique en temps quasi réel utilisent des [groupes d’actions](monitoring-action-groups.md). Vous pouvez utiliser des groupes d’actions pour créer des actions modulaires. Vous pouvez réutiliser des groupes d’actions pour plusieurs règles d’alerte.
- **Métriques à partir de journaux** : à partir de données de journaux populaires dans [Log Analytics](../log-analytics/log-analytics-overview.md), des métriques peuvent être extraites dans Azure Monitor et alertées en temps quasi réel.


## <a name="metrics-and-dimensions-supported"></a>Métriques et dimensions prises en charge
Les alertes de métrique en temps quasi réel prennent en charge la génération d’alertes pour les métriques qui utilisent des dimensions. Vous pouvez utiliser les dimensions pour filtrer votre métrique au niveau approprié. Toutes les métriques prises en charge, ainsi que les dimensions applicables, peuvent être examinées et visualisés à partir d’[Azure Monitor – Metrics Explorer (préversion)](monitoring-metric-charts.md).

Voici la liste complète des sources de métriques basées sur Azure Monitor qui sont prises en charge pour émettre des alertes métriques en temps quasi réel :

|Type de ressource  |Dimensions prises en charge  | Mesures disponibles|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | OUI        | [Gestion des API](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     OUI   | [Comptes Automation](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| [Comptes Batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/A     |[Cache Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    N/A     | [Machines virtuelles](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |[Groupe de machines virtuelles identiques](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   OUI     |[Fabriques de données V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/A      |[Base de données pour MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/A     | [Base de données pour PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  OUI      |[Concentrateurs d'événements](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     N/A    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/A     | [Application Gateways](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  N/A       |[Adresses IP publiques](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   N/A      |[Services Recherche](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  OUI       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    OUI     | [Comptes de stockage](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     OUI    | [Services blob](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [Services de fichiers](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [Services de file d’attente](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) et [Services de table](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/A       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    N/A     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (Préversion) | OUI|[Espaces de travail Log Analytics](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>Créer une alerte métrique plus récente
Actuellement, vous pouvez créer des alertes métriques plus récentes uniquement dans le Portail Azure ou l’API REST. La prise en charge de la configuration d’alertes métriques en temps quasi réel en utilisant PowerShell, l’interface de ligne de commande Azure (Azure CLI) est pour bientôt.

Pour savoir comment créer une alerte métrique plus récente dans le Portail Azure, consultez [Créer une règle d’alerte dans le Portail Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-newer-metric-alerts"></a>Gérer des alertes métriques plus récente
Après avoir créé une alerte de métrique en temps quasi réel, vous pouvez gérer l’alerte à l’aide de la procédure décrite dans [Gérer vos alertes dans le portail Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>Prendre en charge des journaux OMS comme métriques pour les alertes

Vous pouvez également utiliser des alertes métriques en temps quasi réel sur des journaux OMS populaires extraits comme des métriques dans le cadre des Métriques de la préversion des Journaux.  
- [Les compteurs de performance](../log-analytics/log-analytics-data-sources-performance-counters.md) pour les machines Windows et Linux
- [Enregistrements de pulsations pour Agent Health](../operations-management-suite/oms-solution-agenthealth.md)
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
> La métrique et/ou la dimension spécifique ne s’affichera que si des données correspondantes existent pour la période choisie. Ces métriques sont disponibles pour les clients avec les espaces de travail de l’est des États-Unis, ouest du centre des États-Unis et Europe de l’Ouest qui ont souscrit à la préversion. Si vous souhaitez faire partie de cette préversion, inscrivez-vous à l’aide de [l’enquête](https://aka.ms/MetricLogPreview).


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

* En savoir plus sur la nouvelle [expérience Alertes](monitoring-overview-unified-alerts.md).
* En savoir plus sur les [alertes de journal dans Azure](monitor-alerts-unified-log.md).
* En savoir plus sur les [alertes dans Azure](monitoring-overview-alerts.md).
