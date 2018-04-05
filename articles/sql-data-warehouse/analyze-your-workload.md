---
title: Analyser la charge de travail - Azure SQL Data Warehouse | Microsoft Docs
description: Techniques d’analyse des priorités de requête de votre charge de travail dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: sqlmojo
manager: jhubbard
ms.topic: conceptual
ms.component: manage
ms.date: 03/28/2018
ms.author: joeyong
ms.reviewer: jrj
ms.openlocfilehash: 7fa5bbd8d9a50bb1dcd1ab5be73f4e248cbbf8fc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="analyze-your-workload"></a>Analyser votre charge de travail
Techniques d’analyse des priorités de requête de votre charge de travail dans Azure SQL Data Warehouse.

## <a name="workload-groups"></a>Groupes de charges de travail 
SQL Data Warehouse implémente des classes de ressources à l’aide de groupes de charges de travail. Il existe en tout huit groupes de charges de travail qui contrôlent le comportement des classes de ressources dans les différentes tailles de DWU. Pour les DWU, SQL Data Warehouse n’utilise que quatre des huit groupes de charges de travail. Cela est logique car chaque groupe de charges de travail est affecté à une des quatre classes de ressources : smallrc, mediumrc, largerc ou xlargerc. Il est important de bien comprendre ces groupes de charges de travail, car certains sont définis comme ayant une *importance*plus élevée. L’importance est utilisée pour la planification du processeur. Les requêtes exécutées avec une importance élevée obtiennent trois fois plus de cycles processeur que celles exécutées avec une importance moyenne. Ainsi, les mappages d’emplacements de concurrence déterminent également la priorité du processeur. Quand une requête utilise 16 emplacements ou plus, elle s’exécute avec une importance élevée.

Le tableau ci-dessous présente les mappages d’importance pour chaque groupe de charges de travail.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mappage des groupes de charges de travail aux emplacements de concurrence et au niveau d’importance

| Groupes de charges de travail | Mappage d’emplacement de concurrence | Mo / Distribution (élasticité) | Mo / Distribution (calcul) | Mappage d’importance |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Moyenne             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Moyenne             |
| SloDWGroupC02   | 4                        |    400                         | 1 000                        | Moyenne             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Moyenne             |
| SloDWGroupC04   | 16                       |  1 600                         | 4000                        | Élevé               |
| SloDWGroupC05   | 32                       |  3 200                         | 8000                        | Élevé               |
| SloDWGroupC06   | 64                       |  6 400                         | 16 000                      | Élevé               |
| SloDWGroupC07   | 128                      | 12 800                         | 32 000                      | Élevé               |
| SloDWGroupC08   | 256                      | 25 600                         | 64 000                      | Élevé               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
À partir du graphique **Allocation et consommation des emplacements de concurrence** , vous pouvez constater qu’une DW500 utilise 1, 4, 8 ou 16 emplacements de concurrence pour smallrc, mediumrc, largerc et xlargerc, respectivement. Vous pouvez rechercher ces valeurs dans le graphique précédent pour connaître l’importance de chaque classe de ressources.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mappage d’importance des DW500 aux classes de ressources
| classe de ressources | Groupe de charges de travail | Emplacements de concurrence utilisés | Mo / Distribution | importance |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Moyenne     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Moyenne     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Moyenne     |
| xlargerc       | SloDWGroupC04  | 16                     | 1 600             | Élevé       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Moyenne     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Moyenne     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Moyenne     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Moyenne     |
| staticrc50     | SloDWGroupC03  | 16                     | 1 600             | Élevé       |
| staticrc60     | SloDWGroupC03  | 16                     | 1 600             | Élevé       |
| staticrc70     | SloDWGroupC03  | 16                     | 1 600             | Élevé       |
| staticrc80     | SloDWGroupC03  | 16                     | 1 600             | Élevé       |

## <a name="view-workload-groups"></a>Afficher les groupes de charges de travail
Vous pouvez utiliser la requête DMV suivante pour examiner en détail les différences d’allocation des ressources mémoire du point de vue du gouverneur de ressources, ou analyser l’utilisation active et historique des groupes de charges de travail lors de la résolution des problèmes.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Détection des requêtes en file d’attente et autres vues de gestion dynamique
Vous pouvez utiliser la DMV `sys.dm_pdw_exec_requests` pour identifier les requêtes en attente dans une file d’attente de concurrence. Les requêtes en attente pour un emplacement de concurrence auront le statut **suspendu**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Vous pouvez afficher les rôles de gestion des charges de travail avec `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

La requête suivante montre le rôle auquel est affecté chaque utilisateur.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse offre les types d’attente suivants :

* **LocalQueriesConcurrencyResourceType**: requêtes qui figurent à l’extérieur de l’infrastructure d’emplacements de concurrence. Les requêtes DMV et les fonctions système telles que `SELECT @@VERSION` sont des exemples de requête locale.
* **UserConcurrencyResourceType**: requêtes qui figurent à l’intérieur de l’infrastructure d’emplacements de concurrence. Les requêtes exécutées sur des tables d’utilisateurs finaux sont des exemples de requêtes qui doivent utiliser ce type de ressource.
* **DmsConcurrencyResourceType**: attentes résultant d’opérations de déplacement de données.
* **BackupConcurrencyResourceType**: cette attente indique qu’une base de données est en cours de sauvegarde. La valeur maximale de ce type de ressource est égale à 1. Si plusieurs sauvegardes ont été demandées en même temps, les autres sont placées en file d’attente.

Vous pouvez utiliser la DMV `sys.dm_pdw_waits` pour connaître les ressources attendues par une demande.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

La DMV `sys.dm_pdw_resource_waits` affiche uniquement les attentes de ressources consommées par une requête donnée. Le temps d’attente d’une ressource mesure uniquement le délai nécessaire à la fourniture de la ressource, par opposition au temps d’attente de signal qui correspond au délai requis par les serveurs SQL sous-jacents pour planifier la requête dans le processeur.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
Vous pouvez aussi utiliser la DMV `sys.dm_pdw_resource_waits` pour calculer la quantité d’emplacements de concurrence accordée.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

Vous pouvez utiliser la DMV `sys.dm_pdw_wait_stats` pour l’analyse des tendances historiques des attentes.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la gestion de la sécurité et des utilisateurs de base de données, consultez [Sécuriser une base de données dans SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Pour plus d’informations sur la façon dont les classes de ressources plus élevées peuvent améliorer la qualité des index columnstore en cluster, consultez [Reconstruire des index pour améliorer la qualité de segment].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Reconstruire des index pour améliorer la qualité de segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
