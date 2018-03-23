---
title: Service Azure SQL Database | Microsoft Docs
description: Découvrez les niveaux de service des bases de données uniques du pool qui permettent de fournir divers niveaux de performance et diverses tailles de stockage.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: 6153616de763eee1b20fff40d38816eca8b455de
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Quels sont les niveaux de service Azure SQL Database ?

[Azure SQL Database](sql-database-technical-overview.md) offre les niveaux de service **De base**, **Standard** et **Premium** pour les [bases de données uniques](sql-database-single-database-resources.md) et les [pools élastiques](sql-database-elastic-pool.md). SQL Database offre un niveau de service d’usage général pour [Azure SQL Database Managed Instance](sql-database-managed-instance.md#managed-instance-service-tier). Les niveaux de service sont principalement différenciés par la plage de niveau de performance, les options de taille de stockage et le prix.  Tous les niveaux de service fournissent une grande souplesse dans la modification de la taille de stockage et le niveau de performance.  Les bases de données uniques et les pools élastiques sont facturés en fonction du niveau de service, du niveau de performance et de la taille de stockage.   

> [!IMPORTANT]
> SQL Database Managed Instance, actuellement en préversion publique, offre un niveau de service unique d’usage général. Pour plus d’informations, consultez [Azure SQL Database Managed Instance](sql-database-managed-instance.md). Le reste de cet article ne s’applique pas à Managed Instance.

## <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Le choix d’un niveau de service dépend principalement des exigences de continuité d’activité, de stockage et de performance.
| | **De base** | **Standard** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|Charge de travail cible|Développement et production|Développement et production|Développement et production||
|Contrat SLA de durée de fonctionnement|99,99 %|99,99 %|99,99 %|N/A pendant la version préliminaire|
|Rétention des sauvegardes|7 jours|35 jours|35 jours|
|UC|Faible|Faible, moyen, élevé|Faible, élevé|
|Débit d’E/S (approximatif) |2,5 IOPS par DTU  | 2,5 IOPS par DTU | 48 IOPS par DTU|
|Latence d’E/S (approximative)|5 ms (lecture), 10 ms (écriture)|5 ms (lecture), 10 ms (écriture)|2 ms (lecture/écriture)|
|OLTP en mémoire et indexation ColumnStore|N/A|N/A|Prise en charge|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Limites de niveau de performance et de taille de stockage

Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, voir [Explication des unités de transaction de base de données (DTU) et des unités de transaction de base de données élastique (eDTU)](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Bases de données uniques

|  | **De base** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale* | 2 Go | 1 To | 4 To  | 
| DTU maximales | 5. | 3000 | 4000 | |
||||||

### <a name="elastic-pools"></a>Pools élastiques

| | **De base** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale par base de données*  | 2 Go | 1 To | 1 To | 
| Taille de stockage maximale par pool* | 156 Go | 4 To | 4 To | 
| Nombre maximal d’eDTU par base de données | 5. | 3000 | 4000 | 
| eDTU maximales par pool | 1 600 | 3000 | 4000 | 
| Nombre maximal de bases de données par pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* Les tailles de stockage supérieures à la quantité de stockage inclue sont en version préliminaire et des coûts supplémentaires s’appliquent. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* Au niveau Premium, plus de 1 To de stockage est actuellement disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, France-Centre, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Corée Centre, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Royaume-Uni Sud, Royaume-Uni Ouest, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis – Virginie et Europe de l’Ouest. Consultez [Limitations actuelles P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Pour plus d’informations sur les niveaux de performances et options de taille de stockage spécifiques disponibles, consultez [Limites de ressources de base de données SQL](sql-database-resource-limits.md).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Ressources d’une base de données unique](sql-database-single-database-resources.md).
- Pour en savoir plus sur les pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).
- En savoir plus sur [l’abonnement Azure et les limites, quotas et contraintes des services](../azure-subscription-service-limits.md)
* En savoir plus sur les [DTU et eDTU](sql-database-what-is-a-dtu.md).
* Pour savoir comment surveiller l’utilisation des DTU, voir [Surveiller et régler les performances](sql-database-troubleshoot-performance.md).

