---
title: Service Azure SQL Database | Microsoft Docs
description: "Découvrez les niveaux de service des bases de données uniques du pool qui permettent de fournir divers niveaux de performance et diverses tailles de stockage."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 01/29/2018
ms.author: carlrab
ms.openlocfilehash: af845d62b8e635449ada98cdea23f407815ffeb0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Quels sont les niveaux de service Azure SQL Database

[Azure SQL Database](sql-database-technical-overview.md) offre les niveaux de service **De base**, **Standard**, **Premium**, et **Premium RS** pour les [bases de données uniques](sql-database-single-database-resources.md) et les [pools élastiques](sql-database-elastic-pool.md). Les niveaux de service sont principalement différenciés par la plage de niveau de performance, les options de taille de stockage et le prix.  Tous les niveaux de service fournissent une grande souplesse dans la modification de la taille de stockage et le niveau de performance.  Les bases de données uniques et les pools élastiques sont facturés en fonction du niveau de service, du niveau de performance et de la taille de stockage.   

## <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Le choix d’un niveau de service dépend principalement des exigences de continuité d’activité, de stockage et de performance.
| | **De base** | **Standard** |**Premium** |**Premium RS** |
| :-- | --: |--:| --:| --:| 
|Charge de travail cible|Développement et production|Développement et production|Développement et production|Une charge de travail qui peut tolérer une perte de données jusqu'à 5 minutes en raison d’échecs de service|
|Contrat SLA de durée de fonctionnement|99,99 %|99,99 %|99,99 %|N/A pendant la version préliminaire|
|Rétention des sauvegardes|7 jours|35 jours|35 jours|35 jours|
|UC|Faible|Faible, moyen, élevé|Faible, élevé|Moyenne|
|Débit d’E/S|Faible  | Moyenne | Ordre de grandeur supérieur à Standard|Identique à Premium|
|Latence d’E/S|Supérieur à Premium|Supérieur à Premium|Inférieur aux niveaux De base et Standard|Identique à Premium|
|OLTP en mémoire et indexation ColumnStore|N/A|N/A|Prise en charge|Prise en charge|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Limites de niveau de performance et de taille de stockage

Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, voir [Explication des unités de transaction de base de données (DTU) et des unités de transaction de base de données élastique (eDTU)](sql-database-what-is-a-dtu.md).

### <a name="single-databases"></a>Bases de données uniques

|  | **De base** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale* | 2 Go | 1 To | 4 To  | 1 To  |
| DTU maximales | 5. | 3000 | 4000 | 1 000 |
||||||

### <a name="elastic-pools"></a>Pools élastiques

| | **De base** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale par base de données*  | 2 Go | 1 To | 1 To | 1 To |
| Taille de stockage maximale par pool* | 156 Go | 4 To | 4 To | 1 To |
| Nombre maximal d’eDTU par base de données | 5. | 3000 | 4000 | 1 000 |
| eDTU maximales par pool | 1 600 | 3000 | 4000 | 1 000 |
| Nombre maximal de bases de données par pool | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \* Les tailles de stockage supérieures à la quantité de stockage inclue sont en version préliminaire et des coûts supplémentaires s’appliquent. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* Au niveau Premium, plus de 1 To de stockage est actuellement disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, France-Centre, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Corée Centre, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Royaume-Uni Sud, Royaume-Uni Ouest, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis - Virginie et Europe de l’Ouest. Consultez [Limitations actuelles P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Pour plus d’informations sur les niveaux de performances et options de taille de stockage spécifiques disponibles, consultez [Limites de ressources de base de données SQL](sql-database-resource-limits.md).


## <a name="next-steps"></a>étapes suivantes

- En savoir plus sur les [Ressources d’une base de données unique](sql-database-single-database-resources.md).
- Pour en savoir plus sur les pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).
- En savoir plus sur [l’abonnement Azure et les limites, quotas et contraintes des services](../azure-subscription-service-limits.md)
* En savoir plus sur les [DTU et eDTU](sql-database-what-is-a-dtu.md).
* Pour savoir comment surveiller l’utilisation des DTU, voir [Surveiller et régler les performances](sql-database-troubleshoot-performance.md).

