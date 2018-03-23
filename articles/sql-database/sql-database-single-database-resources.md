---
title: Base de données Azure SQL Database unique | Microsoft Docs
description: Gérer le niveau de service, le niveau de performance et le volume de stockage pour une base de données Azure SQL unique.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 167a72ae55052b8ac1dfe8f032f136a9bf8bcedf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Gérer les ressources pour une base de données unique dans Azure SQL Database

Avec une base de données unique, vous déterminez la quantité de ressources nécessaires à la base de données pour gérer sa charge de travail sur la couche de service, le niveau de performance et la quantité de stockage nécessaire. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Gérer les ressources d’une base de données unique via le portail Azure

Pour définir ou modifier le niveau de service, le niveau de performance ou la capacité de stockage d’une base de données Azure SQL Database (nouvelle ou existante) via le portail Azure, ouvrez la fenêtre **Configurer les performances** de la base de données en cliquant sur **Niveau tarifaire (DTU de mise à l’échelle)**, comme indiqué dans la capture d’écran suivante. 

- Définissez ou modifiez le niveau de service en sélectionnant le niveau de service de la charge de travail. 
- Définissez ou modifiez le niveau de performance (**DTU**) dans un niveau de service à l’aide du curseur **DTU**.
- Définissez ou modifiez la capacité de stockage pour le niveau de performances à l’aide du curseur **Stockage**. 

![Configurer le niveau de service et le niveau de performances](./media/sql-database-single-database-resources/change-service-tier.png)

Cliquez sur **Vue d’ensemble** pour analyser et/ou annuler une opération en cours.

![Annuler une opération](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Parcourez la section [Limitations actuelles des bases de données P11 et P15 avec une taille maximale de 4 To](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) lors de la sélection d’un niveau de service P11 ou P15.
>

## <a name="manage-single-database-resources-using-powershell"></a>Gérer les ressources d’une base de données unique via PowerShell

Pour définir ou modifier les niveaux de service, les niveaux de performances et la capacité de stockage de bases de données Azure SQL Database via PowerShell, utilisez les applets de commande PowerShell suivantes. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crée une base de données |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtient une ou plusieurs bases de données|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique. Par exemple, utilisez la propriété **MaxSizeBytes** pour définir la taille maximale d’une base de données.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Obtient l’état des opérations sur la base de données. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Annule l’opération de mise à jour asynchrone sur la base de données.|


> [!TIP]
> Consultez [Surveillance et mise à l’échelle d’une instance SQL Database unique à l’aide de PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) pour un exemple de script PowerShell qui surveille les mesures de performances d’une base de données, l’adapte à un niveau de performances supérieur et crée une règle d’alerte sur l’une des mesures de performances.

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Gérer les ressources d’une base de données unique via l’interface de ligne de commande Azure

Pour définir ou modifier les niveaux de service, les niveaux de performances et la capacité de stockage Azure SQL Database via Azure CLI, utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows. Pour créer et gérer les pools élastiques SQL, voir [Pools élastiques](sql-database-elastic-pool.md).

| Applet de commande | Description |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Crée la règle de pare-feu d’un serveur|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Répertorie les règles de pare-feu sur un serveur|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Affiche les détails d’une règle de pare-feu|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Met à jour une règle de pare-feu|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Supprime une règle de pare-feu|
|[az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list)|Obtient une liste des opérations effectuées sur la base de données.|
|[az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Annule l’opération asynchrone sur la base de données.|

> [!TIP]
> Consultez [Utiliser CLI pour surveiller et mettre à l’échelle une base de données SQL](scripts/sql-database-monitor-and-scale-database-cli.md) pour obtenir un exemple de script Azure CLI permettant la mise à l’échelle d’une base de données Azure SQL après avoir demandé les informations de taille de la base de données.
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Gérer les ressources d’une base de données unique via Transact-SQL

Pour définir ou modifier les niveaux de service, les niveaux de performances et la capacité de stockage Azure SQL Database via Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. 

| Commande | Description |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crée une base de données. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifie une base de données SQL Azure. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, le cas échéant, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, renvoie les informations au sujet de toutes les bases de données. Pour Azure SQL Data Warehouse, vous devez être connecté à la base de données MASTER.|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Liste le nombre, le type et la durée des bases de données sur un serveur de base de données SQL Azure.|

L’exemple suivant illustre la taille maximale pour une base de données modifiée à l’aide de la commande ALTER DATABASE :

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Gérer les ressources de base de données uniques via l’interface de programmation d’applications (API) REST

Pour définir ou modifier les niveaux de service, les niveaux de performances et la capacité de stockage Azure SQL Database via l’API REST, utilisez les requêtes d’API REST suivantes.

| Commande | Description |
| --- | --- |
|[Bases de données : Create ou Update](/rest/api/sql/databases/createorupdate)|Crée une base de données ou met à jour une base de données existante.|
|[Bases de données - Obtenir](/rest/api/sql/databases/get)|Obtient une base de données.|
|[Bases de données - Obtenir par pool élastique](/rest/api/sql/databases/getbyelasticpool)|Obtient une base de données à l’intérieur d’un pool élastique.|
|[Bases de données - Obtenir par pool élastique recommandé](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtient une base de données à l’intérieur d’un pool élastique recommandé.|
|[Bases de données - Lister par pool élastique](/rest/api/sql/databases/listbyelasticpool)|Renvoie une liste des bases de données dans un pool élastique.|
|[Bases de données - Lister par pool élastique recommandé](/rest/api/sql/databases/listbyrecommendedelasticpool)|Retourne une liste de bases de données à l’intérieur d’un pool élastique recommandé.|
|[Bases de données - Lister par serveur](/rest/api/sql/databases/listbyserver)|Retourne une liste de bases de données d’un serveur.|
|[Bases de données - Mettre à jour](/rest/api/sql/databases/update)|Met à jour une base de données existante.|
|[Operations - List](/rest/api/sql/Operations/List)|Répertorie toutes les opérations d’API Rest SQL disponibles.|



## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les niveaux de service, les niveaux de performance et les quantités de stockage, consultez [Niveaux de service](sql-database-service-tiers.md).
- Pour en savoir plus sur les pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).
- En savoir plus sur [l’abonnement Azure et les limites, quotas et contraintes des services](../azure-subscription-service-limits.md)
