---
title: "Sauvegarde automatisée pour les machines virtuelles Azure SQL Server 2014 | Microsoft Docs"
description: "Décrit la fonctionnalité de sauvegarde automatisée pour les machines virtuelles exécutant SQL Server 2014 dans Azure. Cet article est spécifique aux machines virtuelles utilisant Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/05/2018
ms.author: jroth
ms.openlocfilehash: e7e4aab3a4c4f1ccca6868134ec0b829cb7af2f2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Sauvegarde automatisée pour les machines virtuelles SQL Server 2014 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

La sauvegarde automatisée configure automatiquement une [sauvegarde managée sur Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données nouvelles et existantes sur une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. Cela vous permet de configurer des sauvegardes régulières de base de données utilisant le stockage d’objets blob Azure durable. La sauvegarde automatisée dépend l’ [extension de l’agent IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>configuration requise
Pour utiliser la sauvegarde automatisée, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Édition/version de SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> La sauvegarde automatisée fonctionne avec SQL Server 2014. Si vous utilisez SQL Server 2016, vous pouvez utiliser la sauvegarde automatisée en version 2 pour sauvegarder vos bases de données. Pour plus d’informations, consultez [Sauvegarde automatisée en version 2 pour les machines virtuelles Azure SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

**Configuration de la base de données**:

- Les bases de données cibles doivent utiliser le modèle de récupération complète. Pour plus d’informations sur l’impact du modèle de récupération complète sur les sauvegardes, consultez [Sauvegarde en mode de récupération complète](https://technet.microsoft.com/library/ms190217.aspx).
- Les bases de données cibles doivent se trouver sur l’instance SQL Server par défaut. L’extension IaaS SQL Server ne prend pas en charge les instances nommées.

**Modèle de déploiement Azure** :

- Gestionnaire de ressources

**Azure PowerShell**:

- [Installez les dernières commandes Azure PowerShell](/powershell/azure/overview) si vous projetez de configurer la sauvegarde automatisée avec PowerShell.

> [!NOTE]
> La sauvegarde automatisée utilise l’extension de l’agent IaaS de SQL Server. Les images actuelles de la galerie de machines virtuelles SQL ajoutent cette extension par défaut. Pour plus d’informations, consultez [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)(Extension de l’agent IaaS SQL Server).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour une sauvegarde automatisée. Les étapes de la configuration varient selon que vous utilisez les commandes du portail Azure ou Azure Windows PowerShell.

| Paramètre | Plage (par défaut) | Description |
| --- | --- | --- |
| **Sauvegarde automatisée** | Activer/Désactiver (désactivé) | Active ou désactive la sauvegarde automatisée d’une machine virtuelle Azure exécutant SQL Server 2014 Standard ou Enterprise. |
| **Période de rétention** | 1 à 30 jours (30 jours) | Nombre de jours durant lesquels une sauvegarde est conservée. |
| **Compte de stockage** | Compte Azure Storage | Compte de stockage Azure à utiliser pour stocker les fichiers de sauvegarde automatisée dans le stockage d’objets blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La convention de dénomination des fichiers de sauvegarde inclut la date, l’heure et le nom de la machine. |
| **Chiffrement** | Activer/Désactiver (désactivé) | Active ou désactive le chiffrement. Quand le chiffrement est activé, les certificats utilisés pour restaurer la sauvegarde se trouvent dans le compte de stockage spécifié dans le même conteneur `automaticbackup` avec la même convention de dénomination. Si le mot de passe change, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat est conservé pour restaurer les sauvegardes antérieures. |
| **Mot de passe** | Texte du mot de passe | Mot de passe pour les clés de chiffrement. Il est uniquement requis si le chiffrement est activé. Pour restaurer une sauvegarde chiffrée, vous devez disposer du mot de passe correct et du certificat associé qui a été utilisé lorsque la sauvegarde a été effectuée. |

## <a name="configuration-in-the-portal"></a>Configuration dans le portail

Vous pouvez utiliser le portail Azure pour configurer la sauvegarde automatisée lors de l’approvisionnement ou pour des machines virtuelles SQL Server 2014 existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles

Utilisez le portail Azure pour configurer la sauvegarde automatisée quand vous créez une machine virtuelle SQL Server 2014 dans le modèle de déploiement Resource Manager.

Dans le panneau **Paramètres SQL Server**, sélectionnez **Sauvegarde automatisée**. La capture d’écran suivante du portail Azure illustre le panneau **Sauvegarde automatisée SQL** .

![Configuration d’une sauvegarde automatisée SQL dans le portail Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Pour plus de contexte, consultez la rubrique complète intitulée [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes

Pour les machines virtuelles SQL Server existantes, sélectionnez votre machine virtuelle SQL Server. Puis sélectionnez la section **Configuration de SQL Server** du panneau **Paramètres**.

![Sauvegarde automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Dans le panneau **Configuration de SQL Server**, cliquez sur le bouton **Modifier** dans la section de sauvegarde automatisée.

![Configurer la sauvegarde automatisée SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** au bas du panneau **Configuration de SQL Server** pour enregistrer vos modifications.

Si vous activez la sauvegarde automatisée pour la première fois, Azure configure l’agent IaaS de SQL Server en arrière-plan. Pendant ce temps, le portail Azure n’indiquera peut-être pas que la sauvegarde automatisée est configurée. Patientez quelques minutes jusqu’à ce que l’agent soit installé et configuré. Le portail Azure reflète alors les nouveaux paramètres.

> [!NOTE]
> Vous pouvez également configurer la sauvegarde automatisée à l’aide d’un modèle. Pour plus d’informations, consultez l’article [Azure quickstart template for Automated Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)(Modèle de démarrage rapide d’Azure pour la sauvegarde automatisée).

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Vous pouvez utiliser PowerShell pour configurer une sauvegarde automatisée. Avant de commencer, vous devez :

- [Télécharger et installer la version la plus récente d’Azure PowerShell](http://aka.ms/webpi-azps).
- Ouvrir Windows PowerShell et l’associer à votre compte. Pour cela, procédez selon les étapes de la section [Configurer votre abonnement](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) de la rubrique consacrée à l’approvisionnement.

### <a name="install-the-sql-iaas-extension"></a>Installer l’extension IaaS SQL
Si vous avez configuré une machine virtuelle SQL Server à partir du portail Azure, l’extension IaaS SQL Server devrait déjà être installée. Vous pouvez déterminer si cette extension est installée pour votre machine virtuelle en appelant la commande **Get-AzureRmVM** puis en examinant la propriété **Extensions**.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Si l’extension de l’agent IaaS SQL Server est installée, elle devrait s’afficher sous la forme « SqlIaaSAgent » ou « SQLIaaSExtension ». La propriété **ProvisioningState** de l’extension devrait également indiquer « Succeeded » (Réussie).

Si elle n’est pas installée ou n’a pas pu être configurée, vous pouvez l’installer avec la commande suivante. Outre le nom de la machine virtuelle et le groupe de ressources, vous devez également spécifier la région (**$region**) où se trouve votre machine virtuelle.

```powershell
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Si elle n’a pas encore été réalisée, l’installation de l’extension redémarre le service SQL Server.

### <a id="verifysettings"></a> Vérifier les paramètres actuels

Si vous avez activé la sauvegarde automatisée lors de la configuration, vous pouvez utiliser PowerShell pour vérifier votre configuration actuelle. Exécutez la commande **Get-AzureRmVMSqlServerExtension** et examinez la propriété **AutoBackupSettings** :

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

La sortie doit ressembler à ce qui suit :

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Si votre sortie montre que l’option **Enable** (Activer) est définie sur **False**, vous devez activer la sauvegarde automatisée. La bonne nouvelle est que vous activez et configurez la sauvegarde automatisée de la même façon. Pour en savoir plus, consultez la section suivante.

> [!NOTE] 
> Si vous vérifiez les paramètres immédiatement après une modification, les anciennes valeurs de configuration peuvent s’afficher. Patientez quelques minutes et revérifiez les paramètres pour vous assurer que vos modifications ont bien été appliquées.

### <a name="configure-automated-backup"></a>Configurer une sauvegarde automatisée
Vous pouvez utiliser PowerShell pour activer la sauvegarde automatisée ainsi que pour modifier sa configuration et son comportement à tout moment.

Tout d’abord, sélectionnez ou créez un compte de stockage pour les fichiers de sauvegarde. Le script suivant sélectionne un compte de stockage ou le crée s’il n’existe pas.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> La sauvegarde automatisée ne prend pas en charge le stockage des sauvegardes dans un stockage Premium, mais elle peut effectuer des sauvegardes à partir de disques de machines virtuelles qui utilisent le stockage Premium.

Utilisez ensuite la commande **New-AzureRmVMSqlServerAutoBackupConfig** pour activer et configurer les paramètres de sauvegarde automatisée afin de stocker les sauvegardes dans le compte de stockage Azure. Dans cet exemple, les sauvegardes sont définies pour être conservées pendant 10 jours. La seconde commande **Set-AzureRmVMSqlServerExtension** met à jour la machine virtuelle Azure spécifiée avec ces paramètres.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

L’installation et la configuration de l’agent IaaS de SQL Server peuvent prendre plusieurs minutes.

> [!NOTE]
> Il existe d’autres paramètres pour **New-AzureRmVMSqlServerAutoBackupConfig** qui s’appliquent uniquement à SQL Server 2016 et à la sauvegarde automatisée version 2. SQL Server 2014 ne prend pas en charge les paramètres suivants : **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** et **LogBackupFrequencyInMinutes**. Si vous essayez de configurer ces paramètres sur une machine virtuelle SQL Server 2014, aucune erreur n’apparaît, mais les paramètres ne sont pas appliqués. Si vous souhaitez utiliser ces paramètres sur une machine virtuelle SQL Server 2016, consultez [Sauvegarde automatisée version 2 pour SQL Server 2016 dans des machines virtuelles Azure](virtual-machines-windows-sql-automated-backup-v2.md).

Pour activer le chiffrement, modifiez le script précédent pour transmettre le paramètre **EnableEncryption** avec un mot de passe (chaîne sécurisée) pour le paramètre **CertificatePassword**. Le script suivant active les paramètres de sauvegarde automatisée dans l’exemple précédent et ajoute le chiffrement.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pour confirmer que vos paramètres ont été appliqués, [vérifiez la configuration de la sauvegarde automatisée](#verifysettings).

### <a name="disable-automated-backup"></a>Désactiver la sauvegarde automatisée

Pour désactiver la sauvegarde automatisée, exécutez le même script sans le paramètre **-Enable** pour la commande **New-AzureRmVMSqlServerAutoBackupConfig**. L’absence du paramètre **-Enable** indique à la commande de désactiver la fonctionnalité. À l’instar de l’installation, la désactivation de la sauvegarde automatisée peut prendre plusieurs minutes.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Exemple de script

Le script suivant fournit un ensemble de variables que vous pouvez personnaliser afin d’activer et de configurer la sauvegarde automatisée pour votre machine virtuelle. Dans votre cas, vous devrez peut-être personnaliser le script selon vos besoins. Par exemple, vous devrez apporter des modifications si vous souhaitez désactiver la sauvegarde des bases de données système ou activer le chiffrement.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Étapes suivantes

La sauvegarde automatisée configure une sauvegarde managée sur les machines virtuelles Azure. Il est donc important de [lire la documentation relative à la sauvegarde gérée](https://msdn.microsoft.com/library/dn449496.aspx) pour comprendre son comportement et ses implications.

Vous trouverez des conseils supplémentaires pour la sauvegarde et la restauration de SQL Server sur les machines virtuelles Azure dans la rubrique suivante : [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur les autres tâches d’automatisation disponibles, voir [Extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

