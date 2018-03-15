---
title: "Déployer le service Mobilité Site Recovery avec Azure Automation DSC | Microsoft Docs"
description: "Explique comment utiliser Azure Automation DSC pour déployer automatiquement le service Mobilité Azure Site Recovery et l’agent Azure pour la réplication de serveurs physiques et de machines virtuelles VMware sur Azure."
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: krnese
ms.openlocfilehash: eb837858fe81fa1f2d4966051bd4b7b068defe8b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>Déployer le service Mobilité avec Azure Automation DSC pour la réplication de machines virtuelles
Dans Operations Management Suite, nous fournissons une solution complète de sauvegarde et de récupération d’urgence que vous pouvez exploiter dans le cadre de votre plan de continuité d’activité.

Nous avions commencé avec Hyper-V en utilisant Hyper-V Replica. Mais nous avons étendu notre offre pour prendre en charge une installation hétérogène, car les clients ont plusieurs hyperviseurs et plusieurs plateformes dans les nuages.

Aujourd’hui, si vous exécutez des charges de travail VMware et/ou des serveurs physiques, un serveur d’administration exécute l’ensemble des composants Azure Site Recovery dans votre environnement pour gérer la communication et la réplication des données avec Azure (dans la mesure où Azure est votre destination).

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Déploiement du service Site Recovery Mobility à l’aide d’Automation DSC
Commençons par analyser rapidement les opérations réellement effectuées par ce serveur d’administration.

Le serveur d’administration exécute plusieurs rôles de serveur. L’un de ces rôles est la *configuration*, qui coordonne la communication et gère les processus de réplication et de récupération des données.

En outre, le rôle de *processus* fait office de passerelle de réplication. Il reçoit les données de réplication des ordinateurs source protégés, les optimise grâce à la mise en cache, à la compression et au chiffrement, et les envoie vers un compte Azure Storage. L’une des fonctions du rôle processus consiste également à gérer l’installation Push du service Mobilité sur les machines protégées et à exécuter la détection automatique des machines virtuelles VMware.

Dans le cas d’une restauration automatique à partir d’Azure, le rôle *Cible principale* est responsable. Il gère la réplication des données dans le cadre de cette opération.

Pour les ordinateurs protégés, nous utilisons le *service Mobilité*. Ce composant est déployé sur chaque machine (machine virtuelle VMware ou serveur physique) que vous souhaitez répliquer vers Azure. Il capture les opérations d’écriture de données sur la machine et les transfère vers le serveur d’administration (rôle processus).

Lorsque la continuité de l’activité est en jeu, vous devez absolument comprendre vos charges de travail, votre infrastructure et les composants impliqués. Vous pouvez ensuite répondre aux exigences d’objectif de temps de récupération (RTO) et d’objectif de point de récupération (RPO). Dans ce contexte, le service Mobilité est indispensable pour garantir que vos charges de travail sont protégées comme attendu.

Alors, comment pouvez-vous nous assurer de manière optimisée que l’installation est protégée et fiable, à l’aide de certains composants Operations Management Suite ?

Cet article fournit un exemple de la manière dont vous pouvez utiliser Configuration de l’état souhaité (DSC) Azure Automation, ainsi que Site Recovery, pour vous assurer que :

* le service Mobilité et l’agent de machine virtuelle Azure sont déployés sur les machines Windows que vous souhaitez protéger ;
* le service Mobilité et l’agent de machine virtuelle Azure sont toujours en cours d’exécution lorsqu’Azure est la cible de réplication.

## <a name="prerequisites"></a>Prérequis
* Un référentiel pour stocker l’installation requise
* Un référentiel pour stocker la phrase secrète requise pour s’enregistrer avec le serveur d’administration

  > [!NOTE]
  > Une phrase secrète unique est générée pour chaque serveur d’administration. Si vous envisagez de déployer plusieurs serveurs d’administration, vous devez vous assurer que la phrase secrète correcte est stockée dans le fichier passphrase.txt.
  >
  >
