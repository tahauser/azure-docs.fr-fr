---
title: Gestion de la puissance de calcul dans Azure SQL Data Warehouse (PowerShell) | Microsoft Docs
description: "Tâches PowerShell permettant de gérer la puissance de calcul. Mettez à l’échelle les ressources de calcul en ajustant les unités DWU. Ou suspendez et reprenez des ressources de calcul pour réduire les coûts."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6a185d96447c2e1b0b463439dd062081e783da5f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gestion de la puissance de calcul dans Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-data-warehouse-manage-compute-overview.md)
> * [Portail](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

## <a name="before-you-begin"></a>Avant de commencer
### <a name="install-the-latest-version-of-azure-powershell"></a>Installer la dernière version d’Azure PowerShell
> [!NOTE]
> Pour utiliser Azure PowerShell avec SQL Data Warehouse, vous devez installer Azure PowerShell version 1.0.3 ou supérieure.  Pour vérifier votre version, exécutez la commande **Get-Module -ListAvailable -Name Azure**. Vous pouvez installer la version la plus récente à partir de [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Pour plus d’informations, consultez [Installation et configuration d’Azure PowerShell][How to install and configure Azure PowerShell].
>
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Prise en main des applets de commande Azure PowerShell
Pour commencer :

1. Ouvrez Azure PowerShell.
2. À l’invite de PowerShell, exécutez les commandes suivantes pour vous connecter à Azure Resource Manager et sélectionnez votre abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Mise à l’échelle de la puissance de calcul
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les unités DWU, utilisez l’applet de commande PowerShell [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Suspension du calcul
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour interrompre une base de données, utilisez l’applet de commande [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est interrompue. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

> [!NOTE]
> Si votre serveur est nommé foo.database.windows.net, utilisez « foo » en tant que nom du serveur dans les applets de commande PowerShell.
>
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Une variante, l'exemple suivant récupère la base de données dans l'objet $database. Puis il redirige l’objet récupéré vers [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. Les résultats sont stockés dans l'objet resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Reprise du calcul
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Pour démarrer une base de données, utilisez l’applet de commande [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]. Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est démarrée. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Une variante, l'exemple suivant récupère la base de données dans l'objet $database. Il redirige ensuite l’objet [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase] et stocke les résultats dans $resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state"></a>Vérifier l’état de base de données

Comme indiqué dans les exemples ci-dessus, vous pouvez utiliser l’applet de commande [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase] pour obtenir des informations sur une base de données, pour vérifier ainsi son état mais aussi pour l’utiliser en tant qu’argument. 

```powershell
Get-AzureRmSqlDatabase [-ResourceGroupName] <String> [-ServerName] <String> [[-DatabaseName] <String>]
 [-InformationAction <ActionPreference>] [-InformationVariable <String>] [-Confirm] [-WhatIf]
 [<CommonParameters>]
```

De qui donnera quelque chose comme ceci 

```powershell   
ResourceGroupName             : nytrg
ServerName                    : nytsvr
DatabaseName                  : nytdb
Location                      : West US
DatabaseId                    : 86461aae-8e3d-4ded-9389-ac9d4bc69bbb
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1General_CP1CI_AS
CatalogCollation              :
MaxSizeBytes                  : 32212254720
Status                        : Online
CreationDate                  : 10/26/2016 4:33:14 PM
CurrentServiceObjectiveId     : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
CurrentServiceObjectiveName   : System2
RequestedServiceObjectiveId   : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           : 1/1/0001 12:00:00 AM
```

Où vous pouvez ensuite vérifier *l’état* de la base de données. Dans ce cas, vous pouvez voir que la base de données est en ligne. 

Lorsque vous exécutez cette commande, vous devriez recevoir une valeur d’état En ligne, Suspension, Reprise, Mise à l’échelle ou Suspendu.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes
Pour d’autres tâches de gestion, consultez la rubrique [Vue d’ensemble du système de gestion][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Get-AzureRmSqlDatabase]: /powershell/servicemanagement/azure.sqldatabase/v1.6.1/get-azuresqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
