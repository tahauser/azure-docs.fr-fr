---
title: "Sauvegardes Azure SQL Data Warehouse - instantanés, géoredondant | Microsoft Docs"
description: "Découvrez les sauvegardes de base de données intégrées de SQL Data Warehouse, qui vous permettent de restaurer un entrepôt de données SQL Azure à un point de restauration ou dans une autre région géographique."
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 159a1d34caba829750da33dbc4ad403fb21cd147
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Sauvegarde et restauration dans SQL Data Warehouse
Cet article explique les spécificités des sauvegardes dans SQL Data Warehouse. Utilisez des sauvegardes d’entrepôts de données pour restaurer une capture instantanée de base de données dans la région principale, ou restaurer une géosauvegarde pour votre région associée géographiquement. 

## <a name="what-is-a-data-warehouse-backup"></a>Qu’est-ce qu’une sauvegarde d’entrepôt de données ?
Une sauvegarde d’entrepôt de données est une copie de votre base de données que vous pouvez utiliser pour restaurer un entrepôt de données.  Comme SQL Data Warehouse est un système distribué, une sauvegarde d’entrepôt de données est constituée de nombreux fichiers qui sont stockés dans le stockage Azure. Une sauvegarde de l’entrepôt de données inclut des instantanés de bases de données locales et des géosauvegardes de toutes les bases de données et les fichiers qui sont associés à un entrepôt de données. 

## <a name="local-snapshot-backups"></a>Sauvegardes de captures instantanées locales
SQL Data Warehouse capture des instantanés de votre entrepôt de données tout au long de la journée. Les instantanés sont disponibles pour sept jours. SQL Data Warehouse prend en charge un objectif de point de récupération (RPO) de huit heures. Vous pouvez restaurer votre entrepôt de données dans la région primaire à partir de n’importe quel instantané pris au cours des sept derniers jours.

Pour voir quand la dernière capture instantanée a démarré, exécutez cette requête sur votre entrepôt de données SQL Data Warehouse en ligne. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>Géosauvegardes
SQL Data Warehouse effectue une géosauvegarde une fois par jour vers un [centre de données couplé](../best-practices-availability-paired-regions.md). Le RPO pour une géo-restauration est de 24 heures. Vous pouvez restaurer la géosauvegarde sur le serveur dans la région associée géographiquement. Cette géosauvegarde vous garantit de pouvoir restaurer un entrepôt de base de données dans le cas où vous ne pouvez pas accéder aux captures instantanées de votre région primaire.

Les géosauvegardes sont activées par défaut. Si votre entrepôt de données est optimisé pour l’élasticité, vous pouvez les [désactiver](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) si vous le souhaitez. Vous ne pouvez pas désactiver les géosauvegardes avec le niveau de performance optimisé pour le calcul.

## <a name="backup-costs"></a>Coûts de sauvegarde
Vous remarquerez que la facture Azure a un élément de ligne pour le Stockage Premium Azure et un élément de ligne pour le stockage géoredondant. Les frais de Stockage Premium sont le coût total pour le stockage de vos données dans la région primaire, ce qui inclut les instantanés.  Les frais géoredondants couvrent le coût du stockage des géosauvegardes.  

Le coût total de votre entrepôt de données principal et de sept jours de captures instantanées d’objets blob Azure est arrondi au To le plus proche. Par exemple, si la taille de votre entrepôt de données est de 1,5 To et que les captures instantanées utilisent 100 Go, vous êtes facturé pour 2 To de données aux prix du Stockage Premium Azure. 

> [!NOTE]
> Chaque capture instantanée est initialement vide et augmente à mesure que vous apportez des modifications à l’entrepôt de données principal. La taille de toutes les captures instantanées augmente au fil des modifications de l’entrepôt de données. Par conséquent, les coûts de stockage pour les captures instantanées augmentent en fonction de l’importance des modifications.
> 
> 

Si vous utilisez le stockage géoredondant, vous payez des frais de stockage distincts. Le stockage géoredondant est facturé au prix standard du stockage géoredondant avec accès en lecture (RA-GRS).

Pour plus d’informations sur la tarification de SQL Data Warehouse, consultez [SQL Data Warehouse Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Rétention des captures instantanées lorsqu’un entrepôt de données est mis en suspens
SQL Data Warehouse ne crée pas de captures instantanées et ne fait pas expirer les captures instantanées pendant la mise en suspens d’un entrepôt de données. L’ancienneté de la capture instantanée ne change pas pendant que l’entrepôt de données est mis en suspens. La rétention des captures instantanées est basée sur le nombre de jours pendant lesquels l’entrepôt de données est en ligne et non pas sur les jours du calendrier.

Par exemple, si une capture instantanée démarre le 1er octobre à 16h00 et que l’entrepôt de données est mis en suspens le 3 octobre à 16h00, l’ancienneté des captures instantanées est de deux jours. Quand l’entrepôt de données revient en ligne, la capture instantanée a une ancienneté de deux jours. Si l’entrepôt de données est mis en ligne le 5 octobre à 16h00, l’instantané a une ancienneté de deux jours et est conservé pendant encore cinq jours.

Quand l’entrepôt de données revient en ligne, SQL Data Warehouse reprend de nouvelles captures instantanées et fait expirer les captures instantanées quand elles ont plus de sept jours de données.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>Puis-je restaurer un entrepôt de données supprimé ?
Lorsque vous supprimez un entrepôt de données, SQL Data Warehouse crée une capture instantanée finale et l’enregistre pendant sept jours. Vous pouvez restaurer l’entrepôt de données au point de restauration final créé lors de la suppression. 

> [!IMPORTANT]
> Si vous supprimez une instance SQL Server logique, toutes les bases de données qui appartiennent à l’instance sont également supprimées, sans pouvoir être récupérées. Vous ne pouvez pas restaurer un serveur supprimé.
> 

## <a name="next-steps"></a>Étapes suivantes
Pour restaurer un entrepôt de données SQL, consultez [Restaurer un entrepôt de données SQL](sql-data-warehouse-restore-database-overview.md).

Pour une vue d’ensemble de la continuité des activités, consultez [Vue d’ensemble de la continuité des activités](../sql-database/sql-database-business-continuity.md)
