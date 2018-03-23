---
title: Qu’est-ce que les Data Warehouse Units (DWU, cDWU) dans Azure SQL Data Warehouse ? | Microsoft Docs
description: Capacités de montée en puissance des performances dans Azure SQL Data Warehouse. Montez en puissance en ajustant le nombre de DWU et de cDWU, ou suspendez et reprenez des ressources de calcul pour réduire les coûts.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/15/2018
ms.author: jrj;barbkess
ms.openlocfilehash: f634bdde2c71f7563df11f686d7ce217311df81d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Data Warehouse Units (DWU) et Data Warehouse Units de calcul (cDWU)
Explique les Data Warehouse Units (DWU) et les Data Warehouse Units de calcul (cDWU) pour Azure SQL Data Warehouse. Incluez des recommandations sur le choix du nombre idéal de Data Warehouse Units et la modification de leur nombre. 

## <a name="what-are-data-warehouse-units"></a>Que sont les Data Warehouse Units ?
Avec SQL Data Warehouse, l’UC, la mémoire et les E/S sont regroupées dans des unités d’échelle de calcul appelées Data Warehouse Units (DWU). Une DWU représente une mesure abstraite et standardisée des ressources de calcul et de performances. En modifiant votre niveau de service, vous changez le nombre de DWU allouées sur le système, qui à son tour ajuste les performances et le coût de votre système. 

Pour acheter des performances supérieures, vous pouvez augmenter le nombre de DWU. Pour payer moins et réduire les performances, diminuez le nombre de DWU. Les coûts de stockage et de calcul sont facturés séparément. Ainsi, la modification des DWU n’a pas d’effet sur les coûts de stockage.

Les performances des DWU sont basées sur les métriques de charge de travail d’entrepôt de données suivantes :

- À quelle vitesse une requête d’entreposage de données standard analyse-t-elle un grand nombre de lignes avant d’effectuer une agrégation complexe ? C’est une opération très gourmande en E/S et en UC.
- À quelle vitesse un entrepôt de données peut-il traiter des données provenant d’Azure Storage Blobs ou d’Azure Data Lake ? C’est une opération très gourmande en réseau et en UC. 
- À quelle vitesse la commande T-SQL [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) peut-elle copier une table ? Cette opération implique la lecture des données sur le système de stockage, leur distribution entre les nœuds de l’appliance et la réécriture dans le système de stockage. Cette opération est très gourmande en UC, E/S et réseau.

Augmentation du nombre de DWU :
- Modification linéaire des performances du système pour les analyses, les agrégations et les instructions CTAS
- Augmentation du nombre de processus de lecture et d’écriture pour les opérations de chargement PolyBase
- Augmentation du nombre maximal de requêtes simultanées et d’emplacements de concurrence

## <a name="performance-tiers-and-data-warehouse-units"></a>Niveaux de performances et Data Warehouse Units

Chaque niveau de performances utilise une unité de mesure légèrement différente pour ses DWU. Cette différence est répercutée sur la facture, car l’unité d’échelle s’applique directement à la facturation.

- Le niveau de performances optimisé pour l’élasticité est mesuré en Data Warehouse Units (DWU).
- Le niveau de performances optimisé pour le calcul est mesuré en Data Warehouse Units de calcul (cDWU). 

Les DWU et les cDWU prennent en charge la mise à l’échelle du calcul (augmentation ou réduction) et la suspension du calcul lorsque vous n’avez pas besoin d’utiliser l’entrepôt de données. Ces opérations se font toutes à la demande. Le niveau de performances optimisé pour le calcul utilise également un cache sur disque local sur les nœuds de calcul pour améliorer les performances. Lorsque vous mettez à l’échelle ou suspendez le système, le cache est invalidé, et une période de préchauffage du cache est nécessaire pour pouvoir bénéficier de performances optimales.  

Lorsque vous augmentez les DWU, vous augmentez de façon linéaire le nombre de ressources de calcul. Le niveau de performances optimisé pour le calcul offre les meilleures performances de requête et la plus grande échelle, mais à un prix d’entrée plus élevé. Il est conçu pour les entreprises en demande constante de performances élevées. Ces systèmes exploitent au maximum le cache. 

