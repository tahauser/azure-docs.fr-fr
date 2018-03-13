---
title: "Restaurer une base de données SQL Azure à partir d’une sauvegarde | Microsoft Docs"
description: "Apprenez-en plus sur la limite de restauration dans le temps, qui vous permet de restaurer une base de données SQL Azure à un point antérieur dans le temps (jusqu’à 35 jours)."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 02/13/2018
ms.author: carlrab
ms.openlocfilehash: 81b61d59e63924e4af5befe4cbd638be98b385e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données
SQL Database fournit trois options pour la récupération de base de données à l’aide des [sauvegardes de base de données automatisées](sql-database-automated-backups.md) et des [sauvegardes depuis la rétention à long terme](sql-database-long-term-retention.md). Vous pouvez effectuer une restauration à partir d’une sauvegarde de base de données vers :

* Une nouvelle base de données sur le même serveur logique récupéré à un point spécifié dans le temps durant la période de rétention. 
* Une base de données sur le même serveur logique récupéré à l’heure de suppression pour une base de données supprimée.
* Une nouvelle base de données sur un serveur logique dans n’importe quelle région récupérée au point des sauvegardes quotidiennes les plus récentes dans le stockage d’objets blob géo-répliqué (RA-GRS).

> [!IMPORTANT]
> Vous ne pouvez pas remplacer une base de données existante lors de la restauration.
>

Une base de données restaurée implique un coût de stockage supplémentaire dans les conditions suivantes : 
- La restauration de P11-P15 vers S4-S12 ou P1-P6 si la taille maximale de la base de données est supérieure à 500 Go.
- La restauration de P1-P6 vers S4-S12 si la taille maximale de la base de données est supérieure à 250 Go.

Un coût supplémentaire s’applique, car la taille maximale de la base de données restaurée est supérieure à la quantité de stockage incluse pour le niveau de performance, et le stockage configuré au-delà de la quantité incluse est facturé en plus.  Pour les détails de la tarification du stockage supplémentaire, consultez la page [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).  Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en réduisant la taille maximale de la base de données à la quantité incluse. Pour plus d’informations sur les tailles de stockage de base de données et la modification de la taille maximale d’une base de données, consultez [Limites de ressources d’une base de données unique](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels).  

> [!NOTE]
> Les [sauvegardes de base de données automatiques](sql-database-automated-backups.md) sont utilisées lorsque vous créez une [copie de base de données](sql-database-copy.md). 
>


## <a name="recovery-time"></a>Temps de récupération
Le temps de récupération pour restaurer une base de données à l’aide des sauvegardes automatisées de bases de données est affecté par plusieurs facteurs : 

* la taille de la base de données ;
* le niveau de performances de la base de données ;
* le nombre de journaux de transactions impliqués ;
* la quantité d’activité devant être relue pour effectuer une récupération au point de restauration ;
* la bande passante du réseau, si la restauration s’effectue dans une autre région ; 
* le nombre de demandes de restauration simultanées en cours de traitement dans la région cible. 
  
  Pour une base de données très volumineuse et/ou très active, la restauration peut prendre plusieurs heures. En cas de panne prolongée dans une région, il est possible qu’un grand nombre de demandes de géorestauration soient traitées par d’autres régions. S’il y a un grand nombre de demandes, le temps de récupération des bases de données de cette région peut s’en trouver augmenté. La plupart des restaurations de bases de données se terminent dans un délai de 12 heures.

Pour un abonnement unique, des limitations s’appliquent sur le nombre de requêtes de restauration simultanées (y compris de restauration dans le temps, de restauration géographique et de restauration à partir d’une sauvegarde de rétention à long terme) soumises et effectuées :
|  | **Nombre maximum de requêtes simultanées traitées** | **Nombre maximum de requêtes simultanées soumises** |
| :--- | --: | --: |
|Base de données unique (par abonnement)|10|60|
|Pool élastique (par pool)|4|200|
||||

