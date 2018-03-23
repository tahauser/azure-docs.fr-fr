---
title: Interroger des bases de données SQL Azure partitionnées | Microsoft Docs
description: Exécutez des requêtes en utilisant la bibliothèque cliente des bases de données élastiques.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/28/2017
ms.author: sstein
ms.openlocfilehash: 2712968f2929c48318e781fa846a8de525a0ef0c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="multi-shard-querying"></a>Requête sur plusieurs partitions
## <a name="overview"></a>Vue d'ensemble
Avec les [outils des bases de données élastiques](sql-database-elastic-scale-introduction.md), vous pouvez créer des solutions de base de données partitionnée. **Requête sur plusieurs partitions** est utilisée pour des tâches telles que la collecte de données / la création de rapports qui nécessitent l'exécution d'une requête qui s'étend sur plusieurs partitions. (Comparez cela au [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)qui effectue tout le travail sur une partition unique.) 

1. Obtenez un **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) ou **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) à l’aide de la méthode **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), ou **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)). Consultez les rubriques **[Construction d’un objet ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)** et **[Obtenir un objet RangeShardMap ou ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Créez un objet **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)).
3. Créez **MultiShardStatement ou MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Définissez la **propriété CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) sur une commande T-SQL.
5. Exécutez la commande en appelant la méthode **ExecuteQueryAsync ou ExecuteReader** ([Java](), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)).
6. Affichez les résultats à l’aide de la classe **MultiShardResultSet ou MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)). 

## <a name="example"></a>Exemples
Le code suivant illustre l'utilisation de la requête sur plusieurs partitions à l'aide d'une **ShardMap** donnée nommée *myShardMap*. 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
{ 
    using (MultiShardCommand cmd = conn.CreateCommand())
    { 
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
        cmd.CommandType = CommandType.Text; 
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

        using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
        { 
            while (sdr.Read())
            { 
                var c1Field = sdr.GetString(0); 
                var c2Field = sdr.GetFieldValue<int>(1); 
                var c3Field = sdr.GetFieldValue<Int64>(2);
            } 
        } 
    } 
} 
```

La principale différence est la construction de connexions à plusieurs partitions. Lorsque **SqlConnection** fonctionne sur une base de données unique, l’objet **MultiShardConnection** utilise une ***collection de partitions*** comme entrée. Remplissez la collection de partitions à partir d'une carte de partitions. La requête est ensuite exécutée sur la collection de partitions à l'aide de la sémantique **UNION ALL** pour assembler un seul résultat global. Le nom de la partition d'où provient la ligne peut éventuellement être ajouté à la sortie à l'aide de la propriété sur la commande **ExecutionOptions** . 

Notez l'appel à **myShardMap.GetShards()**. Cette méthode récupère toutes les partitions de la carte de partitions et offre un moyen facile d’exécuter une requête sur toutes les bases de données concernées. La collection de partitions pour une requête sur plusieurs partitions peut être affinée davantage en effectuant une requête LINQ sur la collection retournée par l'appel à **myShardMap.GetShards()**. En combinaison avec la stratégie de résultats partiels, la capacité actuelle de l'interrogation de plusieurs partitions a été conçue pour fonctionner correctement avec des dizaines, voire des centaines, de partitions.

Une limitation de l'interrogation de plusieurs partitions est actuellement le manque de validation des partitions et shardlets interrogés. Tandis que le routage dépendant des données vérifie qu'une partition donnée fait partie de la carte de partitions au moment de l'interrogation, les requêtes sur plusieurs partitions n'effectuent pas cette vérification. De ce fait, les requêtes sur plusieurs partitions peuvent s’exécuter sur des bases de données qui ont été supprimées de la carte de partitions.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Requêtes sur plusieurs partitions et opérations de fractionnement et de fusion
Les requêtes sur plusieurs partitions ne vérifient pas si les shardlets de la base de données interrogée participent à des opérations de fractionnement et de fusion en cours. (Consultez [Mise à l’échelle utilisant l’outil de fractionnement et de fusion de bases de données élastiques](sql-database-elastic-scale-overview-split-and-merge.md).) Cela peut entraîner des incohérences avec des lignes du même shardlet qui s’affichent pour plusieurs bases de données dans la même requête sur plusieurs partitions. Tenez compte de ces limitations et envisagez de purger les opérations de fractionnement et de fusion en cours et les modifications apportées à la carte de partitions lors de l’exécution de requêtes sur plusieurs partitions.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


