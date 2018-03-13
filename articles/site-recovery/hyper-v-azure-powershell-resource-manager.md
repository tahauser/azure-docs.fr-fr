---
title: "Répliquer des machines virtuelles Hyper-V avec PowerShell et Azure Resource Manager | Microsoft Docs"
description: "Automatisez la réplication de machines virtuelles Hyper-V sur Azure avec Azure Site Recovery à l’aide de PowerShell et d’Azure Resource Manager."
services: site-recovery
author: bsiva
manager: abhiag
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: bsiva
ms.openlocfilehash: 5269fa528b6c32576b9cf1fb945ebf85b41ce819
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Configurer la récupération d’urgence dans Azure pour les machines virtuelles Hyper-V à l’aide de PowerShell et de Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles Azure, de machines virtuelles locales et de serveurs physiques.

Cet article décrit comment utiliser Windows PowerShell, ainsi que Azure Resource Manager pour répliquer des ordinateurs virtuels Hyper-V vers Azure. L’exemple utilisé dans cet article vous explique comment répliquer une machine virtuelle unique exécutée sur un hôte Hyper-V sur Azure.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell fournit des applets de commande pour gérer Azure à l’aide de Windows PowerShell. Les applets de commande PowerShell de Site Recovery, disponibles avec Azure PowerShell pour Azure Resource Manager, vous permettent de protéger et récupérer vos serveurs dans Azure.

