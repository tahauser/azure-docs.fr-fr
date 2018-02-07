---
title: "Gestion de la puissance de calcul dans Azure SQL Data Warehouse (Vue d’ensemble) | Documents Microsoft"
description: "Capacités de montée en puissance des performances dans Azure SQL Data Warehouse. Montez en puissance en ajustant le nombre d’unités DWU ou suspendez et reprenez des ressources de calcul pour réduire les coûts."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 3/23/2017
ms.author: elbutter
ms.openlocfilehash: d795abe5254d47a72a468b0989e46829a5c5142a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gestion de la puissance de calcul dans Azure SQL Data Warehouse (Vue d’ensemble)
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-data-warehouse-manage-compute-overview.md)
> * [Portail](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

L’architecture de SQL Data Warehouse sépare le stockage du calcul, ce qui permet de les mettre à l’échelle indépendamment l’un de l’autre. En conséquence, le calcul peut être mis à l’échelle pour répondre aux exigences de performance, indépendamment du volume de données. Une conséquence naturelle de cette architecture est la séparation de la [facturation][billed] du calcul et du stockage. 

Cette présentation décrit le fonctionnement de la mise à l’échelle avec SQL Data Warehouse. Elle explique également comment utiliser les fonctionnalités de mise en pause, de reprise et de mise à l’échelle de SQL Data Warehouse. 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>Fonctionnement des opérations de gestion du calcul dans SQL Data Warehouse
L’architecture de SQL Data Warehouse consiste en un nœud de contrôle, des nœuds de calcul et la couche de stockage, le tout réparti sur 60 distributions. 

Au cours d’une session active normale dans SQL Data Warehouse, le nœud principal du système gère les métadonnées et contient l’optimiseur de requête distribuée. Sous ce nœud principal, se trouvent les nœuds de calcul et la couche de stockage. Pour une instance de 400 DWU, votre système possède un nœud principal, quatre nœuds de calcul et la couche de stockage, soit 60 distributions. 

Lorsque vous entreprenez une mise à l’échelle ou que vous interrompez l’opération, le système supprime tout d’abord toutes les requêtes entrantes, puis restaure les transactions pour garantir un état cohérent. La mise à l’échelle intervient uniquement une fois la restauration des transactions effectuée. Pour une opération de montée en puissance, le système configure le nombre de nœuds de calcul souhaité, puis commence le rattachement des nœuds de calcul à la couche de stockage. Pour une opération de descente en puissance, les nœuds inutiles sont rendus disponibles et les nœuds de calcul restants sont rattachés au nombre de distributions approprié. Pour une opération de mise en pause, tous les nœuds de calcul sont rendus disponibles et le système entreprend diverses opérations sur les métadonnées afin de garantir la stabilité du système final.

| DWU  | \# de nœuds de calcul | \# de distributions par nœud |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5.                  | 12                           |
| 600  | 6.                  | 10                           |
| 1 000 | 10                 | 6.                            |
| 1 200 | 12                 | 5.                            |
| 1 500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

Les trois principales fonctions pour la gestion du calcul sont :

1. Suspendre
2. Reprendre
3. Scale

Chacune de ces opérations peut prendre plusieurs minutes. Si vous effectuez une mise à l’échelle/mise en pause/reprise automatique, il vous faudra peut-être implémenter une logique pour vous assurer que certaines opérations sont bien terminées avant de passer à une autre action. 

Nous vous conseillons de vérifier l’état de la base de données en différents points de terminaison afin d’automatiser correctement de telles opérations. Si le portail vous informe de la fin d’une opération et de l’état actuel des bases de données, il ne vous permet pas de programmer la vérification de l’état. 

>  [!NOTE]
>
>  La fonctionnalité de gestion du calcul n’existe pas sur tous les points de terminaison.
>
>  

|              | Suspendre/Reprendre | Scale | Vérifier l’état de la base de données |
| ------------ | ------------ | ----- | -------------------- |
| Portail Azure | OUI          | OUI   | **Non**               |
| PowerShell   | OUI          | OUI   | OUI                  |
| de l’API REST     | OUI          | OUI   | OUI                  |
| T-SQL        | **Non**       | OUI   | OUI                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Mise à l’échelle des ressources de calcul

Les performances dans SQL Data Warehouse se mesurent en [data warehouse units (DWU)][data warehouse units (DWU)] qui sont une unité abstraite de ressources de calcul (processeur, mémoire et bande passante d’E/S). Un utilisateur qui souhaite augmenter les performances de son système peut y parvenir de plusieurs façons, par exemple via le portail, T-SQL et les API REST. 

### <a name="how-do-i-scale-compute"></a>Comment mettre à l’échelle les ressources de calcul ?
La puissance de calcul est gérée pour vous par SQL Data Warehouse en modifiant le paramètre DWU. Les performances augmentent de manière linéaire à mesure que vous ajoutez des DWU pour certaines opérations.  Nous proposons des offres DWU qui vous garantissent une évolution notable de vos performances lorsque vous effectuez une montée ou une descente en puissance de votre système. 

Pour ajuster les unités DWU, vous pouvez utiliser l’une des différentes méthodes suivantes.

* [Mise à l’échelle de la puissance de calcul avec le portail Azure][Scale compute power with Azure portal]
* [Mise à l’échelle de la puissance de calcul avec PowerShell][Scale compute power with PowerShell]
* [Mise à l’échelle de la puissance de calcul avec les API REST][Scale compute power with REST APIs]
* [Mise à l’échelle de la puissance de calcul avec TSQL][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>Combien d’unités DWU dois-je utiliser ?

Pour obtenir votre valeur DWU idéale, essayez d’augmenter et de réduire vos DWU et d’exécuter quelques requêtes après le chargement de vos données. La mise à l’échelle étant rapide, vous pouvez essayer plusieurs niveaux de performances en une heure ou moins. 

> [!Note] 
> SQL Data Warehouse est conçu pour traiter de grandes quantités de données. Pour étudier ses fonctionnalités de mise à l’échelle, en particulier sur les unités DWU de grande taille, nous vous conseillons d’utiliser un jeu de données qui avoisine ou dépasse 1 To.

Recommandations pour rechercher l’unité DWU la mieux adaptée à votre charge de travail :

1. Si vous disposez d’un entrepôt de données en développement, commencez par sélectionner un niveau de performances utilisant un nombre réduit d’unités DWU.  DW400 ou DW200 est un bon point de départ.
2. Surveillez les performances de votre application, en observant notamment le nombre d’unités DWU sélectionné.
3. Déterminez le niveau de performances le mieux adapté aux exigences en modulant la capacité de votre système à l’aide d’une mise à l’échelle linéaire.
4. Augmentez ou diminuez le nombre de DWU en fonction de la performance de charge de travail dont vous avez besoin. 
5. Continuez à effectuer des ajustements jusqu’à ce que vous atteigniez le niveau de performances requis par vos activités.

> [!NOTE]
>
> Si les travaux peuvent être fractionnés entre les nœuds de calcul, les performances des requêtes augmentent uniquement avec une parallélisation renforcée. Si vous trouvez que la mise à l’échelle ne modifie pas les performances, consultez nos articles sur le réglage des performances et voyez si vos données sont distribuées de manière inégale ou si vous provoquez un déplacement trop important des données. 

### <a name="when-should-i-scale-dwus"></a>Quand dois-je mettre les unités DWU à l’échelle ?
La mise à l’échelle des unités DWU modifie les principaux scénarios suivants :

1. Modification linéaire des performances du système pour les analyses, les agrégations et les instructions CTAS
2. Augmentation du nombre de lecteurs et d’auteurs lors du chargement des données avec PolyBase
3. Nombre maximal de requêtes simultanées et d’emplacements concurrentiels

Recommandations sur le moment approprié pour mettre des unités DWU à l’échelle :

1. Avant d’exécuter une opération de chargement ou de transformation de données importante, vous pouvez augmenter le nombre d’unités DWU afin que vos données soient disponibles plus rapidement.
2. Pendant les heures de pointe, envisagez une mise à l’échelle pour prendre en charge un nombre plus important de requêtes simultanées. 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Suspension du calcul
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez l’une des différentes méthodes suivantes.

* [Suspension du calcul avec le portail Azure][Pause compute with Azure portal]
* [Suspension du calcul avec PowerShell][Pause compute with PowerShell]
* [Suspension du calcul avec des API REST][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Reprise du calcul
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Pour reprendre une base de données, utilisez l’une des différentes méthodes suivantes.

* [Reprise du calcul avec le portail Azure][Resume compute with Azure portal]
* [Reprise du calcul avec PowerShell][Resume compute with PowerShell]
* [Reprise du calcul avec des API REST][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>Vérifier l’état de la base de données 

Pour reprendre une base de données, utilisez l’une des différentes méthodes suivantes.

- [Vérifier l’état de la base de données avec T-SQL][Check database state with T-SQL]
- [Vérifier l’état de la base de données avec PowerShell][Check database state with PowerShell]
- [Vérifier l’état de la base de données avec les API REST][Check database state with REST APIs]

## <a name="permissions"></a>Autorisations

La mise à l’échelle de la base de données requiert les autorisations décrites dans [ALTER DATABASE][ALTER DATABASE].  La suspension et la reprise requièrent l’autorisation [SQL DB Contributor][SQL DB Contributor], notamment Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>étapes suivantes
Consultez les articles suivants pour mieux comprendre certains concepts supplémentaires essentiels en matière de performances :

* [Gestion des charges de travail et d’accès concurrentiel][Workload and concurrency management]
* [Vue d’ensemble de conception de table][Table design overview]
* [Distribution de tables][Table distribution]
* [Indexation de table][Table indexing]
* [Partitionnement de tables][Table partitioning]
* [Statistiques de table][Table statistics]
* [Meilleures pratiques][Best practices]

<!--Image reference-->

<!--Article references-->
[billed]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
