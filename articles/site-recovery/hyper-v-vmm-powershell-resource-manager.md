---
title: "Répliquer les machines virtuelles Hyper-V de clouds VMM sur un site secondaire avec PowerShell (Azure Resource Manager) | Microsoft Docs"
description: "Explique comment répliquer les machines virtuelles Hyper-V de clouds VMM sur un site VMM secondaire avec PowerShell (Resource Manager)"
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: a5e36546494223b20844303f3f76782746658411
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-using-powershell-resource-manager"></a>Répliquer des machines virtuelles Hyper-V sur un site secondaire au moyen de PowerShell (Resource Manager)

Cet article montre comment automatiser les étapes de la réplication des machines virtuelles Hyper-V de clouds VMM (System Center Virtual Machine Manager) sur le cloud VMM d’un site local secondaire en utilisant [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Prérequis

- Examinez [l’architecture et les composants du scénario](hyper-v-vmm-architecture.md).
- Vérifiez les [exigences de prise en charge](site-recovery-support-matrix-to-sec-site.md) pour tous les composants.
- Assurez-vous que les serveurs VMM et les hôtes Hyper-V sont conformes aux [exigences de prise en charge](site-recovery-support-matrix-to-sec-site.md).
- Vérifiez que les machines virtuelles à répliquer sont conformes aux [exigences de prise en charge de la machine répliquée](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="prepare-for-network-mapping"></a>Préparer le mappage réseau

Le [mappage réseau](hyper-v-vmm-network-mapping.md) effectue un mappage entre les réseaux de machines virtuelles VMM sur site dans les clouds source et cible. Il effectue les opérations suivantes :

- Connecte les machines virtuelles aux réseaux de machines virtuelles cibles appropriés après le basculement. 
- Place de manière optimale les machines virtuelles de réplication sur les serveurs hôtes Hyper-V cibles. 
- Si vous ne configurez pas le mappage réseau, les machines virtuelles de réplication ne se connecteront à aucun réseau de machines virtuelles après le basculement.

Préparez les serveurs VMM comme suit :

1. Assurez-vous que vous avez des [réseaux logiques VMM](https://docs.microsoft.com/system-center/vmm/network-logical) sur les serveurs VMM source et cible.
    - Le réseau logique sur le serveur source doit être associé au cloud source dans lequel se trouvent les hôtes Hyper-V.
    - Le réseau logique sur le serveur cible doit être associé au cloud cible.
1. Assurez-vous que vous avez des [réseaux de machines virtuelles](https://docs.microsoft.com/system-center/vmm/network-virtual) sur les serveurs VMM source et cible. Les réseaux de machines virtuelles doivent être associés au réseau logique dans chaque emplacement.
2. Connectez les machines virtuelles sur les hôtes Hyper-V sources au réseau de machines virtuelles source. 

## <a name="prepare-for-powershell"></a>Préparer PowerShell

Assurez-vous qu’Azure PowerShell est prêt à l’emploi.

- Si vous utilisez déjà PowerShell, vous devrez passer à la version 0.8.10 ou ultérieure.  [En savoir plus](/powershell/azureps-cmdlets-docs) sur la configuration de Powershell.
- Après avoir installé et configuré PowerShell, examinez les [applets de commande de service](/powershell/azure/overview).
- Pour en savoir plus sur l’utilisation des valeurs de paramètre, des entrées et des sorties dans Azure PowerShell, consultez le guide [Prise en main](/powershell/azure/get-started-azureps).

## <a name="set-up-a-subscription"></a>Configurer un abonnement
1. Dans Azure PowerShell, connectez-vous à votre compte Azure :

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Récupérez la liste de vos abonnements, avec les ID d’abonnement. Notez l’ID de l’abonnement dans lequel vous voulez créer le coffre Recovery Service.   

        Get-AzureRmSubscription
3. Définissez l’abonnement du coffre :

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
1. Si vous ne disposez d’aucun groupe de ressources Azure Resource Manager, créez-en un.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Créez un coffre Recovery Services et enregistrez cet objet de coffre dans une variable utilisée plus tard : 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Vous pouvez récupérer l’objet de coffre après sa création à l’aide de l’applet de commande Get-AzureRMRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Définir le contexte du coffre
1. Récupérez un coffre existant :

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Définissez le contexte du coffre :

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-azure-site-recovery-provider"></a>installation du fournisseur Azure Site Recovery
1. Sur la machine VMM, créez un répertoire en exécutant la commande suivante :

       New-Item c:\ASR -type directory
2. Extrayez les fichiers à l’aide du fichier de configuration du fournisseur téléchargé : pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installez le fournisseur et attendez que l’installation se termine :

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

4. Inscrivez le serveur dans le coffre :

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Créer et associer une stratégie de réplication
1. Créez une stratégie de réplication (dans ce cas pour Hyper-V 2012 R2) de la manière suivante :

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
    > Le cloud VMM peut contenir des hôtes Hyper-V s’exécutant sur différentes versions de Windows Server, alors que la stratégie de réplication est destinée à une version spécifique d’un système d’exploitation. Si vos hôtes s’exécutent sur des systèmes d’exploitation différents, créez des stratégies de réplication distinctes pour chaque système. Par exemple, si vous avez cinq hôtes exécutés sur Windows Servers 2012 et trois hôtes exécutés sur Windows Server 2012 R2, créez deux stratégies de réplication : une pour chaque type de système d’exploitation.

2. Récupérez le conteneur de protection principal (cloud VMM principal) et le conteneur de protection de récupération (cloud VMM de récupération) :

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Récupérez la stratégie de réplication que vous avez créée en utilisant le nom convivial :

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Lancez l’association du conteneur de protection (cloud VMM) avec la stratégie de réplication :

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Attendez que la tâche d’association de la stratégie se termine. Vous pouvez savoir si la tâche est terminée au moyen de l’extrait de code PowerShell suivant :

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

Pour vérifier que l’opération est terminée, suivez les étapes décrites à la section [Analyser l’activité](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurer le mappage réseau
1. Utilisez cette commande pour récupérer des serveurs pour le coffre actif. La commande stocke les serveurs Azure Site Recovery dans la variable tableau $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Exécutez cette commande pour récupérer les réseaux pour le serveur VMM source et le serveur VMM cible.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Le serveur VMM source peut être le premier ou le second serveur dans le groupe de serveurs. Vérifiez les noms de serveur VMM et récupérez les réseaux appropriés.


3. L’applet de commande crée un mappage entre le réseau principal et le réseau de récupération. Elle spécifie le réseau principal en tant que premier élément de $PrimaryNetworks, et le réseau de récupération en tant que premier élément de $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Activer la protection des machines virtuelles
Après avoir correctement configuré les serveurs, les clouds et les réseaux, vous pouvez activer la protection pour les machines virtuelles dans le cloud.

1. Afin d’activer la protection, exécutez la commande suivante pour récupérer le conteneur de protection :

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obtenez l’entité de protection (machine virtuelle) de la façon suivante :

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Activez la réplication de la machine virtuelle :

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Exécuter un test de basculement

Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une machine virtuelle, ou créer un plan de récupération contenant plusieurs machines virtuelles et exécuter sur lui un test de basculement. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé.

1. Récupérez la machine virtuelle dans laquelle les machines virtuelles basculent :

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Effectuez un test de basculement en procédant comme suit :
    - Pour une machine virtuelle :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
    - Pour un plan de récupération :

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Pour vérifier que l’opération est terminée, suivez les étapes décrites à la section [Analyser l’activité](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Exécuter des basculements planifiés et non planifiés

1. Effectuez un basculement planifié de la façon suivante :
    -  Pour une machine virtuelle :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
    - Pour un plan de récupération :

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan
2. Effectuez un basculement non planifié de la façon suivante :
    - Pour une machine virtuelle :
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

    - Pour un plan de récupération :

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Analyser l’activité
Utilisez les commandes suivantes pour analyser l’activité de basculement. Vous devez attendre la fin du traitement entre les tâches.

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

[En savoir plus](/powershell/module/azurerm.recoveryservices.backup/#recovery) sur Site Recovery avec les applets de commande PowerShell Azure Resource Manager.
