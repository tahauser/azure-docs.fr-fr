---
title: Ajout d’une partition à l’aide des outils de base de données élastique | Microsoft Docs
description: Utilisation des API avec infrastructure élastique pour ajouter de nouvelles partitions à un ensemble de partitions.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/28/2017
ms.author: sstein
ms.openlocfilehash: 7fd62fff5426dd6aaefaf6dc7d571ab4e7df9cc9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Ajout d’une partition à l’aide des outils de base de données élastique
## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Pour ajouter une partition pour une nouvelle plage ou clé
Les applications ont souvent besoin d’ajouter de nouvelles partitions pour gérer des données attendues de nouvelles clés ou plages de clés, pour une carte de partitions qui existe déjà. Par exemple, une application partitionnée par un ID de client peut requérir l'approvisionnement d'une nouvelle partition pour un nouveau client, ou des données partitionnées mensuellement peuvent requérir l'approvisionnement d'une nouvelle partition avant le début de chaque mois. 

Si la nouvelle plage de valeurs de clé n’appartient pas déjà à un mappage existant, il est simple d’ajouter la nouvelle partition et d’associer la nouvelle clé ou la plage à cette partition. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemple : ajout d’une partition et de sa plage à une carte de partition existante
Cet exemple utilise les méthodes TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.trygetshard), [.NET](https://msdn.microsoft.com/library/azure/dn823929.aspx)), CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)) et CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping\(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0}\))), puis crée une instance de la classe ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base._shard_location), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.)). Dans l’exemple ci-dessous, une base de données nommée **sample_shard_2** et tous les objets de schéma nécessaires qu’elle contient ont été créés pour contenir la plage [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added. 
Shard shard2 = null; 

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
{ 
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
} 

// Create the mapping and associate it with the new shard 
sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 
```

Pour la version .NET, vous pouvez aussi utiliser PowerShell comme alternative pour créer un Gestionnaire de cartes de partitions. Un exemple est disponible [ici](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Pour ajouter une partition pour une partie vide d’une plage existante
Il peut arriver que vous ayez déjà mappé une plage à une partition et l’ayez partiellement remplie avec des données, mais que vous souhaitiez maintenant que les données futures soient dirigées vers une autre partition. Par exemple, vous partitionnez par plage de jours et avez déjà alloué 50 jours à une partition, mais le jour 24, vous souhaitez que les données futures soient dirigées vers une autre partition. [L’outil de fusion et de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md) de la base de données élastique peut effectuer cette opération, mais s’il n’est pas nécessaire de déplacer des données (par exemple, les données de la plage de jours [25, 50), c’est-à-dire le jour 25 inclus et le jour 50 exclu, n’existent pas encore) vous pouvez effectuer cela entièrement en utilisant directement les API de gestion de carte de partition.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemple : fractionnement d’une plage et affectation de la partie vide dans une partition nouvellement ajoutée
Une base de données nommée « sample_shard_2 » et tous les objets de schéma nécessaires qu’elle contient ont été créés.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move 
Shard shard2 = null; 

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
{ 
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
} 

// Split the Range holding Key 25 
sm.SplitMapping(sm.GetMappingForKey(25), 25); 

// Map new range holding [25-50) to different shard: 
// first take existing mapping offline 
sm.MarkMappingOffline(sm.GetMappingForKey(25)); 

// now map while offline to a different shard and take online 
RangeMappingUpdate upd = new RangeMappingUpdate(); 
upd.Shard = shard2; 
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 
```

**Important** : utilisez cette technique seulement si vous êtes certain que la plage du mappage mis à jour est vide.  Les méthodes précédentes ne vérifient pas les données de la plage déplacée. Il est donc préférable d’inclure des vérifications dans votre code.  S’il existe des lignes dans la plage déplacée, la distribution des données réelle ne correspondra pas à la carte des partitions mise à jour. Utilisez [l’outil de fusion et de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md) pour effectuer cette opération, au lieu de le faire dans ces cases.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

