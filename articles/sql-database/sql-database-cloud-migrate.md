---
title: Migration de base de données SQL Server vers Azure SQL Database | Microsoft Docs
description: Découvrez comment migrer une base de données SQL Server vers Azure SQL Database dans le cloud.
keywords: migration de base de données, migration de base de données sql server, outils de migration de base de données, migrer la base de données, migrer la base de données sql
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: article
ms.date: 03/16/2018
ms.author: carlrab
ms.openlocfilehash: 59ee56e225623295dd63bf5ae303bfe1aa8e95cf
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migration de base de données SQL Server vers Azure SQL Database

Cet article décrit les méthodes principales de migration d’une base de données SQL Server 2005 ou version ultérieure locale vers une base de données unique ou mise en pool Azure SQL Database. Pour plus d’informations sur la migration vers une Managed Instance, consultez [Migrer vers une Azure SQL Database Managed Instance (préversion)](sql-database-managed-instance-migrate.md). 

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrer vers une base de données unique ou une base de données mise en pool
Il existe deux méthodes principales de migration d’une base de données SQL Server 2005 ou version ultérieure locale vers une base de données unique ou mise en pool Azure SQL Database. La première méthode est plus simple, mais elle implique un temps d’arrêt potentiellement important pendant la migration. La seconde méthode est plus complexe, mais elle élimine en grande partie les temps d’arrêt lors de la migration.

Dans les deux cas, vous devez vérifier que la base de données source est compatible avec Azure SQL Database à l’aide de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database V12 approche de la [parité des fonctionnalités](sql-database-features.md) avec SQL Server, à l’exception des problèmes liés aux opérations au niveau du serveur et sur plusieurs bases de données. Les bases de données et les applications qui reposent sur des [fonctions partiellement ou pas du tout prises en charge](sql-database-transact-sql-information.md) ont besoin d’une [nouvelle ingénierie pour corriger ces incompatibilités](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) avant de pouvoir migrer la base de données SQL Server.

> [!NOTE]
> Pour migrer une base de données non SQL Server, notamment Microsoft Access, Sybase, MySQL Oracle et DB2, vers une base de données SQL Azure, consultez l’ [Assistant Migration SQL Server](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).
> 

### <a name="method-1-migration-with-downtime-during-the-migration"></a>Méthode 1 : Migration avec un temps d’arrêt pendant l’opération

 Utilisez cette méthode pour migrer une base de données unique ou en mise pool si vous pouvez vous permettre un temps d’arrêt ou si vous effectuez un test de migration d’une base de données de production que vous envisagez de migrer. Pour un didacticiel, consultez [Migrer une base de données SQL Server](sql-database-migrate-your-sql-server-database.md).

La liste suivante contient le workflow général pour la migration d’une base de données SQL Server unique ou mise en pool à l’aide de cette méthode. Pour la migration vers une Managed Instance, consultez [Migration vers Managed Instance](sql-database-managed-instance-migrate.md).

  ![Schéma de migration VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Évaluez](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) la compatibilité de la base de données à l’aide de la dernière version de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Préparez les corrections nécessaires en tant que scripts Transact-SQL.