Il n'existe aucune fonctionnalité intégrée pour une restauration en bloc. Le script [Base de données SQL Azure : récupération de serveur complète](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) est un exemple d'une façon d'accomplir cette tâche.

> [!IMPORTANT]
> Pour effectuer une récupération à l’aide de sauvegardes automatisées, vous devez avoir un rôle de collaborateur SQL Server dans l’abonnement ou être le propriétaire de l’abonnement. Vous pouvez effectuer une récupération en utilisant le portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Limite de restauration dans le temps

Vous pouvez restaurer une base de données existante à un point antérieur dans le temps en tant que nouvelle base de données sur le même serveur logique à l’aide du portail Azure, de [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase) ou de l’[API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment restaurer une base de données à un point antérieur dans le temps, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
>

La base de données peut être restaurée à n’importe quel niveau de performances ou de service, et comme base de données unique ou dans un pool élastique. Assurez-vous d'avoir suffisamment de ressources sur le serveur logique ou dans le pool élastique vers lequel vous restaurez la base de données. Une fois le processus terminé, la base de données restaurée est une base de données normale entièrement accessible en ligne. La base de données restaurée est facturée aux tarifs habituels en fonction de ses performances et de son niveau de service. Aucun frais ne vous sera facturé jusqu’à ce que la restauration de la base de données soit terminée.

En règle générale, vous restaurez une base de données à un point antérieur à des fins de récupération. Dans ce cas, vous pouvez traiter la base de données restaurée comme remplacement de la base de données d’origine, ou l’utiliser pour en extraire des données afin de mettre à jour la base de données d’origine. 

* ***Remplacement de la base de données :*** si la base de données restaurée est destinée à remplacer la base de données d’origine, vous devez vérifier que les performances et/ou le niveau de service sont appropriés, et effectuer une mise à l’échelle si nécessaire. Vous pouvez renommer la base de données d’origine, puis donner le nom d’origine à la base de données restaurée à l’aide de la commande [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) dans T-SQL. 
* ***Récupération des données :*** si vous souhaitez récupérer des données à partir de la base de données restaurée suite à une erreur utilisateur ou d’application, vous devez écrire et exécuter les scripts de récupération de données nécessaires pour extraire les données à partir de la base de données restaurée et les transférer vers la base de données d’origine. Bien que l’opération de restauration puisse prendre un certain temps, la base de données en cours de restauration sera visible dans la liste de bases de données pendant tout le processus de restauration. Si vous supprimez la base de données pendant la restauration, l’opération de restauration est annulée et vous ne serez pas facturé pour la base de données dont la restauration ne s’est pas terminée. 

### <a name="azure-portal"></a>Portail Azure

Pour effectuer une récupération à un point dans le temps à l’aide du portail Azure, ouvrez la page de votre base de données, puis cliquez sur **Restaurer** dans la barre d’outils.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Restauration d’une base de données supprimée
Vous pouvez restaurer une base de données supprimée à l’heure de suppression sur le même serveur logique à l’aide du portail Azure, de [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase) ou de [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx). Vous pouvez restaurer une base de données supprimée à un point antérieur dans le temps au cours de la rétention à l’aide de [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment restaurer une base de données supprimée, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> Si vous supprimez une instance de serveur Azure SQL Database, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérées. Il n'existe aucune prise en charge pour la restauration d'un serveur supprimé pour l'instant.
> 

### <a name="azure-portal"></a>Portail Azure

Pour récupérer une base de données supprimée pendant sa [période de rétention](sql-database-service-tiers.md) à l’aide du portail Azure, ouvrez la page de votre serveur et dans la zone des opérations, cliquez sur **Bases de données supprimées**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Géo-restauration
Vous pouvez restaurer une base de données SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la dernière sauvegarde géo-répliquée complète ou différentielle. La géorestauration utilise une sauvegarde géoredondante en tant que source et peut être mise à profit pour récupérer une base de données même si la base de données ou le centre de données est inaccessible en raison d’une défaillance. 

La géorestauration constitue l’option de récupération par défaut lorsque votre base de données est indisponible en raison d’un incident dans la région où elle est hébergée. Si un incident à grande échelle dans une région entraîne l’indisponibilité de votre application de base de données, vous pouvez restaurer une base de données à partir des sauvegardes géorépliquées sur un serveur situé dans n’importe quelle autre région. Il peut y avoir un délai entre le moment où la sauvegarde différentielle est effectuée et celui où elle est géo-répliquée dans un objet blob Azure dans une autre région. Ce délai peut atteindre une heure. En cas d’incident, il peut donc y avoir jusqu’à une heure de pertes de données. L’illustration ci-dessous illustre la restauration de la base de données à partir de la dernière sauvegarde disponible dans une autre région.

![Restauration géographique](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment effectuer une géorestauration, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

La restauration dans le temps sur un géo-réplica secondaire n’est pas prise en charge actuellement. La restauration dans le temps peut être effectuée uniquement sur une base de données primaire. Pour plus d’informations sur l’utilisation de la géorestauration pour la récupération suite à une panne, voir [Récupération après une panne](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> La récupération à partir des sauvegardes est la solution de récupération d’urgence la plus élémentaire proposée dans SQL Database, avec le RPO (objectif de point de récupération) et l’ERT (temps de récupération estimé) les plus longs. Pour des solutions utilisant des bases de données De base, la géorestauration est souvent une solution de récupération d’urgence raisonnable avec un ERT de 12 heures. Pour des solutions utilisant des bases de données Standard ou Premium de plus grande taille qui nécessitent des temps de récupération plus courts, vous devez envisager d’utiliser une [géoréplication active](sql-database-geo-replication-overview.md). La géoréplication active offre un objectif de point de récupération et un temps de récupération estimé sensiblement inférieurs, car elle nécessite simplement un basculement vers une base de données secondaire répliquée en continu. Pour plus d’informations sur les choix de continuité d’activité, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
> 

### <a name="azure-portal"></a>Portail Azure

Pour effectuer une géorestauration d’une base de données lors de sa [période de rétention](sql-database-service-tiers.md) à l’aide du portail Azure, ouvrez la page des bases de données SQL, puis cliquez sur **Ajouter**. Dans la zone de texte **Sélectionner une source**, sélectionnez **Sauvegarde**. Spécifiez la sauvegarde à partir de laquelle effectuer la récupération dans la région et sur le serveur de votre choix. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Exécution par programme d’une récupération à l’aide des sauvegardes automatisées
Comme indiqué précédemment, en plus du Portail Azure, la récupération de la base de données peut être effectuée par programme à l’aide d’Azure PowerShell ou de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles.

### <a name="powershell"></a>PowerShell
| Applet de commande | Description |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Obtient une ou plusieurs bases de données. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Obtient une base de données supprimée que vous pouvez restaurer. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Obtient une sauvegarde géo-redondante d’une base de données. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Restaure une base de données SQL. |
|  | |

### <a name="rest-api"></a>de l’API REST
| API | Description |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Restaure une base de données |
| [Créer ou mettre à jour l’état de la base de données](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Retourne l’état durant une opération de restauration |
|  | |

## <a name="summary"></a>Résumé
Les sauvegardes automatiques protègent vos bases de données des erreurs utilisateur et des erreurs d’application, de la suppression accidentelle d’une base de données et des interruptions prolongées. Cette fonctionnalité intégrée est disponible pour tous les niveaux de service et de performances. 

## <a name="next-steps"></a>étapes suivantes
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
* Pour en savoir plus sur les sauvegardes automatisées Azure SQL Database, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
* Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez [Rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
* Pour configurer, gérer et restaurer depuis la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec le portail Azure, consultez [Configurer et utiliser la rétention des sauvegardes à long terme](sql-database-long-term-backup-retention-configure.md). 
* Pour plus d’informations sur les options de récupération plus rapides, consultez [Groupes de basculement et géoréplication active](sql-database-geo-replication-overview.md).  