Vous n’avez pas besoin d’être un expert de PowerShell pour utiliser cet article, mais vous devez tout de même connaître des concepts de base, tels que les modules, les applets de commande et les sessions. Lisez [Prise en main de Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx) et [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Les partenaires Microsoft membres du programme Cloud Solution Provider (CSP) peuvent configurer et gérer la protection des serveurs clients sur leurs abonnements CSP respectifs (abonnements des locataires).
>
>

## <a name="before-you-start"></a>Avant de commencer
Assurez-vous que les conditions préalables sont remplies :

* Un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). Vous pouvez aussi consulter la [Tarification Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Pour plus d’informations sur cette version et la méthode d’installation, consultez [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* Les modules [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) et [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/). Vous pouvez obtenir les dernières versions de ces modules à partir de la [galerie PowerShell](https://www.powershellgallery.com/)

De plus, l’exemple décrit dans cet article présente les conditions préalables suivantes :

* Un hôte Hyper-V exécuté sous Windows Server 2012 R2 ou Microsoft Hyper-V Server 2012 R2 et hébergeant une ou plusieurs machines virtuelles. Les serveurs Hyper-V doivent être connectés à Internet, directement ou à travers un proxy.
* Les machines virtuelles que vous souhaitez répliquer doivent respecter ces [conditions préalables](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="step-1-sign-in-to-your-azure-account"></a>Étape 1 : Connexion à votre compte Azure

1. Ouvrez une console PowerShell et exécutez la commande suivante pour vous connecter à votre compte Azure. L’applet de commande permet d’afficher une page web qui vous demande les informations d’identification de votre compte : **Login-AzureRmAccount**.
    - Vous pouvez également inclure vos informations d’identification de compte en tant que paramètre dans l’applet de commande **Login-AzureRmAccount**, à l’aide du paramètre **-Credential**.
    - Si vous êtes partenaire CSP travaillant pour le compte d’un locataire, spécifiez le client en tant que locataire à l’aide de son ID locataire ou de son nom de domaine principal. Par exemple : **Login-AzureRmAccount -Tenant "fabrikam.com"**
2. Associez l’abonnement que vous souhaitez utiliser avec le compte, car un compte peut compter plusieurs abonnements :

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. Utilisez les commandes suivantes pour vérifier que votre abonnement vous autorise à utiliser les fournisseurs Azure pour Recovery Services et Site Recovery :

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices``Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. Vérifiez que dans la sortie de commande **RegistrationState** est défini sur **Inscrit**, vous pouvez ensuite passer à l’étape 2. Dans le cas contraire, vous devez inscrire le fournisseur manquant dans votre abonnement en exécutant ces commandes :

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery``Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Vérifiez que les fournisseurs ont été correctement inscrits à l’aide des commandes suivantes :

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-vault"></a>Étape 2 : Configuration du coffre

1. Créez un groupe de ressources Azure Resource Manager dans lequel vous allez créer le coffre, ou utilisez un groupe de ressources existant. Créez un nouveau groupe de ressources de la manière suivante. La variable $ResourceGroupName contient le nom du groupe de ressources que vous souhaitez créer et la variable $Geo contient la région Azure dans laquelle créer le groupe de ressources (par exemple, « Sud du Brésil »).

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. Pour obtenir une liste de groupes de ressources dans votre abonnement, exécutez l’applet de commande **Get-AzureRmResourceGroup**.
2. Créez un coffre Azure Recovery Services de la manière suivante :

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Vous pouvez récupérer la liste des coffres existants avec l’applet de commande **Get-AzureRmRecoveryServicesVault**.


## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définition du contexte du coffre Recovery Services

Définissez le contexte d’archivage comme suit :

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Étape 4 : Créer un site Hyper-V

1. Créez un nouveau site Hyper-V comme suit :

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. Cette applet de commande démarre une tâche Site Recovery pour créer le site et renvoie un objet de tâche Site Recovery. Attendez que la tâche soit terminée, puis vérifiez son exécution.
3. Utilisez **l’applet de commande Get-AzureRmSiteRecoveryJob** pour récupérer l’objet de tâche et vérifier l’état actuel de la tâche.
4. Générez et téléchargez une clé d’inscription pour le site comme suit :

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. Copiez la clé téléchargée sur l’hôte Hyper-V. Vous avez besoin de la clé pour inscrire l’hôte Hyper-V sur le site.

## <a name="step-5-install-the-provider-and-agent"></a>Étape 5 : Installez le fournisseur et l’agent

1. Téléchargez le programme d’installation de la dernière version du fournisseur sur le site de [Microsoft](https://aka.ms/downloaddra).
2. Exécutez le programme d’installation sur l’ordinateur hôte Hyper-V.
3. À la fin de l’installation, passez à l’étape d’inscription.
4. Quand vous y êtes invité, renseignez la clé que vous avez téléchargée et terminez l’inscription de l’hôte Hyper-V.
5. Vérifiez que l’hôte Hyper-V a bien été inscrit sur le site comme suit :

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>Étape 6 : Créer une stratégie de réplication

Avant de commencer, notez que le compte de stockage spécifié doit se trouver dans la même région Azure que le coffre et doit utiliser la fonction de géo-réplication.

1. Créez une stratégie de réplication comme suit :

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Vérifiez la tâche renvoyée pour vous assurer que la stratégie de réplication a bien été créée.

3. Récupérez le conteneur de protection correspondant au site comme suit :

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Associez le conteneur de protection avec la stratégie de réplication comme suit :

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

4. Attendez que la tâche d’association se termine.

## <a name="step-7-enable-vm-protection"></a>Étape 7 : Activation de la protection de machine virtuelle

1. Récupérez l’entité de protection correspondant à la machine virtuelle que vous souhaitez protéger comme suit :

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Protéger la machine virtuelle. Si la machine virtuelle que vous protégez possède plusieurs disques, spécifiez le disque de système d’exploitation à l’aide du paramètre *OSDiskName* .

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. Attendez que les machines virtuelles basculent à l’état protégé après la réplication initiale. Cette opération prendra un certain temps compte tenu de certains facteurs, notamment la quantité de données à répliquer et la bande passante en amont disponible pour Azure. Lorsque l’état protégé est en place, les paramètres State et StateDescription de la tâche s’actualisent comme suit : 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Mettez à jour les propriétés de récupération (telles que la taille du rôle de machine virtuelle) et le réseau Azure auquel attacher la carte réseau de la machine virtuelle après le basculement.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Étape 8 : Exécution d’un test de basculement
1. Exécutez un test de basculement, en procédant comme suit :

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Vérifiez que la machine virtuelle de test est bien créée dans Azure. La tâche de test de basculement est suspendue après la création de la machine virtuelle de test dans Azure.
3. Pour nettoyer et terminer le test de basculement, exécutez :

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](https://msdn.microsoft.com/library/azure/mt637930.aspx) sur Azure Site Recovery avec les applets de commande PowerShell Azure Resource Manager.
