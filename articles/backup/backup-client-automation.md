---
title: Utiliser PowerShell pour sauvegarder Windows Server dans Azure | Microsoft Docs
description: Découvrez comment déployer et gérer Sauvegarde Azure à l’aide de PowerShell
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: 65218095-2996-44d9-917b-8c84fc9ac415
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: saurse;markgal;jimpark;nkolli;trinadhk
ms.openlocfilehash: cabf40367a6bd8401cae3eade4b832702e5acf31
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Déployer et gérer une sauvegarde vers Azure pour un serveur/client Windows à l’aide de PowerShell
Cet article décrit comment utiliser PowerShell pour configurer Sauvegarde Azure sur un serveur Windows Server ou sur un client Windows, ainsi que pour gérer les sauvegardes et la récupération.

## <a name="install-azure-powershell"></a>Installation d'Azure PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Cet article se concentre sur les applets de commande PowerShell d’Azure Resource Manager (ARM) et MS Sauvegarde en ligne qui vous permettent d’utiliser un coffre Recovery Services dans un groupe de ressources.

Azure PowerShell 1.0 a été publié en octobre 2015. Cette version, qui fait suite à la release 0.9.8, a introduit d’importantes modifications, en particulier dans le modèle de dénomination des applets de commande. Les applets de commande version 1.0 suivent le modèle d’affectation de noms {verbe}-AzureRm{nom} ; les noms 0.9.8 n’incluent pas **Rm** (par exemple, New-AzureRmResourceGroup au lieu de New-AzureResourceGroup). Lorsque vous utilisez Azure PowerShell 0.9.8, vous devez d’abord activer le mode Resource Manager en exécutant la commande **Switch-AzureMode AzureResourceManager** . Cette commande n’est pas nécessaire dans les versions 1.0 ou ultérieures.

Si vous souhaitez utiliser dans l’environnement 1.0 (ou ultérieur) des scripts écrits pour l’environnement 0.9.8, veillez à les mettre à jour et à les tester dans un environnement de préproduction avant de les utiliser en production, afin d’éviter tout résultat inattendu.

