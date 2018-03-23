---
title: Importer un fichier BACPAC pour créer une base de données SQL Azure | Microsoft Docs
description: Créez une base de données SQL Azure en important un fichier BACPAC.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 01/29/2018
ms.author: carlrab
ms.topic: article
ms.openlocfilehash: 9d829bd312bb1ae2468258bc2ec8619b2858394e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Importer un fichier BACPAC dans une nouvelle base de données SQL Azure

Lorsque vous avez besoin d’importer une base de données à partir d’une archive ou lors de la migration à partir d’une autre plateforme, vous pouvez importer les données et le schéma de base de données à partir d’un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Un fichier BACPAC est un fichier ZIP avec une extension de fichier BACPAC contenant les métadonnées et les données à partir d’une base de données SQL Server. Il peut être importé à partir du Stockage Blob Azure (stockage standard uniquement) ou à partir du stockage local dans un emplacement local. Pour optimiser la vitesse de l’importation, nous vous recommandons de spécifier un niveau de performances et un niveau de service supérieurs, par exemple P6, puis de descendre en puissance une fois l’importation réussie. En outre, le niveau de compatibilité de la base de données après l’importation est basé sur le niveau de compatibilité de la base de données source. 

> [!IMPORTANT] 
> Après avoir migré votre base de données vers la base de données SQL Azure, vous pouvez choisir d’utiliser la base de données avec son niveau de compatibilité actuel (niveau 100 pour la base de données AdventureWorks2008R2) ou à un niveau supérieur. Pour plus d’informations sur les implications et les options du fonctionnement d’une base de données à un niveau de compatibilité spécifique, consultez [Niveau de compatibilité ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consultez également [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) pour plus d’informations sur des paramètres supplémentaires au niveau de la base de données relatifs aux niveaux de compatibilité.   >

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Importation à partir d’un fichier BACPAC à l’aide du portail Azure

Cet article fournit des instructions pour créer une base de données SQL Azure à partir d’un fichier BACPAC stocké dans le Stockage Blob Azure à l’aide du [portail Azure](https://portal.azure.com). L’importation à l’aide du portail Azure ne prend en charge que l’importation d’un fichier BACPAC à partir du Stockage Blob Azure.

Pour importer une base de données à l’aide du portail Azure, ouvrez la page du serveur auquel associer la base de données, puis cliquez sur **Importer** dans la barre d’outils. Spécifiez le compte de stockage et le conteneur, puis sélectionnez le fichier BACPAC que vous souhaitez importer. Sélectionnez la taille de la base de données (généralement la même que celle d’origine) et indiquez les informations d’identification du serveur SQL Server de destination.  

   ![Importation de base de données](./media/sql-database-import/import.png)

Pour surveiller la progression de l’opération d’importation, ouvrez la page du serveur logique qui contient la base de données en cours d’importation. Faites défiler jusqu'à **Operations**, puis cliquez sur l’historique **Import/Export**.

### <a name="monitor-the-progress-of-an-import-operation"></a>Surveiller la progression d’une opération d’importation

Pour surveiller la progression de l’opération d’importation, ouvrez la page du serveur logique dans laquelle la base de données est en cours d’importation. Faites défiler jusqu'à **Operations**, puis cliquez sur l’historique **Import/Export**.
   
   ![importer](./media/sql-database-import/import-history.png)![état d’importation](./media/sql-database-import/import-status.png)

Pour vérifier que la base de données est en ligne sur le serveur, cliquez sur **Bases de données SQL** et assurez-vous que la nouvelle base de données est définie sur **En ligne**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importer à partir d’un fichier BACPAC à l’aide de SQLPackage

Pour importer une base de données SQL à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), consultez [Paramètres et propriétés d’importation](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties). L’utilitaire SQLPackage est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.

Nous recommandons l’utilisation de l’utilitaire SQLPackage afin de bénéficier de l’évolutivité et des performances dans la plupart des environnements de production. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Consultez la commande SQLPackage suivante pour obtenir un exemple de script afin de savoir comment importer la base de données **AdventureWorks2008R2** à partir du stockage local vers un serveur logique de base de données SQL Azure, appelé **mynewserver20170403** dans cet exemple. Ce script illustre la création d’une base de données appelée **myMigratedDatabase**, avec un niveau de service **Premium**et un objectif de service **P6**. Modifiez ces valeurs en fonction de votre environnement.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Un serveur logique Azure SQL Database écoute sur le port 1433. Si vous essayez de vous connecter à un serveur logique Azure SQL Database à partir d’un pare-feu d’entreprise, ce port doit être ouvert dans le pare-feu d’entreprise pour que vous puissiez vous connecter.
>

Cet exemple montre comment exporter une base de données à l’aide de SqlPackage.exe avec l’authentification Active Directory universelle :

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importer à partir d’un fichier BACPAC à l’aide de PowerShell

Utilisez l’applet de commande [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) pour soumettre une demande d’importation de base de données au service de base de données SQL Azure. Selon la taille de votre base de données, l'opération d'importation peut prendre plus ou moins longtemps.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Pour vérifier l’état de la demande d’importation, utilisez l’applet de commande [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). L’exécution immédiatement après la demande retourne généralement **État : en cours**. Lorsque **État : réussite** s’affiche, l’importation est terminée.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Pour un autre exemple de script, consultez [Importation d’une base de données à partir d’un fichier BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour vous connecter et interroger une base de données SQL importée, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
* Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Pour une description du processus complet de migration d’une base de données SQL Server, y compris les recommandations relatives aux performances, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).



