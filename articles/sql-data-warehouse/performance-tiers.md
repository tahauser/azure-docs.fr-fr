---
title: "Niveaux de performance d’Azure SQL Data Warehouse | Microsoft Docs"
description: "Présentation de l’élasticité et des niveaux de performance de calcul optimisé disponibles dans Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 03881c12faed723999e97431e4a69fdeb6bfa10d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="azure-sql-data-warehouse-performance-tiers-preview"></a>Niveaux de performance d’Azure SQL Data Warehouse (préversion)
SQL Data Warehouse offre deux niveaux de performance optimisés pour les charges de travail analytiques. Cet article explique les concepts des niveaux de performance pour vous aider à choisir celui qui est le plus approprié pour votre charge de travail. 

## <a name="what-is-a-performance-tier"></a>Qu’est-ce qu’un niveau de performance ?
Un niveau de performance est une option déterminant la configuration de votre entrepôt de données. Cette option est l’un des premiers choix que vous opérez lors de la création d’un entrepôt de données.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- Le **niveau de performance Optimisé pour l’élasticité** sépare les couches de calcul et de stockage dans l’architecture. Cette option convient parfaitement à des charges de travail qui peuvent exploiter au maximum la séparation entre le calcul et le stockage en effectuant des mises à l’échelle régulières pour prendre en charge de brèves périodes de pic d’activité. Ce niveau de calcul est proposé à un prix très attractif, et peut être mis à l’échelle pour prendre en charge la majorité des charges de travail des clients.

- Le **niveau de performance Optimisé pour le calcul** utilise le matériel Azure le plus récent pour introduire un nouveau cache de disque SSD NVMe qui garde les données les plus fréquemment consultées à proximité des processeurs, précisément là où vous souhaitez qu’elles se trouvent. En hiérarchisant automatiquement le stockage, ce niveau de performance correspond parfaitement aux requêtes complexes, puisque toutes les E/S sont gardées en local au niveau de la couche de calcul. En outre, le columnstore a été amélioré pour stocker un nombre illimité de données dans votre SQL Data Warehouse. Le niveau de performance Optimisé pour le calcul fournit le plus haut niveau d’extensibilité pour vous permettre de mettre à l’échelle jusqu’à 30 000 cDWU. Choisissez ce niveau pour les charges de travail nécessitant des performances exceptionnelles en continu.

