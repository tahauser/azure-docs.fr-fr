---
title: "Déploiement et gestion d’une sauvegarde pour les machines virtuelles Azure à l’aide de PowerShell | Microsoft Docs"
description: "Découvrez comment déployer et gérer Sauvegarde Azure à l’aide de PowerShell."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 2e24b1d9-4375-4049-a28d-e3bc01152f32
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/9/2017
ms.author: markgal;trinadhk;jimpark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88ca71f83696c5865405799cddc3645778261408
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="use-azurermbackup-cmdlets-to-back-up-virtual-machines"></a>Utilisation des applets de commande AzureRM.Backup pour sauvegarder des machines virtuelles
> [!div class="op_single_selector"]
> * [Gestionnaire de ressources](backup-azure-vms-automation.md)
> * [Classique](backup-azure-vms-classic-automation.md)
>
>

Cet article vous montre comment utiliser Azure PowerShell pour la sauvegarde et la restauration des machines virtuelles Azure. Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : Resource Manager et classique. Cet article traite de l’utilisation du modèle de déploiement classique pour sauvegarder des données dans un coffre de sauvegarde. Si vous n’avez pas créé de coffre de sauvegarde dans votre abonnement, consultez la version du Gestionnaire des ressources de cet article, [Utilisez les applets de commande AzureRM.RecoveryServices.Backup pour sauvegarder des machines virtuelles](backup-azure-vms-automation.md). Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

