---
title: Exporter une base de données SQL Azure dans un fichier BACPAC | Microsoft Docs
description: Exporter une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 10/11/2017
ms.author: carlrab
ms.topic: article
ms.openlocfilehash: 8f722f6d7563d22008105b503eb2433c7bd310a5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exporter une base de données SQL Azure dans un fichier BACPAC

Si vous avez besoin d’exporter une base de données afin de l’archiver ou de la déplacer vers une autre plateforme, vous pouvez exporter le schéma et les données de cette base de données vers un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Un fichier BACPAC est un fichier ZIP avec une extension de fichier BACPAC contenant les métadonnées et les données à partir d’une base de données SQL Server. Il peut être stocké dans le Stockage Blob Azure ou un stockage local dans un emplacement local, puis importé dans Azure SQL Database ou une installation locale SQL Server. 

> [!IMPORTANT] 
> L’exportation automatisée Azure SQL Database a été retirée le 1er mars 2017. Vous pouvez utiliser la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md
) ou [Azure Automation](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) pour archiver régulièrement les bases de données SQL à l’aide de PowerShell selon la planification de votre choix. Pour obtenir un exemple, téléchargez l[’exemple de script PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) à partir de Github.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considérations à prendre en compte lors de l’exportation d’une base de données SQL Azure

* Pour qu’un export soit cohérent au niveau transactionnel, vous devez vérifier qu’aucune activité d’écriture n’a lieu lors de l’exportation ou effectuer une exportation à partir d’une [copie cohérente au niveau transactionnel](sql-database-copy.md) de votre base de données SQL Azure.
* Si vous exportez dans le stockage Blob, la taille maximale d’un fichier BACPAC est de 200 Go. Pour archiver un fichier BACPAC plus volumineux, exportez dans le stockage local.
* L’exportation d’un fichier BACPAC vers le stockage Premium Azure en utilisant les méthodes décrites dans cet article n’est pas prise en charge.
* Si l’opération d’exportation depuis Azure SQL Database dure plus de 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :
  * Augmentez temporairement votre niveau de service.
  * Interrompez toutes les activités de lecture et d’écriture lors de l’exportation.
  * Utilisez un [index cluster](https://msdn.microsoft.com/library/ms190457.aspx) avec des valeurs non nulles sur toutes les tables de grande taille. Sans index cluster, une exportation peut échouer si elle dure plus de 6 à 12 heures. Cela est dû au fait que les services d’exportation doivent effectuer une analyse complète de la table pour tenter de l’exporter en entier. Une bonne méthode pour déterminer si vos tables sont optimisées pour l’exportation consiste à exécuter **DBCC SHOW_STATISTICS** et à vérifier que *RANGE_HI_KEY* n’est pas nul et que sa valeur a une bonne distribution. Pour plus d’informations, consultez [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Vue d’ensemble de la continuité de l’activité avec la base de données SQL Azure](sql-database-business-continuity.md) et [Découvrir les sauvegardes SQL Database](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exporter vers un fichier BACPAC à l’aide du portail Azure

Pour exporter une base de données à l’aide du [portail Azure](https://portal.azure.com), ouvrez la page de votre base de données, puis cliquez sur **Exporter** dans la barre d’outils. Spécifiez le nom du fichier BACPAC, indiquez le compte Azure Storage ainsi que le conteneur pour l’exportation, puis fournissez les informations d’identification pour se connecter à la base de données source.  

![Exportation de base de données](./media/sql-database-export/database-export.png)

Pour surveiller la progression de l’opération d’exportation, ouvrez la page du serveur logique qui contient la base de données en cours d’exportation. Faites défiler jusqu'à **Operations**, puis cliquez sur l’historique **Import/Export**.

![exporter l’historique](./media/sql-database-export/export-history.png)
![exporter l’état de l’historique](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exporter vers un fichier BACPAC à l’aide de l’utilitaire SQLPackage

Pour exporter une base de données SQL à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), consultez [Paramètres et propriétés d’exportation](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). L’utilitaire SQLPackage est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.

Nous recommandons l’utilisation de l’utilitaire SQLPackage afin de bénéficier de l’évolutivité et des performances dans la plupart des environnements de production. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Cet exemple montre comment exporter une base de données à l’aide de SqlPackage.exe avec l’authentification Active Directory universelle :

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exporter vers un fichier BACPAC à l’aide de SQL Server Management Studio (SSMS)

Les dernières versions de SQL Server Management Studio incluent également un assistant permettant d’exporter une base de données SQL Azure vers un fichier BACPAC. Consultez [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exporter vers un fichier BACPAC à l’aide de PowerShell

Utilisez la cmdlet [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) pour soumettre une demande d’exportation de base de données au service de base de données SQL Azure. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Pour vérifier l’état de la demande d’exportation, utilisez l’applet de commande [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). L’exécution immédiatement après la demande retourne généralement **État : en cours**. Lorsque **État : réussite** s’affiche, l’exportation est terminée.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")   
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la rétention des sauvegardes à long terme d’une sauvegarde de base de données SQL Azure comme alternative à l’exportation d’une base de données à des fins d’archivage, consultez [Rétention à long terme](sql-database-long-term-retention.md).
- Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Pour en savoir plus sur l’importation d’un fichier BACPAC dans une base de données SQL Server, consultez [Importer un fichier BACPCAC dans une base de données SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
* Pour en savoir plus sur l’exportation d’un fichier BACPAC à partir d’une base de données SQL Server, consultez [Exporter une application de la couche Données](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) et [Migrer votre première base de données](sql-database-migrate-your-sql-server-database.md).
* Si vous exportez depuis SQL Server avant de procéder à la migration vers Azure SQL Database, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