### <a name="capacity-limits"></a>Limites de capacité
Chaque serveur SQL (par exemple, myserver.database.windows.net) a un quota de [d’unités de transaction de base de données (DTU)](../sql-database/sql-database-what-is-a-dtu.md) qui autorise un nombre spécifique d’unités d’entrepôt de données. Pour plus d’informations, consultez les [limites de capacité de gestion de la charge de travail](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>De combien de DWU ai-je besoin ?
Le nombre idéal de DWU dépend en grande partie de votre charge de travail et de la quantité de données que vous avez chargées dans le système.

Étapes pour rechercher la DWU la mieux adaptée à votre charge de travail :

1. Pendant le développement, commencez par sélectionner une petite DWU avec le niveau de performances optimisé pour l’élasticité.  Le critère à ce stade étant la validation fonctionnelle, le niveau de performances optimisé pour l’élasticité est une option raisonnable. La DW200 est un bon point de départ. 
2. Surveillez les performances de votre application pendant le test des charges de données dans le système, en observant notamment le nombre de DWU sélectionné.
3. Identifiez des besoins supplémentaires pour les périodes ponctuelles de pics d’activité. Si la charge de travail montre d’importants pics et creux d’activité, et qu’il existe une bonne raison d’effectuer une mise à l’échelle fréquemment, préférez le niveau de performances optimisé pour l’élasticité.
4. Si vous avez besoin de plus de 1 000 DWU, choisissez le niveau de performances optimisé pour le calcul, car il offre de meilleures performances.

SQL Data Warehouse est un système de montée en puissance parallèle qui peut fournir des quantités importantes de calcul et lancer des requêtes sur une grande quantité de données. Pour tester ses capacités de mise à l’échelle, surtout avec un nombre élevé de DWU, nous vous recommandons d’effectuer la mise à l’échelle de l’ensemble de données en vous assurant que vous disposez de suffisamment de données pour alimenter les UC. Pour le test de mise à l’échelle, nous recommandons d’utiliser au moins 1 To.

> [!NOTE]
>
> Si les travaux peuvent être fractionnés entre les nœuds de calcul, les performances des requêtes augmentent uniquement avec une parallélisation renforcée. Si vous trouvez que la mise à l’échelle n’altère pas les performances, vous devrez éventuellement modifier la conception de votre table et/ou vos requêtes. Pour des conseils de paramétrage des requêtes, reportez-vous aux articles suivants sur les [performances](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Autorisations

La modification des DWU requiert les autorisations décrites dans [ALTER DATABASE][ALTER DATABASE]. 

## <a name="view-current-dwu-settings"></a>Afficher les paramètres d’unités DWU actuels

Pour afficher le paramètre DWU actuel :

1. Ouvrez l’Explorateur d’objets SQL Server dans Visual Studio.
2. Connectez-vous à la base de données associée au serveur de base de données SQL logique.
3. Sélectionnez dans la vue de gestion dynamique sys.database_service_objectives. Voici un exemple :  

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Modifier les DWU

### <a name="azure-portal"></a>Portail Azure
Pour modifier les DWU ou les cDWU :

1. Ouvrez le [portail Azure](https://portal.azure.com), ouvrez votre base de données, puis cliquez sur **Mettre à l’échelle**.

2. Sous **Mettre à l’échelle**, déplacez le curseur vers la gauche ou vers la droite pour modifier le paramètre DWU.

3. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

### <a name="powershell"></a>PowerShell
Pour modifier les DWU ou les cDWU, utilisez la cmdlet PowerShell [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
Avec T-SQL, vous pouvez afficher les paramètres actuels de DWU ou cDWU, modifier ces paramètres et vérifier la progression. 

Pour modifier les DWU ou les cDWU :

1. Connectez-vous à la base de données associée à votre serveur de base de données SQL logique.
2. Utilisez l’instruction TSQL [ALTER DATABASE][ALTER DATABASE]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>API REST

Pour modifier les DWU, utilisez l’API REST [Créer ou mettre à jour une base de données][Créer ou mettre à jour une base de données]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


## <a name="check-status-of-dwu-changes"></a>Vérifier l’état des modifications de DWU

Les modifications de DWU peuvent prendre plusieurs minutes. Si vous effectuez une mise à l’échelle automatique, envisagez d’implémenter une logique pour vous assurer que certaines opérations sont bien terminées avant de passer à une autre action. 

Nous vous conseillons de vérifier l’état de la base de données en différents points de terminaison afin d’implémenter correctement l’automatisation. Si le portail vous informe de la fin d’une opération et de l’état actuel des bases de données, il ne vous permet pas de programmer la vérification de l’état. 

Vous ne pouvez pas vérifier l’état de la base de données pour les opérations de montée en puissance avec le portail Azure.

Pour vérifier l’état des modifications de DWU :

1. Connectez-vous à la base de données associée à votre serveur de base de données SQL logique.
2. Envoyez la requête suivante pour vérifier l’état de la base de données.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Envoyez la requête suivante pour vérifier l’état de l’opération.

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Cette vue de gestion dynamique renvoie des informations sur diverses opérations de gestion de votre SQL Data Warehouse, comme l’opération et l’état de l’opération, qui a la valeur IN_PROGRESS ou COMPLETED.

## <a name="the-scaling-workflow"></a>Flux de travail de mise à l’échelle

Lorsque vous lancez une opération de mise à l’échelle, le système arrête tout d’abord toutes les sessions ouvertes, ce qui annule toutes les transactions en cours pour garantir un état cohérent. La mise à l’échelle intervient uniquement une fois la restauration des transactions effectuée.  

- Pour une opération de montée en puissance, le système provisionne le calcul supplémentaire, puis le réassocie à la couche de stockage. 
- Pour une opération de réduction, les nœuds inutiles se détachent du stockage et se rattachent aux nœuds restants.

## <a name="next-steps"></a>Étapes suivantes
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

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./resource-classes-for-workload-management.md
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
