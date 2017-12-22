---
title: 'Sauvegarde Azure : Utiliser PowerShell pour sauvegarder des charges de travail DPM | Microsoft Docs'
description: "Découvrez comment déployer et gérer Azure Backup pour Data Protection Manager (DPM) à l’aide de PowerShell"
services: backup
documentationcenter: 
author: NKolli1
manager: shreeshd
editor: 
ms.assetid: e9bd223c-2398-4eb1-9bf3-50e08970fea7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: adigan;anuragm;trinadhk;markgal
ms.openlocfilehash: 9322037427c84f0b8a91cc76f5c0fed52167bc3c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Déployer et gérer une sauvegarde vers Azure pour des serveurs Data Protection Manager (DPM) à l’aide de PowerShell
Cet article décrit comment utiliser PowerShell pour configurer Azure Backup sur un serveur DPM, ainsi que pour gérer les sauvegardes et la récupération.

## <a name="setting-up-the-powershell-environment"></a>Configuration de l’environnement PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Avant de pouvoir utiliser PowerShell pour gérer les sauvegardes de Data Protection Manager vers Azure, vous devez disposer de l’environnement approprié dans PowerShell. Au début de la session PowerShell, veillez à exécuter la commande suivante pour importer les modules adéquats et avoir la possibilité de référencer correctement les applets de commande DPM :

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Installation et inscription
Pour commencer :

