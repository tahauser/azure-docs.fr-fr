---
title: Types de ressource pris en charge par Azure Resource Health | Microsoft Docs
description: Types de ressource pris en charge par Azure Resource Health
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 0694c45363eb2534416f1dad59196ecfa1dacd17
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Types de ressources et les contrôles d’intégrité dans Azure Resource Health
Voici une liste complète de toutes les vérifications exécutées via Resource Health par type de ressource.

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Vérifications exécutées|
|---|
|<ul><li>Est-ce que le service Gestion des API est opérationnel ?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Vérifications exécutées|
|---|
|<ul><li>Tous les nœuds du cache sont-ils en cours d’exécution ?</li><li>Le cache peut-il être joint à partir du centre de données ?</li><li>Le cache a-t-il atteint le nombre maximal de connexions ?</li><li> Le cache a-t-il épuisé sa mémoire disponible ? </li><li>Le cache rencontre-t-il un grand nombre de défauts de page ?</li><li>Le cache subit-il une charge lourde ?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Vérifications exécutées|
|---|
|<ul> <li>Un point de terminaison est-il arrêté, supprimé ou mal configuré ?</li><li>Le portail supplémentaire est-il accessible pour les opérations de configuration CDN ?</li><li>Existe-t-il des problèmes de livraison continue avec les points de terminaison CDN ?</li><li>Les utilisateurs peuvent-ils modifier la configuration de leurs ressources CDN ?</li><li>Les modifications de configuration sont-elles propagées au rythme attendu ?</li><li>Les utilisateurs peuvent-ils gérer la configuration du CDN via le portail Azure, PowerShell ou l’API ?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Vérifications exécutées|
|---|
|<ul><li>Le serveur hôte est-il en cours d’exécution ?</li><li>Le démarrage du système d’exploitation hôte est-il terminé ?</li><li>Le conteneur de machine virtuelle est-il configuré et sous tension ?</li><li>Existe-t-il une connectivité réseau entre l’hôte et le compte de stockage ?</li><li>Le démarrage du système d’exploitation invité est-il terminé ?</li><li>Y a-t-il une maintenance planifiée régulière ?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Vérifications exécutées|
|---|
|<ul><li>Le compte peut-il être joint à partir du centre de données ?</li><li>Le fournisseur de ressources Cognitive Services est-il disponible ?</li><li>Cognitive Service est-il disponible dans la région appropriée ?</li><li>Les opérations de lecture peuvent-elles être effectuées sur le compte de stockage contenant les métadonnées des ressources ?</li><li>Le quota d’appels à l’API a-t-il été atteint ?</li><li>La limite de lecture d’appels à l’API a-t-elle été atteinte ?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Vérifications exécutées|
|---|
|<ul><li>Est-ce que le serveur d’hébergement de cette machine virtuelle est en cours d’exécution ?</li><li>Le démarrage du système d’exploitation hôte est-il terminé ?</li><li>Le conteneur de machine virtuelle est-il configuré et sous tension ?</li><li>Existe-t-il une connectivité réseau entre l’hôte et le compte de stockage ?</li><li>Le démarrage du système d’exploitation invité est-il terminé ?</li><li>Y a-t-il une maintenance planifiée régulière ?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Vérifications exécutées|
|---|
|<ul><li>Les utilisateurs peuvent-ils envoyer des travaux à Data Lake Analytics dans la région ?</li><li>Est-ce que les travaux s’exécutent et se terminent correctement dans la région ?</li><li>Les utilisateurs peuvent-ils répertorier des éléments de catalogue dans la région ?</li>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Vérifications exécutées|
|---|
|<ul><li>Les utilisateurs peuvent-ils télécharger des données vers Data Lake Store dans la région ?</li><li>Les utilisateurs peuvent-ils télécharger des données à partir de Data Lake Store dans la région ?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Vérifications exécutées|
|---|
|<ul><li>Le hub IoT est-il opérationnel ?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Vérifications exécutées|
|---|
|<ul><li>Y a-t-il eu des demandes de base de données ou de collection non servies en raison d’une indisponibilité du service Azure Cosmos DB ?</li><li>Y a-t-il eu des demandes de document non servies en raison d’une indisponibilité du service Azure Cosmos DB ?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Vérifications exécutées|
|---|
|<ul><li>Le tunnel VPN est-il connecté ?</li><li>Existe-t-il des conflits de configuration dans la connexion ?</li><li>Les clés prépartagées sont-elles correctement configurées ?</li><li>L’appareil VPN local est-il accessible ?</li><li>Existe-t-il des non-correspondances dans la stratégie de sécurité IPSec/IKE ?</li><li>La connexion VPN S2S est-elle correctement configurée ou dans un état d’échec ?</li><li>La connexion de réseau virtuel à réseau virtuel est-elle correctement configurée ou dans un état d’échec ?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Vérifications exécutées|
|---|
|<ul><li>La passerelle VPN est-elle accessible à partir d’internet ?</li><li>La passerelle VPN est-elle en mode veille ?</li><li>Le service VPN est-il en cours d’exécution sur la passerelle ?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Vérifications exécutées|
|---|
|<ul><li> Les opérations de runtime comme l’inscription, l’installation ou l’envoi peuvent-elles être effectuées sur l’espace de noms ?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Vérifications exécutées|
|---|
|<ul><li>Le système d’exploitation hôte est-il en cours d’exécution ?</li><li>WorkspaceCollection est-elle accessible depuis l’extérieur du centre de données ?</li><li>Le fournisseur de ressources PowerBI est-il disponible ?</li><li>Le service PowerBI est-il disponible dans la région appropriée ?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Vérifications exécutées|
|---|
|<ul><li>Les opérations de diagnostic peuvent-elles être exécutées sur le cluster ?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Vérifications exécutées|
|---|
|<ul><li> Y a-t-il eu des connexions à la base de données ?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Vérifications exécutées|
|---|
|<ul><li>Tous les hôtes sur lesquels le travail est exécuté sont-ils en cours d’exécution ?</li><li>Le travail a-t-il échoué à démarrer ?</li><li>Y a-t-il des mises à niveau du runtime en cours ?</li><li>Le travail est-il dans un état prévu (par exemple en cours d’exécution ou arrêtée par le client) ?</li><li>Le travail a-t-il rencontré des exceptions de mémoire insuffisante ?</li><li>Y a-t-il des mises à jour de calcul planifiées en cours ?</li><li>Le Gestionnaire d’exécution (plan de contrôle) est-il disponible ?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Vérifications exécutées|
|---|
|<ul><li>Le serveur hôte est-il en cours d’exécution ?</li><li>Internet Information Services (IIS) est-il en cours d’exécution ?</li><li>L’équilibreur de charge est-il en cours d’exécution ?</li><li>Le plan de service web peut-il être joint à partir du centre de données ?</li><li>Le compte de stockage hébergeant le contenu des sites de la batterie de serveurs est-il disponible ?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Vérifications exécutées|
|---|
|<ul><li>Le serveur hôte est-il en cours d’exécution ?</li><li>Le serveur Internet Information est-il en cours d’exécution ?</li><li>L’équilibreur de charge est-il en cours d’exécution ?</li><li>L’application web peut-elle être jointe à partir du centre de données ?</li><li>Le compte de stockage hébergeant le contenu du site est-il disponible ?</li></ul>|

# <a name="next-steps"></a>Étapes suivantes
-  Pour en savoir plus à leur sujet, voir [Présentation du tableau de bord d’Azure Service Health](service-health-overview.md) et [Présentation d’Azure Resource Health](resource-health-overview.md). 
-  [Forum aux questions sur Azure Resource Health](resource-health-faq.md)
- Configurez des alertes afin d’être averti des problèmes d’intégrité. Pour plus d’informations, voir [Configurer des alertes pour les événements d’intégrité du service](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 