---
title: "Configurer la rétention des sauvegardes à long terme - Base de données SQL Azure | Microsoft Docs"
description: "Découvrez comment stocker des sauvegardes automatisées dans le coffre Azure Recovery Services et comment restaurer des bases de données à partir du coffre Azure Recovery Services"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: business continuity
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: carlrab
ms.openlocfilehash: e75facfd77fc1cb3c23aa4e1f6f7f799620fef39
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="configure-and-restore-from-azure-sql-database-long-term-backup-retention"></a>Configurer et restaurer à partir de la rétention des sauvegardes à long terme de base de données SQL Azure

Vous pouvez configurer le coffre Azure Recovery Services pour stocker les sauvegardes de base de données SQL Azure, puis récupérer une base de données à l’aide des sauvegardes conservées dans le coffre avec le portail Azure ou PowerShell.

## <a name="azure-portal"></a>Portail Azure

Les sections suivantes vous montrent comment utiliser le portail Azure pour configurer le coffre Azure Recovery Services, afficher les sauvegardes dans le coffre et restaurer à partir du coffre.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Configurer le coffre, inscrire le serveur et sélectionner les bases de données

Vous [configurez un coffre Azure Recovery Services de façon à conserver des sauvegardes automatisées](sql-database-long-term-retention.md) sur une période plus longue que la période de rétention associée à votre niveau de service. 

