---
title: "Restauration d’Azure SQL Data Warehouse (portail Azure) | Microsoft Docs"
description: "Tâches du portail Azure permettant de restaurer Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 7f13208a55d19930aed99017221386a4498307d1
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Restauration d’Azure SQL Data Warehouse (portail)
> [!div class="op_single_selector"]
> * [Vue d’ensemble][Overview]
> * [Portail][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
Dans cet article, vous allez découvrir comment restaurer Azure SQL Data Warehouse à l’aide du portail Azure.

## <a name="before-you-begin"></a>Avant de commencer
**Vérifiez votre capacité de DTU.** Chaque instance de SQL Data Warehouse est hébergée par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota d’unité de débit de données (DTU) par défaut. Avant de pouvoir restaurer SQL Data Warehouse, vérifiez que le quota DTU restant sur votre serveur SQL est suffisant pour la base de données que vous restaurez. Pour découvrir comment calculer le quota DTU nécessaire ou pour demander davantage de capacité DTU, consultez [Request a DTU quota change][Request a DTU quota change] (Demander une modification du quota DTU).

## <a name="restore-an-active-or-paused-database"></a>Restauration d’une base de données active ou en pause
Pour restaurer une base de données :

1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le panneau gauche, sélectionnez **Parcourir**, puis choisissez **Serveurs SQL**.

    ![Sélectionnez Parcourir > Serveurs SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Recherchez votre serveur et sélectionnez-le.

    ![Sélectionnez votre serveur](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Recherchez l’instance SQL Data Warehouse à partir de laquelle effectuer la restauration et sélectionnez-la.

    ![Sélectionnez l’instance de SQL Data Warehouse à restaurer](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. En haut du panneau de l’entrepôt de données, sélectionnez **Restaurer**.

    ![Sélectionnez Restaurer](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Spécifiez un nouveau **nom pour la base de données**.
7. Sélectionnez le dernier **point de restauration**.

   Assurez-vous de sélectionner le dernier point de restauration. Étant donné que les points de restauration sont affichés en temps universel coordonné (UTC), l’option par défaut peut ne pas être le dernier point de restauration.

      ![Sélectionner un point de restauration](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Sélectionnez **OK**.
9. Le processus de restauration de la base de données commence et vous pouvez utiliser **NOTIFICATIONS** pour en surveiller la progression.

> [!NOTE]
> Une fois la restauration terminée, vous pouvez configurer votre base de données restaurée en suivant les instructions de la section [Configurer votre base de données après récupération][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>restauration d’une base de données supprimée.
Pour restaurer une base de données supprimée :

1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le panneau gauche, sélectionnez **Parcourir**, puis choisissez **Serveurs SQL**.

    ![Sélectionnez Parcourir > Serveurs SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Recherchez votre serveur et sélectionnez-le.

    ![Sélectionnez votre serveur](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Faites défiler jusqu’à la section **Opérations** dans le panneau de votre serveur.
5. Sélectionnez la vignette **Bases de données supprimées**.

    ![Sélectionnez la vignette Bases de données supprimées](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Sélectionnez la base de données supprimée que vous souhaitez restaurer.

    ![Sélectionner une base de données à restaurer](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Spécifiez un nouveau **nom pour la base de données**.

    ![Entrez un nom pour la base de données](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Sélectionnez **OK**.
9. Le processus de restauration de la base de données commence et vous pouvez utiliser **NOTIFICATIONS** pour en surveiller la progression.

> [!NOTE]
> Pour configurer votre base de données une fois la restauration terminée, consultez la page [Configurer votre base de données après récupération][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité des activités des éditions de Base de données SQL Azure, consultez [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
