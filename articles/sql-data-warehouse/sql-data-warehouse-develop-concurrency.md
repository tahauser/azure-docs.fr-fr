---
title: Gestion de la concurrence et des charges de travail dans SQL Data Warehouse | Microsoft Docs
description: "Décrit la gestion de la concurrence et des charges de travail dans Azure SQL Data Warehouse pour le développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gestion de la concurrence et des charges de travail dans SQL Data Warehouse
Pour offrir des performances prévisibles à grande échelle, Microsoft Azure SQL Data Warehouse vous permet de contrôler les niveaux de concurrence, ainsi que les allocations de ressources telles que la définition des priorités du processeur et de la mémoire. Cet article vous présente les concepts de gestion de la concurrence et de gestion des charges de travail, en expliquant comment ces deux fonctionnalités ont été implémentées, ainsi que la procédure à suivre pour les contrôler dans votre entrepôt de données. La gestion de charge de travail SQL Data Warehouse a pour but de vous aider à prendre en charge des environnements multi-utilisateurs. Elle n’est pas destinée aux charges de travail multilocataires.

## <a name="concurrency-limits"></a>Limites de concurrence
SQL Data Warehouse autorise jusqu’à 1024 connexions simultanées. Les 1024 connexions peuvent soumettre des requêtes simultanément. Toutefois, pour optimiser le débit, SQL Data Warehouse peut mettre certaines requêtes en file d’attente pour s’assurer que chaque requête reçoit une allocation de mémoire minimale. La mise en file d’attente se produit lors de l’exécution de la requête. La mise en file d’attente lorsque les limites de concurrence sont atteintes permet à SQL Data Warehouse d’augmenter le débit total en veillant à ce que les requêtes actives puissent accéder aux ressources de mémoire essentielles.  

Les limites de concurrence sont régies par deux concepts : les *requêtes simultanées* et les *emplacements de concurrence*. Pour qu’une requête s’exécute, elle doit s’exécuter à la fois dans limite de concurrence de requête et dans les limites de l’allocation d’emplacement de concurrence.

