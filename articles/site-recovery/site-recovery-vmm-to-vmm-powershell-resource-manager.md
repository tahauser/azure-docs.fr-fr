---
title: "Répliquer des machines virtuelles Hyper-V dans VMM sur un site secondaire avec PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "Explique comment déployer Azure Site Recovery pour orchestrer la réplication, le basculement et la récupération des machines virtuelles Hyper-V dans des clouds VMM vers un site VMM secondaire avec PowerShell (Resource Manager)"
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: sutalasi
ms.openlocfilehash: c978c2e31e775f56824d765491f6d7b73648b8ae
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Répliquer des machines virtuelles Hyper-V dans des clouds VMM sur un site VMM secondaire avec PowerShell (Resource Manager)

Cet article vous montre comment utiliser PowerShell afin d’automatiser les tâches courantes lorsque vous configurez Azure Site Recovery pour répliquer des machines virtuelles Hyper-V dans des clouds System Center VMM vers des clouds System Center VMM sur un site secondaire.



## <a name="on-premises-prerequisites"></a>Conditions préalables locales
Voici ce que dont aurez besoin sur les sites locaux principaux et secondaires pour déployer ce scénario :

| **Configuration requise** | **Détails** |
| --- | --- |
| **VMM** |Nous vous recommandons de déployer un serveur VMM dans le site principal et un autre dans le site secondaire.<br/><br/> Vous pouvez également effectuer la réplication entre des clouds sur un seul serveur VMM. Pour ce faire, vous avez besoin d’au moins deux clouds configurés sur le serveur VMM.<br/><br/> Les serveurs VMM doivent exécuter au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Chaque serveur VMM doit disposer d’un ou plusieurs clouds configurés et tous les clouds doivent avoir le profil de capacité Hyper-V. <br/><br/>Les clouds doivent contenir un ou plusieurs groupes hôtes VMM. Les serveurs VMM doivent avoir accès à Internet. |
| **Hyper-V** |Les serveurs Hyper-V doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doit contenir au moins une machine virtuelle.<br/><br/>  Les serveurs hôtes Hyper-V doivent être situés dans des groupes hôtes dans les clouds VMM principaux et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster Windows Server 2012 R2, vous devez installer la [mise à jour 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si vous exécutez Hyper-V dans un cluster sous Windows Server 2012, notez que le répartiteur de clusters n’est pas créé automatiquement si vous avez un cluster avec adresses IP statiques. Vous devez configurer manuellement le service Broker du cluster. [En savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Fournisseur** |Pendant un déploiement de Site Recovery, le fournisseur Azure Site Recovery est installé sur les serveurs VMM. Le fournisseur communique avec Site Recovery sur le port HTTPS 443 pour orchestrer la réplication. La réplication des données a lieu entre les serveurs Hyper-V principaux et secondaires via le réseau local ou une connexion VPN.<br/><br/> Le fournisseur qui s’exécute sur le serveur VMM a besoin d’accéder à ces URL : *.hypervrecoverymanager.windowsazure.com ; *.accesscontrol.windows.net ; *.backup.windowsazure.com ; *.blob.core.windows.net ; *.store.core.windows.net.<br/><br/> Autorisez également la communication de pare-feu des serveurs VMM vers les [plages IP de centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et autorisez le protocole HTTPS (443). |

### <a name="network-mapping-prerequisites"></a>Conditions préalables liées au mappage réseau
Le mappage réseau effectue un mappage entre les réseaux de machines virtuelles VMM sur les serveurs VMM principaux et secondaires pour :

* Optimiser le positionnement des machines virtuelles de réplication sur les hôtes Hyper-V secondaires après le basculement.
* Connecter les machines virtuelles de réplication aux réseaux de machines virtuelles appropriés.
* Si vous ne configurez pas le mappage réseau, les machines virtuelles de réplication ne seront connectées à aucun réseau après le basculement.
* Si vous voulez configurer le mappage réseau lors du déploiement Site Recovery, vous aurez besoin des éléments suivants :

  * Assurez-vous que l’ensemble des machines virtuelles du serveur hôte Hyper-V source sont connectées à un réseau de machines virtuelles VMM. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.
  * Vérifiez que le cloud secondaire que vous allez utiliser pour la récupération est configuré avec un réseau de machines virtuelles correspondant. Ce réseau de machines virtuelles doit être lié à un réseau logique lui-même associé au cloud secondaire.

Pour en savoir plus sur la configuration des réseaux VMM, consultez les articles ci-dessous

* [Vue d’ensemble de la configuration de la mise en réseau logique dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Configuration de réseaux de machines virtuelles et de passerelles dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)


### <a name="powershell-prerequisites"></a>Conditions préalables pour PowerShell
Assurez-vous qu’Azure PowerShell est prêt à l’emploi. Si vous utilisez déjà PowerShell, vous devrez passer à la version 0.8.10 ou ultérieure. Pour plus d’informations sur la configuration de PowerShell, consultez [Guide d’installation et de configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Une fois PowerShell configuré, vous pouvez afficher toutes les applets de commande disponibles pour le service [ici](/powershell/azure/overview).

Pour obtenir des conseils sur l’utilisation des applets de commande, par exemple, comment les valeurs de paramètres, les entrées et les sorties sont gérées dans Azure PowerShell, consultez le [Guide de prise en main des applets de commande Azure](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Étape 1 : Définition de l’abonnement
1. Dans Azure PowerShell, connectez-vous à votre compte Azure à l’aide des applets de commande suivantes

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Obtenez la liste de vos abonnements. Cette opération affiche également la liste des ID de chaque abonnement. Notez l’ID de l’abonnement dans lequel vous voulez créer le coffre Recovery Services    

        Get-AzureRmSubscription
3. Définissez l’abonnement dans lequel le coffre Recovery Services doit être créé en mentionnant l’ID d’abonnement

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Étape 2 : Création du coffre Recovery Services
1. Création d’un groupe de ressources Azure Resource Manager, si ce n’est déjà fait

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Créez un coffre Recovery Services et stockez l’objet de coffre Recovery Services dans une variable (qui sera utilisée plus tard). Vous pouvez également récupérer l’objet de coffre après la création à l’aide de la cmdlet Get-AzureRMRecoveryServicesVault :-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définition du contexte du coffre Recovery Services
1. Si vous avez déjà créé un coffre, exécutez la commande ci-dessous pour y accéder.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Définissez le contexte du coffre en exécutant la commande suivante.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Étape 4 : Installation du fournisseur Azure Site Recovery
1. Sur la machine VMM, créez un répertoire en exécutant la commande suivante :

       New-Item c:\ASR -type directory
2. Extrayez les fichiers à l'aide du fournisseur téléchargé en exécutant la commande suivante

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installez le fournisseur à l’aide de la commande suivante :

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   Attendez que l'installation se termine.
4. Inscrivez le serveur dans le coffre à l'aide de la commande suivante :

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-and-associate-a-replication-policy"></a>Étape 5 : Créer et associer une stratégie de réplication
1. Créez une stratégie de réplication Hyper-V 2012 R2 en exécutant la commande suivante :

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Le cloud VMM peut contenir des hôtes Hyper-V exécutant différentes versions de Windows Server (comme indiqué dans les conditions requises pour Hyper-V), mais la stratégie de réplication est spécifique à la version du système d’exploitation. Si vos hôtes exécutent des versions de système d’exploitation différentes, créez des stratégies de réplication spécifiques à chaque version du système d’exploitation. Par exemple, si vous avez cinq hôtes exécutant Windows Servers 2012 et trois hôtes exécutant Windows Server 2012 R2, créez deux stratégies de réplication : une pour chaque version du système d’exploitation.

1. Obtenez le conteneur de protection principal (cloud VMM principal) et le conteneur de protection de récupération (cloud VMM de récupération) en exécutant les commandes suivantes :

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Récupérer la stratégie que vous avez créé à l’étape 1 en utilisant le nom convivial de la stratégie

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Lancez l’association du conteneur de protection (cloud VMM) avec la stratégie de réplication :

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Attendez que la tâche d’association de la stratégie se termine. Vous pouvez vérifier si la tâche est terminée à l’aide de l’extrait de code PowerShell suivant.

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   Une fois la tâche terminée, exécutez la commande suivante :

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Pour vérifier que l'opération est terminée, suivez les étapes décrites dans la section [Suivi de l'activité](#monitor).

## <a name="step-6-configure-network-mapping"></a>Étape 6 : Configuration du mappage réseau
1. La première commande récupère les serveurs pour le coffre Azure Site Recovery actuel. La commande stocke les serveurs Microsoft Azure Site Recovery dans la variable tableau $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Les commandes suivantes permettent d’obtenir le réseau Site Recovery pour les serveurs VMM source et cible.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Le serveur VMM source peut être le premier ou le second serveur dans le groupe de serveurs. Vérifier les noms des serveurs VMM et obtenir correctement les réseaux


1. L'applet de commande finale crée un mappage entre le réseau principal et le réseau de récupération. L’applet de commande spécifie le réseau principal en tant que premier élément de $PrimaryNetworks, et le réseau de récupération en tant que premier élément de $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-7-configure-storage-mapping"></a>Étape 7 : Configuration du mappage de stockage
1. La commande ci-dessous récupère la liste des classifications de stockage dans la variable $storageclassifications.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. Les commandes suivantes récupèrent la classification source dans la variable $SourceClassification, et la classification cible dans la variable $TargetClassification.

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > Les classifications source et cible peuvent être n’importe quel élément du tableau. Reportez-vous à la sortie de la commande suivante pour déterminer l’index des classifications source et cible dans le tableau $storageclassifications.

    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object -Property FriendlyName, Id | Format-Table


1. L’applet de commande ci-dessous crée un mappage entre la classification source et la classification cible.

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-8-enable-protection-for-virtual-machines"></a>Étape 8 : Activation de la protection des machines virtuelles
Une fois que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud.

1. Afin d’activer la protection, exécutez la commande suivante pour récupérer le conteneur de protection :

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Récupérez l’entité de protection (machine virtuelle) en exécutant les commandes suivantes :

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Activez la réplication de la machine virtuelle en exécutant la commande suivante :

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="test-your-deployment"></a>Tester votre déploiement
Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et exécuter sur lui un test de basculement. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé.

> [!NOTE]
> Vous pouvez créer un plan de récupération pour votre application dans le portail Azure.
>
>

Pour vérifier que l'opération est terminée, suivez les étapes décrites dans la section [Suivi de l'activité](#monitor).

### <a name="run-a-test-failover"></a>Exécution d’un test de basculement
1. Exécutez les applets de commande ci-dessous pour obtenir le réseau de machines virtuelles sur lesquels vous souhaitez tester le basculement de vos machines virtuelles.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Effectuez un test de basculement d’une machine virtuelle en procédant comme suit :

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. Effectuez un test de basculement d’un plan de récupération en procédant comme suit :

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

### <a name="run-a-planned-failover"></a>Exécuter un basculement planifié
1. Effectuez un basculement planifié d’une machine virtuelle en procédant comme suit :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. Effectuez un basculement planifié d’un plan de récupération en procédant comme suit :

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié
1. Effectuez un basculement non planifié d’une machine virtuelle en procédant comme suit :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Effectuez un basculement non planifié d’un plan de récupération en procédant comme suit :

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name=monitor></a> Suivi de l'activité
Utilisez les commandes suivantes pour suivre l’activité. Vous devez attendre la fin du traitement entre les tâches.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](/powershell/module/azurerm.recoveryservices.backup/#recovery) sur Azure Site Recovery avec les applets de commande PowerShell Azure Resource Manager.
