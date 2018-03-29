---
title: Surveiller votre charge de travail à l’aide de vues de gestion dynamique | Microsoft Docs
description: Comment surveiller votre charge de travail à l'aide de vues de gestion dynamique
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 03/15/2018
ms.author: joeyong;barbkess;kevin
ms.openlocfilehash: 3bd7431b9371b1de762558b8bd3c4ed0ed360495
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-your-workload-using-dmvs"></a>Surveiller votre charge de travail à l'aide de vues de gestion dynamique
Cet article décrit comment utiliser des vues de gestion dynamique (DMV) pour surveiller votre charge de travail. Cela inclut l’examen de l’exécution des requêtes dans Azure SQL Data Warehouse.

## <a name="permissions"></a>Autorisations
Pour interroger les vues de gestion dynamique (DMV) de cet article, vous avez besoin de l’autorisation VIEW DATABASE STATE ou CONTROL. L’autorisation généralement accordée est VIEW DATABASE STATE car elle est beaucoup plus restrictive.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Suivi des connexions
Toutes les connexions à SQL Data Warehouse sont consignées dans [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Cette DMV contient les 10 000 dernières connexions.  L’ID de session (session_id) est la clé principale. Elle est affectée de façon séquentielle pour chaque nouvelle connexion.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Surveillance de l’exécution des rêquetes
Toutes les requêtes exécutées sur SQL Data Warehouse sont consignées dans [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Cette DMV contient les 10 000 dernières requêtes exécutées.  L’ID de requête (request_id) identifie de manière unique chaque requête. Il s’agit de la clé principale pour cette DMV.  L’ID de requête (request_id) est affecté de façon séquentielle pour chaque nouvelle requête et a le préfixe QID, qui signifie ID de requête.  En interrogeant cette DMV pour un ID de session (session_id) donné, vous obtenez toutes les requêtes pour une connexion donnée.

> [!NOTE]
> Les procédures stockées utilisent plusieurs ID de requête.  Les ID de requête sont affectés dans un ordre séquentiel. 
> 
> 

Voici les étapes à suivre pour analyser les heures et les plans d’exécution d’une requête spécifique.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>ÉTAPE 1 : Identifier la requête que vous souhaitez examiner
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Dans les résultats de requête précédents, **notez l’ID de la requête** que vous souhaitez examiner.

Les requêtes ayant l’état **Interrompu** sont mises en file d’attente en raison des limites de concurrence. Ces requêtes apparaissent également dans la requête sys.dm_pdw_waits de type UserConcurrencyResourceType. Pour plus d’informations sur les limites de simultanéité, consultez [Niveaux de performances](performance-tiers.md) ou [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md). Les requêtes peuvent également attendre d’autres raisons, par exemple des verrouillages d’objets.  Si votre requête est en attente d’une ressource, consultez [Examen des requêtes en attente de ressources][Investigating queries waiting for resources] plus loin dans cet article.

Pour simplifier la recherche d’une requête dans la table sys.dm_pdw_exec_requests, utilisez [LABEL][LABEL] pour affecter un commentaire à votre requête qui peut être recherché dans la vue sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>ÉTAPE 2 : examiner le plan de requête
Utilisez l’ID de requête pour récupérer le plan SQL distribué (DSQL) de la requête dans [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Lorsqu’un plan DSQL prend plus de temps que prévu, la cause peut être un plan complexe avec de nombreuses étapes DSQL ou une seule étape chronophage.  Si le plan comprend de nombreuses étapes avec plusieurs opérations de déplacement, envisagez d’optimiser vos distributions de table pour réduire le déplacement des données. L’article [Distribution des tables][Table distribution] explique pourquoi des données doivent être déplacées pour résoudre une requête et explique certaines stratégies de distribution permettant de réduire le déplacement des données.

Pour examiner les détails d’une seule étape, vérifiez la colonne *operation_type* de l’étape de la requête longue et notez **l’index de l’étape** :

* Passez à l’étape 3a pour les **opérations SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Passez à l’étape 3b pour **les opérations de déplacement des données**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>ÉTAPE 3a : examiner SQL dans les bases de données distribuées
Utilisez l’ID de requête et l’index de l’étape pour récupérer des détails dans [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], qui contient des informations sur l’exécution de l’étape de la requête sur toutes les bases de données distribuées.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quand l’étape de la requête est en cours d’exécution, [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] peut être utilisé pour récupérer le plan estimé de SQL Server dans le cache du plan SQL Server pour l’étape en cours d’exécution dans une distribution particulière.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>ÉTAPE 3b : examiner le déplacement des données sur les bases de données distribuées
Utilisez l’ID de requête et l’index de l’étape pour récupérer des informations sur une étape de déplacement des données en cours d’exécution sur chaque distribution dans [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Vérifiez la colonne *total_elapsed_time* pour voir si une distribution particulière prend un temps significativement plus important que les autres pour le déplacement des données.
* Pour la distribution longue, consultez la colonne *rows_processed* pour voir si le nombre de lignes déplacées dans le cadre de cette distribution est nettement plus élevé que les autres. Dans ce cas, cette recherche peut indiquer un décalage des données sous-jacentes.

Si la requête est en cours d’exécution, [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] peut être utilisé pour récupérer le plan estimé de SQL Server auprès du cache de plans SQL Serveur pour l’étape SQL en cours d’exécution dans une distribution particulière.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Surveillance des requêtes en attente
Si vous constatez que votre requête ne progresse pas car elle attend une ressource, voici une requête qui vous indique toutes les ressources attendues par une requête.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Si la requête attend activement des ressources provenant d'une autre requête, l'état affichera **AcquireResources**.  Si la requête possède toutes les ressources requises, l'état sera **Granted**.

## <a name="monitor-tempdb"></a>Surveiller tempdb
Une utilisation élevée de tempdb peut être la cause racine du ralentissement des performances et des problèmes de mémoire insuffisante. Envisagez la mise à l’échelle de votre entrepôt de données si tempdb atteint ses limites lors de l’exécution de requêtes. Les informations suivantes décrivent comment identifier l’utilisation de tempdb par requête sur chaque nœud. 

Créez la vue suivante pour associer l’ID de nœud approprié à sys.dm_pdw_sql_requests. L’ID de nœud vous permet d’utiliser d’autres vues de gestion dynamique directes et de joindre ces tables à sys.dm_pdw_sql_requests.

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
Exécutez la requête suivante pour surveiller tempdb :

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>Surveiller la mémoire

La mémoire peut être la cause racine du ralentissement des performances et des problèmes de mémoire insuffisante. Envisagez la mise à l’échelle de votre entrepôt de données si l’utilisation de la mémoire SQL Server atteint ses limites lors de l’exécution de requêtes.

La requête suivante renvoie des informations sur l’utilisation de la mémoire SQL Server et la saturation de la mémoire par nœud :   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>Surveiller la taille du journal des transactions
La requête suivante renvoie la taille du journal des transactions sur chaque distribution. Si l’un des fichiers journaux atteint 160 Go, vous devez envisager la mise à l’échelle de votre instance ou de limiter la taille de votre transaction. 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>Surveiller la restauration du journal des transactions
Si vos requêtes échouent ou que leur traitement prend beaucoup de temps, vous pouvez vérifier et surveiller si certaines transactions sont en cours de restauration.
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les vues de gestion dynamique, consultez [Vues système][System views].


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