* Windows Management Framework (WMF) 5.0 installé sur les machines que vous souhaitez protéger (obligatoire pour Automation DSC).

  > [!NOTE]
  > Si vous souhaitez utiliser DSC pour des ordinateurs Windows sur lesquels WMF 4.0 est installé, reportez-vous à la section [Utilisation de DSC dans les environnements déconnectés](## Use DSC in disconnected environments).
  

Le service Mobilité peut être installé par le biais de la ligne de commande et accepte plusieurs arguments. C’est la raison pour laquelle vous devez extraire les fichiers binaires (à partir de votre installation) et les stocker dans un emplacement où vous pourrez les récupérer à l’aide d’une configuration DSC.

## <a name="step-1-extract-binaries"></a>Étape 1 : extraction des fichiers binaires
1. Pour extraire les fichiers dont vous avez besoin pour cette installation, accédez au répertoire suivant sur votre serveur d’administration :

    **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**

    Dans ce dossier, vous devriez voir un fichier MSI nommé :

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Utilisez la commande suivante pour extraire le programme d’installation :

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Sélectionnez tous les fichiers et envoyez-les vers un dossier compressé (zippé).

Vous disposez maintenant des fichiers binaires dont vous avez besoin pour automatiser l’installation du service Mobilité à l’aide d’Automation DSC.

### <a name="passphrase"></a>Phrase secrète
Ensuite, vous devez déterminer où vous souhaitez placer ce dossier compressé. Vous pouvez utiliser un compte de stockage Azure, comme indiqué par la suite, pour stocker la phrase secrète dont vous avez besoin pour le programme d’installation. L’agent est ensuite inscrit avec le serveur d’administration dans le cadre du processus.

La phrase secrète obtenue lors du déploiement du serveur d’administration peut être enregistrée dans un fichier txt ayant pour nom passphrase.txt.

Placez le dossier compressé et la phrase secrète dans un conteneur dédié au sein du compte de stockage Azure.

![Emplacement du dossier](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Si vous préférez conserver ces fichiers sur un partage de votre réseau, vous en avez la possibilité. Vous devez simplement vous assurer que la ressource DSC que vous utiliserez ultérieurement peut accéder à l’installation et à la phrase secrète et les obtenir.

## <a name="step-2-create-the-dsc-configuration"></a>Étape 2 : création de la configuration DSC
L’installation dépend de WMF 5.0. WMF 5.0 doit être installé pour que l’ordinateur applique la configuration par le biais d’Automation DSC.

L’environnement utilise l’exemple de configuration DSC suivant :

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
La configuration effectue les opérations suivantes :

* Les variables indiquent à la configuration où obtenir les fichiers binaires pour le service Mobilité et l’agent de machine virtuelle Azure, ainsi que la phrase secrète. Elles indiquent également où stocker la sortie.
* La configuration importe la ressource DSC xPSDesiredStateConfiguration pour vous permettre l’utilisation de `xRemoteFile` pour télécharger les fichiers à partir du référentiel.
* La configuration crée un répertoire dans lequel stocker les fichiers binaires.
* La ressource Archive extrait les fichiers à partir du dossier compressé.
* La ressource Installation de package installe le service Mobilité à partir du programme d’installation UNIFIEDAGENT.EXE avec les arguments spécifiques. Les variables qui construisent les arguments doivent être modifiées de manière à refléter votre environnement.
* La ressource AzureAgent de package installe l’agent de machine virtuelle Azure, ce qui est recommandé sur chaque machine virtuelle qui s’exécute dans Azure. L’agent de machine virtuelle Azure rend également possible l’ajout d’extensions à la machine virtuelle après le basculement.
* La ou les ressources Service garantissent que les services Mobilité associés et les services Azure s’exécutent toujours.

Enregistrez la configuration en tant **qu’ASRMobilityService**.

> [!NOTE]
> N’oubliez pas de remplacer le CSIP dans votre configuration pour qu’il reflète le serveur d’administration réel de manière à ce que l’agent soit connecté correctement et utiliser la phrase secrète appropriée.
>
>

## <a name="step-3-upload-to-automation-dsc"></a>Étape 3 : chargement dans Automation DSC
Dans la mesure où votre configuration DSC créée importe un module de ressources DSC nécessaire (xPSDesiredStateConfiguration), vous devez importer ce module dans Automation avant de charger la configuration DSC.

Connectez-vous à votre compte Automation et accédez à **Actifs** > **Modules**, puis cliquez sur **Parcourir la galerie**.

Ici, vous pouvez rechercher le module et l’importer dans votre compte.

![Importer le module](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Lorsque vous avez terminé, accédez à la machine sur laquelle les modules Azure Resource Manager sont installés et procédez à l’importation de la configuration DSC nouvellement créée.

### <a name="import-cmdlets"></a>Importer les applets de commande
Connectez-vous à votre abonnement Azure dans PowerShell. Modifiez les applets de commande pour refléter votre environnement et capturer les informations de votre compte Automation dans une variable :

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Chargez la configuration vers Automation DSC à l’aide de l’applet de commande suivante :

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilation de la configuration dans Automation DSC
Ensuite, vous devez compiler la configuration dans Automation DSC, afin de pouvoir commencer à y enregistrer des nœuds. Pour ce faire, exécutez l’applet de commande suivante :

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Cela peut prendre quelques minutes, car vous déployez la configuration vers le service Pull DSC hébergé.

Une fois que vous avez terminé, vous pouvez récupérer les informations sur la tâche à l’aide de PowerShell (Get-AzureRmAutomationDscCompilationJob) ou du [portail Azure](https://portal.azure.com/).

![Tâche de récupération](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

À présent, vous avez correctement publié et chargé notre configuration DSC sur Automation DSC.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Étape 4 : intégration de machines sur Automation DSC
> [!NOTE]
> L’une des conditions préalables à la réalisation de ce scénario est que les machines Windows soient mises à jour avec la dernière version de WMF. Vous pouvez télécharger et installer la version correcte pour votre plateforme depuis le [Centre de téléchargement](https://www.microsoft.com/download/details.aspx?id=50395).
>
>

Vous allez maintenant créer une métaconfiguration de DSC que vous allez appliquer aux nœuds. Pour y parvenir, vous devez récupérer l’URL du point de terminaison et la clé primaire de votre compte Automation sélectionné dans Azure. Ces valeurs peuvent se trouver sous **Keys** dans le panneau **Tous les paramètres** du compte Automation.

![Valeurs de clé](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Dans cet exemple, vous avez un serveur physique Windows Server 2012 R2 que vous voulez protéger avec Site Recovery.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Rechercher les éventuelles opérations de changement de nom de fichier en attente dans le Registre
Avant de commencer à associer le serveur au point de terminaison Automation DSC, nous recommandons de rechercher s’il existe des opérations de changement de nom de fichier en attente dans le Registre. Cela peut empêcher la bonne exécution de l’installation en raison d’un redémarrage en attente.

Exécutez l’applet de commande suivante pour vérifier qu’il n’y a aucun redémarrage en attente sur le serveur :

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Si rien n’apparaît, vous pouvez continuer. Sinon, vous devez remédier à cette situation en redémarrant le serveur pendant une fenêtre de maintenance.

Pour appliquer la configuration sur le serveur, démarrez l’Environnement d’écriture de scripts intégré Windows PowerShell (ISE) et exécutez le script suivant. Il s’agit essentiellement d’une configuration locale de DSC qui force le moteur du gestionnaire de configuration local à s’inscrire auprès du service Automation DSC et à récupérer la configuration spécifique (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Cette configuration oblige le moteur du gestionnaire de configuration local à s’inscrire lui-même avec Automation DSC. Elle permet également de déterminer comment le moteur doit fonctionner, ce qu’il doit faire s’il existe une différence de configuration (ApplyAndAutoCorrect) et comment il doit continuer la configuration si un redémarrage est nécessaire.

Une fois ce script exécuté, le nœud doit commencer à s’enregistrer avec Automation DSC.

![Enregistrement du nœud en cours](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Si vous revenez au portail Azure, vous pouvez voir que le nœud qui vient d’être enregistré apparaît maintenant dans le portail.

![Nœud enregistré dans le portail](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Sur le serveur, vous pouvez exécuter l’applet de commande PowerShell suivante pour vérifier que le nœud a été enregistré correctement :

```powershell
Get-DscLocalConfigurationManager
```

Une fois la configuration extraite et appliquée au serveur, vous pouvez le vérifier en exécutant l’applet de commande suivante :

```powershell
Get-DscConfigurationStatus
```

La sortie montre que le serveur a correctement extrait sa configuration :

![Sortie](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

En outre, l’installation du service Mobilité a son propre journal, consultable dans *DisqueSystème*\ProgramData\ASRSetupLogs.

Et voilà ! Le service Mobilité est désormais déployé et enregistré sur la machine que vous souhaitez protéger à l’aide de Site Recovery. DSC permet de garantir que les services nécessaires sont toujours exécutés.

![Déploiement réussi](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Une fois que le serveur d’administration détecte que le déploiement a été réalisé avec succès, vous pouvez configurer la protection et activer la réplication sur la machine à l’aide de Site Recovery.

## <a name="use-dsc-in-disconnected-environments"></a>Utilisation de DSC dans des environnements déconnectés
Si vos machines ne sont pas connectées à Internet, vous pouvez toujours vous fier à DSC pour déployer et configurer le service Mobilité sur les charges de travail que vous souhaitez protéger.

Vous pouvez instancier votre propre serveur collecteur DSC dans votre environnement pour fournir essentiellement les mêmes fonctionnalités que vous obtenez d’Automation DSC. Autrement dit, les clients appliquent la configuration (après son inscription) au point de terminaison DSC. Toutefois, une autre option consiste à envoyer manuellement la configuration DSC sur vos machines, localement ou à distance.

Notez que, dans cet exemple, il existe un paramètre ajouté pour le nom de l’ordinateur. Les fichiers distants se trouvent maintenant sur un partage distant qui doit être accessible par les machines que vous souhaitez protéger. La fin du script promulgue la configuration, puis commence à appliquer la configuration DSC à l’ordinateur cible.

### <a name="prerequisites"></a>Prérequis
Assurez-vous que le module PowerShell xPSDesiredStateConfiguration est installé. Pour les machines Windows sur lesquelles WMF 5.0 est installé, vous pouvez installer le module xPSDesiredStateConfiguration en exécutant l’applet de commande suivante sur les machines cibles :

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Vous pouvez également télécharger et enregistrer le module si vous devez le distribuer aux machines Windows sur lesquelles WMF 4.0 est installé. Exécutez cette applet de commande sur une machine sur laquelle PowerShellGet (WMF 5.0) est installé :

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Pour WMF 4.0, vérifiez également que la [mise à jour Windows 8.1 KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) est installée sur les machines.

La configuration suivante peut être transmise aux machines sur lesquelles WMF 5.0 et WMF 4.0 sont installés :

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Si vous souhaitez instancier votre propre serveur collecteur DSC au sein de votre réseau d’entreprise pour imiter les fonctionnalités que vous pouvez obtenir à partir d’Automation DSC, consultez la rubrique [Configuration d’un serveur collecteur web DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Déploiement d’une configuration DSC à l’aide d’un modèle Azure Resource Manager (facultatif)
Cet article se concentre sur la création de votre propre configuration DSC pour déployer automatiquement le service Mobilité et l’agent de machine virtuelle Azure, et s’assurer qu’ils s’exécutent sur les machines que vous souhaitez protéger. Nous avons également un modèle Azure Resource Manager qui déploie cette configuration DSC dans un compte Azure Automation nouveau ou existant. Le modèle utilise des paramètres d’entrée pour créer des ressources Automation qui contiennent les variables pour votre environnement.

Une fois le modèle déployé, vous n’avez plus qu’à vous reporter à l’étape 4 de ce guide concernant l’intégration de vos machines.

Le modèle effectue les opérations suivantes :

1. Utilisation d’un compte Automation existant ou création d’un nouveau compte
2. Prenez les paramètres d’entrée pour :
   * ASRRemoteFile : l’emplacement dans lequel vous avez stocké l’installation du service Mobilité
   * ASRPassphrase : l’emplacement dans lequel vous avez stocké le fichier passphrase.txt
   * ASRCSEndpoint : l’adresse IP de votre serveur d’administration
3. Importez le module PowerShell xPSDesiredStateConfiguration
4. Créez et compilez la configuration DSC

Toutes les étapes précédentes doivent être effectuées dans le bon ordre pour que vous puissiez facilement commencer à intégrer les machines à protéger.

Le modèle comportant des instructions pour le déploiement se trouve sur [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Déployez le modèle à l’aide de PowerShell :

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Étapes suivantes
Après avoir déployé les agents du Service Mobilité, vous pouvez [activer la réplication](site-recovery-vmware-to-azure.md) des machines virtuelles.
