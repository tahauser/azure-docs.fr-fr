---
title: "Sauvegarder des machines virtuelles Azure dans Azure à grande échelle | Microsoft Docs"
description: "Ce didacticiel décrit en détail la sauvegarde de plusieurs machines virtuelles Azure dans un coffre Recovery Services."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "sauvegarde de machine virtuelle ; sauvegarder une machine virtuelle ; sauvegarde et récupération d’urgence"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 01609c00c6f0585eff4843932b9eb7a090a59c19
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Sauvegarder des machines virtuelles Azure dans Azure à grande échelle

Ce didacticiel décrit en détail comment sauvegarder des machines virtuelles Azure dans un coffre Recovery Services. L’essentiel du travail requis pour la sauvegarde de machines virtuelles repose sur la préparation. Avant de sauvegarder ou de protéger une machine virtuelle, vous devez remplir les [conditions préalables](backup-azure-arm-vms-prepare.md) pour préparer votre environnement à la protection de vos machines virtuelles. 

> [!IMPORTANT]
> Ce didacticiel suppose que vous avez déjà créé un groupe de ressources et une machine virtuelle Azure.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Un [coffre Recovery Services](backup-azure-recovery-services-vault-overview.md) est un conteneur dans lequel sont stockés les points de récupération pour les éléments en cours de sauvegarde. Un coffre Recovery Services est une ressource Azure qui peut être déployée et gérée en tant que partie d’un groupe de ressources Azure. Dans ce didacticiel, vous allez créer un coffre Recovery Services dans le même groupe de ressources que la machine virtuelle protégée.


Si vous utilisez Sauvegarde Microsoft Azure pour la première fois, vous devez inscrire le fournisseur du service de récupération Azure avec votre abonnement. Si vous avez déjà inscrit le fournisseur avec votre abonnement, passez à l’étape suivante.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Créez un coffre Recovery Services avec la commande **New-AzureRmRecoveryServicesVault**. Veillez à spécifier le nom et l’emplacement du groupe de ressources utilisé lors de la configuration de la machine virtuelle que vous souhaitez sauvegarder. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

De nombreuses applets de commande Azure Backup nécessitent l’objet coffre Recovery Services en tant qu’entrée. Pour cette raison, il est pratique de stocker l’objet coffre Backup Recovery Services dans une variable. Utilisez ensuite la commande **Set-AzureRmRecoveryServicesBackupProperties** pour définir l’option **-BackupStorageRedundancy** sur [Stockage géo-redondant (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Sauvegarde des machines virtuelles Azure

Avant de pouvoir exécuter la sauvegarde initiale, vous devez définir le contexte du coffre. Le contexte du coffre spécifie le type de données protégées dans le coffre. Quand vous créez un coffre Recovery Services, il est fourni avec des stratégies de protection et de conservation par défaut. La stratégie de protection par défaut déclenche une tâche de sauvegarde chaque jour à une heure spécifiée. La stratégie de conservation par défaut conserve le point de récupération quotidien pendant 30 jours. Pour ce didacticiel, acceptez la stratégie par défaut. 

Pour définir le contexte du coffre, utilisez la commande **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**. Une fois le contexte du coffre défini, il s’applique à toutes les applets de commande suivantes. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Pour déclencher la tâche de sauvegarde, utilisez la commande **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)**. Le travail de sauvegarde crée un point de récupération. S’il s’agit de la sauvegarde initiale, le point de récupération est une sauvegarde complète. Les sauvegardes suivantes créent une copie incrémentielle.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Supprimer le coffre Recovery Services

Pour supprimer un coffre Recovery Services, vous devez supprimer tous les points de récupération stockés dans le coffre, puis annuler l’inscription du coffre. Les commandes suivantes détaillent ces étapes. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Résolution des erreurs
Si vous rencontrez des problèmes pendant la sauvegarde de votre machine virtuelle, voir [l’article sur le dépannage de la sauvegarde de machines virtuelles Azure](backup-azure-vms-troubleshoot.md) pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez protégé vos machines virtuelles, consultez les articles suivants pour découvrir les tâches de gestion et la manière de restaurer des machines virtuelles à partir d’un point de récupération.

* Pour modifier la stratégie de sauvegarde, voir [Utiliser les applets de commande AzureRM.RecoveryServices.Backup pour sauvegarder des machines virtuelles](backup-azure-vms-automation.md#create-a-protection-policy).
* [Gestion et surveillance de vos machines virtuelles](backup-azure-manage-vms.md)
* [Restauration des machines virtuelles](backup-azure-arm-restore-vms.md)