[Téléchargez la dernière version de PowerShell](https://github.com/Azure/azure-powershell/releases) (version minimale requise : 1.0.0).

[!INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
Les étapes suivantes vous montrent comment créer un coffre Recovery Services. Un coffre Recovery Services diffère d’un coffre de sauvegarde.

1. Si vous utilisez Sauvegarde Azure pour la première fois, vous devez recourir à l’applet de commande **Register-AzureRMResourceProvider** pour enregistrer le fournisseur Azure Recovery Service auprès de votre abonnement.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Le coffre Recovery Services constituant une ressource ARM, vous devez le placer dans un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créer un. Quand vous créez un groupe de ressources, spécifiez ses nom et emplacement.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "WestUS"
    ```
3. Utilisez l’applet de commande **New-AzureRmRecoveryServicesVault** pour créer le coffre. Spécifiez pour le coffre le même emplacement que pour le groupe de ressources.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
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

Exécutez la commande **Get-AzureRmRecoveryServicesVault** ; tous les coffres dans l’abonnement sont alors répertoriés.

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


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Installation de l'agent de sauvegarde Azure
Avant d’installer l'agent de sauvegarde Azure, vous devez avoir téléchargé le programme d’installation sur le serveur Windows. Vous pouvez obtenir la dernière version du programme d’installation à partir du [Centre de téléchargement Microsoft](http://aka.ms/azurebackup_agent) ou de la page Tableau de bord du coffre Recovery Services. Enregistrez le programme d’installation dans un emplacement auquel vous pouvez accéder facilement, par exemple *C:\Téléchargements\*.

Vous pouvez également utiliser PowerShell pour obtenir le téléchargeur :
 
 ```
 $MarsAURL = 'Http://Aka.Ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Pour installer l’agent, exécutez la commande ci-après dans une console PowerShell avec élévation de privilèges :

```
PS C:\> MARSAgentInstaller.exe /q
```

Cette opération installe l’agent avec les options par défaut. L’installation s’effectue en arrière-plan et prend quelques minutes. Si vous ne spécifiez pas l’option */nu* , la fenêtre **Windows Update** s’ouvrira à la fin de l’installation pour rechercher des mises à jour. Une fois installé, l’agent apparaît dans la liste des programmes installés.

Pour afficher la liste des programmes installés, cliquez sur **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités**.

![Agent installé](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Options d’installation
Pour afficher toutes les options disponibles via la ligne de commande, utilisez la commande suivante :

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

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Inscription d’un serveur Windows Server ou d’un ordinateur client Windows auprès d’un coffre Recovery Services
Une fois que vous avez créé un coffre Recovery Services, téléchargez le dernier agent et les informations d’identification de coffre et stockez-les dans un emplacement pratique comme C:\Téléchargements.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
```

Sur le serveur Windows Server ou l’ordinateur client Windows, exécutez l’applet de commande [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) pour inscrire l’ordinateur auprès du coffre.
Cette applet de commande, ainsi que d’autres utilisées pour la sauvegarde, proviennent du module MSONLINE que le Mars AgentInstaller a ajouté dans le cadre du processus d’installation. 

Le programme d’installation de l’agent ne met pas à jour la variable $Env:PSModulePath. Cela signifie que le chargement automatique du module échoue. Pour résoudre ce problème, vous pouvez effectuer les étapes suivantes :

```
PS C:\>  $Env:psmodulepath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules
```

En guise d’alternative, vous pouvez charger manuellement le module dans votre script comme suit :

```
PS C:\>  Import-Module  'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'

```

Une fois que vous avez chargé les applets de commande de Sauvegarde en ligne, vous devez inscrire les informations d’identification du coffre :


```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> N’utilisez pas de chemins relatifs pour spécifier le fichier des informations d’identification du coffre. Vous devez fournir un chemin absolu dans l’applet de commande.
>
>

## <a name="networking-settings"></a>Paramètres de mise en réseau
Lorsque l’ordinateur Windows accède à Internet via un serveur proxy, les paramètres proxy peuvent également être fournis à l’agent. Dans cet exemple, il n’y a aucun serveur proxy. Nous effaçons donc explicitement toutes informations concernant un proxy.

L’utilisation de la bande passante peut également être contrôlée avec les options ```work hour bandwidth``` et ```non-work hour bandwidth```, certains jours de la semaine.

La définition des détails sur le proxy et la bande passante s’effectue à l’aide de l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) :

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Paramètres de chiffrement
Les données sauvegardées envoyées à Sauvegarde Azure sont chiffrées pour garantir leur confidentialité. Le mot de passe du chiffrement est le « mot de passe » permettant de déchiffrer les données lors de la restauration.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
PS C:\> $PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force 
PS C:\> $PassCode   = 'AzureR0ckx'
PS C:\> Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
Server properties updated successfully
```

> [!IMPORTANT]
> Conservez les informations de phrase secrète en lieu sûr après les avoir définies. Vous ne pouvez pas restaurer les données à partir d’Azure sans cette phrase secrète.
>
>

## <a name="back-up-files-and-folders"></a>Sauvegarde des fichiers et dossiers
Toutes les sauvegardes de serveurs et clients Windows vers Sauvegarde Azure sont régies par une stratégie. Cette dernière comprend trois parties :

1. Une **planification de sauvegarde** qui spécifie quand les sauvegardes doivent être établies et synchronisées avec le service.
2. Une **planification de rétention** qui spécifie la durée de rétention des points de récupération dans Azure.
3. Une **spécification d'inclusion/exclusion de fichier** qui dicte ce qui doit être sauvegardé.

Dans ce document, comme nous automatisons la sauvegarde, nous supposons que rien n'a été configuré. Nous commençons par créer une stratégie de sauvegarde en utilisant l’applet de commande [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) .

```
PS C:\> $newpolicy = New-OBPolicy
```

À ce stade, la stratégie est vide et les autres applets de commande sont nécessaires pour définir les éléments qui seront inclus ou exclus, le moment auquel les sauvegardes s'exécuteront et leur emplacement de stockage.

### <a name="configuring-the-backup-schedule"></a>Configuration de la planification de sauvegarde
La première des 3 parties d'une stratégie est la planification de sauvegarde, qui est créée à l'aide de l’applet de commande [New-OBSchedule](https://technet.microsoft.com/library/hh770401) . La planification de sauvegarde définit le moment où les sauvegardes doivent être effectuées. Lors de la création d'une planification, vous devez spécifier 2 paramètres d'entrée :

* **jours de la semaine** où la sauvegarde doit s'exécuter. Vous pouvez exécuter le travail de sauvegarde une seule journée ou tous les jours de la semaine, ou une combinaison des deux.
* **heures** où la sauvegarde doit être exécutée. Vous pouvez définir jusqu'à 3 différentes heures pour le déclenchement de la sauvegarde.

Par exemple, vous pouvez configurer une stratégie de sauvegarde qui s'exécute à 16 h 00 chaque samedi et chaque dimanche.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

La planification de sauvegarde doit être associée à une stratégie à l'aide de l’applet de commande [Set-OBSchedule](https://technet.microsoft.com/library/hh770407) .

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Configuration d'une stratégie de rétention
La stratégie de rétention définit la durée de conservation des points de récupération créés à partir des travaux de sauvegarde. Lorsque vous créez une stratégie de rétention à l'aide de l’applet de commande [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) , vous pouvez spécifier le nombre de jours pendant lesquels les points de récupération de sauvegarde doivent être conservés avec Sauvegarde Azure. L'exemple suivant définit une stratégie de rétention de 7 jours.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

La stratégie de rétention doit être associée à la stratégie principale à l'aide de l'applet de commande [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### <a name="including-and-excluding-files-to-be-backed-up"></a>Inclusion et exclusion des fichiers à sauvegarder
Un objet ```OBFileSpec``` définit les fichiers à inclure et à exclure d'une sauvegarde. Il s'agit d'un ensemble de règles qui définissent l'étendue des fichiers et dossiers protégés sur un ordinateur. Vous pouvez avoir de nombreuses règles d'inclusion ou d’exclusion en fonction de vos besoins, et les associer à une stratégie. Lorsque vous créez un objet OBFileSpec, vous pouvez :

* Spécifier les fichiers et dossiers à inclure
* Spécifier les fichiers et dossiers à exclure
* Indiquer la sauvegarde récursive des données dans un dossier (ou) indiquer si seuls les fichiers de niveau supérieur du dossier spécifié doivent être sauvegardés

Dans le dernier cas, l’opération est effectuée à l’aide de l'indicateur -NonRecursive dans la commande New-OBFileSpec.

Dans l'exemple ci-dessous, nous sauvegardons les volumes C: et D: et excluons les fichiers binaires de système d'exploitation dans le dossier Windows et tous les dossiers temporaires. Pour cela, nous allons créer deux spécifications de fichiers à l'aide de l’applet de commande [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) : une pour l’inclusion et une pour l’exclusion. Une fois que les spécifications de fichiers ont été créées, elles sont associées à la stratégie à l'aide de l’applet de commande [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) .

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Application de la stratégie
L'objet de stratégie est à présent complet. Il est associé à une planification de sauvegarde, à une stratégie de rétention et à une liste d’inclusion/exclusion de fichiers. Cette stratégie peut maintenant être validée à des fins d’utilisation par Sauvegarde Azure. Avant d’appliquer la stratégie que vous venez de créer, vérifiez qu’aucune stratégie de sauvegarde existante n’est associée au serveur à l’aide de l’applet de commande [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415). Lors de la suppression de la stratégie, vous êtes invité à confirmer l'opération. Pour ignorer la confirmation, utilisez l’indicateur ```-Confirm:$false``` avec l'applet de commande.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

La validation de l'objet de stratégie s'effectue à l'aide de l’applet de commande [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) . Une confirmation vous est également demandée. Pour ignorer la confirmation, utilisez l’indicateur ```-Confirm:$false``` avec l'applet de commande.

```
PS C:> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Vous pouvez afficher les détails de la stratégie de sauvegarde existante à l'aide de l’applet de commande [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) . Vous pouvez afficher plus de détails à l’aide de l’applet de commande [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) pour la planification de sauvegarde et de l’applet de commande [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) pour les stratégies de rétention

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-ad-hoc-backup"></a>Exécution d’une sauvegarde ad hoc
Une fois qu'une stratégie de sauvegarde a été définie, les sauvegardes ont lieu selon la planification indiquée. Le déclenchement d’une sauvegarde ad hoc est également possible à l’aide de l’applet de commande [Start-OBBackup](https://technet.microsoft.com/library/hh770426) :

```
PS C:> Get-OBPolicy | Start-OBBackup
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Restauration des données à partir de Sauvegarde Azure
Cette section vous guide tout au long des étapes d'automatisation de la récupération des données à partir de Sauvegarde Azure. Cette opération implique les étapes suivantes :

1. Sélection du volume source
2. Choix d’un point de sauvegarde à restaurer
3. Choix d’un élément à restaurer
4. Déclenchement du processus de restauration

### <a name="picking-the-source-volume"></a>Sélection du volume source
Pour restaurer un élément à partir de Sauvegarde Azure, vous devez d'abord identifier la source associée. Étant donné que nous exécutons les commandes dans le contexte d'un serveur ou d’un client Windows, l'ordinateur est déjà identifié. L'étape suivante pour identifier la source consiste à identifier le volume qui la contient. Vous pouvez récupérer la liste des volumes ou des sources en cours de sauvegarde à partir de cet ordinateur en exécutant l’applet de commande [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) . Cette commande renvoie un tableau de toutes les sources sauvegardées à partir de ce serveur/client.

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Choix d’un point de sauvegarde à partir duquel restaurer
Vous récupérez une liste des points de sauvegarde en exécutant l’applet de commande [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) avec les paramètres appropriés. Dans notre exemple, nous allons sélectionner le dernier point de sauvegarde du volume source *D:* et l'utiliser pour récupérer un fichier spécifique.

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```
L'objet ```$rps``` est un tableau de points de sauvegarde. Le premier élément est le point le plus récent et le Nième élément est le point le plus ancien. Pour choisir le point le plus récent, nous allons utiliser ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Choix d’un élément à restaurer
Pour identifier le fichier ou dossier exact à restaurer, utilisez de manière récursive l’applet de commande [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) . De cette façon, la hiérarchie de dossiers est accessible uniquement à l'aide de ```Get-OBRecoverableItem```.

Dans cet exemple, si vous souhaitez restaurer le fichier *finances.xls*, nous pouvons le référencer à l’aide de l’objet ```$filesFolders[1]```.

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Vous pouvez également rechercher des éléments à restaurer à l'aide de l’applet de commande ```Get-OBRecoverableItem``` . Dans notre exemple, pour rechercher le fichier *finances.xls* , nous pouvons trouver le fichier en exécutant la commande suivante :

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Déclenchement du processus de restauration
Pour déclencher le processus de restauration, nous devons d'abord spécifier les options de récupération. Pour ce faire, utilisez l’applet de commande [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) . Dans le cadre de cet exemple, supposons que vous souhaitez restaurer les fichiers dans *C:\temp*. Supposons également que vous souhaitez ignorer les fichiers qui existent déjà dans le dossier de destination *C:\temp*. Pour créer une telle option de récupération, utilisez la commande suivante :

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Déclenchez à présent le processus de restauration en exécutant la commande [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) sur l’élément ```$item``` sélectionné à partir de la sortie de l’applet de commande ```Get-OBRecoverableItem``` :

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Désinstallation de l’agent de sauvegarde Azure
La désinstallation de l’agent de sauvegarde Azure peut être effectuée à l’aide de la commande suivante :

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

La désinstallation des fichiers binaires de l'agent de l'ordinateur a certaines conséquences à prendre en compte :

* Elle supprime le filtre de fichier de l'ordinateur et le suivi des modifications est arrêté.
* Toutes les informations de stratégie sont supprimées de l'ordinateur, mais elles continuent à être stockées dans le service.
* Toutes les planifications de sauvegarde sont supprimées, et aucune autre sauvegarde n'est effectuée.

Cependant, les données stockées dans Azure sont conservées selon la stratégie de rétention que vous avez définie. Les points plus anciens deviennent automatiquement obsolètes.

## <a name="remote-management"></a>Gestion à distance
L’intégralité de la gestion concernant l’agent de sauvegarde Azure, les stratégies et les sources de données peut être effectuée à distance par le biais de PowerShell. L’ordinateur qui sera géré à distance doit être correctement préparé.

Par défaut, le service WinRM est configuré pour un démarrage manuel. Le type de démarrage doit être défini sur *Automatique* et le service devrait être démarré. Pour vérifier que le service WinRM est exécuté, la valeur de la propriété État devrait être défini sur *En cours d’exécution*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell doit être configuré pour la communication à distance.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

L’ordinateur peut maintenant être géré à distance, en commençant par l’installation de l’agent. Par exemple, le script suivant copie et installe l’agent sur l’ordinateur distant.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Sauvegarde Azure pour client/serveur Windows, consultez

* [Présentation de Sauvegarde Azure](backup-introduction-to-azure-backup.md)
* [Sauvegarder des serveurs Windows](backup-configure-vault.md)
