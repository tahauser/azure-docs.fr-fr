---
title: "Réplication de machines virtuelles Hyper-V dans des clouds VMM à l’aide d’Azure Site Recovery et de PowerShell (Resource Manager) | Microsoft Docs"
description: "Réplication de machines virtuelles Hyper-V dans des clouds VMM à l'aide d'Azure Site Recovery et PowerShell"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: rajanaki
ms.openlocfilehash: 67d9164fcf8fb3731c4e3a9d30ac5c38da5908c7
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Réplication vers Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM à l’aide de PowerShell et d’Azure Resource Manager
> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-vmm-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell - Classique](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Vue d'ensemble
Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement, consultez la [Vue d’ensemble d’Azure Site Recovery](site-recovery-overview.md).

Cet article vous montre comment utiliser PowerShell pour automatiser les tâches courantes à effectuer lorsque vous configurez Azure Site Recovery pour répliquer des machines virtuelles Hyper-V dans des clouds System Center VMM vers le stockage Azure.

L’article inclut la configuration requise pour le scénario et décrit les étapes suivantes :

* Configuration d’un coffre Recovery Services
* Installation du fournisseur Azure Site Recovery sur le serveur VMM source
* Inscription du serveur dans le coffre, ajout d’un compte de stockage Azure
* Installation de l’agent Azure Recovery Services sur les serveurs Hyper-V
* Configuration des paramètres de protection des clouds VMM qui seront appliqués à toutes les machines virtuelles protégées
* Activation de la protection de ces machines virtuelles
* Test du basculement pour s’assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes pour mettre en œuvre ce scénario, posez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement de Resource Manager.
>
>

## <a name="before-you-start"></a>Avant de commencer
Assurez-vous que les conditions préalables sont remplies :

### <a name="azure-prerequisites"></a>Conditions préalables pour Azure
* Vous aurez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Si vous n’en avez pas, commencez avec un [compte gratuit](https://azure.microsoft.com/free). Vous pouvez aussi consulter la [Tarification d’Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Vous avez besoin d’un abonnement Fournisseur de solutions cloud si vous essayez une réplication vers un abonnement de ce type. Pour en savoir plus sur le programme Fournisseur de solutions cloud, consultez [S’inscrire au programme Fournisseur de solutions cloud](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
* Vous avez besoin d’un compte de stockage Azure v2 (Resource Manager) pour stocker les données répliquées dans Azure. La géo-réplication doit être activée pour ce compte. Ce dernier doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement ou à l’abonnement Fournisseur de solutions cloud. Pour en savoir plus sur la configuration d’Azure Storage, consultez [Introduction à Microsoft Azure Storage](../storage/common/storage-introduction.md) .
* Vous devez vous assurer que les machines virtuelles que vous voulez protéger sont conformes à la [configuration requise des machines virtuelles Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

> [!NOTE]
> Actuellement, seules les opérations au niveau de la machine virtuelle sont possibles par le biais de Powershell. La prise en charge des opérations au niveau du plan de récupération sera bientôt disponible.  Pour le moment, vous ne pouvez effectuer de basculement qu’au niveau de granularité « machine virtuelle protégée » et non au niveau du plan de récupération.
>
>

### <a name="vmm-prerequisites"></a>Configuration requise pour VMM
* Vous aurez besoin d'un serveur VMM exécuté sur System Center 2012 R2.
* Tout serveur VMM contenant des machines virtuelles à protéger doit exécuter le fournisseur Azure Site Recovery. Celui-ci est installé lors du déploiement d'Azure Site Recovery.
* Vous aurez besoin d'au moins un cloud sur le serveur VMM que vous souhaitez protéger. Le coud doit contenir :
  * un ou plusieurs groupes hôtes VMM ;
  * un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte.
  * une ou plusieurs machines virtuelles sur le serveur Hyper-V source.
* Pour en savoir plus sur la configuration des clouds VMM :
  * Lisez la documentation sur les clouds privés VMM dans les sections [Nouveautés sur le cloud privé dans System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) et [VMM 2012 et les clouds](http://go.microsoft.com/fwlink/?LinkId=324956).
  * En savoir plus sur la [Configuration de l'infrastructure de cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * Une fois vos éléments d’infrastructure cloud en place, apprenez à créer des clouds privés dans [Création d’un cloud privé dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) et [Procédure pas à pas : Création de clouds privés avec System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).

### <a name="hyper-v-prerequisites"></a>Conditions préalables liées à Hyper-V
* Les serveurs hôtes Hyper-V doivent exécuter au moins **Windows Server 2012** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012** et les dernières mises à jour doivent être installées.
* Si vous utilisez Hyper-V dans un cluster, notez que le service Broker du cluster n'est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker du cluster. Concernant l'option
* Pour obtenir des instructions, consultez [How to Configure Hyper-V Replica Broker](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx)(Configuration du service Broker de réplication Hyper-V).
* Tout cluster ou serveur hôte Hyper-V pour lequel vous souhaitez gérer la protection doit être inclus dans un cloud VMM.

### <a name="network-mapping-prerequisites"></a>Conditions préalables liées au mappage réseau
Quand vous protégez des machines virtuelles dans Azure, le mappage réseau relie les réseaux de machines virtuelles sur le serveur VMM source et les réseaux Azure cibles pour permettre ce qui suit :

* Toutes les machines qui basculent sur le même réseau peuvent se connecter les unes aux autres, quel que soit le plan de récupération auquel elles appartiennent.
* Si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter à d'autres machines virtuelles locales.
* Si vous ne configurez pas le mappage réseau, seules les machines virtuelles qui basculent dans le même plan de récupération peuvent se connecter les unes aux autres après le basculement vers Azure.

Si vous souhaitez déployer le mappage réseau, les conditions suivantes doivent être remplies :

* Les machines virtuelles que vous souhaitez protéger sur le serveur VMM source doivent être connectées à un réseau de machines virtuelles. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.
* Un réseau Azure auquel les machines virtuelles répliquées peuvent se connecter après le basculement. Vous sélectionnez ce réseau au moment du basculement. Le réseau doit être dans la même région que votre abonnement Azure Site Recovery.

Pour en savoir plus sur le mappage réseau, consultez :

* [Vue d’ensemble de la configuration de la mise en réseau logique dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Configuration de réseaux de machines virtuelles et de passerelles dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [How to configure and monitor virtual networks in Azure (Configuration et analyse des réseaux virtuels dans Azure)](https://azure.microsoft.com/documentation/services/virtual-network/)

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
1. Création d’un groupe de ressources dans Azure Resource Manager, si ce n’est déjà fait

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Créez un coffre Recovery Services et stockez l’objet de coffre ASR créé dans une variable (qui sera utilisée plus tard). Vous pouvez également récupérer l’objet de coffre ASR après la création à l’aide de l’applet de commande Get-AzureRMRecoveryServicesVault :-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définition du contexte du coffre Recovery Services

Définissez le contexte du coffre en exécutant la commande suivante.

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

## <a name="step-5-create-an-azure-storage-account"></a>Étape 5 : Création d’un compte de stockage Azure

Si vous n’avez pas de compte de stockage Azure, créez un compte avec activation de la géo-réplication en exécutant la commande suivante :

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Le compte de stockage doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Étape 6 : Installation de l'agent Azure Recovery Services
1. Téléchargez l’agent Azure Recovery Services à partir de [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) et installez-le sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous voulez protéger.
2. Exécutez la commande suivante sur l’ensemble des hôtes VMM :

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Étape 7 : Configuration des paramètres de protection de cloud
1. Créez un profil de protection dans Azure en exécutant la commande suivante :

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. Récupérez un conteneur de protection en exécutant les commandes suivantes :

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. Enregistrez les détails de la stratégie dans une variable à l’aide de la tâche qui a été créée et en mentionnant le nom convivial de la stratégie :

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Lancez l’association du conteneur de protection avec la stratégie de réplication :

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. Une fois la tâche terminée, exécutez la commande suivante :

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. Une fois la tâche terminée, exécutez la commande suivante :

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Pour vérifier que l'opération est terminée, suivez les étapes décrites dans la section [Suivi de l'activité](#monitor).

## <a name="step-8-configure-network-mapping"></a>Étape 8 : Configuration du mappage réseau
Avant de commencer le mappage réseau, vérifiez que les machines virtuelles sur le serveur VMM source sont connectées à un réseau de machines virtuelles. En outre, vous devez créer un ou plusieurs réseaux virtuels Azure.

Pour en savoir plus sur la création d’un réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell, consultez [Création d’un réseau virtuel avec une connexion de site à site VPN à l’aide d’Azure Resource Manager et de PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Notez que plusieurs réseaux de machines virtuelles peuvent être mappés à un seul réseau Azure. Si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux porte le même nom que celui du sous-réseau dans lequel se trouve la machine virtuelle source, la machine virtuelle de réplication est connectée à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle est connectée au premier sous-réseau du réseau.

1. La première commande récupère les serveurs pour le coffre Azure Site Recovery actuel. La commande stocke les serveurs Microsoft Azure Site Recovery dans la variable tableau $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. La deuxième commande récupère le réseau Site Recovery pour le premier serveur du tableau $Servers. La commande stocke les réseaux dans la variable $Networks.

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. La troisième commande obtient les réseaux virtuels Azure et stocke cette valeur dans la variable $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. L'applet de commande finale crée un mappage entre le réseau principal et le réseau de la machine virtuelle Azure. L'applet de commande fixe le réseau principal comme premier élément de $Networks. L’applet de commande spécifie un réseau de machines virtuelles comme premier élément de $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>Étape 9 : Activation de la protection des machines virtuelles
Une fois que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud.

 Notez les points suivants :

* Les machines virtuelles doivent répondre aux exigences liées à Azure. Pour en savoir plus sur ces dernières, consultez [Configuration requise et prise en charge](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) dans le guide de planification.
* Pour activer la protection, vous devez définir les propriétés du système d’exploitation et du disque du système d’exploitation pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l'aide d'un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et **Configuration matérielle** des propriétés de la machine virtuelle. Si vous ne définissez pas ces propriétés dans VMM, vous pourrez les configurer dans le portail Azure Site Recovery.

1. Afin d’activer la protection, exécutez la commande suivante pour récupérer le conteneur de protection :

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. Récupérez l’entité de protection (machine virtuelle) en exécutant les commandes suivantes :

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. Activez la récupération d’urgence de la machine virtuelle en exécutant la commande suivante :

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>Tester votre déploiement
Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et y exécuter un test de basculement. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez les points suivants :

* Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d’exécuter le test de basculement.
* Après le basculement, vous utilisez une adresse IP publique pour vous connecter à la machine virtuelle dans Azure avec le Bureau à distance. Dans ce cas, assurez-vous qu'aucune de vos stratégies de domaine ne vous empêche de vous connecter à une machine virtuelle avec une adresse publique.

Pour vérifier que l'opération est terminée, suivez les étapes décrites dans la section [Suivi de l'activité](#monitor).

### <a name="run-a-test-failover"></a>Exécuter un test de basculement
- Lancez le test de basculement en exécutant la commande suivante :

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Exécuter un basculement planifié
- Lancez le basculement planifié en exécutant la commande suivante :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié
- Lancez le basculement non planifié en exécutant la commande suivante :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

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
