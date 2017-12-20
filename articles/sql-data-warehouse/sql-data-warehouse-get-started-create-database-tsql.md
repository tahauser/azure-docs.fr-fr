---
title: "Créer un entrepôt SQL Data Warehouse avec TSQL | Microsoft Docs"
description: "Découvrez comment créer un entrepôt Azure SQL Data Warehouse avec TSQL"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 10d8aa2b3ab8d7d8a9b91e95ffccf03faa89d237
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Portail Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Cet article vous explique comment créer un entrepôt SQL Data Warehouse à l’aide de T-SQL.

## <a name="prerequisites"></a>Composants requis
Pour commencer, vous avez besoin des éléments suivants :

* **Compte Azure** : consultez [Évaluation gratuite d’Azure][Azure Free Trial] ou [Crédits Azure MSDN][MSDN Azure Credits] pour créer un compte.
* **Serveur SQL Azure** : pour plus de détails, consultez [Créer un serveur logique Azure SQL Database avec le portail Azure][Créer un serveur logique Azure SQL Database avec le portail Azure] ou [Créer un serveur logique Azure SQL Database avec PowerShell][Créer un serveur logique Azure SQL Database avec PowerShell].
* **Groupe de ressources**: utilisez le même groupe de ressources que votre serveur Azure SQL Server ou consultez [Créer un groupe de ressources][how to create a resource group].
* **Environnement d’exécution de T-SQL** : vous pouvez utiliser [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] ou [SSMS][SSMS] pour exécuter T-SQL.

> [!NOTE]
> La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable.  Voir [Tarification de SQL Data Warehouse][SQL Data Warehouse pricing] pour plus d’informations sur la tarification.
>
>

## <a name="create-a-database-with-visual-studio"></a>Créer une base de données avec Visual Studio
Si vous débutez avec Visual Studio, consultez l’article [Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)].  Pour commencer, ouvrez l’Explorateur d’objets SQL Server dans Visual Studio et connectez-vous au serveur qui hébergera votre base de données SQL Data Warehouse.  Une fois que vous êtes connecté, vous pouvez créer un entrepôt SQL Data Warehouse en exécutant la commande SQL suivante sur la base de données **master** .  Cette commande crée la base de données MySqlDwDb avec un objectif de service DW400 et permet à la base de données d’atteindre une taille maximale de 10 To.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Créer une base de données avec sqlcmd
Vous pouvez également exécuter la même commande avec sqlcmd. Pour cela, exécutez la commande suivante à partir de l’invite de commandes.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Le classement par défaut est COLLATE SQL_Latin1_General_CP1_CI_AS.  Le paramètre `MAXSIZE` peut être compris entre 250 Go et 240 To.  Le paramètre `SERVICE_OBJECTIVE` peut être compris entre DW100 et DW2000 [DWU][DWU].  Pour obtenir la liste de toutes les valeurs valides, consultez la documentation MSDN pour [CREATE DATABASE][CREATE DATABASE].  Les paramètres MAXSIZE et SERVICE_OBJECTIVE peuvent être modifiés avec une commande T-SQL [ALTER DATABASE][ALTER DATABASE].  Le classement d’une base de données ne peut pas être modifié après la création.   Modifiez le paramètre SERVICE_OBJECTIVE avec prudence car la modification de DWU entraîne un redémarrage des services, qui annule toutes les requêtes en cours.  La modification du paramètre MAXSIZE ne redémarre pas les services car il s’agit d’une simple opération de métadonnées.

## <a name="next-steps"></a>Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][load sample data] ou découvrir comment [développer][develop], [charger][load] ou [migrer][migrate].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
