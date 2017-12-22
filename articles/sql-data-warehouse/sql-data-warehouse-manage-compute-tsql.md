---
title: "Suspendre, reprendre et mettre à l’échelle avec T-SQL dans Azure SQL Data Warehouse | Microsoft Docs"
description: "Tâches Transact-SQL (T-SQL) permettant une montée en puissance des performances en ajustant les unités DWU. Réalisez des économies en réduisant vos ressources pendant les heures creuses."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 03/30/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 9221d72ecf8ab2ba8b04e4bc97eeef7157817cca
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Gestion de la puissance de calcul dans Azure SQL Data Warehouse (T-SQL)
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-data-warehouse-manage-compute-overview.md)
> * [Portail](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Afficher les paramètres d’unités DWU actuels
Pour afficher les paramètres d’unités DWU actuels pour vos bases de données :

1. Ouvrez l’Explorateur d’objets SQL Server dans Visual Studio.
2. Connectez-vous à la base de données associée au serveur de base de données SQL logique.
3. Sélectionnez dans la vue de gestion dynamique sys.database_service_objectives. Voici un exemple : 

```sql
SELECT
    db.name [Database]
,   ds.edition [Edition]
,   ds.service_objective [Service Objective]
FROM
    sys.database_service_objectives ds
JOIN
    sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Mise à l’échelle des ressources de calcul
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les unités DWU :

1. Connectez-vous à la base de données associée à votre serveur de base de données SQL logique.
2. Utilisez l’instruction TSQL [ALTER DATABASE][ALTER DATABASE]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state-and-operation-progress"></a>Vérification de l’état de la base de données et de la progression de l’opération

1. Connectez-vous à la base de données associée à votre serveur de base de données SQL logique.
2. Envoyer une requête pour vérifier l’état de la base de données

```sql
SELECT *
FROM
sys.databases
```

3. Envoyer une requête pour vérifier l’état de l’opération

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySQLDW'
```

Cette vue de gestion dynamique retourne des informations sur diverses opérations de gestion de votre SQL Data Warehouse, comme l’opération et l’état de l’opération, qui aura la valeur IN_PROGRESS ou COMPLETED.



<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes
Pour d’autres tâches de gestion, consultez la rubrique [Vue d’ensemble du système de gestion][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