> [!IMPORTANT]
> Vous pouvez désormais mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Pour en savoir plus, consultez l’article [Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft vous recommande de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services.<br/> À compter du 30 novembre 2017, vous ne pouvez pas vous servir de PowerShell pour créer des coffres de sauvegarde.<br/> **D’ici au 30 novembre 2017** :
>- tous les coffres de sauvegarde restants seront automatiquement mis à niveau vers des coffres Recovery Services.
>- Vous ne pourrez plus accéder à vos données de sauvegarde depuis le portail Classic. Au lieu de cela, vous devrez utiliser le portail Azure pour accéder à ces données au sein de coffres Recovery Services.
>

## <a name="concepts"></a>Concepts
Cet article fournit des informations spécifiques aux applets de commande PowerShell utilisées pour sauvegarder des machines virtuelles. Pour obtenir des informations sur la protection des machines virtuelles Azure, consultez [Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure](backup-azure-vms-introduction.md).

> [!NOTE]
> Avant de commencer, lisez les [conditions préalables](backup-azure-vms-prepare.md) nécessaires pour utiliser Sauvegarde Azure et les [limitations](backup-azure-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm) de la solution actuelle de sauvegarde de machines virtuelles.
>
>

Pour pouvoir utiliser efficacement PowerShell, il est nécessaire de comprendre la hiérarchie d’objets et par où commencer.

![Hiérarchie des objets](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

Les deux processus les plus importants permettent la protection des machines virtuelles et la restauration des données à partir d’un point de restauration. L’objectif de cet article est de vous apprendre à utiliser les applets de commande PowerShell pour permettre ces deux scénarios.

## <a name="setup-and-registration"></a>Installation et inscription
Pour commencer :

1. [Téléchargez la dernière version de PowerShell](https://github.com/Azure/azure-powershell/releases) (version minimale requise : 1.0.0)
2. Rechercher les applets de commande PowerShell Azure Backup disponibles en tapant la commande suivante :

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

Les tâches de configuration et d’inscription ci-après peuvent être automatisées avec PowerShell :

* Créer un coffre de sauvegarde
* Inscription des machines virtuelles auprès du service Sauvegarde Azure

### <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde
> [!WARNING]
> Pour les clients utilisant Sauvegarde Azure pour la première fois, vous devez enregistrer le fournisseur Sauvegarde Azure à utiliser avec votre abonnement. Pour ce faire, exécutez la commande suivante : Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
>
>

Vous pouvez créer un coffre de sauvegarde en utilisant l’applet de commande **New-AzureRmBackupVault** . Le coffre de sauvegarde constituant une ressource ARM, vous devez le placer dans un groupe de ressources. Dans une console Azure PowerShell avec élévation de privilèges, exécutez les commandes suivantes :

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Vous pouvez obtenir la liste de tous les coffres de sauvegarde d’un abonnement donné à l’aide de l’applet de commande **Get-AzureRmBackupVault** .

> [!NOTE]
> Il est pratique de stocker l’objet du coffre de sauvegarde dans une variable. L’objet coffre est nécessaire comme entrée de nombreuses applets de commande Azure Backup.
>
>

### <a name="registering-the-vms"></a>Enregistrement des machines virtuelles
La première étape de la configuration de la sauvegarde avec Sauvegarde Azure consiste à enregistrer votre ordinateur ou machine virtuelle avec un coffre de sauvegarde Azure. L’applet de commande **Register-AzureRmBackupContainer** collecte les informations d’entrée d’une machine virtuelle IaaS Azure et les enregistre dans le coffre spécifié. L’opération d’enregistrement associe la machine virtuelle Azure avec le coffre de sauvegarde et effectue le suivi de la machine virtuelle tout au long du cycle de vie de la sauvegarde.

L’enregistrement de votre machine virtuelle auprès du service Sauvegarde Azure crée un objet conteneur de niveau supérieur. Un conteneur contient généralement plusieurs éléments qui peuvent être sauvegardés, mais dans le cas de machines virtuelles, il n’y a qu’un élément de sauvegarde pour le conteneur.

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## <a name="backup-azure-vms"></a>Sauvegarde des machines virtuelles Azure
### <a name="create-a-protection-policy"></a>Création d’une stratégie de protection
Il n’est pas obligatoire de créer une nouvelle stratégie de protection pour démarrer la sauvegarde de vos machines virtuelles. Le coffre est fourni avec une stratégie par défaut qui peut être utilisée pour activer la protection, puis modifiée ultérieurement avec les bonnes informations. Vous pouvez obtenir la liste des stratégies disponibles dans le coffre à l’aide de l’applet de commande **Get-AzureRmBackupProtectionPolicy** :

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [!NOTE]
> Le fuseau horaire du champ BackupTime dans PowerShell est UTC. Toutefois, lorsque l'heure de sauvegarde s’affiche dans le portail Azure, le fuseau horaire est aligné sur votre système local, tout comme le décalage UTC.
>
>

Une stratégie de sauvegarde est associée à au moins une stratégie de rétention. La stratégie de rétention définit la durée de conservation d’un point de restauration avec Sauvegarde Azure. L’applet de commande **New-AzureRmBackupRetentionPolicy** crée des objets PowerShell qui contiennent des informations sur la stratégie de rétention. Ces objets de stratégie de rétention sont utilisés comme entrées dans l’applet de commande *New-AzureRmBackupProtectionPolicy* ou directement dans le cas de l’applet de commande *Enable-AzureRmBackupProtection*.

Une stratégie de sauvegarde définit quand et à quelle fréquence la sauvegarde d’un élément doit être effectuée. L’applet de commande **New-AzureRmBackupProtectionPolicy** crée un objet PowerShell contenant des informations sur la stratégie de sauvegarde. La stratégie de sauvegarde est utilisée comme entrée dans l’applet de commande *Enable-AzureRmBackupProtection* .

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### <a name="enable-protection"></a>Activer la protection
L’activation de la protection implique deux objets : l’élément et la stratégie, et les deux doivent appartenir au même coffre. Une fois que la stratégie a été associée à l’élément, le flux de travail de la sauvegarde se lancera à l’heure planifiée.

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### <a name="initial-backup"></a>Sauvegarde initiale
La planification de sauvegarde se charge d’effectuer la copie initiale complète de l’élément et la copie incrémentielle pour les sauvegardes suivantes. Cependant, si vous voulez forcer l’exécution de la sauvegarde initiale à un moment déterminé, voire immédiatement, utilisez l’applet de commande **Backup-AzureRmBackupItem** :

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [!NOTE]
> Le fuseau horaire des champs StartTime et EndTime affichés dans PowerShell est UTC. Toutefois, lorsque des informations similaires s'affichent dans le portail Azure, le fuseau horaire est aligné sur l'horloge de votre système local.
>
>

### <a name="monitoring-a-backup-job"></a>Surveillance d’une tâche de sauvegarde
La plupart des opérations longues dans Sauvegarde Azure sont reproduites en tant que tâche. Cela permet de suivre facilement la progression sans avoir à garder le portail Azure ouvert en permanence.

Pour connaître l’état récent d’une tâche en cours de traitement, utilisez l’applet de commande **Get-AzureRmBackupJob** .

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Au lieu d’interroger ces tâches pour connaître leur état d’avancement, ce qui implique du code supplémentaire inutile, il est plus simple d’utiliser l’applet de commande **Wait-AzureRmBackupJob** . Lorsqu’elle est utilisée dans un script, l’applet de commande suspend l’exécution jusqu’à la fin de la tâche ou jusqu’à ce que la valeur spécifiée pour l’expiration du délai soit atteinte.

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## <a name="restore-an-azure-vm"></a>Restauration d’une machine virtuelle Azure
Pour restaurer les données de sauvegarde, vous devez identifier l’élément sauvegardé et le point de restauration contenant les données ponctuelles. Ces informations sont fournies à l’applet de commande Restore-AzureRmBackupItem pour lancer une restauration des données du coffre vers le compte du client.

### <a name="select-the-vm"></a>Sélection de la machine virtuelle
Pour obtenir l’objet PowerShell permettant d’identifier l’élément à restaurer, vous devez commencer au niveau du conteneur dans le coffre et descendre dans la hiérarchie d’objets. Pour sélectionner le conteneur qui représente la machine virtuelle, utilisez l’applet de commande **Get-AzureRmBackupContainer** et dirigez-la vers l’applet de commande **Get-AzureRmBackupItem**.

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### <a name="choose-a-recovery-point"></a>Choisir un point de récupération
Vous pouvez maintenant répertorier tous les points de restauration pour l’élément de sauvegarde via l’applet de commande **Get-AzureRmBackupRecoveryPoint** et choisir le point de récupération à restaurer. En général, les utilisateurs choisissent le point *AppConsistent* le plus récent dans la liste.

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

La variable ```$rp``` est un tableau de points de récupération pour l’élément de sauvegarde sélectionné et triés par ordre chronologique inverse, le dernier point de récupération étant fixé sur l’index 0. Utilisez l'indexation de tableau PowerShell standard pour sélectionner le point de récupération. Par exemple : ```$rp[0]``` sélectionnera le point de récupération le plus récent.

### <a name="restoring-disks"></a>Restauration de disques
Il existe une différence essentielle entre les opérations de restauration effectuées par le biais du portail Azure et celles effectuées via Azure PowerShell. Avec PowerShell, l’opération de restauration se limite à la restauration des disques et informations de configuration à partir d’un point de restauration. Il n’y a pas de création de machine virtuelle.

> [!WARNING]
> L’applet de commande Restore-AzureRmBackupItem ne crée pas une machine virtuelle. Elle restaure uniquement les disques dans le compte de stockage spécifié. Vous ne constaterez pas le même comportement avec le portail Azure.
>
>

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Vous pouvez obtenir les détails de l’opération de restauration à l’aide de l’applet de commande **Get-AzureRmBackupJobDetails** une fois la tâche de restauration terminée. La propriété *ErrorDetails* contient les informations nécessaires pour régénérer la machine virtuelle.

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### <a name="build-the-vm"></a>Création de la machine virtuelle
La création de la machine virtuelle à partir de disques restaurés peut être effectuée à l’aide d’applets de commande Azure Service Management PowerShell plus anciennes, des nouveaux modèles Azure Resource Manager ou même à l’aide du portail Azure. Dans un exemple rapide, nous allons montrer comment y parvenir via les applets de commande Azure Service Management.

```
$properties  = $details.Properties

$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$blobName = $properties["Config Blob Name"]

$keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
$storageAccountKey = $keys.Primary
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


$destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
$pvr = $obj.PersistentVMRole
$os = $pvr.OSVirtualHardDisk
$dds = $pvr.DataVirtualHardDisks
$osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
$vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

if (!($dds -eq $null))
{
    foreach($d in $dds.DataVirtualHardDisk)
    {
        $lun = 0
        if(!($d.Lun -eq $null))
        {
            $lun = $d.Lun
        }
        $name = "panbhadataDisk" + $lun
        Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
        $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
    }
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Pour plus d’informations sur la création d’une machine virtuelle à partir des disques restaurés, découvrez les applets de commande suivantes :

* [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
* [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
* [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## <a name="code-samples"></a>Exemples de code
### <a name="1-get-the-completion-status-of-job-sub-tasks"></a>1. Obtenir l'état d'achèvement des sous-tâches de travail
Pour suivre l’état d’achèvement de sous-tâches individuelles, vous pouvez utiliser l’applet de commande **Get-AzureRmBackupJobDetails** :

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### <a name="2-create-a-dailyweekly-report-of-backup-jobs"></a>2. Création d’un rapport quotidien/hebdomadaire des travaux de sauvegarde
En général, les administrateurs souhaitent connaître les travaux de sauvegarde exécutés au cours des dernières 24 heures ainsi que l'état de ces travaux de sauvegarde. En outre, la quantité de données transférées offre aux administrateurs une manière d'évaluer leur utilisation mensuelle de données. Le script ci-dessous extrait les données brutes à partir du service de sauvegarde Azure et affiche les informations sur la console PowerShell.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Si vous souhaitez ajouter des fonctionnalités graphiques à ce rapport, consultez le billet de blog TechNet sur [Fonctionnalités graphiques avec PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

## <a name="next-steps"></a>Étapes suivantes
Si vous préférez utiliser PowerShell pour gérer vos ressources Azure, consultez l’article de PowerShell pour la protection de Windows Server : [Déployer et gérer une sauvegarde pour Windows Server](backup-client-automation-classic.md). Il existe également un article PowerShell sur la gestion des sauvegardes DPM : [Déployer et gérer une sauvegarde pour DPM](backup-dpm-automation-classic.md). Ces deux articles ont une version concernant les déploiements avec le modèle Resource Manager et le modèle Classic.
