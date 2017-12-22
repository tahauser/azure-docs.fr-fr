---
title: "Restauration d’un entrepôt Azure SQL Data Warehouse (API REST) | Microsoft Docs"
description: "Tâches d’API REST permettant de restaurer un Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 8739429342d3c8bbe0f09041976758276a9c3228
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Restauration d’un Azure SQL Data Warehouse (API REST)
> [!div class="op_single_selector"]
> * [Vue d’ensemble][Overview]
> * [Portail][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Dans cet article, vous allez apprendre à restaurer un Azure SQL Data Warehouse à l’aide de l’API REST.

## <a name="before-you-begin"></a>Avant de commencer
**Vérifiez votre capacité de DTU.** Chaque SQL Data Warehouse est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut.  Avant de pouvoir restaurer un SQL Data Warehouse, vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour savoir comment calculer la capacité DTU nécessaire ou pour demander davantage de capacité DTU, consultez la rubrique [Demander une modification du quota DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Restauration d’une base de données active ou en pause
Pour restaurer une base de données :

1. Suivez la procédure d’obtention de la liste des points de restauration de la base de données.
2. Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][Create database restore request].
3. Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][Database operation status].

> [!NOTE]
> Une fois la restauration terminée, vous pouvez configurer votre base de données restaurée en suivant les instructions de la section [Configurer votre base de données après récupération][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>restauration d’une base de données supprimée.
Pour restaurer une base de données supprimée :

1. Obtenez la liste de toutes vos bases de données supprimées pouvant être restaurées en suivant la procédure [Liste des bases de données supprimées pouvant être restaurées][List restorable dropped databases].
2. Obtenez des informations détaillées sur la base de données que vous voulez restaurer en suivant la procédure [Obtention de base de données supprimée pouvant être restaurée][Get restorable dropped database].
3. Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][Create database restore request].
4. Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][Database operation status].

> [!NOTE]
> Pour configurer votre base de données une fois la restauration terminée, consultez la page [Configurer votre base de données après récupération][Configure your database after recovery].
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions d’Azure SQL Database, consultez la rubrique [Vue d’ensemble de la continuité des activités Azure SQL Database][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
