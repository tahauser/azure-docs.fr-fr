---
title: Sauvegarde et restauration d’Azure SQL Data Warehouse – instantanés, géoredondants | Microsoft Docs
description: Découvrez comment la sauvegarde et la restauration fonctionnent dans Azure SQL Data Warehouse. Utilisez des sauvegardes d’entrepôt de données pour restaurer votre entrepôt de données à un point de restauration dans la région primaire, ou des sauvegardes géo-redondantes pour restaurer dans une autre région géographique.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 03/22/2018
ms.author: jrj;barbkess
ms.openlocfilehash: e909cb6f31d8bc677d9dfd267dab242eb99f42df
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Sauvegarde et restauration dans Azure SQL Data Warehouse
Découvrez comment la sauvegarde et la restauration fonctionnent dans Azure SQL Data Warehouse. Utilisez des sauvegardes d’entrepôt de données pour restaurer votre entrepôt de données à un point de restauration dans la région primaire, ou des sauvegardes géo-redondantes pour restaurer dans une autre région géographique. 

## <a name="what-is-backup-and-restore"></a>La sauvegarde et la restauration, qu’est-ce que c’est ?
Une *sauvegarde d’entrepôt de données* est une copie de votre base de données que vous pouvez utiliser pour restaurer un entrepôt de données.  Comme SQL Data Warehouse est un système distribué, une sauvegarde d’entrepôt de données est constituée de nombreux fichiers qui sont stockés dans le stockage Azure. Une sauvegarde de l’entrepôt de données inclut des instantanés de bases de données locales et des géosauvegardes de toutes les bases de données et les fichiers qui sont associés à un entrepôt de données. 

Une *restauration d’entrepôt de données* est un nouvel entrepôt de données créé à partir de la sauvegarde d’un entrepôt de données existant ou supprimé. L’entrepôt de données restauré recrée l’entrepôt de données sauvegardé à un moment donné. La restauration de votre entrepôt de données est une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elle recrée vos données après des corruptions et des suppressions accidentelles.

Les restaurations locales et géographiques font partie des fonctionnalités de récupération d’urgence de SQL Data Warehouse. 

## <a name="local-snapshot-backups"></a>Sauvegardes de captures instantanées locales
Les sauvegardes d’instantanés locaux sont une fonctionnalité intégrée du service.  Il est inutile de les activer. 

SQL Data Warehouse capture des instantanés de votre entrepôt de données tout au long de la journée. Les instantanés sont disponibles pour sept jours. SQL Data Warehouse prend en charge un objectif de point de récupération (RPO) de huit heures. Vous pouvez restaurer votre entrepôt de données dans la région primaire à partir de n’importe quel instantané pris au cours des sept derniers jours.

Pour voir quand la dernière capture instantanée a démarré, exécutez cette requête sur votre entrepôt de données SQL Data Warehouse en ligne. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Rétention des captures instantanées lorsqu’un entrepôt de données est mis en suspens
SQL Data Warehouse ne crée pas de captures instantanées et ne fait pas expirer les captures instantanées pendant la mise en suspens d’un entrepôt de données. L’ancienneté de la capture instantanée ne change pas pendant que l’entrepôt de données est mis en suspens. La rétention des captures instantanées est basée sur le nombre de jours pendant lesquels l’entrepôt de données est en ligne et non pas sur les jours du calendrier.

Par exemple, si une capture instantanée démarre le 1er octobre à 16h00 et que l’entrepôt de données est mis en suspens le 3 octobre à 16h00, l’ancienneté des captures instantanées est de deux jours. Quand l’entrepôt de données revient en ligne, la capture instantanée a une ancienneté de deux jours. Si l’entrepôt de données est mis en ligne le 5 octobre à 16h00, l’instantané a une ancienneté de deux jours et est conservé pendant encore cinq jours.

Quand l’entrepôt de données revient en ligne, SQL Data Warehouse reprend de nouvelles captures instantanées et fait expirer les captures instantanées quand elles ont plus de sept jours de données.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Rétention des captures instantanées lorsqu’un entrepôt de données est supprimé
Lorsque vous supprimez un entrepôt de données, SQL Data Warehouse crée une capture instantanée finale et l’enregistre pendant sept jours. Vous pouvez restaurer l’entrepôt de données au point de restauration final créé lors de la suppression. 