## <a name="service-levels"></a>Niveaux de service
L’Objectif de niveau de service (SLO) est le paramètre d’extensibilité qui détermine le niveau de coût et de performance de votre entrepôt de données. Les niveaux de service pour l’échelle de niveau de performance Optimisé pour le calcul sont mesurés en unités cDWU (compute Data Warehouse Unit), par exemple DW2000c. Les niveaux de service Optimisé pour l’élasticité sont mesurés en unités DWU (Data Warehouse Unit), par exemple DW2000. Pour plus d’informations, voir la définition des unités [Data Warehouse Unit (DWU) et compute Data Warehouse Unit (cDWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md).

Dans T-SQL, le paramètre SERVICE_OBJECTIVE détermine les niveaux de service et de performance de votre entrepôt de données.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="memory-maximums"></a>Valeurs maximales de mémoire
Les niveaux de performance ont des profils de mémoire différents qui se traduisent par une quantité de mémoire spécifique par requête. Le niveau de performance Optimisé pour le calcul fournit 2,5 fois plus de mémoire par requête que le niveau de performance Optimisé pour l’élasticité. Cette mémoire supplémentaire permet au niveau de performance Optimisé pour le calcul d’offrir une rapidité extrême. Cette mémoire supplémentaire par requête permet également d’exécuter davantage de requêtes simultanément, car les requêtes peuvent utiliser des [classes de ressources](resource-classes-for-workload-management.md) inférieures. 

### <a name="optimized-for-elasticity"></a>Optimisé pour l’élasticité

Les niveaux de service pour le niveau performance Optimisé pour l’élasticité sont compris entre DW100 et DW6000. 

| Niveau de service | Nombre maximal de requêtes simultanées | Nœuds de calcul | Distributions par nœud de calcul | Mémoire maximale par distribution (Mo) | Mémoire maximale par entrepôt de données (Go) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1,200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1 600                            |  96                                |
| DW500         | 20                     | 5.             | 12                             | 2 000                            | 120                                |
| DW600         | 24                     | 6.             | 10                             | 2 400                            | 144                                |
| DW1000        | 32                     | 10            | 6.                              | 4 000                            | 240                                |
| DW1200        | 32                     | 12            | 5.                              | 4 800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6 000 / 750                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8 000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12,000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24 000                           | 1 440                               |

### <a name="optimized-for-compute"></a>Optimisé pour le calcul

Les niveaux de service pour le niveau de performance Optimisé pour le calcul sont compris entre DW1000c et DW30000c. 

| Niveau de service | Nombre maximal de requêtes simultanées | Nœuds de calcul | Distributions par nœud de calcul | Mémoire maximale par distribution (Go) | Mémoire maximale par entrepôt de données (Go) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1 200                              |
| DW2500c       | 32                     | 5.             | 12                             |  25                              |  1 500                              |
| DW3000c       | 32                     | 6.             | 10                             |  30                              |  1 800                              |
| DW5000c       | 32                     | 10            | 6.                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5.                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

Le nombre maximal de cDWU est DW30000c, qui correspond à 60 nœuds de calcul et une distribution par nœud de calcul. Par exemple, un entrepôt de données de 600 To à DW30000c traite environ 10 To par nœud de calcul.

## <a name="concurrency-maximums"></a>Valeurs maximales de concurrence
SQL Data Warehouse offre une concurrence de pointe sur un entrepôt de données unique. Pour s’assurer que chaque requête dispose de suffisamment de ressources pour s’exécuter efficacement, le système suit l’utilisation des ressources de calcul en assignant des emplacements de concurrence à chaque requête. Le système place les requêtes en file d’attente jusqu’à ce que suffisamment d’emplacements de concurrence soient disponibles. 

Les emplacements de concurrence déterminent également la hiérarchisation des priorités du processeur. Pour plus d’informations, voir [Analyser votre charge de travail](analyze-your-workload.md).

### <a name="concurrency-slots"></a>Emplacements de concurrence
Les emplacements de concurrence sont pratiques pour suivre les ressources disponibles pour l’exécution des requêtes. Il sont comparables à des tickets que vous achetez afin de réserver des sièges pour assister à un concert dont le nombre de places est limité. Semblablement, SQL Data Warehouse dispose d’un nombre fini de ressources de calcul. Les requêtes réservent des ressources de calcul en acquérant des emplacements de concurrence. Avant que son exécution puisse démarrer, une requête doit pouvoir réserver suffisamment d’emplacements de concurrence. Une fois terminée, la requête libère ses emplacements de concurrence. 

* Le niveau de performance Optimisé pour l’élasticité peut adapter l’échelle jusqu’à 240 emplacements de concurrence.
* Le niveau de performance Optimisé pour le calcul peut adapter l’échelle jusqu’à 1 200 emplacements de concurrence.

Chaque requête consomme zéro, un ou plusieurs emplacements de concurrence. Les requêtes système et certaines requêtes banales ne consomment aucun emplacement. Par défaut, les requêtes régies par des classes de ressources nécessitent un emplacement de concurrence. Des requêtes plus complexes peuvent nécessiter les emplacements de concurrence supplémentaires.  

- Une requête s’exécutant avec 10 emplacements de concurrence peut accéder à 5 fois plus de ressources de calcul qu’une requête s’exécutant avec 2 emplacements de concurrence.
- Si chaque requête nécessite 10 emplacements de concurrence alors que 40 sont disponibles, seules 4 requêtes peuvent s’exécuter simultanément.
 
Seules ds requêtes régies par des ressources consomment des emplacements de concurrence. Le nombre exact d’emplacements de concurrence consommés est déterminé par la [classe de ressources](resource-classes-for-workload-management.md) de la requête.

### <a name="optimized-for-compute"></a>Optimisé pour le calcul
Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources dynamique](resource-classes-for-workload-management.md).  Ces informations s’appliquent au niveau de performance Optimisé pour le calcul.

**Classes de ressources dynamiques**
| Niveau de service | Nombre maximal de requêtes concurrentes | Emplacements de concurrence disponibles | Emplacements utilisés par smallrc | Emplacements utilisés par mediumrc | Emplacements utilisés par largerc | Emplacements utilisés par xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1 200                        | 1                     | 64                     | 128                   | 256                    |

**Classes de ressources statiques**

Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources statique](resource-classes-for-workload-management.md).  

| Niveau de service | Nombre maximal de requêtes concurrentes | Emplacements de concurrence disponibles |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1 200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Optimisé pour l’élasticité
Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources dynamique](resource-classes-for-workload-management.md).  Ces informations s’appliquent au niveau de performance Optimisé pour l’élasticité.

**Classes de ressources dynamiques**

| Niveau de service | Nombre maximal de requêtes concurrentes | Emplacements de concurrence disponibles | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**Classes de ressources statiques** Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources statique](resource-classes-for-workload-management.md).  Ces informations s’appliquent au niveau de performance Optimisé pour l’élasticité.

| Niveau de service | Nombre maximal de requêtes concurrentes | Nombre maximal d’emplacements de concurrence |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Lorsque l’un de ces seuils est atteint, les nouvelles requêtes sont mises en file d’attente et exécutées sur la base du modèle premier entré, premier sorti.  À mesure que les requêtes se terminent et que le nombre de requêtes et d’emplacements chute sous les limites, SQL Data Warehouse libère des requêtes en file d’attente. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon de tirer parti des classes de ressources pour optimiser davantage votre charge de travail, voir les articles suivants :
* [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md)
* [Analyse de votre charge de travail](analyze-your-workload.md)

