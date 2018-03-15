---
title: "Métriques Azure Monitor : métriques prises en charge par type de ressource | Microsoft Docs"
description: "Liste des métriques disponibles pour chaque type de ressource avec Azure Monitor."
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/31/2018
ms.author: ancav
ms.openlocfilehash: 97dca282bd7bbf00ce1d03899f6de0444a41163a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="supported-metrics-with-azure-monitor"></a>Métriques prises en charge avec Azure Monitor
Azure Monitor offre plusieurs moyens d’interagir avec les métriques, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec PowerShell ou l’interface CLI. Voici une liste complète de toutes les métriques actuellement offertes par le pipeline de métrique d’Azure Monitor.

> [!NOTE]
> D’autres métriques peuvent être disponibles dans le portail ou via les API héritées. Cette liste ne comprend que les métriques disponibles en utilisant le pipeline de métriques Azure Monitor consolidé. Pour rechercher et accéder aux métriques avec des dimensions, veuillez utiliser [2017-05-01-preview api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Moyenne|QPU. Plage de 0 à 100 pour S1, de 0 à 200 pour S2 et de 0 à 400 pour S4|ServerResourceType|
|memory_metric|Mémoire|Octets|Moyenne|Mémoire. Plage de 0 à 25 Go pour S1, de 0 à 50 Go pour S2 et de 0 à 100 Go pour S4|ServerResourceType|
|TotalConnectionRequests|Nombre total de demandes de connexion|Count|Moyenne|Nombre total de demandes de connexion. Il s’agit des arrivées.|ServerResourceType|
|SuccessfullConnectionsPerSec|Connexions réussies par seconde|CountPerSecond|Moyenne|Taux de connexions terminées réussies.|ServerResourceType|
|TotalConnectionFailures|Nombre total d’échecs de connexion|Count|Moyenne|Total des échecs de tentatives de connexion.|ServerResourceType|
|CurrentUserSessions|Sessions utilisateur actuelles|Count|Moyenne|Nombre actuel de sessions utilisateur établies.|ServerResourceType|
|QueryPoolBusyThreads|Threads occupés du pool de threads de requêtes|Count|Moyenne|Nombre de threads occupés dans le pool de threads de requêtes.|ServerResourceType|
|CommandPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de commandes|Count|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de commandes.|ServerResourceType|
|ProcessingPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de traitement|Count|Moyenne|Nombre de travaux autres que d’E/S contenus dans la file d’attente du pool de threads de traitement.|ServerResourceType|
|CurrentConnections|Connexion : connexions actuelles|Count|Moyenne|Nombre actuel de connexions client établies.|ServerResourceType|
|CleanerCurrentPrice|Mémoire : prix actuel du nettoyage|Count|Moyenne|Prix actuel de la mémoire, $/octet/temps, normalisé à 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Mémoire : mémoire de nettoyage réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui doit être vidée par le nettoyage en arrière-plan.|ServerResourceType|
|CleanerMemoryNonshrinkable|Mémoire : mémoire de nettoyage non réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui ne doit pas être vidée par le nettoyage en arrière-plan.|ServerResourceType|
|MemoryUsage|Mémoire : utilisation de la mémoire|Octets|Moyenne|Utilisation de la mémoire du processus serveur telle qu’utilisée dans le calcul du coût de la mémoire de nettoyage. Équivaut au compteur Process\PrivateBytes, plus la taille des données mappées en mémoire, en ignorant la mémoire mappée ou allouée par le moteur d’analyse de mémoire xVelocity (VertiPaq) dépassant la limite de mémoire du moteur xVelocity.|ServerResourceType|
|MemoryLimitHard|Mémoire : limite de mémoire physique|Octets|Moyenne|Limite de mémoire physique, du fichier de configuration.|ServerResourceType|
|MemoryLimitHigh|Mémoire : limite de mémoire élevée|Octets|Moyenne|Limite de mémoire élevée, du fichier de configuration.|ServerResourceType|
|MemoryLimitLow|Mémoire : limite de mémoire basse|Octets|Moyenne|Limite de mémoire basse, du fichier de configuration.|ServerResourceType|
|MemoryLimitVertiPaq|Mémoire : limite de mémoire VertiPaq|Octets|Moyenne|Limite en mémoire, du fichier de configuration.|ServerResourceType|
|Quota|Mémoire : quota|Octets|Moyenne|Quota de mémoire actuel, en octets. Le quota de mémoire est également appelé réserve de mémoire ou d’allocation.|ServerResourceType|
|QuotaBlocked|Mémoire : quota bloqué|Count|Moyenne|Nombre actuel de requêtes de quota qui sont bloquées en attendant la libération d’autres quotas de mémoire.|ServerResourceType|
|VertiPaqNonpaged|Mémoire : réserve non paginée VertiPaq|Octets|Moyenne|Octets de mémoire verrouillée dans la plage de travail pour utilisation par le moteur en mémoire.|ServerResourceType|
|VertiPaqPaged|Mémoire : réserve paginée VertiPaq|Octets|Moyenne|Octets de mémoire paginée utilisée pour les données en mémoire.|ServerResourceType|
|RowsReadPerSec|Traitement : lignes lues par seconde|CountPerSecond|Moyenne|Taux de lignes lues à partir de toutes les bases de données relationnelles.|ServerResourceType|
|RowsConvertedPerSec|Traitement : lignes converties par seconde|CountPerSecond|Moyenne|Taux de lignes converties lors du traitement.|ServerResourceType|
|RowsWrittenPerSec|Traitement : lignes écrites par seconde|CountPerSecond|Moyenne|Taux de lignes écrites lors du traitement.|ServerResourceType|
|CommandPoolBusyThreads|Threads : threads occupés du pool commandes|Count|Moyenne|Nombre de threads occupés dans le pool de threads de commandes.|ServerResourceType|
|CommandPoolIdleThreads|Threads : threads inactifs du pool commande|Count|Moyenne|Nombre de threads inactifs dans le pool de threads de commandes.|ServerResourceType|
|LongParsingBusyThreads|Threads : threads d’analyse longue occupés|Count|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse longue.|ServerResourceType|
|LongParsingIdleThreads|Threads : threads d’analyse longue inactifs|Count|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse longue.|ServerResourceType|
|LongParsingJobQueueLength|Threads : durée de file d’attente des travaux d’analyse longue|Count|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse longue.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads : traitement des threads de travail d’E/S occupés du pool|Count|Moyenne|Nombre de threads pour les travaux d’E/S en cours d’exécution dans le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads : traitement des threads de travail autres qu’E/S occupés du pool|Count|Moyenne|Nombre de threads pour les travaux autres que d’E/S en cours d’exécution dans le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads : longueur de la file d’attente des travaux d’E/S du pool de traitement|Count|Moyenne|Nombre de travaux d’E/S contenus dans la file d’attente du pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads : traitement des threads de travail d’E/S ignorés du pool|Count|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads : traitement des threads de travail autres qu’E/S inactifs du pool|Count|Moyenne|Nombre de threads inactifs le pool de threads de traitement dédiés aux travaux autres qu’E/S.|ServerResourceType|
|QueryPoolIdleThreads|Threads : threads inactifs du pool de requêtes|Count|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|ServerResourceType|
|QueryPoolJobQueueLength|Threads : longueur de file d’attente de travaux du pool de requêtes|Count|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|ServerResourceType|
|ShortParsingBusyThreads|Threads : threads d’analyse courte occupés|Count|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse courte.|ServerResourceType|
|ShortParsingIdleThreads|Threads : threads d’analyse courte inactifs|Count|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse courte.|ServerResourceType|
|ShortParsingJobQueueLength|Threads : durée de file d’attente des travaux d’analyse courte|Count|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse courte.|ServerResourceType|
|memory_thrashing_metric|Vidage de mémoire|Pourcentage|Moyenne|Vidage de mémoire moyenne.|ServerResourceType|
|mashup_engine_qpu_metric|QPU du moteur M|Count|Moyenne|Utilisation des QPU par les processus de moteur mashup|ServerResourceType|
|mashup_engine_memory_metric|Mémoire du moteur M|Octets|Moyenne|Utilisation de la mémoire par les processus de moteur mashup|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|Nombre total de demandes de la passerelle|Count|Total|Nombre de demandes de la passerelle|Emplacement, nom d’hôte|
|SuccessfulRequests|Demandes de la passerelle ayant abouti|Count|Total|Nombre de demandes de la passerelle ayant abouti|Emplacement, nom d’hôte|
|UnauthorizedRequests|Demandes de la passerelle non autorisées|Count|Total|Nombre de demandes de la passerelle non autorisées|Emplacement, nom d’hôte|
|FailedRequests|Demandes de la passerelle ayant échoué|Count|Total|Nombre de défaillances des demandes de la passerelle|Emplacement, nom d’hôte|
|OtherRequests|Autres demandes de la passerelle|Count|Total|Nombre d’autres demandes de la passerelle|Emplacement, nom d’hôte|
|Duration|Durée globale des demandes de passerelle|Millisecondes|Moyenne|Durée globale des demandes de passerelle en millisecondes|Emplacement, nom d’hôte|
|Capacity|Capacité (préversion)|Pourcentage|Maximale|Métrique d’utilisation pour le service ApiManagement|Lieu|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalJob|Nombre total de travaux|Count|Total|Nombre total de travaux|RunbookName, Status|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CoreCount|Nombre de cœurs dédiés|Count|Total|Nombre total de cœurs dédiés dans le compte Batch|Aucune dimension|
|TotalNodeCount|Nombre de nœuds dédiés|Count|Total|Nombre total de nœuds dédiés dans le compte Batch|Aucune dimension|
|LowPriorityCoreCount|Nombre de cœurs à priorité basse|Count|Total|Nombre total de cœurs à priorité basse dans le compte Batch|Aucune dimension|
|TotalLowPriorityNodeCount|Nombre de nœuds à priorité basse|Count|Total|Nombre total de nœuds à priorité basse dans le compte Batch|Aucune dimension|
|CreatingNodeCount|Nombre de nœuds créés|Count|Total|Nombre de nœuds en cours de création|Aucune dimension|
|StartingNodeCount|Nombre de nœuds de départ|Count|Total|Nœuds de nœuds de départ|Aucune dimension|
|WaitingForStartTaskNodeCount|Nombre de nœuds en attente de démarrage de tâche|Count|Total|Nombre de nœuds en attente de la fin d’une tâche de démarrage|Aucune dimension|
|StartTaskFailedNodeCount|Nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|Count|Total|Le nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|Aucune dimension|
|IdleNodeCount|Nombre de nœuds inactifs|Count|Total|Le nombre de nœuds inactifs|Aucune dimension|
|OfflineNodeCount|Nombre de nœuds hors ligne|Count|Total|Le nombre de nœuds hors ligne|Aucune dimension|
|RebootingNodeCount|Nombre de nœuds en cours de redémarrage|Count|Total|Le nombre de nœuds en cours de redémarrage|Aucune dimension|
|ReimagingNodeCount|Nombre de nœuds en cours de réimageage|Count|Total|Le nombre de nœuds en cours de réimageage|Aucune dimension|
|RunningNodeCount|Nombre de nœuds en cours d’exécution|Count|Total|Le nombre de nœuds en cours d’exécution|Aucune dimension|
|LeavingPoolNodeCount|Nombre de nœuds quittant le pool|Count|Total|Le nombre de nœuds quittant le pool|Aucune dimension|
|UnusableNodeCount|Nombre de nœuds inutilisables|Count|Total|Le nombre de nœuds inutilisables|Aucune dimension|
|PreemptedNodeCount|Nombre de nœuds reportés|Count|Total|Nombre de nœuds reportés|Aucune dimension|
|TaskStartEvent|Événements de lancement de tâche|Count|Total|Nombre total de tâches ayant démarré|Aucune dimension|
|TaskCompleteEvent|Événements de fin de tâche|Count|Total|Le nombre total de tâches terminées|Aucune dimension|
|TaskFailEvent|Événements d’échec de tâches|Count|Total|Le nombre total de tâches terminées dans un état d’échec|Aucune dimension|
|PoolCreateEvent|Événements de création de pool|Count|Total|Nombre total de pools créés|Aucune dimension|
|PoolResizeStartEvent|Événements de démarrage de redimensionnement de pool|Count|Total|Nombre total de redimensionnements de pool ayant démarré|Aucune dimension|
|PoolResizeCompleteEvent|Événements de fin de redimensionnement de pool|Count|Total|Nombre total de redimensionnements de pool terminés|Aucune dimension|
|PoolDeleteStartEvent|Événements de démarrage de suppression de pool|Count|Total|Nombre total de suppressions de pool ayant démarré|Aucune dimension|
|PoolDeleteCompleteEvent|Événements de suppression de pool terminés|Count|Total|Nombre total de suppressions de pool terminées|Aucune dimension|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|connectedclients|Clients connectés|Count|Maximale||Aucune dimension|
|totalcommandsprocessed|Total des opérations|Count|Total||Aucune dimension|
|cachehits|Présences dans le cache|Count|Total||Aucune dimension|
|cachemisses|Absences dans le cache|Count|Total||Aucune dimension|
|getcommands|Gets|Count|Total||Aucune dimension|
|setcommands|Sets|Count|Total||Aucune dimension|
|evictedkeys|Clés exclues|Count|Total||Aucune dimension|
|totalkeys|Nombre total de clés|Count|Maximale||Aucune dimension|
|expiredkeys|Clés expirées|Count|Total||Aucune dimension|
|usedmemory|Mémoire utilisée|Octets|Maximale||Aucune dimension|
|usedmemoryRss|Taille de la mémoire résidente utilisée|Octets|Maximale||Aucune dimension|
|serverLoad|Charge du serveur|Pourcentage|Maximale||Aucune dimension|
|cacheWrite|Cache d’écriture|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead|Lecture du cache|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime|UC|Pourcentage|Maximale||Aucune dimension|
|connectedclients0|Clients connectés (Shard 0)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed0|Total des opérations (Shard 0)|Count|Total||Aucune dimension|
|cachehits0|Présences dans le cache (Shard 0)|Count|Total||Aucune dimension|
|cachemisses0|Absences dans le cache (Shard 0)|Count|Total||Aucune dimension|
|getcommands0|Gets (Shard 0)|Count|Total||Aucune dimension|
|setcommands0|Sets (Shard 0)|Count|Total||Aucune dimension|
|evictedkeys0|Clés exclues (Shard 0)|Count|Total||Aucune dimension|
|totalkeys0|Nombre total de clés (Shard 0)|Count|Maximale||Aucune dimension|
|expiredkeys0|Clés expirées (Shard 0)|Count|Total||Aucune dimension|
|usedmemory0|Mémoire utilisée (Shard 0)|Octets|Maximale||Aucune dimension|
|usedmemoryRss0|Mémoire résidente utilisée (Shard 0)|Octets|Maximale||Aucune dimension|
|serverLoad0|Charge du serveur (Shard 0)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite0|Cache d’écriture (Shard 0)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead0|Cache de lecture (Shard 0)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime0|UC (Shard 0)|Pourcentage|Maximale||Aucune dimension|
|connectedclients1|Clients connectés (Shard 1)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed1|Total des opérations (Shard 1)|Count|Total||Aucune dimension|
|cachehits1|Présences dans le cache (Shard 1)|Count|Total||Aucune dimension|
|cachemisses1|Absences dans le cache (Shard 1)|Count|Total||Aucune dimension|
|getcommands1|Gets (Shard 1)|Count|Total||Aucune dimension|
|setcommands1|Sets (Shard 1)|Count|Total||Aucune dimension|
|evictedkeys1|Clés exclues (Shard 1)|Count|Total||Aucune dimension|
|totalkeys1|Nombre total de clés (Shard 1)|Count|Maximale||Aucune dimension|
|expiredkeys1|Clés expirées (Shard 1)|Count|Total||Aucune dimension|
|usedmemory1|Mémoire utilisée (Shard 1)|Octets|Maximale||Aucune dimension|
|usedmemoryRss1|Mémoire résidente utilisée (Shard 1)|Octets|Maximale||Aucune dimension|
|serverLoad1|Charge du serveur (Shard 1)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite1|Cache d’écriture (Shard 1)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead1|Lecture du cache (Shard 1)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime1|UC (Shard 1)|Pourcentage|Maximale||Aucune dimension|
|connectedclients2|Clients connectés (Shard 2)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed2|Total des opérations (Shard 2)|Count|Total||Aucune dimension|
|cachehits2|Présences dans le cache (Shard 2)|Count|Total||Aucune dimension|
|cachemisses2|Absences dans le cache (Shard 2)|Count|Total||Aucune dimension|
|getcommands2|Gets (Shard 2)|Count|Total||Aucune dimension|
|setcommands2|Sets (Shard 2)|Count|Total||Aucune dimension|
|evictedkeys2|Clés exclues (Shard 2)|Count|Total||Aucune dimension|
|totalkeys2|Nombre total de clés (Shard 2)|Count|Maximale||Aucune dimension|
|expiredkeys2|Clés expirées (Shard 2)|Count|Total||Aucune dimension|
|usedmemory2|Mémoire utilisée (Shard 2)|Octets|Maximale||Aucune dimension|
|usedmemoryRss2|Mémoire résidente utilisée (Shard 2)|Octets|Maximale||Aucune dimension|
|serverLoad2|Charge du serveur (Shard 2)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite2|Cache d’écriture (Shard 2)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead2|Lecture du cache (Shard 2)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime2|UC (Shard 2)|Pourcentage|Maximale||Aucune dimension|
|connectedclients3|Clients connectés (Shard 3)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed3|Total des opérations (Shard 3)|Count|Total||Aucune dimension|
|cachehits3|Présences dans le cache (Shard 3)|Count|Total||Aucune dimension|
|cachemisses3|Absences dans le cache (Shard 3)|Count|Total||Aucune dimension|
|getcommands3|Gets (Shard 3)|Count|Total||Aucune dimension|
|setcommands3|Sets (Shard 3)|Count|Total||Aucune dimension|
|evictedkeys3|Clés exclues (Shard 3)|Count|Total||Aucune dimension|
|totalkeys3|Nombre total de clés (Shard 3)|Count|Maximale||Aucune dimension|
|expiredkeys3|Clés expirées (Shard 3)|Count|Total||Aucune dimension|
|usedmemory3|Mémoire utilisée (Shard 3)|Octets|Maximale||Aucune dimension|
|usedmemoryRss3|Mémoire résidente utilisée (Shard 3)|Octets|Maximale||Aucune dimension|
|serverLoad3|Charge du serveur (Shard 3)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite3|Cache d’écriture (Shard 3)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead3|Lecture du cache (Shard 3)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime3|UC (Shard 3)|Pourcentage|Maximale||Aucune dimension|
|connectedclients4|Clients connectés (Shard 4)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed4|Total des opérations (Shard 4)|Count|Total||Aucune dimension|
|cachehits4|Présences dans le cache (Shard 4)|Count|Total||Aucune dimension|
|cachemisses4|Absences dans le cache (Shard 4)|Count|Total||Aucune dimension|
|getcommands4|Gets (Shard 4)|Count|Total||Aucune dimension|
|setcommands4|Sets (Shard 4)|Count|Total||Aucune dimension|
|evictedkeys4|Clés exclues (Shard 4)|Count|Total||Aucune dimension|
|totalkeys4|Nombre total de clés (Shard 4)|Count|Maximale||Aucune dimension|
|expiredkeys4|Clés expirées (Shard 4)|Count|Total||Aucune dimension|
|usedmemory4|Mémoire utilisée (Shard 4)|Octets|Maximale||Aucune dimension|
|usedmemoryRss4|Mémoire résidente utilisée (Shard 4)|Octets|Maximale||Aucune dimension|
|serverLoad4|Charge du serveur (Shard 4)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite4|Cache d’écriture (Shard 4)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead4|Lecture du cache (Shard 4)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime4|UC (Shard 4)|Pourcentage|Maximale||Aucune dimension|
|connectedclients5|Clients connectés (Shard 5)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed5|Total des opérations (Shard 5)|Count|Total||Aucune dimension|
|cachehits5|Présences dans le cache (Shard 5)|Count|Total||Aucune dimension|
|cachemisses5|Absences dans le cache (Shard 5)|Count|Total||Aucune dimension|
|getcommands5|Gets (Shard 5)|Count|Total||Aucune dimension|
|setcommands5|Sets (Shard 5)|Count|Total||Aucune dimension|
|evictedkeys5|Clés exclues (Shard 5)|Count|Total||Aucune dimension|
|totalkeys5|Nombre total de clés (Shard 5)|Count|Maximale||Aucune dimension|
|expiredkeys5|Clés expirées (Shard 5)|Count|Total||Aucune dimension|
|usedmemory5|Mémoire utilisée (Shard 5)|Octets|Maximale||Aucune dimension|
|usedmemoryRss5|Mémoire résidente utilisée (Shard 5)|Octets|Maximale||Aucune dimension|
|serverLoad5|Charge du serveur (Shard 5)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite5|Cache d’écriture (Shard 5)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead5|Lecture du cache (Shard 5)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime5|UC (Shard 5)|Pourcentage|Maximale||Aucune dimension|
|connectedclients6|Clients connectés (Shard 6)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed6|Total des opérations (Shard 6)|Count|Total||Aucune dimension|
|cachehits6|Présences dans le cache (Shard 6)|Count|Total||Aucune dimension|
|cachemisses6|Absences dans le cache (Shard 6)|Count|Total||Aucune dimension|
|getcommands6|Gets (Shard 6)|Count|Total||Aucune dimension|
|setcommands6|Sets (Shard 6)|Count|Total||Aucune dimension|
|evictedkeys6|Clés exclues (Shard 6)|Count|Total||Aucune dimension|
|totalkeys6|Nombre total de clés (Shard 6)|Count|Maximale||Aucune dimension|
|expiredkeys6|Clés expirées (Shard 6)|Count|Total||Aucune dimension|
|usedmemory6|Mémoire utilisée (Shard 6)|Octets|Maximale||Aucune dimension|
|usedmemoryRss6|Mémoire résidente utilisée (Shard 6)|Octets|Maximale||Aucune dimension|
|serverLoad6|Charge du serveur (Shard 6)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite6|Cache d’écriture (Shard 6)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead6|Lecture du cache (Shard 6)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime6|UC (Shard 6)|Pourcentage|Maximale||Aucune dimension|
|connectedclients7|Clients connectés (Shard 7)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed7|Total des opérations (Shard 7)|Count|Total||Aucune dimension|
|cachehits7|Présences dans le cache (Shard 7)|Count|Total||Aucune dimension|
|cachemisses7|Absences dans le cache (Shard 7)|Count|Total||Aucune dimension|
|getcommands7|Gets (Shard 7)|Count|Total||Aucune dimension|
|setcommands7|Sets (Shard 7)|Count|Total||Aucune dimension|
|evictedkeys7|Clés exclues (Shard 7)|Count|Total||Aucune dimension|
|totalkeys7|Nombre total de clés (Shard 7)|Count|Maximale||Aucune dimension|
|expiredkeys7|Clés expirées (Shard 7)|Count|Total||Aucune dimension|
|usedmemory7|Mémoire utilisée (Shard 7)|Octets|Maximale||Aucune dimension|
|usedmemoryRss7|Mémoire résidente utilisée (Shard 7)|Octets|Maximale||Aucune dimension|
|serverLoad7|Charge du serveur (Shard 7)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite7|Cache d’écriture (Shard 7)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead7|Lecture du cache (Shard 7)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime7|UC (Shard 7)|Pourcentage|Maximale||Aucune dimension|
|connectedclients8|Clients connectés (Shard 8)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed8|Total des opérations (Shard 8)|Count|Total||Aucune dimension|
|cachehits8|Présences dans le cache (Shard 8)|Count|Total||Aucune dimension|
|cachemisses8|Absences dans le cache (Shard 8)|Count|Total||Aucune dimension|
|getcommands8|Gets (Shard 8)|Count|Total||Aucune dimension|
|setcommands8|Sets (Shard 8)|Count|Total||Aucune dimension|
|evictedkeys8|Clés exclues (Shard 8)|Count|Total||Aucune dimension|
|totalkeys8|Nombre total de clés (Shard 8)|Count|Maximale||Aucune dimension|
|expiredkeys8|Clés expirées (Shard 8)|Count|Total||Aucune dimension|
|usedmemory8|Mémoire utilisée (Shard 8)|Octets|Maximale||Aucune dimension|
|usedmemoryRss8|Mémoire résidente utilisée (Shard 8)|Octets|Maximale||Aucune dimension|
|serverLoad8|Charge du serveur (Shard 8)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite8|Cache d’écriture (Shard 8)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead8|Lecture du cache (Shard 8)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime8|UC (Shard 8)|Pourcentage|Maximale||Aucune dimension|
|connectedclients9|Clients connectés (Shard 9)|Count|Maximale||Aucune dimension|
|totalcommandsprocessed9|Total des opérations (Shard 9)|Count|Total||Aucune dimension|
|cachehits9|Présences dans le cache (Shard 9)|Count|Total||Aucune dimension|
|cachemisses9|Absences dans le cache (Shard 9)|Count|Total||Aucune dimension|
|getcommands9|Gets (Shard 9)|Count|Total||Aucune dimension|
|setcommands9|Sets (Shard 9)|Nombre|Total||Aucune dimension|
|evictedkeys9|Clés exclues (Shard 9)|Count|Total||Aucune dimension|
|totalkeys9|Nombre total de clés (Shard 9)|Count|Maximale||Aucune dimension|
|expiredkeys9|Clés expirées (Shard 9)|Count|Total||Aucune dimension|
|usedmemory9|Mémoire utilisée (Shard 9)|Octets|Maximale||Aucune dimension|
|usedmemoryRss9|Mémoire résidente utilisée (Shard 9)|Octets|Maximale||Aucune dimension|
|serverLoad9|Charge du serveur (Shard 9)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite9|Cache d’écriture (Shard 9)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead9|Cache de lecture (Shard 9)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime9|UC (Shard 9)|Pourcentage|Maximale||Aucune dimension|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles.|Aucune dimension|
|Network In|Network In|Octets|Total|Nombre d’octets reçus sur toutes les interfaces réseau par les machines virtuelles (trafic entrant).|Aucune dimension|
|Network Out|Network Out|Octets|Total|Nombre d’octets envoyés sur toutes les interfaces réseau par les machines virtuelles (trafic sortant).|Aucune dimension|
|Disk Read Bytes/Sec|Lecture du disque|BytesPerSecond|Moyenne|Moyenne d’octets lus à partir du disque pendant la période d’analyse.|Aucune dimension|
|Disk Write Bytes/Sec|Écriture sur le disque|BytesPerSecond|Moyenne|Moyenne d’octets écrits sur le disque pendant la période d’analyse.|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde.|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture sur disque par seconde.|Aucune dimension|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalCalls|Nombre total d’appels|Count|Total|Nombre total d’appels.|Aucune dimension|
|SuccessfulCalls|Appels réussis|Count|Total|Nombre d’appels réussis.|Aucune dimension|
|TotalErrors|Nombre total d’erreurs|Count|Total|Nombre total d’appels avec réponse d’erreur (code de réponse HTTP : 4xx ou 5xx).|Aucune dimension|
|BlockedCalls|Appels bloqués|Count|Total|Nombre d’appels ayant dépassé la limite de débit ou de quota.|Aucune dimension|
|ServerErrors|Erreurs de serveur|Count|Total|Nombre d’appels avec erreur interne du service (code de réponse HTTP : 5xx).|Aucune dimension|
|ClientErrors|Erreurs de client|Count|Total|Nombre d’appels avec erreur côté client (code de réponse HTTP : 4xx).|Aucune dimension|
|DataIn|Données entrantes|Octets|Total|Taille des données entrantes en octets.|Aucune dimension|
|DataOut|Données sortantes|Octets|Total|Taille des données sortantes en octets.|Aucune dimension|
|Latency|Latency|Millisecondes|Moyenne|Latence en millisecondes.|Aucune dimension|
|CharactersTranslated|Caractères traduits|Count|Total|Nombre total de caractères dans la requête de texte entrante.|Aucune dimension|
|SpeechSessionDuration|Durée de la session vocale|Secondes|Total|Durée totale de la session vocale en secondes.|Aucune dimension|
|TotalTransactions|Nombre total de transactions|Count|Total|Nombre total de transactions|Aucune dimension|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Count|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Count|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Count|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Count|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Count|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Count|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|DCIApiCalls|Appels de l’API Insights client|Count|Total||Aucune dimension|
|DCIMappingImportOperationSuccessfulLines|Mappage des lignes de réussite d’opération d’importation|Count|Total||Aucune dimension|
|DCIMappingImportOperationFailedLines|Mappage des lignes en échec d’opération d’importation|Count|Total||Aucune dimension|
|DCIMappingImportOperationTotalLines|Mappage du total des lignes d’opération d’importation|Count|Total||Aucune dimension|
|DCIMappingImportOperationRuntimeInSeconds|Mappage d’exécution d’opération d’importation en secondes|Secondes|Total||Aucune dimension|
|DCIOutboundProfileExportSucceeded|Exportation réussie de profil de sortie|Count|Total||Aucune dimension|
|DCIOutboundProfileExportFailed|Échec de l’exportation de profil de sortie|Count|Total||Aucune dimension|
|DCIOutboundProfileExportDuration|Durée d’exportation de profil de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundKpiExportSucceeded|Exportation réussie de l’indicateur KPI sortant|Count|Total||Aucune dimension|
|DCIOutboundKpiExportFailed|Échec de l’exportation de l’indicateur KPI sortant|Count|Total||Aucune dimension|
|DCIOutboundKpiExportDuration|Durée de l’exportation de l’indicateur KPI sortant|Secondes|Total||Aucune dimension|
|DCIOutboundKpiExportStarted|Exportation démarrée de l’indicateur KPI sortant|Secondes|Total||Aucune dimension|
|DCIOutboundKpiRecordCount|Nombre d’enregistrements KPI sortants|Secondes|Total||Aucune dimension|
|DCIOutboundProfileExportCount|Nombre d’exportations de profils de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportFailed|Échec de l’exportation de profil initial de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportSucceeded|Réussite de l’exportation de profil initial de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialKpiExportFailed|Échec de l’exportation de l’indicateur KPI initial sortant|Secondes|Total||Aucune dimension|
|DCIOutboundInitialKpiExportSucceeded|Réussite de l’exportation de l’indicateur KPI initial sortant|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportDurationInSeconds|Durée d’exportation de profil initial de sortie en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiFailed|Échec du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiTimeOut|Délai d’expiration du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiCompleted|Achèvement du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|ImportASAValuesFailed|Nombre d’importations de valeurs ASA en échec|Count|Total||Aucune dimension|
|ImportASAValuesSucceeded|Nombre d’importations de valeurs ASA réussies|Count|Total||Aucune dimension|
|DCIProfilesCount|Nombre d’instances de profil|Count|Dernier||Aucune dimension|
|DCIInteractionsPerMonthCount|Nombre d’interactions par mois|Count|Dernier||Aucune dimension|
|DCIKpisCount|Nombre de KPI|Count|Dernier||Aucune dimension|
|DCISegmentsCount|Nombre de segments|Count|Dernier||Aucune dimension|
|DCIPredictiveMatchPoliciesCount|Nombre de correspondances prédictives|Count|Dernier||Aucune dimension|
|DCIPredictionsCount|Nombre de prédictions|Count|Dernier||Aucune dimension|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRuns|Exécutions réussies|Count|Total|Nombre d’exécutions réussies.|Aucune dimension|
|FailedRuns|Exécutions échouées|Count|Total|Nombre d’échecs d’exécutions.|Aucune dimension|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métriques d’exécutions de pipeline ayant échoué|Count|Total||Aucune dimension|
|PipelineSucceededRuns|Métriques d’exécutions de pipeline ayant abouti|Count|Total||Aucune dimension|
|ActivityFailedRuns|Métriques d’exécutions d’activité ayant échoué|Count|Total||Aucune dimension|
|ActivitySucceededRuns|Métriques d’exécutions d’activité ayant abouti|Count|Total||Aucune dimension|
|TriggerFailedRuns|Métriques d’exécutions de déclencheur ayant échoué|Count|Total||Aucune dimension|
|TriggerSucceededRuns|Métriques d’exécutions de déclencheur ayant abouti|Count|Total||Aucune dimension|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|JobEndedSuccess|Travaux réussis|Count|Total|Nombre de travaux réussis|Aucune dimension|
|JobEndedFailure|Travaux en échec|Count|Total|Nombre de travaux en échec|Aucune dimension|
|JobEndedCancelled|Travaux annulés|Count|Total|Nombre de travaux annulés|Aucune dimension|
|JobAUEndedSuccess|Durée AU de réussite|Secondes|Total|Durée AU totale des travaux réussis.|Aucune dimension|
|JobAUEndedFailure|Durée AU d’échec|Secondes|Total|Durée AU totale des travaux en échec|Aucune dimension|
|JobAUEndedCancelled|Durée AU d’annulation|Secondes|Total|Durée AU totale des travaux annulés|Aucune dimension|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalStorage|Stockage total|Octets|Maximale|Volume total de données stockées dans le compte.|Aucune dimension|
|DataWritten|Données écrites|Octets|Total|Volume total de données écrites dans le compte.|Aucune dimension|
|DataRead|Données lues|Octets|Total|Volume total de données lues à partir du compte.|Aucune dimension|
|WriteRequests|Demandes d’écriture|Count|Total|Nombre de demandes d’écriture de données sur le compte.|Aucune dimension|
|ReadRequests|Demandes de lecture|Count|Total|Nombre de demandes de lecture de données sur le compte.|Aucune dimension|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|Aucune dimension|
|compute_limit|Limite d’unités de calcul|Count|Moyenne|Limite d’unités de calcul|Aucune dimension|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Moyenne|Pourcentage d’unités de calcul|Aucune dimension|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Aucune dimension|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|active_connections|Nombre total de connexions actif|Count|Moyenne|Nombre total de connexions actif|Aucune dimension|
|connections_failed|Total de connexions ayant échoué|Count|Moyenne|Total de connexions ayant échoué|Aucune dimension|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|Aucune dimension|
|compute_limit|Limite d’unités de calcul|Count|Moyenne|Limite d’unités de calcul|Aucune dimension|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Moyenne|Pourcentage d’unités de calcul|Aucune dimension|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Aucune dimension|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|active_connections|Nombre total de connexions actif|Count|Moyenne|Nombre total de connexions actif|Aucune dimension|
|connections_failed|Total de connexions ayant échoué|Count|Moyenne|Total de connexions ayant échoué|Aucune dimension|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Count|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|Aucune dimension|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Count|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|Aucune dimension|
|c2d.commands.egress.complete.success|Commandes terminées|Count|Total|Nombre de commandes cloud vers appareil terminées avec succès par l’appareil|Aucune dimension|
|c2d.commands.egress.abandon.success|Commandes abandonnées|Count|Total|Nombre de commandes cloud vers appareil abandonnées par l’appareil|Aucune dimension|
|c2d.commands.egress.reject.success|Commandes rejetées|Count|Total|Nombre de commandes cloud vers appareil rejetées par l’appareil|Aucune dimension|
|devices.totalDevices|Nombre total d’appareils|Count|Total|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|devices.connectedDevices.allProtocol|Appareils connectés|Count|Total|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|d2c.telemetry.egress.success|Messages de télémétrie remis|Count|Total|Nombre de fois où des messages ont été écrits aux points de terminaison (total)|Aucune dimension|
|d2c.telemetry.egress.dropped|Messages supprimés|Count|Total|Nombre de messages ignorés, car le point de terminaison de livraison était indisponible|Aucune dimension|
|d2c.telemetry.egress.orphaned|Messages orphelins|Count|Total|Nombre de messages ne correspondant à aucun itinéraire, itinéraire de secours compris|Aucune dimension|
|d2c.telemetry.egress.invalid|Messages non valides|Count|Total|Nombre de messages non remis en raison d’une incompatibilité avec le point de terminaison|Aucune dimension|
|d2c.telemetry.egress.fallback|Messages correspondant à une condition de secours|Count|Total|Nombre de messages écrits au point de terminaison de secours|Aucune dimension|
|d2c.endpoints.egress.eventHubs|Messages remis aux points de terminaison Event Hub|Count|Total|Nombre de fois où des messages ont été écrits aux points de terminaison Event Hub|Aucune dimension|
|d2c.endpoints.latency.eventHubs|Latence des messages des points de terminaison Event Hub|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison Event Hub, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.serviceBusQueues|Messages remis aux points de terminaison de file d’attente Service Bus|Count|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de file d’attente Service Bus|Aucune dimension|
|d2c.endpoints.latency.serviceBusQueues|Latence des messages des points de terminaison de files d’attente Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de file d’attente Service Bus, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.serviceBusTopics|Messages remis aux points de terminaison de rubrique Service Bus|Count|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de rubrique Service Bus|Aucune dimension|
|d2c.endpoints.latency.serviceBusTopics|Latence des messages des points de terminaison de rubriques Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de rubrique Service Bus, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.builtIn.events|Messages remis au point de terminaison intégré (messages/événements)|Count|Total|Nombre de fois où des messages ont été écrits au point de terminaison intégré (messages/événements)|Aucune dimension|
|d2c.endpoints.latency.builtIn.events|Latence de message pour le point de terminaison intégré (messages/événements)|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison prédéfini (messages/événements), en millisecondes |Aucune dimension|
|d2c.endpoints.egress.storage|Messages remis aux points de terminaison de stockage|Count|Total|Nombre de fois où des messages ont correctement été écrits sur des points de terminaison de stockage|Aucune dimension|
|d2c.endpoints.latency.storage|Latence des messages pour les points de terminaison de stockage|Millisecondes|Moyenne|Latence moyenne entre l’entrée des messages dans IoT Hub et l’entrée des message dans un point de terminaison de stockage, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.storage.bytes|Données écrites dans le stockage|Octets|Total|Volume de données, en octets, écrites sur des points de terminaison de stockage|Aucune dimension|
|d2c.endpoints.egress.storage.blobs|Objets blob écrits dans le stockage|Count|Total|Nombre d’objets blob écrits sur des points de terminaison de stockage|Aucune dimension|
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Count|Total|Total des lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Count|Total|Total des lectures de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Count|Total|Total des mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|c2d.methods.success|Appels de méthode directe réussis|Count|Total|Total des appels de méthode directe réussis.|Aucune dimension|
|c2d.methods.failure|Appels de méthode directe en échec|Count|Total|Total des appels de méthode directe en échec.|Aucune dimension|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|Aucune dimension|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|Aucune dimension|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Count|Total|Total des lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Count|Total|Total des lectures de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Count|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|twinQueries.success|Requêtes de représentations réussies|Count|Total|Total des requêtes de représentations réussies.|Aucune dimension|
|twinQueries.failure|Requêtes de représentations en échec|Count|Total|Total des requêtes de représentations en échec.|Aucune dimension|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Moyenne|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|Aucune dimension|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Count|Total|Total des créations réussies de travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Count|Total|Total des créations en échec des travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Count|Total|Total des créations réussies des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Count|Total|Total des créations en échec des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Count|Total|Total des appels réussis pour répertorier les travaux.|Aucune dimension|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Count|Total|Total des appels en échec pour répertorier les travaux.|Aucune dimension|
|jobs.cancelJob.success|Annulations de travaux réussies|Count|Total|Total des appels réussis pour annuler un travail.|Aucune dimension|
|jobs.cancelJob.failure|Annulations de travaux en échec|Count|Total|Total des appels en échec pour annuler un travail.|Aucune dimension|
|jobs.queryJobs.success|Requêtes de travaux réussies|Count|Total|Total des appels réussis pour interroger les travaux.|Aucune dimension|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Count|Total|Total des appels en échec pour interroger les travaux.|Aucune dimension|
|jobs.completed|Travaux terminés|Count|Total|Total des travaux terminés.|Aucune dimension|
|jobs.failed|Travaux en échec|Count|Total|Total des travaux en échec.|Aucune dimension|
|d2c.telemetry.ingress.sendThrottle|Nombre d’erreurs de limitation|Count|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|Aucune dimension|
|dailyMessageQuotaUsed|Nombre total de messages utilisés|Count|Moyenne|Nombre total de messages utilisés aujourd’hui. Il s’agit d’une valeur cumulative qui est réinitialisée sur zéro à 00h00 UTC chaque jour.|Aucune dimension|
|deviceDataUsage|Utilisation totale des données d’appareil|Count|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|Aucune dimension|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentatives d’enregistrement|Count|Total|Nombre d’inscriptions d’appareils tentées|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Appareils attribués|Count|Total|Nombre d’appareils affectés à un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentatives d’attestation|Count|Total|Nombre d’attestations d’appareils tentées|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Taux d’utilisation demandée|Pourcentage|Moyenne|Taux d’utilisation demandée|Aucune dimension|

## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|tenantHub.requestedUsageRate|Taux d’utilisation demandée|Pourcentage|Moyenne|Taux d’utilisation demandée|Aucune dimension|
|deviceDataUsage|Utilisation totale des données d’appareil|Count|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|Aucune dimension|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Count|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|Aucune dimension|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Count|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|Aucune dimension|
|c2d.commands.egress.complete.success|Commandes terminées|Count|Total|Nombre de commandes cloud vers appareil terminées avec succès par l’appareil|Aucune dimension|
|c2d.commands.egress.abandon.success|Commandes abandonnées|Count|Total|Nombre de commandes cloud vers appareil abandonnées par l’appareil|Aucune dimension|
|c2d.commands.egress.reject.success|Commandes rejetées|Count|Total|Nombre de commandes cloud vers appareil rejetées par l’appareil|Aucune dimension|
|devices.totalDevices|Nombre total d’appareils|Count|Total|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|devices.connectedDevices.allProtocol|Appareils connectés|Count|Total|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|d2c.telemetry.egress.success|Messages de télémétrie remis|Count|Total|Nombre de fois où des messages ont été écrits aux points de terminaison (total)|Aucune dimension|
|d2c.telemetry.egress.dropped|Messages supprimés|Count|Total|Nombre de messages ignorés, car le point de terminaison de livraison était indisponible|Aucune dimension|
|d2c.telemetry.egress.orphaned|Messages orphelins|Count|Total|Nombre de messages ne correspondant à aucun itinéraire, itinéraire de secours compris|Aucune dimension|
|d2c.telemetry.egress.invalid|Messages non valides|Count|Total|Nombre de messages non remis en raison d’une incompatibilité avec le point de terminaison|Aucune dimension|
|d2c.telemetry.egress.fallback|Messages correspondant à une condition de secours|Count|Total|Nombre de messages écrits au point de terminaison de secours|Aucune dimension|
|d2c.endpoints.egress.eventHubs|Messages remis aux points de terminaison Event Hub|Count|Total|Nombre de fois où des messages ont été écrits aux points de terminaison Event Hub|Aucune dimension|
|d2c.endpoints.latency.eventHubs|Latence des messages des points de terminaison Event Hub|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison Event Hub, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.serviceBusQueues|Messages remis aux points de terminaison de file d’attente Service Bus|Count|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de file d’attente Service Bus|Aucune dimension|
|d2c.endpoints.latency.serviceBusQueues|Latence des messages des points de terminaison de files d’attente Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de file d’attente Service Bus, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.serviceBusTopics|Messages remis aux points de terminaison de rubrique Service Bus|Count|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de rubrique Service Bus|Aucune dimension|
|d2c.endpoints.latency.serviceBusTopics|Latence des messages des points de terminaison de rubriques Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de rubrique Service Bus, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.builtIn.events|Messages remis au point de terminaison intégré (messages/événements)|Count|Total|Nombre de fois où des messages ont été écrits au point de terminaison intégré (messages/événements)|Aucune dimension|
|d2c.endpoints.latency.builtIn.events|Latence de message pour le point de terminaison intégré (messages/événements)|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison prédéfini (messages/événements), en millisecondes |Aucune dimension|
|d2c.endpoints.egress.storage|Messages remis aux points de terminaison de stockage|Count|Total|Nombre de fois où des messages ont correctement été écrits sur des points de terminaison de stockage|Aucune dimension|
|d2c.endpoints.latency.storage|Latence des messages pour les points de terminaison de stockage|Millisecondes|Moyenne|Latence moyenne entre l’entrée des messages dans IoT Hub et l’entrée des message dans un point de terminaison de stockage, en millisecondes|Aucune dimension|
|d2c.endpoints.egress.storage.bytes|Données écrites dans le stockage|Octets|Total|Volume de données, en octets, écrites sur des points de terminaison de stockage|Aucune dimension|
|d2c.endpoints.egress.storage.blobs|Objets blob écrits dans le stockage|Count|Total|Nombre d’objets blob écrits sur des points de terminaison de stockage|Aucune dimension|
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Count|Total|Total des lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Count|Total|Total des lectures de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Count|Total|Total des mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|c2d.methods.success|Appels de méthode directe réussis|Count|Total|Total des appels de méthode directe réussis.|Aucune dimension|
|c2d.methods.failure|Appels de méthode directe en échec|Count|Total|Total des appels de méthode directe en échec.|Aucune dimension|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|Aucune dimension|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|Aucune dimension|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Count|Total|Total des lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Count|Total|Total des lectures de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Count|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|twinQueries.success|Requêtes de représentations réussies|Count|Total|Total des requêtes de représentations réussies.|Aucune dimension|
|twinQueries.failure|Requêtes de représentations en échec|Count|Total|Total des requêtes de représentations en échec.|Aucune dimension|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Moyenne|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|Aucune dimension|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Count|Total|Total des créations réussies de travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Count|Total|Total des créations en échec des travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Count|Total|Total des créations réussies des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Count|Total|Total des créations en échec des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Count|Total|Total des appels réussis pour répertorier les travaux.|Aucune dimension|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Count|Total|Total des appels en échec pour répertorier les travaux.|Aucune dimension|
|jobs.cancelJob.success|Annulations de travaux réussies|Count|Total|Total des appels réussis pour annuler un travail.|Aucune dimension|
|jobs.cancelJob.failure|Annulations de travaux en échec|Count|Total|Total des appels en échec pour annuler un travail.|Aucune dimension|
|jobs.queryJobs.success|Requêtes de travaux réussies|Count|Total|Total des appels réussis pour interroger les travaux.|Aucune dimension|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Count|Total|Total des appels en échec pour interroger les travaux.|Aucune dimension|
|jobs.completed|Travaux terminés|Count|Total|Total des travaux terminés.|Aucune dimension|
|jobs.failed|Travaux en échec|Count|Total|Total des travaux en échec.|Aucune dimension|
|d2c.telemetry.ingress.sendThrottle|Nombre d’erreurs de limitation|Count|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|Aucune dimension|
|dailyMessageQuotaUsed|Nombre total de messages utilisés|Count|Moyenne|Nombre total de messages utilisés aujourd’hui. Il s’agit d’une valeur cumulative qui est réinitialisée sur zéro à 00h00 UTC chaque jour.|Aucune dimension|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|MetadataRequests|Demandes de métadonnées|Count|Count|Nombre de demandes de métadonnées. Cosmos DB gère la collection des métadonnées système pour chaque compte, ce qui vous permet d’énumérer les collections, les bases de données, etc., ainsi que leur configuration, et ce gratuitement.|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|MongoRequestCharge|Frais des requêtes Mongo|Count|Total|Unités de requête Mongo consommées|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Requêtes Mongo|Count|Count|Nombre de requêtes Mongo effectuées|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|TotalRequestUnits|Unités de requête totales|Count|Total|Unités de requête consommées|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|TotalRequests|Total de requêtes|Count|Count|Nombre de requêtes effectuées|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Requêtes ayant réussi (préversion)|Count|Total|Requête réussies pour Microsoft.EventHub. (Préversion)|EntityName|
|ServerErrors|Erreurs de serveur. (Préversion)|Count|Total|Erreurs de serveur pour Microsoft.EventHub. (Préversion)|EntityName|
|UserErrors|Erreurs d’utilisateur. (Préversion)|Count|Total|Erreurs d’utilisateur pour Microsoft.EventHub. (Préversion)|EntityName|
|QuotaExceededErrors|Erreurs de dépassement de quota. (Préversion)|Count|Total|Erreurs de dépassement de quota pour Microsoft.EventHub. (Préversion)|EntityName|
|ThrottledRequests|Demandes limitées. (Préversion)|Count|Total|Demandes limitées pour Microsoft.EventHub. (Préversion)|EntityName|
|IncomingRequests|Demandes entrantes (préversion)|Count|Total|Demandes entrantes pour Microsoft.EventHub. (Préversion)|EntityName|
|IncomingMessages|Messages entrants (préversion)|Count|Total|Messages entrants pour Microsoft.EventHub. (Préversion)|EntityName|
|OutgoingMessages|Messages sortants (préversion)|Count|Total|Messages sortants pour Microsoft.EventHub. (Préversion)|EntityName|
|IncomingBytes|Octets entrants. (Préversion)|Octets|Total|Octets entrants pour Microsoft.EventHub. (Préversion)|EntityName|
|OutgoingBytes|Octets sortants. (Préversion)|Octets|Total|Octets sortants pour Microsoft.EventHub. (Préversion)|EntityName|
|ActiveConnections|ActiveConnections (préversion)|Count|Total|Nombre total de connexions actives pour Microsoft.EventHub. (Préversion)|EntityName|
|ConnectionsOpened|Connexions ouvertes. (Préversion)|Count|Total|Connexions ouvertes pour Microsoft.EventHub. (Préversion)|EntityName|
|ConnectionsClosed|Connexions fermées. (Préversion)|Count|Total|Connexions fermées pour Microsoft.EventHub. (Préversion)|EntityName|
|CaptureBacklog|Backlog des captures. (Préversion)|Count|Total|Backlog des captures de Microsoft.EventHub. (Préversion)|EntityName|
|CapturedMessages|Messages capturés. (Préversion)|Count|Total|Messages capturés pour Microsoft.EventHub. (Préversion)|EntityName|
|CapturedBytes|Octets capturés. (Préversion)|Octets|Total|Octets capturés pour Microsoft.EventHub. (Préversion)|EntityName|
|INREQS|Demandes entrantes|Count|Total|Nombre total des demandes d’envoi entrantes pour un espace de noms|Aucune dimension|
|SUCCREQ|Requêtes ayant réussi|Count|Total|Nombre total de demandes ayant réussi pour un espace de noms|Aucune dimension|
|FAILREQ|Demandes ayant échoué|Count|Total|Nombre total de demandes ayant échoué pour un espace de noms|Aucune dimension|
|SVRBSY|Erreurs de serveur occupé|Count|Total|Nombre total d’erreurs de serveur occupé pour un espace de noms|Aucune dimension|
|INTERR|Erreurs internes du serveur|Count|Total|Nombre total d’erreurs de serveur internes pour un espace de noms|Aucune dimension|
|MISCERR|Autres erreurs|Count|Total|Nombre total de demandes ayant échoué pour un espace de noms|Aucune dimension|
|INMSGS|Messages entrants (déconseillé)|Count|Total|Nombre total de messages entrants pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Messages entrants|Aucune dimension|
|EHINMSGS|Messages entrants|Count|Total|Nombre total de messages entrants pour un espace de noms|Aucune dimension|
|OUTMSGS|Messages sortants (déconseillé)|Count|Total|Nombre total de messages sortants pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Messages sortants|Aucune dimension|
|EHOUTMSGS|Messages sortants|Count|Total|Nombre total de messages sortants pour un espace de noms|Aucune dimension|
|EHINMBS|Octets entrants (déconseillé)|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Octets entrants|Aucune dimension|
|EHINBYTES|Octets entrants|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms|Aucune dimension|
|EHOUTMBS|Octets sortants (déconseillé)|Octets|Total|Débit de messages sortants Event Hub pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Octets sortants|Aucune dimension|
|EHOUTBYTES|Octets sortants|Octets|Total|Débit de messages sortants Event Hub pour un espace de noms|Aucune dimension|
|EHABL|Archiver les messages de file d’attente|Count|Total|Messages d’archive Event Hub dans le backlog pour un espace de noms|Aucune dimension|
|EHAMSGS|Archiver les messages|Count|Total|Messages archivés Event Hub dans un espace de noms|Aucune dimension|
|EHAMBS|Débit message archive|Octets|Total|Débit de messages archivés Event Hub dans un espace de noms|Aucune dimension|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ObservedMetricValue|Valeur de métrique observée|Count|Moyenne|Valeur calculée par mise à l’échelle automatique lors de l’exécution|MetricTriggerSource|
|MetricThreshold|Seuil de métrique|Count|Moyenne|Seuil de mise à l’échelle automatique configurée lors de l’exécution de la mise à l’échelle automatique.|MetricTriggerRule|
|ObservedCapacity|Capacité observée|Count|Moyenne|Capacité envoyée à la mise à l’échelle automatique lors de l’exécution.|Aucune dimension|
|ScaleActionsInitiated|Actions de mise à l’échelle initiées|Count|Total|Direction de l’opération de mise à l’échelle.|ScaleDirection|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ServiceApiHit|Correspondances totales de l'API de service|Count|Count,Total|Nombre total de correspondances de l'API de service|ActivityType, ActivityName|
|ServiceApiLatency|Latence globale de l'API de service|Millisecondes|Count,Average,Minimum,Maximum|Latence globale des demandes de l'API de service|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Résultats totaux de l'API de service|Count|Count,Total|Nombre total de résultats de l'API de service|ActivityType, ActivityName, StatusCode|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Latency|Latency|Millisecondes|Moyenne|Durée des appels d’API|OperationName, OperationResult|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Exécutions démarrées|Count|Total|Nombre d’exécutions de flux de travail démarrées.|Aucune dimension|
|RunsCompleted|Exécutions terminées|Count|Total|Nombre d’exécutions de flux de travail terminées.|Aucune dimension|
|RunsSucceeded|Exécutions réussies|Count|Total|Nombre d’exécutions de flux de travail ayant réussi.|Aucune dimension|
|RunsFailed|Exécutions ayant échoué|Count|Total|Nombre d’exécutions de flux de travail ayant échoué.|Aucune dimension|
|RunsCancelled|Exécutions annulées|Count|Total|Nombre d’exécutions de flux de travail annulées.|Aucune dimension|
|RunLatency|Latence d’exécution|Secondes|Moyenne|Latence des exécutions de flux de travail terminées.|Aucune dimension|
|RunSuccessLatency|Latence d’exécution ayant réussi|Secondes|Moyenne|Latence des exécutions de flux de travail ayant réussi.|Aucune dimension|
|RunThrottledEvents|Exécuter événements limités|Count|Total|Nombre d’actions de flux de travail ou d’événements limités par déclencheur.|Aucune dimension|
|RunFailurePercentage|Pourcentage d’échec des exécutions|Pourcentage|Total|Pourcentage d’exécutions de flux de travail ayant échoué.|Aucune dimension|
|ActionsStarted|Actions démarrées |Count|Total|Nombre d’actions de flux de travail démarrées.|Aucune dimension|
|ActionsCompleted|Actions terminées |Count|Total|Nombre d’actions de flux de travail terminées.|Aucune dimension|
|ActionsSucceeded|Actions ayant réussi |Count|Total|Nombre d’actions de flux de travail ayant réussi.|Aucune dimension|
|ActionsFailed|Actions ayant échoué|Count|Total|Nombre d’actions de flux de travail ayant échoué.|Aucune dimension|
|ActionsSkipped|Actions ignorées |Count|Total|Nombre d’actions de flux de travail ignorées.|Aucune dimension|
|ActionLatency|Latence de l’action |Secondes|Moyenne|Latence des actions de flux de travail terminés.|Aucune dimension|
|ActionSuccessLatency|Latence de réussite d’action |Secondes|Moyenne|Latence des actions de flux de travail ayant réussi.|Aucune dimension|
|ActionThrottledEvents|Événements d’action limitée|Count|Total|Nombre d’événements limités d’action de flux de travail.|Aucune dimension|
|TriggersStarted|Déclenchements lancés |Count|Total|Nombre de déclencheurs de flux de travail démarrés.|Aucune dimension|
|TriggersCompleted|Déclenchements terminés |Count|Total|Nombre de déclencheurs de flux de travail terminés.|Aucune dimension|
|TriggersSucceeded|Déclenchements ayant réussi |Count|Total|Nombre de déclencheurs de flux de travail ayant réussi.|Aucune dimension|
|TriggersFailed|Déclenchements ayant échoué |Count|Total|Nombre de déclencheurs de flux de travail ayant échoué.|Aucune dimension|
|TriggersSkipped|Déclenchements ignorés|Count|Total|Nombre de déclencheurs de flux de travail ignorés.|Aucune dimension|
|TriggersFired|Déclenchements activés |Count|Total|Nombre de déclencheurs de flux de travail activés.|Aucune dimension|
|TriggerLatency|Latence de déclencheur |Secondes|Moyenne|Latence des déclenchements de flux de travail terminés.|Aucune dimension|
|TriggerFireLatency|Latence d’activation de déclencheur |Secondes|Moyenne|Latence des déclencheurs de flux de travail activés.|Aucune dimension|
|TriggerSuccessLatency|Latence déclencheur ayant réussi |Secondes|Moyenne|Latence des déclencheurs de flux de travail ayant réussi.|Aucune dimension|
|TriggerThrottledEvents|Événements limités par déclencheur|Count|Total|Nombre d’événements de flux de travail limités par déclencheur.|Aucune dimension|
|BillableActionExecutions|Exécutions d’actions facturables|Count|Total|Nombre d’exécutions d’actions de flux de travail facturées.|Aucune dimension|
|BillableTriggerExecutions|Exécutions de déclencheurs facturables|Count|Total|Nombre d’exécutions de déclencheurs de flux de travail facturées.|Aucune dimension|
|TotalBillableExecutions|Nombre total d’exécutions facturables|Count|Total|Nombre d’exécutions de flux de travail facturées.|Aucune dimension|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|VipAvailability|Disponibilité VIP|Count|Moyenne|Disponibilité des points de terminaison VIP, en fonction des résultats du sondage|VipAddress, VipPort|
|DipAvailability|Disponibilité DIP|Count|Moyenne|Disponibilité des points de terminaison DIP, en fonction des résultats du sondage|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Nombre d’octets|Count|Total|Nombre total d’octets transmis dans une période de temps|VipAddress, VipPort, Direction|
|PacketCount|Nombre de paquets|Count|Total|Nombre total de paquets transmis dans une période de temps|VipAddress, VipPort, Direction|
|SYNCount|Nombre de SYN|Count|Total|Nombre total de paquets SYN transmis dans une période de temps|VipAddress, VipPort, Direction|
|SnatConnectionCount|Nombre de connexions SNAT|Count|Total|Nombre total de connexions SNAT créées dans une période de temps|VipAddress, DipAddress, ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PacketsInDDoS|Paquets entrants DDoS|CountPerSecond|Maximale|Paquets entrants DDoS|Aucune dimension|
|PacketsDroppedDDoS|Paquets entrants ignorés DDoS|CountPerSecond|Maximale|Paquets entrants ignorés DDoS|Aucune dimension|
|PacketsForwardedDDoS|Paquets entrants transférés DDoS|CountPerSecond|Maximale|Paquets entrants transférés DDoS|Aucune dimension|
|TCPPacketsInDDoS|Paquets TCP entrants DDoS|CountPerSecond|Maximale|Paquets TCP entrants DDoS|Aucune dimension|
|TCPPacketsDroppedDDoS|Paquets TCP entrants TCP ignorés DDoS|CountPerSecond|Maximale|Paquets TCP entrants TCP ignorés DDoS|Aucune dimension|
|TCPPacketsForwardedDDoS|Paquets TCP entrants transférés DDoS|CountPerSecond|Maximale|Paquets TCP entrants transférés DDoS|Aucune dimension|
|UDPPacketsInDDoS|Paquets UDP entrants DDoS|CountPerSecond|Maximale|Paquets UDP entrants DDoS|Aucune dimension|
|UDPPacketsDroppedDDoS|Paquets UDP entrants ignorés DDoS|CountPerSecond|Maximale|Paquets UDP entrants ignorés DDoS|Aucune dimension|
|UDPPacketsForwardedDDoS|Paquets UDP entrants transférés DDoS|CountPerSecond|Maximale|Paquets UDP entrants transférés DDoS|Aucune dimension|
|BytesInDDoS|Octets entrants DDoS|BytesPerSecond|Maximale|Octets entrants DDoS|Aucune dimension|
|BytesDroppedDDoS|Octets entrants supprimés DDoS|BytesPerSecond|Maximale|Octets entrants supprimés DDoS|Aucune dimension|
|BytesForwardedDDoS|Octets entrants transférés DDoS|BytesPerSecond|Maximale|Octets entrants transférés DDoS|Aucune dimension|
|TCPBytesInDDoS|Octets TCP entrants DDoS|BytesPerSecond|Maximale|Octets TCP entrants DDoS|Aucune dimension|
|TCPBytesDroppedDDoS|Octets TCP entrants supprimés DDoS|BytesPerSecond|Maximale|Octets TCP entrants supprimés DDoS|Aucune dimension|
|TCPBytesForwardedDDoS|Octets TCP entrants transférés DDoS|BytesPerSecond|Maximale|Octets TCP entrants transférés DDoS|Aucune dimension|
|UDPBytesInDDoS|Octets UDP entrants DDoS|BytesPerSecond|Maximale|Octets UDP entrants DDoS|Aucune dimension|
|UDPBytesDroppedDDoS|Octets UDP entrants supprimés DDoS|BytesPerSecond|Maximale|Octets UDP entrants supprimés DDoS|Aucune dimension|
|UDPBytesForwardedDDoS|Octets UDP entrants transférés DDoS|BytesPerSecond|Maximale|Octets UDP entrants transférés DDoS|Aucune dimension|
|IfUnderDDoSAttack|Sous attaque DDoS ou non|Count|Maximale|Sous attaque DDoS ou non|Aucune dimension|
|DDoSTriggerTCPPackets|Paquets TCP entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets TCP entrants pour déclencher l’atténuation DDoS|Aucune dimension|
|DDoSTriggerUDPPackets|Paquets UDP entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets UDP entrants pour déclencher l’atténuation DDoS|Aucune dimension|
|DDoSTriggerSYNPackets|Paquets SYN entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets SYN entrants pour déclencher l’atténuation DDoS|Aucune dimension|
|VipAvailability|Disponibilité|Count|Moyenne|Disponibilité moyenne de l’adresse IP dans une période de temps|Port|
|ByteCount|Nombre d’octets|Count|Total|Nombre total d’octets transmis dans une période de temps|Port, Direction|
|PacketCount|Nombre de paquets|Count|Total|Nombre total de paquets transmis dans une période de temps|Port, Direction|
|SynCount|Nombre de SYN|Count|Total|Nombre total de paquets SYN transmis dans une période de temps|Port, Direction|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Throughput|Throughput|BytesPerSecond|Total|Nombre d’octets par seconde servis par Application Gateway|Aucune dimension|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Bande passante de tunnel|BytesPerSecond|Moyenne|Bande passante moyenne d’un tunnel en octets par seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Octets de sortie de tunnel|Octets|Total|Octets sortants d’un tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Octets d’entrée de tunnel|Octets|Total|Octets entrants d’un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Paquets de sortie de tunnel|Count|Total|Nombre de paquets sortants d’un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Paquets en entrée de tunnel|Count|Total|Nombre de paquets entrants d’un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Rejet de paquets TS de sortie de tunnel pour incompatibilité|Count|Total|Nombre de rejets de paquets sortants du sélecteur de trafic pour incompatibilité dans un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Rejet de paquets TS d’entrée de tunnel pour incompatibilité|Count|Total|Nombre de rejets de paquets entrants du sélecteur de trafic pour incompatibilité dans un tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Moyenne|Bits entrant dans Azure par seconde|Aucune dimension|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Moyenne|Bits sortant d’Azure par seconde|Aucune dimension|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QpsByEndpoint|Requêtes par point de terminaison renvoyé|Count|Total|Nombre de fois où un point de terminaison Traffic Manager a été renvoyé dans le laps de temps donné|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|État du point de terminaison par point de terminaison|Count|Maximale|1 si l’état de sondage d’un point de terminaison est « activé », 0 dans le cas contraire.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|registration.all|Opérations d’inscription|Count|Total|Total des opérations d’inscription réussies (requêtes de mises à jour de créations et suppressions). |Aucune dimension|
|registration.create|Opérations de création d’inscription|Count|Total|Total des créations d’inscription réussies.|Aucune dimension|
|registration.update|Opérations de mise à jour d’inscription|Count|Total|Total des mises à jour d’inscription réussies.|Aucune dimension|
|registration.get|Opérations de lecture d’inscription|Count|Total|Total des requêtes d’inscription réussies.|Aucune dimension|
|registration.delete|Opérations de suppression d’inscription|Count|Total|Total des suppressions d’inscription réussies.|Aucune dimension|
|incoming|Messages entrants|Count|Total|Total des appels d’API d’envoi réussis. |Aucune dimension|
|incoming.scheduled|Notifications Push planifiées envoyées|Count|Total|Notifications Push planifiées annulées|Aucune dimension|
|incoming.scheduled.cancel|Notifications Push planifiées annulées|Count|Total|Notifications Push planifiées annulées|Aucune dimension|
|scheduled.pending|Notifications planifiées en attente|Count|Total|Notifications planifiées en attente|Aucune dimension|
|installation.all|Opérations de gestion de l’installation|Count|Total|Opérations de gestion de l’installation|Aucune dimension|
|installation.get|Opérations d’obtention d’installation|Count|Total|Opérations d’obtention d’installation|Aucune dimension|
|installation.upsert|Opérations de création ou de mise à jour d’installation|Count|Total|Opérations de création ou de mise à jour d’installation|Aucune dimension|
|installation.patch|Opérations d’installation de correctif|Count|Total|Opérations d’installation de correctif|Aucune dimension|
|installation.delete|Opérations de suppression d’installation|Count|Total|Opérations de suppression d’installation|Aucune dimension|
|outgoing.allpns.success|Notifications réussies|Count|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.allpns.invalidpayload|Erreurs de charge utile|Count|Total|Nombre d’opérations Push en échec, car le PNS a retourné une erreur de charge utile incorrecte.|Aucune dimension|
|outgoing.allpns.pnserror|Erreurs système de notification externe|Count|Total|Nombre d’opérations Push en échec en raison d’un problème de communication avec le PNS (à l’exclusion des problèmes d’authentification).|Aucune dimension|
|outgoing.allpns.channelerror|Erreurs de canal|Count|Total|Nombre d’opérations Push en échec, car le canal n’était pas valide ni associé à l’application appropriée, limité ou arrivé à expiration.|Aucune dimension|
|outgoing.allpns.badorexpiredchannel|Erreurs de canal incorrect ou arrivé à expiration|Count|Total|Nombre d’opérations Push en échec, car le canal/jeton/registrationId indiqué dans l’inscription est arrivé à expiration ou non valide.|Aucune dimension|
|outgoing.wns.success|Notifications WNS réussies|Count|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.wns.invalidcredentials|Erreurs d’autorisation WNS (informations d’identification non valides)|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées. (Windows Live ne reconnaît pas les informations d’identification).|Aucune dimension|
|outgoing.wns.badchannel|Erreur de canal WNS incorrect|Count|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI indiqué dans l’inscription n’a pas été reconnu (état WNS : 404 introuvable).|Aucune dimension|
|outgoing.wns.expiredchannel|Erreur de canal WNS arrivé à expiration|Count|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI est arrivé à expiration (état WNS : 410 Supprimé).|Aucune dimension|
|outgoing.wns.throttled|Notifications WNS limitées|Count|Total|Nombre d’opérations Push en échec, car WNS limite cette application (état WNS : 406 Non accepté).|Aucune dimension|
|outgoing.wns.tokenproviderunreachable|Erreurs d’autorisation WNS (inaccessible)|Count|Total|Windows Live n’est pas accessible.|Aucune dimension|
|outgoing.wns.invalidtoken|Erreurs d’autorisation WNS (jeton non valide)|Count|Total|Le jeton fourni à WNS n’est pas valide (état WNS : 401 Non autorisé).|Aucune dimension|
|outgoing.wns.wrongtoken|Erreurs d’autorisation WNS (jeton incorrect)|Count|Total|Le jeton fourni à WNS est valide, mais il concerne une autre application (état WNS : 403 Interdit). Cela peut se produire si l’élément ChannelURI indiqué dans l’inscription est associé à une autre application. Vérifiez que l’application cliente est associée à l’application dont les informations d’identification figurent dans le hub de notification.|Aucune dimension|
|outgoing.wns.invalidnotificationformat|Format de notification WNS non valide|Count|Total|Le format de la notification n’est pas valide (état WNS : 400). Notez que WNS ne rejette pas toutes les charges utiles non valides.|Aucune dimension|
|outgoing.wns.invalidnotificationsize|Erreur de taille de notification WNS non valide|Count|Total|La charge utile de notification est trop grande (état WNS : 413).|Aucune dimension|
|outgoing.wns.channelthrottled|Canal WNS limité|Count|Total|La notification a été supprimée, car l’élément ChannelURI indiqué dans l’inscription est limité (en-tête de réponse WNS : X-WNS-NotificationStatus:channelThrottled).|Aucune dimension|
|outgoing.wns.channeldisconnected|Canal WNS déconnecté|Count|Total|La notification a été supprimée, car l’élément ChannelURI contenu dans l’inscription est limité (en-tête de réponse WNS : X-WNS-DeviceConnectionStatus : déconnecté).|Aucune dimension|
|outgoing.wns.dropped|Notifications WNS supprimées|Count|Total|La notification a été supprimée, car l’élément ChannelURI indiqué dans l’inscription est limité (X-WNS-NotificationStatus : supprimé, mais pas X-WNS-DeviceConnectionStatus : déconnecté).|Aucune dimension|
|outgoing.wns.pnserror|Erreurs WNS|Count|Total|La notification n’a pas été remise en raison d’erreurs de communication avec WNS.|Aucune dimension|
|outgoing.wns.authenticationerror|Erreurs d’authentification WNS|Count|Total|La notification n’a pas été remise en raison d’erreurs de communication avec Windows Live, d’informations d’identification non valides ou d’un jeton incorrect.|Aucune dimension|
|outgoing.apns.success|Notifications APNS réussies|Count|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.apns.invalidcredentials|Erreurs d’autorisation APNS|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.apns.badchannel|Erreur de canal APNS incorrect|Count|Total|Nombre d’opérations Push en échec, car le jeton n’est pas valide (code d’état APNS : 8).|Aucune dimension|
|outgoing.apns.expiredchannel|Erreur de canal APNS arrivé à expiration|Count|Total|Nombre de jetons qui ont été invalidés par le canal de commentaires APNS.|Aucune dimension|
|outgoing.apns.invalidnotificationsize|Erreur de taille de notification APNS non valide|Count|Total|Nombre d’opérations Push en échec, car la charge utile était trop importante (code d’état APNS : 7).|Aucune dimension|
|outgoing.apns.pnserror|Erreurs APNS|Count|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec APNS.|Aucune dimension|
|outgoing.gcm.success|Notifications GCM réussies|Count|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.gcm.invalidcredentials|Erreurs d’autorisation GCM (informations d’identification non valides)|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.gcm.badchannel|Erreur de canal GCM incorrect|Count|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription n’a pas été reconnu (résultat GCM : inscription non valide).|Aucune dimension|
|outgoing.gcm.expiredchannel|Erreur de canal GCM arrivé à expiration|Count|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription est arrivé à expiration (résultat GCM : NotRegistered).|Aucune dimension|
|outgoing.gcm.throttled|Notifications GCM limitées|Count|Total|Nombre d’opérations Push en échec, car GCM a limité cette application (code d’état GCM : 501-599 ou résultat : indisponible).|Aucune dimension|
|outgoing.gcm.invalidnotificationformat|Format de notification GCM non valide|Count|Total|Nombre d’opérations Push en échec, car la charge utile n’était pas mise en forme correctement (résultat GCM : InvalidDataKey ou InvalidTtl).|Aucune dimension|
|outgoing.gcm.invalidnotificationsize|Erreur de taille de notification GCM non valide|Count|Total|Nombre d’opérations Push en échec, car la charge utile était trop importante (résultat GCM : MessageTooBig).|Aucune dimension|
|outgoing.gcm.wrongchannel|Erreur de canal GCM incorrect|Count|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription n’est pas associé à l’application actuelle (résultat GCM : InvalidPackageName).|Aucune dimension|
|outgoing.gcm.pnserror|Erreurs GCM|Count|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec GCM.|Aucune dimension|
|outgoing.gcm.authenticationerror|Erreurs d’authentification GCM|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies, celles-ci sont bloquées ou l’élément SenderId n’est pas configuré correctement dans l’application (résultat GCM : MismatchedSenderId).|Aucune dimension|
|outgoing.mpns.success|Notifications MPNS réussies|Count|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.mpns.invalidcredentials|Informations d’identification MPNS non valides|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.mpns.badchannel|Erreur de canal incorrect MPNS|Count|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI contenu dans l’inscription n’a pas été reconnu (état MPNS : 404 introuvable).|Aucune dimension|
|outgoing.mpns.throttled|Notifications MPNS limitées|Count|Total|Nombre d’opérations Push en échec, car MPNS limite cette application (WNS MPNS : 406 Non accepté).|Aucune dimension|
|outgoing.mpns.invalidnotificationformat|Format de notification MPNS non valide|Count|Total|Nombre d’opérations Push en échec, car la charge utile de la notification était trop importante.|Aucune dimension|
|outgoing.mpns.channeldisconnected|Canal MPNS déconnecté|Count|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI contenu dans l’inscription était déconnecté (état MPNS : 412 introuvable).|Aucune dimension|
|outgoing.mpns.dropped|Notifications MPNS supprimées|Count|Total|Nombre d’opérations Push qui ont été supprimées par MPNS (en-tête de réponse MPNS : X-NotificationStatus : QueueFull ou Suppressed).|Aucune dimension|
|outgoing.mpns.pnserror|Erreurs MPNS|Count|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec MPNS.|Aucune dimension|
|outgoing.mpns.authenticationerror|Erreurs d’authentification MPNS|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|notificationhub.pushes|Toutes les notifications sortantes|Count|Total|Toutes les notifications sortantes du hub de notification|Aucune dimension|
|incoming.all.requests|Toutes les demandes entrantes|Count|Total|Nombre total des demandes entrantes pour un hub de notification|Aucune dimension|
|incoming.all.failedrequests|Toutes les requêtes entrantes ayant échoué|Count|Total|Nombre total des demandes entrantes ayant échoué pour un hub de notification|Aucune dimension|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueryDuration|Durée de la requête|Millisecondes|Moyenne|Durée de la requête DAX dans le dernier intervalle|Aucune dimension|
|QueryPoolJobQueueLength|Threads : longueur de file d’attente de travaux du pool de requêtes|Count|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|Aucune dimension|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Total|ListenerConnections ayant abouti pour Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Total|ClientError sur ListenerConnections pour Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Total|ServerError sur ListenerConnections pour Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Total|SenderConnections ayant abouti pour Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Total|ClientError sur SenderConnections pour Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Total|ServerError sur SenderConnections pour Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Total|Nombre total de ListenerConnections pour Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Total|Nombre total de demandes SenderConnections pour Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Total|Nombre total d’ActiveConnections pour Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Total|Nombre total d’ActiveListeners pour Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Total|Nombre total de BytesTransferred pour Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Total|Nombre total de ListenerDisconnects pour Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Total|Nombre total de SenderDisconnects pour Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SearchLatency|Latence de recherche|Secondes|Moyenne|Latence moyenne de recherche du service de recherche|Aucune dimension|
|SearchQueriesPerSecond|Requêtes de recherche par seconde|CountPerSecond|Moyenne|Requêtes de recherche par seconde pour le service de recherche|Aucune dimension|
|ThrottledSearchQueriesPercentage|Pourcentage de requêtes de recherche limitées|Pourcentage|Moyenne|Pourcentage de requêtes de recherche limitées par le service de recherche|Aucune dimension|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Requêtes ayant réussi (préversion)|Count|Total|Nombre total de demandes ayant réussi pour un espace de noms (préversion)|EntityName|
|ServerErrors|Erreurs de serveur. (Préversion)|Count|Total|Erreurs de serveur pour Microsoft.ServiceBus. (Préversion)|EntityName|
|UserErrors|Erreurs d’utilisateur. (Préversion)|Count|Total|Erreurs d’utilisateur pour Microsoft.ServiceBus. (Préversion)|EntityName|
|ThrottledRequests|Demandes limitées. (Préversion)|Count|Total|Demandes limitées pour Microsoft.ServiceBus. (Préversion)|EntityName|
|IncomingRequests|Demandes entrantes (préversion)|Count|Total|Demandes entrantes pour Microsoft.ServiceBus. (Préversion)|EntityName|
|IncomingMessages|Messages entrants (préversion)|Count|Total|Messages entrants pour Microsoft.ServiceBus. (Préversion)|EntityName|
|OutgoingMessages|Messages sortants (préversion)|Count|Total|Messages sortants pour Microsoft.ServiceBus. (Préversion)|EntityName|
|ActiveConnections|ActiveConnections (préversion)|Count|Total|Nombre total de connexions actives pour Microsoft.ServiceBus. (Préversion)|EntityName|
|ConnectionsOpened|Connexions ouvertes. (Préversion)|Count|Total|Connexions ouvertes pour Microsoft.ServiceBus. (Préversion)|EntityName|
|ConnectionsClosed|Connexions fermées. (Préversion)|Count|Total|Connexions fermées pour Microsoft.ServiceBus. (Préversion)|EntityName|
|CPUXNS|Utilisation du processeur par espace de noms|Pourcentage|Maximale|Métrique d’utilisation du processeur de l’espace de noms Service Bus Premium|Aucune dimension|
|WSXNS|Utilisation de la taille mémoire par espace de noms|Pourcentage|Maximale|Métrique d’utilisation de la mémoire de l’espace de noms Service Bus Premium|Aucune dimension|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Aucune dimension|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|Aucune dimension|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|Aucune dimension|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|Aucune dimension|
|storage|Taille de base de données totale|Octets|Maximale|Taille de base de données totale|Aucune dimension|
|connection_successful|Connexions réussies|Count|Total|Connexions réussies|Aucune dimension|
|connection_failed|Connexions ayant échoué|Count|Total|Connexions ayant échoué|Aucune dimension|
|blocked_by_firewall|Bloqué par le pare-feu|Count|Total|Bloqué par le pare-feu|Aucune dimension|
|deadlock|Blocages|Count|Total|Blocages|Aucune dimension|
|storage_percent|Pourcentage de la taille de la base de données|Pourcentage|Maximale|Pourcentage de la taille de la base de données|Aucune dimension|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Moyenne|Pourcentage de stockage OLTP en mémoire|Aucune dimension|
|workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|Aucune dimension|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|Aucune dimension|
|dtu_limit|Limite DTU|Count|Moyenne|Limite DTU|Aucune dimension|
|dtu_used|DTU utilisé|Count|Moyenne|DTU utilisé|Aucune dimension|
|dwu_limit|Limite DWU|Count|Maximale|Limite DWU|Aucune dimension|
|dwu_consumption_percent|Pourcentage DWU|Pourcentage|Maximale|Pourcentage DWU|Aucune dimension|
|dwu_used|DWU utilisé|Count|Maximale|DWU utilisé|Aucune dimension|
|dw_cpu_percent|Pourcentage d’utilisation de l’unité centrale au niveau du nœud DW|Pourcentage|Moyenne|Pourcentage d’utilisation de l’unité centrale au niveau du nœud DW|dw_logical_node_id|
|dw_physical_data_read_percent|Pourcentage E/S des données au niveau du nœud DW|Pourcentage|Moyenne|Pourcentage E/S des données au niveau du nœud DW|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Aucune dimension|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|Aucune dimension|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|Aucune dimension|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|Aucune dimension|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|Aucune dimension|
|eDTU_limit|Limite eDTU|Count|Moyenne|Limite eDTU|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|eDTU_used|eDTU utilisé|Count|Moyenne|eDTU utilisé|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Moyenne|Pourcentage de stockage OLTP en mémoire|Aucune dimension|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|ElasticPoolResourceId|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacité utilisée|Octets|Moyenne|Capacité de compte utilisée|Aucune dimension|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacité d’objet blob|Octets|Moyenne|Quantité de stockage utilisée par le service BLOB du compte de stockage, en octets.|/BlobType|
|BlobCount|Nombre d’objets blob|Count|Moyenne|Nombre d’objets blob dans le service BLOB du compte de stockage.|/BlobType|
|ContainerCount|Nombre de conteneurs d’objets blob|Count|Moyenne|Nombre de conteneurs d’objets blob dans le service BLOB du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacité de la table|Octets|Moyenne|Quantité de stockage utilisée par le service de Table du compte de stockage, en octets.|Aucune dimension|
|TableCount|Nombre de tables|Count|Moyenne|Nombre de tables dans le service de Table du compte de stockage.|Aucune dimension|
|TableEntityCount|Nombre d’entités de table|Count|Moyenne|Nombre d’entités de table dans le service de Table du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacité de la file d’attente|Octets|Moyenne|Quantité de stockage utilisée par le service de File d’attente du compte de stockage, en octets.|Aucune dimension|
|QueueCount|Nombre de files d’attente|Count|Moyenne|Nombre de files d’attente dans le service de File d’attente du compte de stockage.|Aucune dimension|
|QueueMessageCount|Nombre de messages dans la file d’attente|Count|Moyenne|Nombre approximatif de messages en file d’attente dans le service de File d’attente du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacité de fichiers|Octets|Moyenne|Quantité de stockage utilisée par le service de Fichier du compte de stockage, en octets.|Aucune dimension|
|FileCount|Nombre de fichiers|Count|Moyenne|Nombre de fichiers dans le service de Fichier du compte de stockage.|Aucune dimension|
|FileShareCount|Nombre de partages de fichiers|Count|Moyenne|Nombre de partage de fichiers dans le service de Fichier du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ResourceUtilization|Utilisation de % d’unités de diffusion|Pourcentage|Maximale|Utilisation de % d’unités de diffusion|Aucune dimension|
|InputEvents|Événements d’entrée|Count|Total|Événements d’entrée|Aucune dimension|
|InputEventBytes|Octets des événements d’entrée|Octets|Total|Octets des événements d’entrée|Aucune dimension|
|LateInputEvents|Événements d’entrée tardifs|Count|Total|Événements d’entrée tardifs|Aucune dimension|
|OutputEvents|Événements de sortie|Count|Total|Événements de sortie|Aucune dimension|
|ConversionErrors|Erreurs de conversion de données|Count|Total|Erreurs de conversion de données|Aucune dimension|
|Errors|Erreurs d’exécution|Count|Total|Erreurs d’exécution|Aucune dimension|
|DroppedOrAdjustedEvents|Événements en désordre|Count|Total|Événements en désordre|Aucune dimension|
|AMLCalloutRequests|Requêtes de fonction|Count|Total|Requêtes de fonction|Aucune dimension|
|AMLCalloutFailedRequests|Requêtes de fonction ayant échoué|Count|Total|Requêtes de fonction ayant échoué|Aucune dimension|
|AMLCalloutInputEvents|Événements de fonction|Count|Total|Événements de fonction|Aucune dimension|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Instance|
|DiskQueueLength|Longueur de file d'attente de disque|Count|Moyenne|Longueur de file d'attente de disque|Instance|
|HttpQueueLength|Longueur de la file d’attente HTTP|Count|Moyenne|Longueur de la file d’attente HTTP|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (à l’exclusion de Functions)

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|Instance|
|Requests|Requests|Count|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Count|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instance|
|Http401|Http 401|Count|Total|Http 401|Instance|
|Http403|Http 403|Count|Total|Http 403|Instance|
|Http404|Http 404|Count|Total|Http 404|Instance|
|Http406|Http 406|Count|Total|Http 406|Instance|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Count|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|AppConnections|connexions|Count|Moyenne|connexions|Instance|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http5xx|Erreurs de serveur http|Count|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Count|Total|Unités d’exécution de fonctions|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Count|Total|Nombre d’exécutions de fonctions|Instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|Instance|
|Requests|Requests|Count|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Count|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instance|
|Http401|Http 401|Count|Total|Http 401|Instance|
|Http403|Http 403|Count|Total|Http 403|Instance|
|Http404|Http 404|Count|Total|Http 404|Instance|
|Http406|Http 406|Count|Total|Http 406|Instance|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Count|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Count|Total|Unités d’exécution de fonctions|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Count|Total|Nombre d’exécutions de fonctions|Instance|
|AppConnections|connexions|Count|Moyenne|connexions|Instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Requests|Requests|Count|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Count|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instance|
|Http401|Http 401|Count|Total|Http 401|Instance|
|Http403|Http 403|Count|Total|Http 403|Instance|
|Http404|Http 404|Count|Total|Http 404|Instance|
|Http406|Http 406|Count|Total|Http 406|Instance|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Count|Total|Erreurs de serveur http|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Instance|
|DiskQueueLength|Longueur de file d'attente de disque|Count|Moyenne|Longueur de file d'attente de disque|Instance|
|HttpQueueLength|Longueur de la file d’attente HTTP|Count|Moyenne|Longueur de la file d’attente HTTP|Instance|
|ActiveRequests|Requêtes actives|Count|Total|Requêtes actives|Instance|
|TotalFrontEnds|Nombre total de serveurs frontaux|Count|Moyenne|Nombre total de serveurs frontaux|Aucune dimension|
|SmallAppServicePlanInstances|Workers de plan App Service de petite taille|Count|Moyenne|Workers de plan App Service de petite taille|Aucune dimension|
|MediumAppServicePlanInstances|Workers de plan App Service de taille moyenne|Count|Moyenne|Workers de plan App Service de taille moyenne|Aucune dimension|
|LargeAppServicePlanInstances|Workers de plan App Service de grande taille|Count|Moyenne|Workers de plan App Service de grande taille|Aucune dimension|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|WorkersTotal|Nombre total de workers|Count|Moyenne|Nombre total de workers|Aucune dimension|
|WorkersAvailable|Workers disponibles|Count|Moyenne|Workers disponibles|Aucune dimension|
|WorkersUsed|Workers utilisés|Count|Moyenne|Workers utilisés|Aucune dimension|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Instance|

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les métriques dans Azure Monitor](monitoring-overview-metrics.md)
* [Créer des alertes sur les métriques](insights-receive-alert-notifications.md)
* [Exporter des métriques vers le stockage, un hub d’événements ou Log Analytics](monitoring-overview-of-diagnostic-logs.md)