3. Faites une copie cohérente de la base de données source en cours de migration ou interrompez les nouvelles transactions qui se produisent dans la base de données source pendant la migration. Les méthodes qui existent pour accomplir cette dernière option incluent la désactivation de la connectivité des clients ou la création d’un [instantané de base de données](https://msdn.microsoft.com/library/ms175876.aspx). Après la migration, vous pourrez peut-être utiliser la réplication transactionnelle pour mettre à jour les bases de données migrées avec les modifications qui se produisent après le point d’arrêt pour la migration. Consultez [Migration à l’aide de la migration transactionnelle](sql-database-cloud-migrate.md#method-2-use-transactional-replication).  
4. Déployez les scripts Transact-SQL pour appliquer les correctifs à la copie de base de données.
5. [Migrez](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) la copie de base de données vers une nouvelle base de données SQL Azure à l’aide de Data Migration Assistant.

> [!NOTE]
> Au lieu d’utiliser DMA, vous pouvez également utiliser un fichier BACPAC. Consultez [Importer un fichier BACPAC dans une nouvelle base de données Azure SQL Database](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optimisation des performances de transfert de données pendant la migration 

La liste suivante contient des recommandations pour optimiser les performances pendant le processus d’importation.

* Choisissez le niveau de service et le niveau de performance les plus élevés permis par votre budget pour maximiser les performances de transfert. Vous pourrez descendre en puissance une fois la migration terminée pour économiser de l’argent. 
* Réduisez la distance entre votre fichier BACPAC et le centre de données de destination.
* Désactivez les statistiques automatiques pendant la migration.
* Partitionnez les tables et les index.
* Supprimez les vues indexées et recréez-les une fois la migration terminée.
* Déplacez les données historiques rarement interrogées dans une autre base de données et migrez ces données historiques vers une base de données SQL Azure distincte. Vous pourrez ensuite interroger ces données historiques à l’aide de [requêtes élastiques](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optimiser les performances une fois la migration terminée

[Mettez à jour les statistiques](https://msdn.microsoft.com/library/ms187348.aspx) avec une analyse complète une fois la migration terminée.

### <a name="method-2-use-transactional-replication"></a>Méthode 2: Utiliser la réplication transactionnelle

Quand vous ne pouvez pas vous permettre de sortir votre base de données SQL Server de la production pendant la migration, vous pouvez utiliser la réplication transactionnelle SQL Server comme solution de migration. Pour que vous puissiez utiliser cette méthode, la base de données source doit remplir les [conditions requises pour la réplication transactionnelle](https://msdn.microsoft.com/library/mt589530.aspx) et être compatible avec Azure SQL Database. Pour plus d’informations sur la réplication SQL avec AlwaysOn, consultez [Configurer la réplication pour les groupes de disponibilité AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Pour utiliser cette solution, vous devez configurer votre base de données SQL Azure en tant qu’abonné à l’instance de SQL Server que vous souhaitez migrer. Le distributeur de réplication transactionnelle synchronise les données nécessaires de la base de données (l’éditeur), de nouvelles transactions continuant d’avoir lieu. 

Avec la réplication transactionnelle, toutes les modifications apportées à vos données ou à votre schéma apparaissent dans votre Base de données SQL Azure. Une fois la synchronisation terminée, lorsque vous êtes prêt à migrer, modifiez la chaîne de connexion de vos applications de façon à ce qu’elle pointe vers votre Base de données SQL Azure. Une fois que la réplication transactionnelle a vidé toutes les modifications restant sur votre base de données source et que toutes vos applications pointent vers Azure SQL Database, vous pouvez désinstaller la réplication transactionnelle. Votre Base de données SQL Azure est maintenant votre système de production.

 ![Diagramme SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Vous pouvez également utiliser la réplication transactionnelle pour migrer un sous-ensemble de votre base de données source. La publication que vous répliquez vers une base de données SQL Azure peut être limitée à un sous-ensemble des tables dans la base de données en cours de réplication. Pour chaque table répliquée, vous pouvez limiter les données à un sous-ensemble de lignes et/ou un sous-ensemble de colonnes.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Workflow de migration vers SQL Database à l’aide de la réplication transactionnelle

> [!IMPORTANT]
> Utilisez la dernière version de SQL Server Management Studio pour rester synchronisé avec les mises à jour de Microsoft Azure et de Base de données SQL. Les versions antérieures de SQL Server Management Studio ne peuvent pas configurer Base de données SQL en tant qu’abonné. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Configurer la distribution
   -  [Avec SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Avec Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Créer une publication
   -  [Avec SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Avec Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Créer un abonnement
   -  [Avec SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Avec Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Quelques conseils et différences pour la migration vers SQL Database

- Utilisez un serveur de distribution local 
   - Cela a un impact sur les performances du serveur. 
   - Si l’impact sur les performances est inacceptable, vous pouvez utiliser un autre serveur, mais cela contribue à compliquer la gestion et l’administration.
- Lorsque vous sélectionnez un dossier d’instantanés, assurez-vous qu’il est suffisamment grand pour contenir un BCP de chaque table que vous souhaitez répliquer. 
- La création d’instantanés verrouille les tables associées jusqu’à la fin de l’opération. Par conséquent, prenez soin de bien planifier votre instantané. 
- Seuls les abonnements par émission de données sont pris en charge dans Azure SQL Database. Vous pouvez uniquement ajouter des abonnés à partir de la base de données source.

### <a name="resolving-database-migration-compatibility-issues"></a>Résolution des problèmes de compatibilité de migration de la base de données
Vous pouvez rencontrer une grande variété de problèmes de compatibilité, selon la version de SQL Server dans la base de données source et la complexité de la base de données que vous êtes en train de migrer. Les versions antérieures de SQL Server ont plus de problèmes de compatibilité. Utilisez les ressources suivantes en plus d’une recherche Internet ciblée dans le moteur de recherche de votre choix :

* [Fonctionnalités de base de données SQL Server non prises en charge dans Azure SQL Database](sql-database-transact-sql-information.md)
* [Discontinued Database Engine Functionality in SQL Server 2016 (Fonctionnalités du moteur de base de données supprimées dans SQL Server 2016)](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Discontinued Database Engine Functionality in SQL Server 2014 (Fonctionnalités du moteur de base de données non disponibles dans SQL Server 2014)](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Discontinued Database Engine Functionality in SQL Server 2012 (Fonctionnalités du moteur de base de données non disponibles dans SQL Server 2012)](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Discontinued Database Engine Functionality in SQL Server 2008 R2 (Fonctionnalités du moteur de base de données non disponibles dans SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Discontinued Database Engine Functionality in SQL Server 2005 (Fonctionnalités du moteur de base de données supprimées dans SQL Server 2005)](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Outre les recherches sur Internet et ces ressources, utilisez les [forums de communauté MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou [StackOverflow](http://stackoverflow.com/).

> [!IMPORTANT]
> SQL Database Managed Instance vous permet de migrer une instance existante de SQL Server et ses bases de données avec peu voire pas de problèmes de compatibilité. Consultez [Qu’est-ce que Managed Instance ?](sql-database-managed-instance.md).


## <a name="next-steps"></a>Étapes suivantes
* Utilisez le script fourni sur le blog Azure SQL EMEA Engineers pour [surveiller l’utilisation de tempdb pendant la migration](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
* Utilisez le script fourni sur le blog Azure SQL EMEA Engineers pour [surveiller l’espace réservé au journal des transactions de votre base de données pendant la migration](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
* Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Pour plus d’informations sur l’utilisation de l’heure UTC après la migration, consultez [Modifying the default time zone for your local time zone](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (Modification du fuseau horaire par défaut pour votre fuseau horaire local).
* Pour plus d’informations sur la modification de la langue par défaut d’une base de données après la migration, consultez [How to change the default language of Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (Modification de la langue par défaut d’Azure SQL Database).