1. [Téléchargez la dernière version de PowerShell](https://github.com/Azure/azure-powershell/releases) (version minimale requise : 1.0.0)
2. Activez les applets de commande Azure Backup en passant en mode *AzureResourceManager* via l’applet de commande **Switch-AzureMode** :

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Les tâches de configuration et d’inscription ci-après peuvent être automatisées avec PowerShell :

* Créer un coffre Recovery Services
* Installation de l'agent Azure Backup
* Inscription auprès du service Sauvegarde Azure
* Paramètres de mise en réseau
* Paramètres de chiffrement

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
Les étapes suivantes vous montrent comment créer un coffre Recovery Services. Un coffre Recovery Services diffère d’un coffre de sauvegarde.

1. Si vous utilisez Sauvegarde Azure pour la première fois, vous devez recourir à l’applet de commande **Register-AzureRMResourceProvider** pour enregistrer le fournisseur Azure Recovery Service auprès de votre abonnement.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Le coffre Recovery Services constituant une ressource ARM, vous devez le placer dans un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créer un. Quand vous créez un groupe de ressources, spécifiez ses nom et emplacement.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Utilisez l’applet de commande **New-AzureRmRecoveryServicesVault** pour créer un coffre. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Spécifiez le type de redondance de stockage à utiliser : [Stockage localement redondant (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) ou [Stockage géo-redondant (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). L’exemple suivant montre que l’option -BackupStorageRedundancy pour testVault a la valeur GeoRedundant.

   > [!TIP]
   > De nombreuses applets de commande Azure Backup nécessitent l’objet coffre Recovery Services en tant qu’entrée. Pour cette raison, il est pratique de stocker l’objet coffre Backup Recovery Services dans une variable.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Afficher les coffres dans un abonnement
Utilisez **Get-AzureRmRecoveryServicesVault** pour afficher la liste de tous les coffres dans l’abonnement actuel. Vous pouvez utiliser cette commande pour vérifier qu’un coffre a été créé ou pour voir les coffres disponibles dans l’abonnement.

Exécutez la commande Get-AzureRmRecoveryServicesVault ; tous les coffres dans l’abonnement sont alors répertoriés.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Installation de l'agent Azure Backup sur un serveur DPM
Avant d’installer l'agent Azure Backup, vous devez avoir téléchargé le programme d’installation sur le serveur Windows. Vous pouvez obtenir la dernière version du programme d’installation à partir du [Centre de téléchargement Microsoft](http://aka.ms/azurebackup_agent) ou de la page Tableau de bord du coffre Recovery Services. Enregistrez le programme d’installation dans un emplacement auquel vous pouvez accéder facilement, par exemple *C:\Téléchargements\*.

Pour installer l’agent, exécutez la commande ci-après dans une console PowerShell avec élévation de privilèges **sur le serveur DPM**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Cette opération installe l’agent avec les options par défaut. L’installation s’effectue en arrière-plan et prend quelques minutes. Si vous ne spécifiez pas l’option */nu* , la fenêtre **Windows Update** s’ouvre à la fin de l’installation pour rechercher des mises à jour.

L’agent apparaît dans la liste des programmes installés. Pour afficher la liste des programmes installés, cliquez sur **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités**.

![Agent installé](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Options d’installation
Pour afficher toutes les options disponibles via la ligne de commande, utilisez la commande suivante :

```
PS C:\> MARSAgentInstaller.exe /?
```

Les options disponibles incluent :

| Option | Détails | Default |
| --- | --- | --- |
| /q |Installation silencieuse |- |
| /p:"emplacement" |Chemin d’accès du dossier d’installation de l’agent de Sauvegarde Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"emplacement" |Chemin d’accès du dossier de cache de l’agent de Sauvegarde Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Abonnement à Microsoft Update |- |
| /nu |Ne pas vérifier les mises à jour une fois l’installation terminée |- |
| /d |Désinstalle l’agent Microsoft Azure Recovery Services |- |
| /ph |Adresse de l’hôte proxy |- |
| /po |Numéro de port de l’hôte proxy |- |
| /pu |Nom d’utilisateur de l’hôte proxy |- |
| /pw |Mot de passe du proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Inscription de DPM dans un coffre Recovery Services
Une fois que vous avez créé un coffre Recovery Services, téléchargez le dernier agent et les informations d’identification de coffre et stockez-les dans un emplacement pratique comme C:\Téléchargements.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Sur le serveur DPM, exécutez l’applet de commande [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) pour inscrire l’ordinateur auprès du coffre.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Paramètres de la configuration initiale
Une fois que le serveur DPM est inscrit auprès du coffre Recovery Services, il démarre avec les paramètres d’abonnement par défaut. Ces paramètres d’abonnement incluent la mise en réseau, le chiffrement et la zone intermédiaire. Pour modifier les paramètres d’abonnement, vous devez d’abord obtenir les paramètres (par défaut) existants à l’aide de l’applet de commande [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) :

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Toutes les modifications sont apportées à cet objet PowerShell local ```$setting```. L’objet complet est ensuite validé vers DPM et la Sauvegarde Azure à des fins d’enregistrement à l’aide de l’applet de commande [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). Vous devez utiliser l’indicateur ```–Commit``` pour vous assurer que les modifications sont conservées. Les paramètres ne sont pas appliqués ni utilisés par Sauvegarde Azure tant qu’ils ne sont pas validés.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Mise en réseau
Si la connectivité entre l’ordinateur DPM et le service Azure Backup sur Internet est établie via un serveur proxy, les paramètres du serveur proxy doivent être fournis pour que les sauvegardes soient réussies. Pour cela, utilisez les paramètres ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` et ```ProxyPassword``` avec l’applet de commande [DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). Dans cet exemple, il n’y a aucun serveur proxy. Nous effaçons donc explicitement toutes informations concernant un proxy.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

L’utilisation de la bande passante peut également être contrôlée avec les options ```-WorkHourBandwidth``` et ```-NonWorkHourBandwidth```, certains jours de la semaine. Dans cet exemple, nous ne fixons aucune limitation.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Configuration de la zone intermédiaire
L’agent de sauvegarde Azure en cours d’exécution sur le serveur DPM a besoin d’un stockage temporaire pour les données restaurées à partir du cloud (zone de transit locale). Configurez la zone intermédiaire à l’aide de l’applet de commande [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) et du paramètre ```-StagingAreaPath```.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

Dans l’exemple ci-dessus, la zone intermédiaire est définie sur *C:\StagingArea* dans l’objet PowerShell ```$setting```. Assurez-vous que le dossier spécifié existe déjà, sinon la validation finale des paramètres d’abonnement échouera.

### <a name="encryption-settings"></a>Paramètres de chiffrement
Les données sauvegardées envoyées à Sauvegarde Azure sont chiffrées pour garantir leur confidentialité. Le mot de passe du chiffrement est le « mot de passe » permettant de déchiffrer les données lors de la restauration. Il est important de conserver ces informations en lieu sûr après les avoir définies.

Dans l’exemple ci-dessous, la première commande convertit la chaîne de mot de passe ```passphrase123456789``` en une chaîne sécurisée et assigne la chaîne sécurisée à la variable nommée ```$Passphrase```. La deuxième commande définit la chaîne sécurise dans ```$Passphrase``` en tant que mot de passe pour le chiffrement des sauvegardes.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Conservez les informations de phrase secrète en lieu sûr après les avoir définies. Vous ne pourrez pas restaurer les données à partir d’Azure sans ce mot de passe.
>
>

À ce stade, vous devez avoir apporté toutes les modifications requises à l’objet ```$setting``` . Pensez à valider les modifications.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Protection des données dans Sauvegarde Azure
Dans cette section, vous allez ajouter un serveur de production à DPM, puis protéger les données sur le stockage DPM local, et enfin dans Azure Backup. Dans les exemples, nous vous montrerons comment sauvegarder des fichiers et des dossiers. La logique peut facilement être étendue pour sauvegarder toute source de données DPM prise en charge. Toutes vos sauvegardes DPM sont régies par un groupe de protection constitué de quatre parties :

1. **Membres du groupe** est une liste de tous les objets pouvant être protégés (également appelés *Sources de données* dans DPM) que vous souhaitez protéger dans le même groupe de protection. Par exemple, vous pouvez protéger les machines virtuelles de production dans un groupe de production et les bases de données SQL Server dans un autre groupe de protection, car leurs exigences de sauvegarde peuvent être différentes. Avant de pouvoir sauvegarder une source de données sur un serveur de production, vous devez vérifier que l'agent DPM est installé sur le serveur de production et géré par DPM. Suivez les étapes d’ [installation de l'agent DPM](https://technet.microsoft.com/library/bb870935.aspx) et de liaison de celui-ci au serveur DPM approprié.
2. **Méthode de protection des données** spécifie les emplacements de sauvegarde cibles (bande, disque ou cloud). Dans notre exemple, nous protégerons les données sur le disque local et dans le cloud.
3. Une **planification de sauvegarde** qui spécifie le moment où les sauvegardes doivent être effectuées et la fréquence de synchronisation des données entre le serveur DPM et le serveur de production.
4. Une **planification de rétention** qui spécifie la durée de rétention des points de récupération dans Azure.

### <a name="creating-a-protection-group"></a>Création d’un groupe de protection
Commençons par créer un groupe de protection à l’aide de l’applet de commande [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) .

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Celle-ci crée un groupe de protection nommé *ProtectGroup01*. Un groupe de protection existant peut également être modifié ultérieurement pour ajouter la sauvegarde vers le cloud Azure. Toutefois, pour apporter des modifications au groupe de protection (nouveau ou existant), nous avons besoin d’obtenir un objet *modifiable* à l'aide de l’applet de commande [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) .

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Ajout de membres au groupe de protection
Chaque agent DPM connaît la liste des sources de données sur le serveur sur lequel il est installé. Pour ajouter une source de données au groupe de Protection, l'agent DPM doit renvoyer tout d'abord une liste des sources de données au serveur DPM. Une ou plusieurs sources de données sont sélectionnées, puis ajoutées au groupe de protection. Les étapes PowerShell requises sont les suivantes :

1. Extrayez une liste de tous les serveurs gérés par DPM via l'agent DPM.
2. Choisissez un serveur spécifique.
3. Extrayez une liste de toutes les sources de données sur le serveur.
4. Choisissez une ou plusieurs sources de données et ajoutez-les au groupe de protection.

Vous pouvez obtenir la liste des serveurs sur lesquels l’agent DPM est installé et géré par le serveur DPM à l'aide de l’applet de commande [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) . Dans cet exemple, nous allons filtrer et configurer uniquement PS avec une sauvegarde nommée *productionserver01* .

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Extrayez à présent la liste des sources de données sur ```$server``` à l’aide de l’applet de commande [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605). Dans cet exemple, nous filtrons le volume *D:\* que vous souhaitez configurer pour une sauvegarde. Cette source de données est ensuite ajoutée au groupe de protection à l’aide de l’applet de commande [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732). N’oubliez pas d’utiliser l’objet de groupe de protection *modifiable* ```$MPG``` pour effectuer les ajouts.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Répétez cette étape autant de fois que nécessaire, jusqu'à ce que vous ayez ajouté toutes les sources de données choisies au groupe de protection. Vous pouvez également commencer avec une seule source de données, puis terminer le flux de travail de création du groupe de protection, avant d’ajouter ultérieurement d’autres sources de données au groupe de protection.

### <a name="selecting-the-data-protection-method"></a>Sélection de la méthode de protection des données
Une fois que les sources de données ont été ajoutées au groupe de protection, l'étape suivante consiste à spécifier la méthode de protection à l'aide de l’applet de commande [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) . Dans cet exemple, le groupe de protection est configuré pour une sauvegarde sur le disque local et dans le cloud. Vous devez également spécifier la source de données que vous souhaitez protéger sur le cloud à l’aide de l’applet de commande [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) avec l’indicateur Online.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Définition de la plage de rétention
Définissez la rétention pour les points de sauvegarde à l'aide de l’applet de commande [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) . Bien que cela puisse sembler étrange de définir la rétention avant la planification de sauvegarde, l’utilisation de l’applet de commande ```Set-DPMPolicyObjective``` définit automatiquement une planification de sauvegarde par défaut qui peut ensuite être modifiée. Il est toujours possible de définir la planification de sauvegarde avant la stratégie de rétention.

Dans l'exemple ci-dessous, l'applet de commande définit les paramètres de rétention pour les sauvegardes sur disque. Cela permet de conserver les sauvegardes pendant 10 jours, et de synchroniser les données toutes les 6 heures entre le serveur de production et le serveur DPM. ```SynchronizationFrequencyMinutes``` ne définit pas la fréquence à laquelle un point de sauvegarde est créé, mais la fréquence de copie des données sur le serveur DPM.  Ce paramètre évite que les sauvegardes soient trop volumineuses.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Pour les sauvegardes vers Azure (qui sont désignées par « sauvegardes en ligne » dans DPM), les plages de rétention peuvent être configurées pour [une rétention à long terme à l’aide d’un schéma GFS (Grandfather-Father-Son)](backup-azure-backup-cloud-as-tape.md). Autrement dit, vous pouvez définir une stratégie de rétention combinée incluant des stratégies de rétention quotidiennes, hebdomadaires, mensuelles et annuelles. Dans cet exemple, nous créons un tableau qui représente le schéma de rétention complexe souhaité, puis configurons la plage de rétention à l'aide de l’applet de commande [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) .

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Définition de la planification de sauvegarde
DPM définit automatiquement la planification de sauvegarde par défaut si vous spécifiez l'objectif de protection à l'aide de l’applet de commande ```Set-DPMPolicyObjective``` . Pour modifier les planifications par défaut, utilisez la cmdlet [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) suivie de la cmdlet [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723).

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

Dans l’exemple ci-dessus, ```$onlineSch``` est un tableau constitué de quatre éléments qui contient la planification de protection en ligne existante pour le groupe de protection dans le schéma GFS :

1. ```$onlineSch[0]``` contient la planification quotidienne
2. ```$onlineSch[1]``` contient la planification hebdomadaire
3. ```$onlineSch[2]``` contient la planification mensuelle
4. ```$onlineSch[3]``` contient la planification annuelle

Par conséquent, si vous devez modifier la planification hebdomadaire, vous devez faire référence à ```$onlineSch[1]```.

### <a name="initial-backup"></a>Sauvegarde initiale
Lorsque vous sauvegardez la source de données pour la première fois, DPM doit créer un réplica initial qui crée une copie intégrale de la source de données à protéger sur le volume du réplica DPM. Cette activité peut être planifiée pour une heure spécifique, ou peut être déclenchée manuellement à l’aide de l’applet de commande [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) avec le paramètre ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Modification de la taille de réplica DPM et volume du point de récupération
Vous pouvez également modifier la taille du volume de réplica DPM, ainsi que du volume de clichés instantanés à l’aide de l’applet de commande [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) comme dans l’exemple suivant : Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Validation des modifications dans le groupe de protection
Pour terminer, les modifications doivent être validées avant que DPM puisse effectuer la sauvegarde conformément à la nouvelle configuration du groupe de protection. Vous pouvez le faire à l’aide de l’applet de commande [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) .

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>Affichage des points de sauvegarde
Vous pouvez utiliser l’applet de commande [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) pour obtenir la liste de tous les points de récupération d’une source de données. Dans cet exemple, nous allons :

* extraire tous les groupes de protection sur le serveur DPM et les stocker dans un tableau ```$PG```
* obtenir les sources de données correspondant à ```$PG[0]```
* obtenir tous les points de récupération pour une source de données.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Restaurer des données protégées sur Azure
La restauration des données est une combinaison d'un objet ```RecoverableItem``` et d’un objet ```RecoveryOption```. Dans la section précédente, nous avions une liste des points de sauvegarde pour une source de données.

Dans l'exemple ci-dessous, nous démontrons comment restaurer une machine virtuelle Hyper-V à partir d'Azure Backup en combinant les points de sauvegarde avec la cible pour la récupération. Cet exemple inclut :

* Création d’une option de récupération à l’aide de l’applet de commande [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592).
* Extraction du tableau de points de sauvegarde à l'aide de l’applet de commande ```Get-DPMRecoveryPoint``` .
* Choix d’un point de sauvegarde à partir duquel effectuer la restauration.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Les commandes peuvent facilement être étendues à n'importe quel type de source de données.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la sauvegarde DPM dans Azure, consultez la rubrique [Présentation des sauvegardes DPM](backup-azure-dpm-introduction.md)