* Les requêtes simultanées sont les requêtes s’exécutant simultanément. SQL Data Warehouse prend en charge jusqu’à 32 requêtes simultanées sur les tailles de DWU plus importantes.
* Les emplacements de concurrence sont alloués en fonction de la DWU. Chaque DWU100 fournit 4 emplacements de concurrence. Par exemple, une DW100 alloue 4 emplacements de concurrence et une DW1000 en alloue 40. Chaque requête consomme un ou plusieurs emplacements de concurrence, selon la [classe de ressources](#resource-classes) de la requête. Les requêtes en cours d’exécution dans la classe de ressource smallrc consomment un emplacement de concurrence. Les requêtes en cours d’exécution dans une classe de ressource supérieure consomment plusieurs emplacements de concurrence.

Le tableau suivant décrit les limites des requêtes simultanées et des emplacements de concurrence pour différentes tailles de DWU.

### <a name="concurrency-limits"></a>Limites de concurrence
| DWU | Nombre maximal de requêtes simultanées | Emplacements de concurrence alloués |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

Lorsque l’un de ces seuils est atteint, les nouvelles requêtes sont mises en file d’attente et exécutées sur la base du modèle premier entré, premier sorti.  À mesure que les requêtes se terminent et que le nombre de requêtes et d’emplacements chute en deçà de la limite, les requêtes mises en file d’attente sont publiées. 

> [!NOTE]  
> *Certaines* requêtes s’exécutant exclusivement sur les vues de gestion dynamique (DMV) ou les affichages catalogue ne sont régies par aucune des limites de concurrence. Les utilisateurs peuvent surveiller le système en toutes circonstances, quel que soit le nombre de requêtes en cours d’exécution dessus.
> 
> 

## <a name="resource-classes"></a>Classes de ressources
Les classes de ressources vous aident à contrôler l’allocation de mémoire et les cycles de processeur pour une requête. Vous pouvez affecter deux types de classes de ressources à un utilisateur sous la forme de rôles de base de données. Les deux types de classes de ressources sont les suivantes :
1. Les classes de ressources dynamiques (**smallrc, mediumrc, largerc, xlargerc**) allouent une quantité de mémoire qui varie en fonction de la DWU active. Cela signifie que quand vous évoluez vers une DWU plus importante, vos requêtes obtiennent automatiquement plus de mémoire. 
2. Les classes de ressources statiques (**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**) allouent la même quantité de mémoire, quelle que soit la DWU active (à condition que la DWU dispose elle-même de suffisamment de mémoire). Cela signifie que sur les DWU volumineuses, vous pouvez exécuter davantage de requêtes simultanées dans chaque classe de ressources.

La quantité de mémoire allouée aux utilisateurs de **smallrc** et **staticrc10** étant inférieure, ils peuvent profiter d’une plus grande concurrence. En revanche, les utilisateurs affectés à **xlargerc** ou **staticrc80** disposent de grandes quantités de mémoire, et donc la proportion de leurs requêtes qui peuvent s’exécuter simultanément est plus faible.

Par défaut, chaque utilisateur appartient à la petite classe de ressources, **smallrc**. La procédure `sp_addrolemember` sert à augmenter la classe de ressources, et `sp_droprolemember` à la réduire. Par exemple, cette commande fait évoluer la classe de ressources de loaduser à **largerc** :

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Requêtes qui ne tiennent pas compte des classes de ressources

Certains types de requêtes ne bénéficient pas d’une allocation de mémoire supérieure. Le système ignore la classe de ressources qui leur est allouée et exécute toujours ces requêtes dans la petite classe de ressources. Si ces requêtes sont toujours exécutées dans la petite classe de ressources, elles peuvent s’exécuter lorsque des emplacements de concurrence sont sous pression et elles ne consomment pas plus d’emplacements que nécessaire. Consultez [Exceptions de classe de ressources](#query-exceptions-to-concurrency-limits) pour plus d’informations.

## <a name="details-on-resource-class-assignment"></a>Détails concernant l’affectation des classes de ressources


Voici quelques autres détails concernant la classe de ressources :

* *Modifier le rôle* est obligatoire pour modifier la classe de ressources d’un utilisateur.
* Même si pouvez ajouter un utilisateur à une ou plusieurs classes de ressources supérieures, les classes de ressources dynamiques sont prioritaires sur les classes de ressources statiques. Autrement dit, si un utilisateur est affecté à **mediumrc** (dynamique) et **staticrc80** (statique), la classe de ressources **mediumrc** est celle qui est prise en compte.
 * Quand un utilisateur est affecté à plusieurs classes de ressources d’un type de classe de ressource spécifique (plusieurs classes de ressources dynamiques ou plusieurs classes de ressources statiques), la classe de ressources supérieure est prise en compte. En d’autres termes, si un utilisateur est affecté à la fois à mediumrc et à largerc, la classe de ressources supérieure (largerc) est prise en compte. Et si un utilisateur est affecté à **staticrc20** et à **statirc80**, la classe **staticrc80** est prise en compte.
* Vous ne pouvez pas modifier la classe de ressources de l’administrateur système.

Pour obtenir un exemple détaillé, consultez [Exemple de modification d’une classe de ressources utilisateur](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Allocation de mémoire
L’augmentation de la classe de ressources d’un utilisateur présente des avantages et des inconvénients. Quand la classe de ressources d’un utilisateur évolue, celui-ci obtient plus de mémoire pour ses requêtes, ce qui peut se traduire par une exécution plus rapide de celles-ci.  Toutefois, les classes de ressources supérieures sont par ailleurs associées à une réduction du nombre de requêtes simultanées pouvant s’exécuter. Il s’agit d’un compromis entre l’allocation de grandes quantités de mémoire à une seule requête et la permission de l’exécution d’autres requêtes simultanées (qui nécessitent également des allocations de mémoire). Si de grandes quantités de mémoire sont allouées à un utilisateur pour une requête, les autres utilisateurs n’auront pas accès à cette mémoire pour exécuter une requête.

Le tableau suivant mappe la mémoire allouée à chaque distribution par DWU et classe de ressources.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Allocations de mémoire par distribution pour les classes de ressources dynamiques (Mo)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1 600 |
| DW500 |100 |400 |800 |1 600 |
| DW600 |100 |400 |800 |1 600 |
| DW1000 |100 |800 |1 600 |3 200 |
| DW1200 |100 |800 |1 600 |3 200 |
| DW1500 |100 |800 |1 600 |3 200 |
| DW2000 |100 |1 600 |3 200 |6 400 |
| DW3000 |100 |1 600 |3 200 |6 400 |
| DW6000 |100 |3 200 |6 400 |12 800 |

Le tableau suivant mappe la mémoire allouée à chaque distribution par DWU et classe de ressources statique. Notez que la quantité de mémoire est réduite pour les classes de ressources supérieures de façon à respecter les limites DWU globales.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Allocations de mémoire par distribution pour les classes de ressources statiques (Mo)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1 600 |1 600 |1 600 |1 600 |
| DW500 |100 |200 |400 |800 |1 600 |1 600 |1 600 |1 600 |
| DW600 |100 |200 |400 |800 |1 600 |1 600 |1 600 |1 600 |
| DW1000 |100 |200 |400 |800 |1 600 |3 200 |3 200 |3 200 |
| DW1200 |100 |200 |400 |800 |1 600 |3 200 |3 200 |3 200 |
| DW1500 |100 |200 |400 |800 |1 600 |3 200 |3 200 |3 200 |
| DW2000 |100 |200 |400 |800 |1 600 |3 200 |6 400 |6 400 |
| DW3000 |100 |200 |400 |800 |1 600 |3 200 |6 400 |6 400 |
| DW6000 |100 |200 |400 |800 |1 600 |3 200 |6 400 |12 800 |

À partir du tableau précédent, vous pouvez constater qu’une requête s’exécutant sur une DW2000 dans la classe de ressources **xlargerc** a accès à 6 400 Mo de mémoire dans chacune des 60 bases de données distribuées.  Dans SQL Data Warehouse, il existe 60 distributions. Par conséquent, pour calculer l’allocation totale de mémoire pour une requête d’une classe de ressources données, les valeurs ci-dessus doivent être multipliées par 60.

### <a name="memory-allocations-system-wide-gb"></a>Allocations de mémoire à l’échelle du système (Go)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Dans ce tableau d’allocations de mémoire à l’échelle du système, vous pouvez constater qu’une requête s’exécutant sur une DW2000 dans la classe de ressource xlargerc se voit allouer un total de 375 Go de mémoire (6 400 Mo * 60 distributions / 1 024 pour la conversion en Go) sur l’intégralité de votre instance SQL Data Warehouse.

Le même calcul s’applique aux classes de ressources statiques.
 
## <a name="concurrency-slot-consumption"></a>Consommation des emplacements de concurrence  
SQL Data Warehouse accorde plus de mémoire aux requêtes qui s’exécutent dans des classes de ressources supérieures. La mémoire est une ressource fixe.  Par conséquent, plus la mémoire allouée par requête est importante, plus le nombre de requêtes s’exécutant en simultané est faible. Le tableau suivant reprend tous les concepts précédents dans une vue unique qui présente le nombre d’emplacements de concurrence disponibles par DWU, ainsi que les emplacements consommés par chaque classe de ressources.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Allocation et consommation des emplacements de concurrence pour les classes de ressources dynamiques  
| DWU | Nombre maximal de requêtes concurrentes | Emplacements de concurrence alloués | Emplacements utilisés par smallrc | Emplacements utilisés par mediumrc | Emplacements utilisés par largerc | Emplacements utilisés par xlargerc |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Allocation et consommation des emplacements de concurrence pour les classes de ressources statiques  
| DWU | Nombre maximal de requêtes concurrentes | Emplacements de concurrence alloués |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

À partir de ces tableaux, vous pouvez constater que SQL Data Warehouse s’exécutant comme DW1000 alloue au maximum 32 requêtes simultanées et un total de 40 emplacements de concurrence. Si tous les utilisateurs étaient en cours d’exécution dans smallrc, 32 requêtes simultanées seraient alors autorisées car chaque requête consommerait un emplacement de concurrence. Si tous les utilisateurs sur une DW1000 étaient en cours d’exécution dans mediumrc, chaque requête bénéficierait de 800 Mo par distribution pour une allocation de mémoire totale de 47 Go par requête, et la concurrence serait limitée à cinq utilisateurs (40 emplacements de concurrence / 8 emplacements par utilisateur mediumrc).

## <a name="selecting-proper-resource-class"></a>Sélection d’une classe de ressources appropriée  
Une bonne pratique consiste à affecter définitivement des utilisateurs à une classe de ressources, plutôt que de modifier leur classe de ressources. Par exemple, les charges des tables columnstore en cluster créent des index de qualité supérieure quand davantage de mémoire leur est allouée. Pour vous assurer que les charges ont accès à une mémoire plus élevée, créez un utilisateur spécifiquement pour le chargement des données et affectez-le définitivement à une classe de ressources supérieure.
Il existe à cet égard plusieurs bonnes pratiques. Comme nous l’avons vu précédemment, SQL DW prend en charge deux types de classes de ressources : les classes de ressources statiques et les classes de ressources dynamiques.
### <a name="loading-best-practices"></a>Bonnes pratiques de chargement
1.  Si les charges attendues portent sur des quantité de données normales, une classe de ressources statique s’avère un bon choix. Par la suite, quand il s’agira de monter en puissance pour bénéficier d’une plus grande capacité de calcul, l’entrepôt de données pourra immédiatement exécuter davantage de requêtes simultanées, car l’utilisateur de la charge ne consommera pas plus de mémoire.
2.  Si des charges plus importantes sont attendues à certaines occasions, une classe de ressources dynamique sera toute indiquée. Par la suite, quand il s’agira de monter en puissance pour bénéficier d’une plus grande capacité de calcul, l’utilisateur de la charge aura de suite accès à une plus grande quantité de mémoire, ce qui autorisera de meilleures performances au niveau de la charge.

La quantité de mémoire nécessaire au traitement des charges dépend en fait de la nature de la table chargée et de la quantité de données traitées. Par exemple, le chargement de données dans des tables à index columnstore cluster (ICC) nécessite de la mémoire pour permettre aux rowgroups ICC d’atteindre l’optimalité. Pour plus d’informations, consultez Index columnstore - Conseils en matière de chargement de données.

En guise de bonne pratique, nous vous conseillons d’utiliser au moins 200 Mo de mémoire pour les charges.

### <a name="querying-best-practices"></a>Bonnes pratiques de requête
Les exigences sur le plan des requêtes varient en fonction de leur complexité. L’augmentation de la quantité de mémoire par requête ou l’accroissement de la concurrence sont deux moyens valables d’augmenter le débit global. En fait, tout dépend des besoins en matière de requêtes.
1.  Si les requêtes s’annoncent régulières et complexes (par exemple, pour générer des rapports quotidiens et hebdomadaires) et que vous n’avez pas besoin de tirer parti de la concurrence, une classe de ressources dynamique s’avère un choix judicieux. Si le système a davantage de données à traiter, une montée en puissance de l’entrepôt de données fournira automatiquement plus de mémoire à l’utilisateur exécutant la requête.
2.  Si les attentes correspondent à des modèles de concurrence variable ou diurne (par exemple, si la base de données est interrogée via une interface utilisateur web largement accessible), une classe de ressources statique sera un bon choix. Par la suite, quand l’entrepôt de données fera l’objet d’une montée en puissance, l’utilisateur associé à la classe de ressources statique pourra automatiquement exécuter davantage de requêtes simultanées.

Choisir l’allocation de mémoire appropriée en fonction de vos besoins en requêtes n’est pas anodin, car divers facteurs entrent en jeu, comme la quantité de données interrogées, la nature des schémas de table et les divers prédicats join, selection et group. D’un point de vue général, si le fait d’allouer une plus grande quantité de mémoire permet de faire aboutir plus rapidement les requêtes, cela limite la concurrence globale. Si la concurrence n’est pas un problème, une allocation excessive de mémoire n’est pas dommageable. Pour optimiser le débit, il peut être nécessaire de tester différentes variantes de classes de ressources.

Vous pouvez utiliser la procédure stockée suivante pour déterminer la concurrence et l’allocation de mémoire par classe de ressources pour un SLO donné et la classe de ressources optimale pour les opérations ICC sur une table ICC non partitionnée pour une classe de ressources donnée :

#### <a name="description"></a>Description :  
Cette procédure stockée vise à :  
1. Aider l’utilisateur à déterminer l’accès concurrentiel et l’allocation de mémoire par classe de ressources pour un SLO donné. L’utilisateur doit pour cela fournir la valeur NULL pour le schéma et le nom de table comme indiqué dans l’exemple ci-dessous.  
2. Aider l’utilisateur à déterminer la classe de ressources optimale pour les opérations ICC gourmandes en mémoire (chargement, copie de table, reconstruction d’index, etc.) sur une table ICC non partitionnée pour une classe de ressources donnée. La procédure stockée utilise le schéma de table pour déterminer l’allocation de mémoire nécessaire.

#### <a name="dependencies--restrictions"></a>Dépendances et restrictions :
- Cette procédure stockée n’est pas conçue pour calculer les besoins en mémoire pour une table ICC partitionnée.    
- Cette procédure stockée ne prend pas en compte les besoins en mémoire pour la partie SELECT d’une instruction CTAS/INSERT-SELECT et part du principe qu’il s’agit d’une instruction SELECT simple.
- Cette procédure stockée utilise une table temporaire pour qu’elle puisse être utilisée dans la session au cours de laquelle cette procédure stockée a été créée.    
- Cette procédure stockée dépend des offres du moment (par exemple, configuration matérielle, configuration DMS) et si l’une d’elles change, cette procédure stockée ne fonctionnera pas correctement.  
- Cette procédure stockée dépend de la limite de concurrence offerte existante et si celle-ci change, cette procédure stockée ne fonctionnera pas correctement.  
- Cette procédure stockée dépend des offres de classes de ressources existantes et si celles-ci changent, cette procédure stockée ne fonctionnera pas correctement.  

>  [!NOTE]  
>  Si vous n’obtenez pas de sortie après l’exécution de la procédure stockée avec les paramètres fournis, il est possible que vous soyez confronté à l’un des deux cas suivants : <br />1. Un paramètre DW contient une valeur SLO non valide. <br />2. OU il n’existe aucune classe de ressources correspondante pour l’opération ICC si le nom de la table a été fourni. <br />Par exemple, pour DW100, l’allocation de mémoire disponible la plus élevée est de 400 Mo si le schéma de table est suffisamment volumineux pour dépasser l’exigence de 400 Mo.
      
#### <a name="usage-example"></a>Exemple d’utilisation :
Syntaxe :  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: fournissez un paramètre NULL pour extraire la DWU active de la base de données DW ou fournissez une DWU prise en charge dans le format « DW100 »
2. @SCHEMA_NAME: fournissez le nom de schéma de la table
3. @TABLE_NAME: fournissez le nom de la table qui vous intéresse

Exemples d’exécution de cette procédure stockée :  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

La table Table1 utilisée dans les exemples ci-dessus pourrait être créée comme ceci :  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Voici la définition de la procédure stockée :
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>Importance de la requête
SQL Data Warehouse implémente des classes de ressources à l’aide de groupes de charges de travail. Il existe en tout huit groupes de charges de travail qui contrôlent le comportement des classes de ressources dans les différentes tailles de DWU. Pour les DWU, SQL Data Warehouse n’utilise que quatre des huit groupes de charges de travail. Cela est logique car chaque groupe de charges de travail est affecté à une des quatre classes de ressources : smallrc, mediumrc, largerc ou xlargerc. Il est important de bien comprendre ces groupes de charges de travail, car certains sont définis comme ayant une *importance*plus élevée. L’importance est utilisée pour la planification du processeur. Les requêtes exécutées avec une importance élevée obtiennent trois fois plus de cycles processeur que celles exécutées avec une importance moyenne. Ainsi, les mappages d’emplacements de concurrence déterminent également la priorité du processeur. Quand une requête utilise 16 emplacements ou plus, elle s’exécute avec une importance élevée.

Le tableau ci-dessous présente les mappages d’importance pour chaque groupe de charges de travail.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mappage des groupes de charges de travail aux emplacements de concurrence et au niveau d’importance
| Groupes de charges de travail | Mappage d’emplacement de concurrence | Mo / Distribution | Mappage d’importance |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Moyenne |
| SloDWGroupC01 |2 |200 |Moyenne |
| SloDWGroupC02 |4 |400 |Moyenne |
| SloDWGroupC03 |8 |800 |Moyenne |
| SloDWGroupC04 |16 |1 600 |Élevé |
| SloDWGroupC05 |32 |3 200 |Élevé |
| SloDWGroupC06 |64 |6 400 |Élevé |
| SloDWGroupC07 |128 |12 800 |Élevé |

À partir du graphique **Allocation et consommation des emplacements de concurrence** , vous pouvez constater qu’une DW500 utilise 1, 4, 8 ou 16 emplacements de concurrence pour smallrc, mediumrc, largerc et xlargerc, respectivement. Vous pouvez rechercher ces valeurs dans le graphique précédent pour connaître l’importance de chaque classe de ressources.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mappage d’importance des DW500 aux classes de ressources
| classe de ressources | Groupe de charges de travail | Emplacements de concurrence utilisés | Mo / Distribution | importance |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Moyenne |
| mediumrc |SloDWGroupC02 |4 |400 |Moyenne |
| largerc |SloDWGroupC03 |8 |800 |Moyenne |
| xlargerc |SloDWGroupC04 |16 |1 600 |Élevé |
| staticrc10 |SloDWGroupC00 |1 |100 |Moyenne |
| staticrc20 |SloDWGroupC01 |2 |200 |Moyenne |
| staticrc30 |SloDWGroupC02 |4 |400 |Moyenne |
| staticrc40 |SloDWGroupC03 |8 |800 |Moyenne |
| staticrc50 |SloDWGroupC03 |16 |1 600 |Élevé |
| staticrc60 |SloDWGroupC03 |16 |1 600 |Élevé |
| staticrc70 |SloDWGroupC03 |16 |1 600 |Élevé |
| staticrc80 |SloDWGroupC03 |16 |1 600 |Élevé |

Vous pouvez utiliser la requête DMV suivante pour examiner en détail les différences d’allocation des ressources mémoire du point de vue du gouverneur de ressources, ou analyser l’utilisation active et historique des groupes de charges de travail lors de la résolution des problèmes.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Requêtes qui honorent les limites de concurrence
La plupart des requêtes sont régies par des classes de ressource. Ces requêtes doivent figurer à la fois dans le seuil des requêtes de concurrence et dans le seuil des emplacements de concurrence. Un utilisateur ne peut pas choisir d’exclure une requête du modèle d’emplacement de concurrence.

Les instructions suivantes honorent les classes de ressources :

* INSERT-SELECT
* UPDATE
* SUPPRIMER
* SELECT (lors de l’interrogation des tables d’utilisateur)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* Chargement de données
* Opérations de déplacement de données effectuées par le Service le déplacement des données (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Exceptions de requêtes aux limites de concurrence
Certaines requêtes ne tiennent pas compte de la classe de ressources à laquelle l’utilisateur est affecté. Ces exceptions aux limites de concurrence s’appliquent lorsque les ressources nécessaires à une commande particulière sont insuffisantes, souvent parce que la commande est une opération de métadonnées. L’objectif de ces exceptions est d’éviter que des quantités de mémoire plus importantes soient allouées à des requêtes qui ne le nécessitent pas. Dans ces cas-là, la petite classe de ressources par défaut (smallrc) est toujours utilisée, quelle que soit la classe de ressources réellement affectée à l’utilisateur. Par exemple, `CREATE LOGIN` s’exécute toujours dans smallrc. Les ressources nécessaires pour accomplir cette opération sont très faibles. Par conséquent, il est inutile d’inclure la requête dans le modèle d’emplacement de concurrence.  Ces requêtes n’étant pas non plus restreintes par la limite de concurrence de 32 utilisateurs, un nombre illimité de ces requêtes peuvent s’exécuter jusqu’à la limite de 1 024 sessions.

Les instructions suivantes ne respectent pas les classes de ressources :

* CREATE ou DROP TABLE
* ALTER TABLE ... SWITCH, SPLIT ou MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE ou DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER ou DROP USER
* CREATE, ALTER ou DROP PROCEDURE
* CREATE ou DROP VIEW
* INSERT VALUES
* SELECT à partir des affichages système et des DMV
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a> Exemple de modification d’une classe de ressources utilisateur
1. **Créer une connexion** : ouvrez une connexion à votre base de données **master** sur le serveur SQL hébergeant votre base de données SQL Data Warehouse et exécutez les commandes suivantes.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Il est judicieux de créer un utilisateur dans la base de données master pour les utilisateurs d’Azure SQL Data Warehouse. La création d’un utilisateur dans la base de données master permet à un utilisateur de se connecter à l’aide d’outils tels que SSMS sans spécifier un nom de base de données.  Elle permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL Server.  Pour plus d’informations sur la création et la gestion des utilisateurs, consultez [Sécuriser une base de données dans SQL Data Warehouse][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Créer un utilisateur SQL Data Warehouse** : ouvrez une connexion à la base de données **SQL Data Warehouse** et exécutez la commande suivante.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Accorder des autorisations** : l’exemple suivant accorde `CONTROL` dans la base de données **SQL Data Warehouse**. `CONTROL` au niveau de la base de données est l’équivalent de db_owner dans SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Augmenter la classe de ressources :** exécutez la requête suivante pour ajouter un utilisateur à un rôle de gestion des charges de travail plus élevé.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Diminuer la classe de ressources :** exécutez la requête suivante pour supprimer un utilisateur d’un rôle de gestion des charges de travail.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Il n’est pas possible de supprimer un utilisateur dans smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Détection des requêtes en file d’attente et autres vues de gestion dynamique
Vous pouvez utiliser la DMV `sys.dm_pdw_exec_requests` pour identifier les requêtes en attente dans une file d’attente de concurrence. Les requêtes en attente pour un emplacement de concurrence auront le statut **suspendu**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Vous pouvez afficher les rôles de gestion des charges de travail avec `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La requête suivante montre le rôle auquel est affecté chaque utilisateur.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
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
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
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
WHERE    [session_id] <> SESSION_ID();
```

Vous pouvez utiliser la DMV `sys.dm_pdw_wait_stats` pour l’analyse des tendances historiques des attentes.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
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
