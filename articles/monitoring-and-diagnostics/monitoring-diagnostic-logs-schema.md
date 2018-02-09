---
title: "Services et schémas pris en charge par les journaux de diagnostic Azure | Microsoft Docs"
description: "Découvrez les services pris en charge et le schéma d’événement pour les journaux de diagnostic Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/24/2018
ms.author: johnkem
ms.openlocfilehash: 3e2b5305b969c96e6b14122af03da9249373094a
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure

Les [journaux de diagnostic des ressources Azure](monitoring-overview-of-diagnostic-logs.md) sont des journaux générés par vos ressources Azure qui décrivent le fonctionnement de cette ressource. Ces journaux sont spécifiques au type de ressource. Dans cet article, nous présentons l’ensemble des services pris en charge et le schéma d’événement des événements générés par chaque service. Cet article inclut également une liste complète des catégories de journal disponibles par type de ressource.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Services et schémas pris en charge pour les journaux de diagnostic des ressources
Le schéma des journaux de diagnostic des ressources varie en fonction de la ressource et de la catégorie de journal.   

| de diffusion en continu | Schéma et documentation |
| --- | --- |
| Analysis Services | Schéma non disponible. |
| API Management | [Journaux de diagnostic Gestion des API](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Passerelles d’application |[Journalisation des diagnostics pour Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pour Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Journalisation des diagnostics Azure Batch](../batch/batch-diagnostics.md) |
| Insights client | Schéma non disponible. |
| Réseau de distribution de contenu | Schéma non disponible. |
| CosmosDB | [Journalisation Azure Cosmos DB](../cosmos-db/logging.md) |
| Data Lake Analytics |[Accès aux journaux de diagnostic d’Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Accès aux journaux de diagnostic d’Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Journaux de diagnostic d’Azure Event Hubs](../event-hubs/event-hubs-diagnostic-logs.md) |
| IoT Hub | [Opérations IoT Hub](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Journalisation d’Azure Key Vault](../key-vault/key-vault-logging.md) |
| Équilibreur de charge |[Analytique des journaux de l'équilibreur de charge Azure](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Schéma de suivi personnalisé Logic Apps B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Group |[Analyse de journaux pour les groupes de sécurité réseau (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Protection DDOS | Schéma non disponible. |
| Recovery Services | [Modèle de données pour la sauvegarde Azure](../backup/backup-azure-reports-data-model.md)|
| action |[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](../search/search-traffic-analytics.md) |
| Gestion de serveur | Schéma non disponible. |
| Service Bus |[Journaux de diagnostic Azure Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Base de données SQL | [Journalisation de diagnostic de base de données SQL Azure](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Journaux de diagnostic des travaux](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Virtual Network | Schéma non disponible. |

## <a name="supported-log-categories-per-resource-type"></a>Catégories de journaux prises en charge par type de ressource
|Type de ressource|Catégorie|Nom d’affichage de la catégorie|
|---|---|---|
|Microsoft.AnalysisServices/servers|Engine (Moteur)|Engine (Moteur)|
|Microsoft.AnalysisServices/servers|de diffusion en continu|de diffusion en continu|
|Microsoft.ApiManagement/service|GatewayLogs|Journaux relatifs à la passerelle ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Journaux de travail|
|Microsoft.Automation/automationAccounts|JobStreams|Flux de travail|
|Microsoft.Automation/automationAccounts|DscNodeStatus|État du nœud DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Journaux de service|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtient les métriques du point de terminaison, par exemple, la bande passante, la sortie, etc.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Journal d’exécutions d’activités de pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Journal d’exécutions de pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Journal d’exécutions de déclencheur|
|Microsoft.DataLakeAnalytics/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeAnalytics/accounts|Requêtes|Journaux de requête|
|Microsoft.DataLakeStore/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeStore/accounts|Requests|Journaux de requête|
|Microsoft.Devices/IotHubs|connexions|connexions|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Télémétrie d’appareil|
|Microsoft.Devices/IotHubs|C2DCommands|Commandes C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Opérations d’identité des appareils|
|Microsoft.Devices/IotHubs|FileUploadOperations|Opérations de chargement de fichier|
|Microsoft.Devices/IotHubs|Itinéraires|Itinéraires|
|Microsoft.Devices/IotHubs|Opérations de jumeaux D2C|Opérations de jumeaux D2C|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Opérations de jumeaux C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Requêtes de jumeaux|
|Microsoft.Devices/IotHubs|JobsOperations|Opérations de travaux|
|Microsoft.Devices/IotHubs|DirectMethods|Méthodes directes|
|Microsoft.Devices/provisioningServices|DeviceOperations|Opérations sur les appareils|
|Microsoft.Devices/provisioningServices|ServiceOperations|Opérations de service|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Journaux d’archivage|
|Microsoft.EventHub/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Journaux de mise à l’échelle automatique|
|Microsoft.KeyVault/vaults|AuditEvent|Journaux d’audit|
|Microsoft.Logic/workflows|WorkflowRuntime|Événements de diagnostic de runtime de workflow|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Suivi des événements de compte d’intégration|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Événement de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Compteur de règle de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Événement de flux de règle de groupe de sécurité réseau|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Événements d’alerte d’équilibreur de charge|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|État d’intégrité de la sonde d’équilibreur de charge|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notifications de protection DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertes de protection de machine virtuelle|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Journal d’accès à la passerelle d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Journal de performance de la passerelle d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Journal de pare-feu de la passerelle d’application|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Journaux de diagnostic de passerelle|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Journaux de diagnostic de tunnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Journaux de diagnostic d’itinéraires|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Événement de résultats d’intégrité de sondage Traffic Manager|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Table des compteurs GWM|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Données de rapport de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Travaux Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Événements Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Éléments répliqués d’Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Statistiques de réplication Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Points de récupération Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taux de chargement de données de réplication Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Activité des données de disque protégé Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Journaux des opérations|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statistiques d’exécution du magasin de données des requêtes|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statistiques d’attente du magasin de requêtes|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statistiques d’attente de base de données|
|Microsoft.Sql/servers/databases|Délais d’expiration|Délais d’expiration|
|Microsoft.Sql/servers/databases|Blocs|Blocs|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|Audit|Journaux d’audit|
|Microsoft.StreamAnalytics/streamingjobs|Exécution|Exécution|
|Microsoft.StreamAnalytics/streamingjobs|Création|Création|

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md)
* [Diffuser en continu les journaux de diagnostic des ressources vers **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modifier les paramètres de diagnostic des ressources via l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md)