> [!IMPORTANT]
> Si vous supprimez une instance SQL Server logique, toutes les bases de données qui appartiennent à l’instance sont également supprimées, sans pouvoir être récupérées. Vous ne pouvez pas restaurer un serveur supprimé.
> 

## <a name="geo-backups"></a>Géosauvegardes
SQL Data Warehouse effectue une géosauvegarde une fois par jour vers un [centre de données couplé](../best-practices-availability-paired-regions.md). Le RPO pour une géo-restauration est de 24 heures. Vous pouvez restaurer la géosauvegarde sur le serveur dans la région associée géographiquement. Une géosauvegarde vous garantit de pouvoir restaurer un entrepôt de base de données dans le cas où vous ne pouvez pas accéder aux captures instantanées de votre région primaire.

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

## <a name="restoring-from-restore-points"></a>Restauration à partir de points de restauration
Chaque capture instantanée a un point de restauration qui représente son heure de début. Pour restaurer un entrepôt de données, choisissez un point de restauration et émettez une commande de restauration.  

SQL Data Warehouse restaure toujours la sauvegarde vers un nouvel entrepôt de données. Vous pouvez conserver les entrepôts de données restauré et actuel ou les supprimer tous les deux. Si vous souhaitez remplacer l’entrepôt de données actuel par l’entrepôt de données restauré, vous pouvez le renommer à l’aide d’[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) avec l’option MODIFIER LE NOM. 

Pour restaurer un entrepôt de données, consultez [Restaurer un entrepôt de données à l’aide du portail Azure](sql-data-warehouse-restore-database-portal.md), [Restaurer un entrepôt de données à l’aide de PowerShell](sql-data-warehouse-restore-database-powershell.md), ou [Restaurer un entrepôt de données à l’aide de T-SQL](sql-data-warehouse-restore-database-rest-api.md).

Si vous devez restaurer un entrepôt de données supprimé ou suspendu, vous pouvez [créer un ticket de support](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Restauration géoredondante
Vous pouvez restaurer votre entrepôt de données vers n’importe quelle région prenant en charge Azure SQL Data Warehouse au niveau de performances que vous avez choisi. 

> [!NOTE]
> Pour effectuer une restauration géoredondante, vous devez avoir activé cette fonctionnalité.
> 
> 

## <a name="restore-timeline"></a>Chronologie de la restauration
Vous pouvez restaurer une base de données à un point de restauration disponible des sept derniers jours. Les captures instantanées démarrent toutes les quatre à huit heures et sont disponibles pendant sept jours. Quand une capture instantanée a une ancienneté supérieure à sept jours, elle expire et son point de restauration n’est plus disponible. 

Il n’y a pas de sauvegardes dans un entrepôt de données suspendu. Si votre entrepôt de données est suspendu pendant plus de sept jours, vous n’aurez pas de points de restauration. 

## <a name="restore-costs"></a>Coûts de la restauration
Les frais de stockage pour l’entrepôt de données restauré sont facturés au tarif du Stockage Premium Azure. 

Si vous mettez en suspens un entrepôt de données restauré, vous êtes facturé pour le stockage au tarif du Stockage Premium Azure. L’avantage de la mise en suspens est que les ressources de calcul ne vous sont pas facturées.

Pour plus d’informations sur la tarification de SQL Data Warehouse, consultez [SQL Data Warehouse Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restore-use-cases"></a>Cas d’usage de restauration
La restauration de l’entrepôt de données permet principalement de récupérer des données après une corruption ou une perte accidentelle. Elle permet également de conserver une sauvegarde pendant plus de sept jours. Une fois que la sauvegarde est restaurée, l’entrepôt de données est en ligne et vous pouvez le suspendre indéfiniment pour réduire les coûts de calcul. La base de données en pause entraîne des frais de stockage aux tarifs du Stockage Premium Azure. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la planification de la récupération d’urgence, consultez [Vue d’ensemble de la continuité de l’activité](../sql-database/sql-database-business-continuity.md).
