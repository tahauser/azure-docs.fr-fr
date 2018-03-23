---
title: Compteurs de performance pour le Gestionnaire de cartes de partitions
description: Classe ShardMapManager et compteurs de performances pour le routage dépendant des données
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/23/2016
ms.author: sstein
ms.openlocfilehash: 11f19348e91f11d73d885f5d9bb70245c709fab9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="performance-counters-for-shard-map-manager"></a>Compteurs de performance pour le Gestionnaire de cartes de partitions
Vous pouvez recueillir les performances d’un [gestionnaire de cartes de partitions](sql-database-elastic-scale-shard-map-management.md), en particulier lorsque vous utilisez un [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md). Les compteurs sont créés à l’aide des méthodes de la classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Ils sont utilisés pour suivre les performances des opérations de [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) . Ces compteurs sont accessibles dans l’Analyseur de performances, sous la catégorie « Base de données élastique : gestion des partitions ».

**Pour obtenir la version la plus récente :** accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Vous pouvez également consulter l’article [Mettre à niveau une application pour utiliser la dernière version de la bibliothèque cliente de bases de données élastiques](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Prérequis

* Pour créer la catégorie et les compteurs de performances, l’utilisateur doit être membre du groupe **Administrateurs** local groupe sur l’ordinateur qui héberge l’application.  
* Pour créer une instance de compteur de performances et mettre à jour les compteurs, l’utilisateur doit être membre du groupe **Administrateurs** ou du groupe **Utilisateurs de l’Analyseur de performances**. 

## <a name="create-performance-category-and-counters"></a>Création de catégories et de compteurs de performances
Pour créer les compteurs, appelez la méthode CreatePeformanceCategoryAndCounters de la [classe ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Seul un administrateur peut exécuter la méthode : 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Vous pouvez également utiliser [ce](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script PowerShell pour exécuter la méthode. La méthode crée les compteurs de performances suivants :  

* **Cached mappings**(Mappages mis en cache) : nombre de mappages mis en cache pour la carte de partitions.
* **DDR operations/sec**(Opérations DDR/s) : taux d’opérations de routage dépendant des données pour la carte de partitions. Ce compteur est mis à jour lorsqu’un appel à [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) permet d’établir la connexion à la partition de destination. 
* **Mapping lookup cache hits/sec**(Nb de recherches de mappage réussies dans le cache/s) : taux de réussite des recherches de mappages dans le cache sur la carte de partitions. 
* **Mapping lookup cache misses/sec**(Nb de recherches de mappage ayant échoué dans le cache/s) : taux d’échec des recherches de mappages dans le cache sur la carte de partitions.
* **Mappings added or updated in cache/sec**(Mappages ajoutés ou mis à jour dans le cache/s) : taux d’ajout ou de mise à jour des mappages dans le cache pour la carte de partitions. 
* **Mappings removed from cache/sec**(Mappages supprimés du cache/s) : taux de suppression des mappages dans le cache pour la carte de partitions. 

Les compteurs de performances sont créés pour chaque carte de partitions mises en cache par processus.  

## <a name="notes"></a>Notes
Les événements suivants déclenchent la création des compteurs de performances :  

* Initialisation de [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) avec [chargement hâtif](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), si l’objet ShardMapManager contient des cartes de partitions, avec les méthodes [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) et [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx).
* Recherche réussie d’une carte de partitions (à l’aide de [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Création réussie de la carte de partitions à l’aide de CreateShardMap().

Les compteurs de performance seront mis à jour par toutes les opérations du cache sur la carte de partitions et sur les mappages. Suppression réussie de la carte de partitions à l’aide de DeleteShardMap(), entraînant la suppression de l’instance des compteurs de performances.  

## <a name="best-practices"></a>Meilleures pratiques
* Il est recommandé de créer la catégorie et les compteurs de performances une fois seulement avant la création de l’objet ShardMapManager. Chaque exécution de la commande CreatePerformanceCategoryAndCounters() efface les compteurs précédents (perte de données signalée par toutes les instances) et en crée de nouveaux.  
* Des instances de compteurs de performances sont créées pour chaque processus. Toute panne de l’application ou suppression d’une carte de partitions dans le cache entraîne la suppression des instances de compteurs de performances.  

### <a name="see-also"></a>Vous pouvez également consulter l’article 
[Vue d’ensemble des fonctionnalités de base de données élastique](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