1. Ouvrez la page **SQL Server** de votre serveur.

   ![page sql server](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Cliquez sur **Long-term backup retention (Rétention des sauvegardes à long terme)**.

   ![lien de rétention des sauvegardes à long terme](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Sur la page **Long-term backup retention** (Rétention des sauvegardes à long terme) de votre serveur, examinez et acceptez les conditions d’utilisation de la version préliminaire (à moins que vous ne l’ayez déjà fait ou que cette fonctionnalité ne soit plus en version préliminaire).

   ![accepter les conditions d’utilisation de la version préliminaire](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Pour configurer la rétention des sauvegardes à long terme, sélectionnez cette base de données dans la grille, puis cliquez sur **Configurer** dans la barre d’outils.

   ![sélectionner la base de données pour la rétention des sauvegardes à long terme](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Sur la page **Configurer**, cliquez sur **Configurer les paramètres requis** sous **Coffre Recovery Services**.

   ![lien de configuration du coffre](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Sur la page **Coffre Recovery Services**, sélectionnez un éventuel coffre existant. Dans le cas contraire, si aucun coffre Recovery Services n’a été trouvé pour votre abonnement, cliquez pour quitter le flux, puis créez un coffre Recovery Services.

   ![créer un lien vers le coffre](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Sur la page **Coffres Recovery Services**, cliquez sur **Ajouter**.

   ![ajouter le lien vers le coffre](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. Sur la page **Coffre Recovery Services**, fournissez un nom valide pour le coffre Recovery Services.

   ![nom du nouveau coffre](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Sélectionnez votre abonnement et votre groupe de ressources, puis sélectionnez l’emplacement du coffre. Une fois que vous avez terminé, cliquez sur **Créer**.

   ![créer le coffre](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Le coffre doit se trouver dans la même région que le serveur logique SQL Azure et doit utiliser le même groupe de ressources que le serveur logique.
   >

10. Une fois le nouveau coffre créé, exécutez les étapes nécessaires pour revenir à la page **Coffre Recovery Services**.

11. Sur la page **Coffre Recovery Services**, cliquez sur le coffre, puis sur **Sélectionner**.

   ![sélectionner un coffre existant](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Sur la page **Configurer**, fournissez un nom valide pour la nouvelle stratégie de rétention, modifiez la stratégie de rétention par défaut à votre convenance, puis cliquez sur **OK**.

   ![définir la stratégie de rétention](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)
   
   >[!NOTE]
   >Certains caractères, comme les espaces, ne sont pas autorisés dans les noms de stratégie de rétention.

13. Sur la page **Long-term backup retention** (Rétention des sauvegardes à long terme) de votre base de données, cliquez sur **Enregistrer**, puis sur **OK** pour appliquer la stratégie de rétention des sauvegardes à long terme à toutes les bases de données sélectionnées.

   ![définir la stratégie de rétention](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Cliquez sur **Enregistrer** pour activer la rétention des sauvegardes à long terme à l’aide de cette nouvelle stratégie dans le coffre Azure Recovery Services que vous avez configuré.

   ![définir la stratégie de rétention](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> Une fois configurées, les sauvegardes s’affichent dans le coffre dans les sept jours qui suivent. Ne poursuivez pas ce didacticiel tant que les sauvegardes n’apparaissent pas dans le coffre.
>

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Visualiser les sauvegardes de la rétention à long terme à l’aide du portail Azure

Visualisez les informations relatives à vos sauvegardes de base de données dans la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md). 

1. Dans le portail Azure, ouvrez le coffre Azure Recovery Services de vos sauvegardes de base de données (accédez à **Toutes les ressources**, puis sélectionnez cette entrée dans la liste des ressources de votre abonnement) pour visualiser la quantité de stockage utilisée par les sauvegardes de votre base de données dans le coffre.

   ![visualiser le coffre Recovery Services avec les sauvegardes](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Ouvrez la page **Base de données SQL** de votre base de données.

   ![page nouvel exemple de base de données](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Dans la barre d’outils, cliquez sur **Restaurer**.

   ![barre d’outils Restaurer](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Sur la page Restaurer, cliquez sur **À long terme**.

5. Sous Azure vault backups (Sauvegardes de coffre Azure), cliquez sur **Sélectionner une sauvegarde** pour visualiser les sauvegardes de base de données disponibles dans la rétention des sauvegardes à long terme.

   ![sauvegardes dans le coffre](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Restaurer une base de données à partir d’une sauvegarde dans la rétention des sauvegardes à long terme à l’aide du portail Azure

Vous restaurez la base de données sous la forme d’une nouvelle base de données à partir d’une sauvegarde dans le coffre Azure Recovery Services.

1. Sur la page **Azure vault backups (Sauvegardes de coffre Azure)**, cliquez sur la sauvegarde à restaurer, puis cliquez sur **Sélectionner**.

   ![sélectionner une sauvegarde dans le coffre](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Dans la zone de texte **Nom de la base de données**, fournissez le nom de la base de données restaurée.

   ![nom de la nouvelle base de données](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Cliquez sur **OK** pour restaurer votre base de données à partir de la sauvegarde dans le coffre sous la forme d’une nouvelle base de données.

4. Dans la barre d’outils, cliquez sur l’icône de notification pour visualiser l’état du travail de restauration.

   ![progression du travail de restauration à partir du coffre](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Lorsque le travail de restauration est terminé, ouvrez la page **Bases de données SQL** pour visualiser la base de données nouvellement restaurée.

   ![base de données restaurée à partir du coffre](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour [extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

Les sections suivantes vous montrent comment utiliser PowerShell pour configurer le coffre Azure Recovery Services, afficher les sauvegardes dans le coffre et restaurer à partir du coffre.

### <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Utilisez [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) pour créer un coffre Recovery Services.

> [!IMPORTANT]
> Le coffre doit se trouver dans la même région que le serveur logique SQL Azure et doit utiliser le même groupe de ressources que le serveur logique.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Configurer votre serveur de manière à utiliser le coffre Recovery pour les sauvegardes de rétention à long terme

Utilisez l’applet de commande [Set-AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) pour associer un coffre Recovery Services créé précédemment à un serveur SQL Azure spécifique.

```PowerShell
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Créer une stratégie de rétention

Vous définissez la durée de conservation d’une sauvegarde de base de données dans une stratégie de rétention. Utilisez l’applet de commande [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) pour obtenir la stratégie de rétention par défaut à utiliser comme modèle pour la création de stratégies. Dans ce modèle, la période de rétention est définie sur 2 ans. Ensuite, exécutez [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) pour créer la stratégie. 

> [!NOTE]
> Certaines applets de commande nécessitent que vous définissiez le contexte du coffre avant l’exécution ([Set-AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)) afin de voir cette applet de commande dans quelques extraits de code connexes. Vous définissez le contexte, car la stratégie fait partie du coffre. Vous pouvez créer plusieurs stratégies de rétention pour chaque coffre, puis appliquer la stratégie souhaitée à des bases de données spécifiques. 


```PowerShell
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Configurer une base de données de manière à utiliser la stratégie de rétention définie précédemment

Utilisez l’applet de commande [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) pour appliquer la nouvelle stratégie à une base de données spécifique.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Visualiser les sauvegardes de la rétention à long terme et les informations sur les sauvegardes

Visualisez les informations relatives à vos sauvegardes de base de données dans la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md). 

Utilisez les applets de commande suivantes pour afficher les informations sur la sauvegarde :

- [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)

```PowerShell
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore

# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restaurer une base de données à partir d’une sauvegarde dans la rétention des sauvegardes à long terme

La restauration à partir de la rétention des sauvegardes à long terme utilise l’applet de commande [AzureRmSqlDatabase de restauration](/powershell/module/azurerm.sql/restore-azurermsqldatabase).

```PowerShell
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> À ce stade, vous pouvez vous connecter à la base de données restaurée à l’aide de SQL Server Management Studio pour exécuter les tâches nécessaires, notamment pour extraire un bit de données de la base de données restaurée à copier dans la base de données existante ou pour supprimer la base de données existante et renommer la base de données restaurée avec le nom de la base de données existante. Consultez [Restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez la page [Sauvegardes automatiques](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
- Pour plus d’informations sur la restauration à partir de sauvegardes, consultez l’article concernant la [restauration à l’aide de sauvegardes](sql-database-recovery-using-backups.md).
