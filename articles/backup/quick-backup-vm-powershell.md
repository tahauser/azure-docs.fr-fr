---
title: "Démarrage rapide de Azure : sauvegarder une machine virtuelle avec PowerShell | Microsoft Docs"
description: "Découvrez comment sauvegarder vos machines virtuelles avec Azure PowerShell"
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 2/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 55de58770bd91e000c12b42c8eeac92fb8c0a710
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Sauvegarder une machine virtuelle dans Azure avec PowerShell
Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans des scripts. Vous pouvez protéger vos données en effectuant des sauvegardes à intervalles réguliers. La sauvegarde Azure crée des points de récupération pouvant être stockés dans des coffres de récupération géo-redondants. Cet article explique comment sauvegarder une machine virtuelle (VM) avec le module Azure PowerShell. Vous pouvez également suivre ces étapes avec l’interface [Azure CLI](quick-backup-vm-cli.md) ou [le portail Azure](quick-backup-vm-portal.md).

Ce démarrage rapide permet la sauvegarde sur une machine virtuelle Azure existante. Si vous devez créer une machine virtuelle, vous pouvez [créer une machine virtuelle avec Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Ce démarrage rapide requiert le module Azure PowerShell version 4.4 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

Si vous utilisez la sauvegarde Azure pour la première fois, vous devez inscrire le fournisseur de services Azure Recovery Services dans votre abonnement avec [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vaults"></a>Créer des coffres Recovery Services
Un coffre Recovery Services est un conteneur logique qui stocke les données de sauvegarde de chaque ressource protégée, telles que des machines virtuelles Azure. Lorsque le travail de sauvegarde d’une ressource protégée s’exécute, il crée un point de récupération à l’intérieur du coffre Recovery Services. Vous pouvez ensuite utiliser un de ces points de récupération pour restaurer des données à un moment donné dans le temps.

Créez un coffre Recovery Services avec [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Spécifiez le même groupe de ressources et le même emplacement que ceux de la machine virtuelle que vous souhaitez protéger. Si vous avez utilisé l’[exemple de script](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) pour créer votre machine virtuelle, le groupe de ressources est nommé *myResourceGroup*, la machine virtuelle est nommée *myVM* et les ressources se trouvent à l’emplacement *WestEurope*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

Par défaut, le coffre est défini pour le stockage géo-redondant. Pour mieux protéger vos données, ce niveau de redondance de stockage garantit que vos données de sauvegarde sont répliquées dans une région Azure secondaire, située à des centaines de kilomètres de la région principale.

Pour utiliser ce coffre avec les étapes restantes, définissez le contexte du coffre avec [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext).

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Activer la sauvegarde pour une machine virtuelle Azure
Vous créez et utilisez des stratégies pour définir l’exécution d’un travail de sauvegarde et la durée de stockage des points de récupération. La stratégie de protection par défaut exécute un travail de sauvegarde chaque jour, et conserve les points de récupération pendant 30 jours. Vous pouvez utiliser ces valeurs de stratégie par défaut pour protéger rapidement votre machine virtuelle. Tout d’abord, définissez la stratégie par défaut avec [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy) :

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Pour activer la protection des sauvegardes pour une machine virtuelle, utilisez [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection). Spécifiez la stratégie à utiliser, puis le groupe de ressources et la machine virtuelle à protéger :

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Démarrer un travail de sauvegarde
Pour démarrer une sauvegarde sans attendre que la stratégie par défaut exécute le travail à l’heure planifiée, utilisez [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). Ce premier travail de sauvegarde crée un point de récupération complet. Chaque travail de sauvegarde après cette sauvegarde initiale crée des points de récupération incrémentielle. Les points de récupération incrémentielle constituent un mode de stockage rapide et efficace, car ils transfèrent uniquement les modifications apportées depuis la dernière sauvegarde.

Dans le jeu de commandes suivant, vous spécifiez un conteneur dans le coffre Recovery Services qui contient vos données de sauvegarde avec [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Chaque machine virtuelle à sauvegarder est traitée comme un élément. Pour démarrer un travail de sauvegarde, obtenez des informations sur l’élément de votre machine virtuelle avec [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Étant donné que ce premier travail de sauvegarde crée un point de récupération complet, le processus peut prendre jusqu’à 20 minutes.


## <a name="monitor-the-backup-job"></a>Surveiller le travail de sauvegarde
Pour surveiller l’état des travaux de sauvegarde, utilisez [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob) :

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

La sortie est similaire à l’exemple suivant, qui montre que le travail de sauvegarde est **En cours** :

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Lorsque l’*état* des rapports des travaux de sauvegarde correspond à *Terminé*, votre machine virtuelle est protégée par Recovery Services et présente un point de récupération complet stocké.


## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Lorsqu’elle n’est plus nécessaire, vous pouvez désactiver la protection sur la machine virtuelle, supprimer les points de restauration et le coffre Recovery Services, puis supprimer le groupe de ressources et les ressources de machine virtuelle associées. Si vous avez utilisé une machine virtuelle existante, vous pouvez ignorer la cmdlet finale [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour laisser en place le groupe de ressources et la machine virtuelle.

Si vous vous apprêtez à passer à un didacticiel de sauvegarde expliquant comment restaurer des données pour votre machine virtuelle, ignorez les étapes de cette section et accédez à [Étapes suivantes](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé un coffre Recovery Services, activé la protection sur une machine virtuelle et créé le point de récupération initial. Pour en savoir plus sur la sauvegarde Azure et Recovery Services, passez aux didacticiels.

> [!div class="nextstepaction"]
> [Sauvegarder plusieurs machines virtuelles Azure](./tutorial-backup-vm-at-scale.md)
