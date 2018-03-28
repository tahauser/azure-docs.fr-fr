---
title: Limites de ressources d’Azure SQL Database | Microsoft Docs
description: Cette page décrit certaines limites de ressources courantes pour une base de données SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: ba192b6dba68f01af796a1099b064d6ec0bd3f1b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="azure-sql-database-resource-limits"></a>Limites de ressources de base de données SQL Azure

> [!IMPORTANT]
> Pour les limites de ressources avec Azure SQL Database Managed Instance, consultez [SQL Database Managed Instance](sql-database-managed-instance.md#managed-instance-service-tier).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Base de données unique : tailles de stockage et niveaux de performance

Pour les bases de données uniques, les tableaux suivants indiquent les ressources disponibles pour une base de données unique à chaque niveau de performance et de service. Vous pouvez définir le niveau de service, le niveau de performance et la quantité de stockage pour une base de données unique à l’aide du [portail Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), de [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), de [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [d’Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) ou de [l’API REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Base de données unique : modifier la taille de stockage

- Le prix des DTU pour une base de données unique inclut une certaine quantité de stockage sans coût supplémentaire. Un espace de stockage en plus du volume inclus peut être approvisionné pour un coût supplémentaire jusqu’à la limite de taille par incréments de 250 Go jusqu’à 1 To, puis par incréments de 256 Go au-delà de 1 To. Pour les quantités de stockage et limites de taille maximale incluses, consultez [Base de données unique : tailles de stockage et niveaux de performance](#single-database-storage-sizes-and-performance-levels).
- Vous pouvez configurer du stockage supplémentaire pour une base de données en augmentant sa taille maximale à l’aide du [portail Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), de [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az_sql_db_update) ou de [l’API REST](/rest/api/sql/databases/update).
- Le prix de l’espace de stockage supplémentaire pour une base de données unique est égal au volume de stockage supplémentaire multiplié par le prix unitaire du stockage supplémentaire pour le niveau de service. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Base de données unique : modifier les DTU

Après la sélection initiale d’un niveau de service, d’un niveau de performance et d’une quantité de stockage, vous pouvez faire évoluer une base de données vers le haut ou vers le bas de façon dynamique en fonction de l’expérience réelle à l’aide du [portail Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), de [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [d’Azure CLI](/cli/azure/sql/db#az_sql_db_update) ou de [l’API REST](/rest/api/sql/databases/update). 

La vidéo suivante montre la modification dynamique du niveau de performance pour augmenter les DTU disponibles pour une base de données.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Le délai de basculement peut varier, mais il est généralement de moins de 4 secondes, et ne dépassera pas les 30 secondes dans 99 % des cas. Ce délai peut se révéler supérieur, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées. 

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service Standard ne demande pas plus de six heures. Le changement des niveaux de performances d’une base de données de la même taille dans le niveau de service Premium doit s’effectuer en moins de trois heures.

> [!TIP]
> Pour surveiller les opérations en cours, consultez la [gestion des opérations à l’aide de l’API REST SQL](/rest/api/sql/Operations/List), la [gestion des opérations à l’aide de l’interface de ligne de commande](/cli/azure/sql/db/op), la [surveillance des opérations à l’aide T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database), ainsi que ces deux commandes PowerShell : [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) et [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Si vous effectuez la mise à niveau vers un niveau de service ou de performance supérieur, la taille maximale de la base de données n’augmente pas, à moins que vous n’en fassiez la demande (maxsize).
* Pour pouvoir passer à une version antérieure, l’espace utilisé par la base de données doit être inférieur à la taille et au niveau de performance autorisés par le niveau de service voulu. 
* Lorsque vous rétrogradez du niveau **Premium** vers le niveau **Standard**, un coût de stockage supplémentaire s’applique si les (1) la taille maximale de la base de données est prise en charge dans le niveau de performance cible et (2) la taille maximale dépasse la quantité de stockage incluse dans le niveau de performance cible. Par exemple, si une base de données P1 avec une taille maximale de 500 Go est rétrogradée en S3, un coût de stockage supplémentaire s’applique, car S3 prend en charge une taille maximale de 500 Go et la quantité de stockage incluse est de seulement 250 Go. Par conséquent, la quantité d’espace de stockage supplémentaire est de 500 Go - 250 Go = 250 Go. Pour la tarification du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en réduisant la taille maximale de la base de données à la quantité incluse. 
* Lors de la mise à niveau d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant de procéder à la mise à niveau de la base de données primaire (conseil général). Lors de la mise à niveau vers une version différente, il est nécessaire de mettre d’abord à niveau la base de données secondaire.
* Lors de la mise à niveau descendante d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données primaires associées vers le niveau de performance inférieur souhaité avant de procéder à la mise à niveau descendante de la base de données secondaire (conseil général). Lors de la mise à niveau vers une version inférieure, il est nécessaire de mettre d’abord à niveau la base de données primaire.
* Les offres de service de restauration sont différentes selon les niveaux de service. Si vous retournez au niveau de service **De base**, la rétention des fichiers de sauvegarde sera de courte durée (consultez la section relative aux [sauvegardes Azure SQL Database](sql-database-automated-backups.md)).
* Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Base de données unique : limitations de P11 et P15 lorsque la taille maximale est supérieure à 1 To

Une taille maximale supérieure à 1 To pour les bases de données P11 et P15 est prise en charge dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, Centre de la France, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Centre de la Corée, Centre-Nord des États-Unis, Europe du Nord, Centre-Sud des États-Unis, Sud-Est asiatique, Sud du Royaume-Uni, Ouest du Royaume-Uni, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis – Virginie et Europe de l’Ouest. Les considérations et limitations suivantes s’appliquent aux bases de données P11 et P15 avec une taille maximale supérieure à 1 To :

- Si vous choisissez une taille maximale supérieure à 1 To lors de la création d’une base de données (avec une valeur de 4 To ou de 4096 Go), la commande de création échoue avec une erreur si la base de données est configurée dans une région non prise en charge.
- Pour les bases de données P11/P15 existantes situées dans l’une des régions prises en charge, vous pouvez augmenter la taille maximale du stockage au-delà de 1 To par incréments de 256 Go jusqu’à 4 To. Pour voir si une plus grande taille est prise en charge dans votre région, utilisez la fonction [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) ou inspectez la taille de la base de données dans le portail Azure. La mise à niveau d’une base de données P11 ou P15 existante ne peut être effectuée que par le biais d’une connexion du principal au niveau du serveur ou par les membres du rôle de base de données dbmanager. 
- En cas d’exécution d’une mise à niveau dans une région prise en charge, la configuration est mise à jour immédiatement. La base de données reste en ligne pendant le processus de mise à niveau. Toutefois, vous ne pourrez pas utiliser la totalité du stockage au-delà de 1 To tant que les fichiers de base de données réels n’ont pas été mis à niveau vers la nouvelle taille maximale. La durée requise dépend de la taille de la base de données mise à niveau. 
- Lors de la création ou de la mise à jour d’une base de données P11 ou P15, vous n’avez le choix qu’entre les tailles maximales de 1 To à 4 To par incréments de 256 Go. Lorsque vous créez une base de données P11/P15, l’option de stockage par défaut de 1 To est présélectionnée. Pour les bases de données situées dans l’une des régions prises en charge, vous pouvez augmenter le stockage maximal d’une base de données unique (nouvelle ou existante) jusqu’à un maximum de 4 To. Pour toutes les autres régions, la taille maximale ne peut pas être supérieure à 1 To. Le prix ne change pas lorsque vous sélectionnez l’option de 4 To de stockage inclus.
- Si la taille maximale d’une base de données est réglée sur une valeur supérieure à 1 To, alors elle ne peut pas être modifiée sur 1 To même si le stockage réel utilisé est inférieur à 1 To. Par conséquent, vous ne pouvez pas rétrograder un compte P11 ou P15 avec une taille maximale supérieure à 1 To vers un P11 ou P15 1 To, ou à un niveau de performance inférieur, comme P1-P6). Cette restriction s’applique également aux scénarios de restauration et de copie, notamment la limite de restauration dans le temps, la restauration géographique, la rétention de sauvegarde à long terme et la copie de bases de données. Une fois qu’une base de données est configurée avec une taille maximale supérieure à 1 To, toutes les opérations de restauration de cette base de données doivent s’effectuer vers une base de données P11/P15 avec une taille maximale supérieure à 1 To.
- Pour les scénarios de géoréplication active :
   - Configuration d’une relation de géoréplication : si la base de données primaire est de niveau P11 ou P15, la ou les bases de données secondaires doivent également être de niveau P11 ou P15. Les niveaux de performance inférieurs sont rejetés pour les bases de données secondaires, car ils ne sont pas en mesure de prendre en charge plus de 1 To.
   - Mise à niveau de la base de données primaire dans une relation de géoréplication : le fait de modifier la taille maximale de la base de données primaire pour plus de 1 To déclenche la même modification sur la base de données secondaire. Les deux mises à niveau doivent aboutir pour que la modification sur la base de données principale prenne effet. Des limitations de région pour les options de plus de 1 To s’appliquent. Si la base de données secondaire se situe dans une région qui ne prend pas en charge plus de 1 To, la base de données primaire n’est pas mise à niveau.
- L’utilisation du service Import/Export pour le chargement des bases de données P11/P15 avec plus de 1 To n’est pas prise en charge. Utilisez SqlPackage.exe pour [importer](sql-database-import.md) et [exporter](sql-database-export.md) les données.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pool élastique : tailles de stockage et niveaux de performance

Pour les pools élastiques SQL Database, les tableaux suivants indiquent les ressources disponibles à chaque niveau de performance et de service. Vous pouvez définir le niveau de service, le niveau de performance et la quantité de stockage à l’aide du [portail Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), de [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [d’Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) ou de [l’API REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Les limites de ressources des bases de données individuelles dans les pools élastiques sont généralement identiques à celles des bases de données uniques situées hors des pools. Elles dépendent du nombre de DTU et du niveau de service. Par exemple, le nombre maximal d’ouvriers simultanés dans une base de données S2 est de 120. Par conséquent, le nombre maximal d’ouvriers simultanés d’une base de données dans un pool Standard est également de 120 si le nombre maximal de DTU par base de données dans le pool est de 50 (soit l’équivalent de S2).

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Si toutes les DTU d’un pool élastique sont utilisées, chaque base de données du pool reçoit une quantité égale de ressources pour traiter les requêtes. Le service de base de données SQL offre un partage équitable des ressources entre les bases de données, garantissant des tranches de temps de calcul égales. Le partage équitable des ressources du pool élastique s’ajoute à n’importe quelle quantité de ressources garantie pour chaque base de données lorsque le nombre minimal de DTU par base de données est défini sur une valeur différente de zéro.

### <a name="database-properties-for-pooled-databases"></a>Propriétés de base de données pour les bases de données mises en pool

Le tableau suivant décrit les propriétés des bases de données mises en pool.

| Propriété | Description |
|:--- |:--- |
| Nombre maximal d’eDTU par base de données |Nombre maximal d’eDTU pouvant être utilisées par une des bases de données du pool en fonction du nombre d’eDTU utilisées par les autres bases de données du pool. Le nombre maximal d’eDTU par base de données n’est pas une garantie concernant l’octroi des ressources pour une base de données. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Définissez un nombre maximal d’eDTU par base de données suffisamment élevé pour gérer les pics d’utilisation des bases de données. Une certaine allocation excessive est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d’utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d’utilisation. Par exemple, supposons que le pic d’utilisation par base de données est de 20 eDTU et que seules 20 % des 100 bases de données du pool connaissent simultanément un pic d’utilisation. Si le nombre maximal d’eDTU par base de données est défini sur 20 eDTU, vous pouvez envisager une allocation 5 fois plus élevée du pool et définir le nombre d’eDTU par pool sur 400. |
| Nombre minimal d’eDTU par base de données |Nombre minimal d’eDTU garanti pour chaque base de données du pool. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Le nombre minimal d’eDTU par base de données peut être défini sur 0, qui est également la valeur par défaut. Cette propriété est définie sur une valeur comprise entre 0 et le nombre moyen d’eDTU utilisées par base de données. Le produit du nombre de bases de données du pool et du nombre minimal d’eDTU par base de données ne peut pas dépasser le nombre d’eDTU par pool. Par exemple, si un pool comporte 20 bases de données et que le nombre minimal d’eDTU par base de données est défini sur 10 eDTU, le nombre d’eDTU par pool doit être d’au moins 200 eDTU. |
| Espace de stockage maximal par base de données |Espace de stockage maximal pour une base de données du pool. Les bases de données regroupées se partagent l’espace de stockage du pool. Par conséquent, le stockage de base de données est limité au stockage de pool minimal restant, dans la limite du stockage maximal par base de données. Le stockage maximal par base de données fait référence à la taille maximale des fichiers de données et n’inclut pas l’espace utilisé par les fichiers journaux. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pool élastique : modifier la taille du stockage

- Le prix des eDTU pour un pool élastique inclut une certaine quantité de stockage sans coût supplémentaire. Un espace de stockage en plus du volume inclus peut être approvisionné pour un coût supplémentaire jusqu’à la limite de taille par incréments de 250 Go jusqu’à 1 To, puis par incréments de 256 Go au-delà de 1 To. Pour les quantités de stockage et limites de taille maximale incluses, consultez [Pool élastique : tailles de stockage et niveaux de performance](#elastic-pool-storage-sizes-and-performance-levels).
- Vous pouvez configurer du stockage supplémentaire pour un pool élastique en augmentant sa taille maximale à l’aide du [portail Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [d’Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou de [l’API REST](/rest/api/sql/elasticpools/update).
- Le prix de l’espace de stockage supplémentaire pour un pool élastique est égal au volume de stockage supplémentaire multiplié par le prix unitaire du stockage supplémentaire pour le niveau de service. Pour plus d’informations sur le prix du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Pool élastique : modifier les eDTU

Vous pouvez augmenter ou diminuer les ressources disponibles pour un pool élastique en fonction des besoins de la ressource à l’aide du [portail Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), de [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [d’Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou de [l’API REST](/rest/api/sql/elasticpools/update).

- Lors de la remise à l’échelle des eDTU du pool, les connexions de base de données sont brièvement supprimées. Il s’agit du même comportement que celui qui se produit lors de la remise à l’échelle des DTU pour une base de données (qui n’est pas dans un pool). Pour plus d’informations sur la durée et l’impact des pertes de connexion pour une base de données lors des opérations de remise à l’échelle, consultez [Remise à l’échelle des DTU pour une base de données unique](#single-database-change-storage-size). 
- La durée de remise à l’échelle des eDTU de pool peut dépendre de la quantité totale d’espace de stockage utilisée par toutes les bases de données dans le pool. En règle générale, la latence de remise à l’échelle moyenne est de 90 minutes ou moins pour 100 Go. Par exemple, si l’espace total utilisé par toutes les bases de données du pool est égal à 200 Go, une opération de redimensionnement du pool prend 3 heures au maximum. Dans certains cas dans le niveau Standard ou De base, la latence de remise à l’échelle peut être de moins de cinq minutes, quelle que soit la quantité d’espace utilisé.
- En général, la durée de modification du nombre minimal d’eDTU par base de données ou du nombre maximal d’eDTU par base de données prend cinq minutes au maximum.
- Lors de la réduction des eDTU d’un pool, l’espace utilisé par le pool doit être inférieur à la taille et au niveau de performance autorisés et aux eDTU maximales du pool.
- Lors de la remise à l’échelle des eDTU du pool, un coût de stockage supplémentaire s’applique si (1) la taille de stockage maximale du pool est prise en charge par le pool cible, et (2) la taille maximale de stockage dépasse la quantité de stockage incluse dans le pool cible. Par exemple, si un pool Standard de 100 eDTU avec une taille maximale de 100 Go est rétrogradé en pool Standard de 50 eDTU, un coût de stockage supplémentaire s’applique, car le pool cible prend en charge une taille maximale de 100 Go et la quantité de stockage incluse est de seulement 50 Go. Par conséquent, la quantité d’espace de stockage supplémentaire est de 100 Go - 50 Go = 50 Go. Pour la tarification du stockage supplémentaire, consultez [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en réduisant la taille maximale de la base de données à la quantité incluse. 

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Quel est le nombre maximal de serveurs et de bases de données ?

| Maximale | Valeur |
| :--- | :--- |
| Bases de données par serveur | 5 000 |
| Nombre de serveurs par région et par abonnement | 21 |
|||

> [!IMPORTANT]
> Le nombre de bases de données approchant la limite par serveur, les éléments suivants peuvent se produire :
> <br> • Augmentation de latence dans l’exécution de requêtes sur la base de données master.  Cela inclut les vues de statistiques d’utilisation des ressources telles que sys.resource_stats.
> <br> • Augmentation de latence des opérations de gestion et le rendu des points de vue de portails qui impliquent des bases de données sur le serveur.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Que se passe-t-il lorsque les limites de ressources d’une base de données ou d’un pool élastique sont atteintes ?

### <a name="compute-dtus-and-edtus"></a>Calcul (DTU et eDTU)

Lorsque l’utilisation du calcul de la base de données (exprimée en DTU et eDTU) est élevée, la latence de la requête augmente et peut même causer une expiration du délai d’attente. Dans ces conditions, les requêtes peuvent être mises en file d’attente par le service et reçoivent des ressources pour leur exécution lorsque des ressources se libèrent.
En cas d’utilisation élevée des calculs, voici certaines des options d’atténuation à votre disposition :

- Augmenter le niveau de performance de la base de données ou d’un pool élastique pour fournir plus de DTU ou eDTU à la base de données. Consultez [Base de données unique : modifier les DTU](#single-database-change-dtus) et [Pool élastique : modifier les eDTU](#elastic-pool-change-edtus).
- Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Stockage

Lorsque l’espace de base de données utilisé atteint la limite de taille maximale, les insertions et mises à jour de base de données qui augmentent la taille des données échouent et les clients reçoivent un [message d’erreur](sql-database-develop-error-messages.md). Les opérations de type SELECT et DELETE sur la base de données continuent à aboutir.

En cas d’utilisation élevée de l’espace, voici certaines des options d’atténuation à votre disposition :

- Augmenter la taille maximale de la base de données ou du pool élastique ou modifier le niveau de performance pour obtenir plus de stockage inclus. Consultez [Limites de ressources de base de données SQL](sql-database-resource-limits.md).
- Si la base de données est dans un pool élastique, vous pouvez également déplacer la base de données en dehors du pool afin que son espace de stockage ne soit pas partagé avec d’autres bases de données.

### <a name="sessions-and-workers-requests"></a>Sessions et workers (requêtes) 

Le nombre maximal de sessions et workers simultanés est déterminé par les niveaux de performance et de service (DTU et eDTU).  Lorsque les limites de sessions ou de workers sont atteintes, les nouvelles requêtes sont refusées et les clients reçoivent un message d’erreur. Si le nombre de connexions disponibles peut être contrôlé par l’application, le nombre de workers simultanés est souvent plus difficile à estimer et à contrôler. Cela est particulièrement vrai pendant les pics de charge lorsque les limites de ressources de base de données sont atteintes et que les workers s’accumulent en raison de requêtes plus longues à exécuter. 

En cas d’utilisation élevée de workers ou de sessions, voici certaines des options d’atténuation à votre disposition :
- Augmenter le niveau de performance ou le niveau de service du pool élastique ou de la base de données. Consultez [Base de données unique : modifier la taille de stockage](#single-database-change-storage-size), [Base de données unique : modifier les DTU](#single-database-change-dtus), [Pool élastique : modifier la taille du stockage](#elastic-pool-change-storage-size) et [Pool élastique : modifier les eDTU](#elastic-pool-change-edtus).
- Optimiser les requêtes afin de réduire l’utilisation des ressources de chaque requête si la cause de l’utilisation du travail accrue est un problème de contention des ressources de calcul. Pour plus d’informations, consultez la page [Paramétrage/Compréhension de requêtes](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de service, consultez [Niveaux de service](sql-database-service-tiers.md).
- Pour plus d’informations sur les bases de données uniques, consultez [Ressources d’une base de données unique](sql-database-resource-limits.md).
- Pour plus d’informations sur les pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
- Pour plus d’informations sur les DTU et eDTU, consultez [DTU et eDTU](sql-database-what-is-a-dtu.md).
- Pour plus d’informations sur les limites de taille de tempdb, consultez https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
