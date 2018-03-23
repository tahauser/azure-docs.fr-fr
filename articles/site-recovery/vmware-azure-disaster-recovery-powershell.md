---
title: Répliquer et basculer des machines virtuelles VMware vers Azure à l’aide de PowerShell dans Azure Site Recovery | Microsoft Docs
description: Découvrez comment configurer la réplication et le basculement vers Azure pour les machines virtuelles VMware à l’aide de PowerShell dans Azure Site Recovery.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 9a2edb874ca969813a4f826cd80ef855e391dc4b
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="replicate-and-fail-over-vmware-vms-to-azure-with-powershell"></a>Répliquer et basculer des machines virtuelles VMware vers Azure avec PowerShell

Dans cet article, vous découvrez comment répliquer et basculer des machines virtuelles VMware vers Azure à l’aide d’Azure PowerShell. 

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> - Créez un coffre Recovery Services.
> - Définir le contexte d’archivage.
> - Vérifier que le serveur de configuration et le serveur de traitement de montée en puissance sont inscrits dans le coffre.
> - Créer une stratégie de réplication et l’associer au serveur de configuration.
> - Ajouter un serveur vCenter et découvrir les machines virtuelles VMware.
> - Créer des comptes de stockage dans lesquels répliquer les machines virtuelles.
> - Répliquer des machines virtuelles VMware vers les comptes de stockage Azure.
> - Configurer les paramètres de basculement pour la réplication des machines virtuelles.
> - Effectuer un test de basculement, puis valider et supprimer le test de basculement.
> - Basculer vers Azure.

## <a name="prerequisites"></a>Prérequis


Avant de commencer :
- Assurez-vous que vous comprenez [l’architecture et les composants du scénario](vmware-azure-architecture.md).
- Vérifiez les [exigences de prise en charge](site-recovery-support-matrix-to-azure.md) pour tous les composants.
- Vous avez la version 5.0.1 ou une version supérieure du module AzureRm PowerShell. Si vous devez installer ou mettre à niveau Azure PowerShell, consultez le [guide sur l’installation et la configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Connexion à un abonnement Microsoft Azure

Connectez-vous à votre abonnement Azure à l’aide de la cmdlet Login-AzureRmAccount.

```azurepowershell
Login-AzureRmAccount
```
Sélectionnez l’abonnement Azure vers lequel répliquer vos machines virtuelles VMware. Utilisez la cmdlet Get-AzureRmSubscription pour obtenir la liste des abonnements Azure auxquels vous avez accès. Sélectionnez l’abonnement Azure à utiliser à l’aide de la cmdlet Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

* Créez un groupe de ressources dans lequel créer le coffre Recovery Services. Dans l’exemple ci-dessous, le groupe de ressources est nommé VMwareDRtoAzurePS et créé dans la région East Asia (Asie orientale).

```azurepowershell
New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
```
```
ResourceGroupName : VMwareDRtoAzurePS
Location          : eastasia
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
```
   
* Créez un coffre Recovery Services. Dans l’exemple ci-dessous, le coffre Recovery Services est nommé VMwareDRToAzurePs et créé dans la région East Asia (Asie orientale), ainsi que dans le groupe de ressources créé à l’étape précédente.

```azurepowershell
New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
```
```
Name              : VMwareDRToAzurePs
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
Type              : Microsoft.RecoveryServices/vaults
Location          : eastasia
ResourceGroupName : VMwareDRToAzurePs
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 

* Téléchargez la clé d’inscription du coffre. La clé d’inscription du coffre est utilisée pour inscrire le serveur de configuration local dans ce coffre. L’inscription fait partie du traitement d’installation du logiciel du serveur de configuration.

```azurepowershell
#Get the vault object by name and resource group and save it to the $vault PowerShell variable 
$vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

#Download vault registration key to the path C:\Work
Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
```
```
FilePath
--------
C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
```

Utiliser la clé d’inscription du coffre téléchargée, et suivez les étapes décrites dans les articles répertoriés ci-dessous pour terminer l’installation et l’inscription du serveur de configuration.
* [Sélectionner vos objectifs en matière de protection](vmware-azure-set-up-source.md#choose-your-protection-goals)
* [Configurer l’environnement source](vmware-azure-set-up-source.md#set-up-the-configuration-server) 

## <a name="set-the-vault-context"></a>Définir le contexte du coffre

> [!TIP]
> Le module Azure Site Recovery PowerShell (module AzureRm.RecoveryServices.SiteRecovery) est fourni avec des alias faciles à utiliser pour la plupart des cmdlets. Les cmdlets du module prennent le format *\<Opération>-**AzureRmRecoveryServicesAsr**\<Objet>* et possèdent des alias équivalents au format *\<Opération>-**ASR**\<Objet>*. Cet article utilise les alias de cmdlet afin de faciliter la lecture.

Définissez le contexte d’archivage à l’aide de la cmdlet Set-ASRVaultContext. Ensuite, les opérations suivantes d’Azure Site Recovery dans la session PowerShell sont effectuées dans le contexte du coffre sélectionné. Dans l’exemple ci-dessous, les détails du coffre obtenus via la variable $vault servent à spécifier le contexte d’archivage pour la session PowerShell.
 ```azurepowershell
Set-ASRVaultContext -Vault $vault
```
```
ResourceName      ResourceGroupName ResourceNamespace          ResouceType
------------      ----------------- -----------------          -----------
VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
```

Les sections suivantes de cet article supposent que le contexte d’archivage pour les opérations Azure Site Recovery a été défini.

## <a name="validate-that-your-configuration-server-and-scale-out-process-servers-are-registered-to-the-vault"></a>Vérifier que le serveur de configuration et les serveurs de traitement de montée en puissance sont inscrits dans le coffre

 Hypothèses :
- Un serveur de configuration nommé *ConfigurationServer* a été inscrit dans ce coffre.
- Un serveur de traitement supplémentaire nommé *ScaleOut-ProcessServer* a été inscrit dans *ConfigurationServer*.
- Des comptes nommés *vCenter_account*, *WindowsAccount* et *LinuxAccount* ont été définis sur le serveur de configuration. Ces comptes sont utilisés pour ajouter le serveur vCenter afin de découvrir les machines virtuelles et pour installer (push) le logiciel du service Mobilité sur les serveurs Windows et Linux qui doivent être répliqués.

Les serveurs de configuration inscrits sont représentés par un objet de structure dans Azure Site Recovery. À cette étape, obtenez la liste des objets de structure dans le coffre et identifiez le serveur de configuration.

```azurepowershell
# Verify that the Configuration server is successfully registered to the vault
$ASRFabrics = Get-ASRFabric
$ASRFabrics.count
```
```
1
```
```azurepowershell
#Print details of the Configuration Server
$ASRFabrics[0]
```
```
Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
FriendlyName          : ConfigurationServer
ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                        /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
FabricType            : VMware
SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
```

* Identifiez les serveurs de traitement qui peuvent être utilisés pour répliquer des machines.

```azurepowershell
$ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
for($i=0; $i -lt $ProcessServers.count; $i++) {
 "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
}
```
```
0     ScaleOut-ProcessServer
1     ConfigurationServer
```

Dans la sortie ci-dessus, ***$ProcessServers[0]*** correspond à *ScaleOut-ProcessServer*, et ***$ProcessServers[1]*** correspond au rôle de serveur de traitement sur *ConfigurationServer*.

* Identifiez les comptes qui ont été définis sur le serveur de configuration.

```azurepowershell
$AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
#Print the account details
$AccountHandles
```
```
AccountId AccountName
--------- -----------
1         vCenter_account
2         WindowsAccount
3         LinuxAccount
```

Dans la sortie ci-dessus, ***$AccountHandles[0]*** correspond au compte *vCenter_account*, ***$AccountHandles[1]*** au compte *WindowsAccount*, et ***$AccountHandles[2]*** au compte *LinuxAccount*.

## <a name="create-a-replication-policy-and-map-it-for-use-with-the-configuration-server"></a>Créer une stratégie de réplication et l’associer au serveur de configuration

À cette étape, deux stratégies de réplication sont créées. La première sert à répliquer les machines virtuelles VMware vers Azure, et la seconde sert à répliquer sur les machines virtuelles basculées s’exécutant dans Azure vers le site VMware local.

> [!NOTE]
> La plupart des opérations Azure Site Recovery sont exécutées de façon asynchrone. Lorsque vous lancez une opération, un travail Azure Site Recovery est envoyé et un objet de suivi de travail est renvoyé. Cet objet de suivi de tâche permet de surveiller l’état de l’opération.

* Créez une stratégie de réplication nommée *ReplicationPolicy* pour répliquer les machines virtuelles VMware vers Azure avec les propriétés spécifiées.

```azurepowershell
$Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

# Track Job status to check for completion
while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
}

#Display job status
$Job_PolicyCreate
```
```
Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                   9-479f-430d-b76b-6bc7eb2d0b3e
Type             :
JobType          : AddProtectionProfile
DisplayName      : Create replication policy
ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:49:24 AM
EndTime          : 11/24/2017 2:49:23 AM
TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
TargetObjectType : ProtectionProfile
TargetObjectName : ReplicationPolicy
AllowedActions   :
Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
Errors           : {}
```

* Créez une stratégie de réplication à utiliser pour la restauration à partir d’Azure vers le site de VMware local.

```azurepowershell
$Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
```

Utilisez les détails du travail dans *$Job_FailbackPolicyCreate* pour suivre l’opération jusqu’à la fin.

* Créez un mappage de conteneur de protection pour mapper les stratégies de réplication avec le serveur de configuration.

```azurepowershell
#Get the protection container corresponding to the Configuration Server
$ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

#Get the replication policies to map by name.
$ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
$FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

# Associate the replication policies to the protection container corresponding to the Configuration Server. 

$Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
}
$Job_AssociatePolicy.State

$Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
}
$Job_AssociateFailbackPolicy.State

```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Ajouter un serveur vCenter et découvrir des machines virtuelles

Ajoutez un serveur vCenter à partir d’une adresse IP ou d’un nom d’hôte. Le paramètre **-port** spécifie le port auquel se connecter sur le serveur vCenter, le paramètre **-nom** spécifie un nom convivial à utiliser pour le serveur vCenter, et le paramètre **-compte** spécifie le gestionnaire de compte sur le serveur de configuration à utiliser pour découvrir les machines virtuelles managées par le serveur vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) { 
        sleep 30; 
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts"></a>Créer des comptes de stockage

À cette étape, les comptes de stockage à utiliser pour la réplication sont créés. Ces comptes de stockage sont utilisés ultérieurement pour répliquer les machines virtuelles. Assurez-vous que les comptes de stockage sont créés dans la même région Azure que le coffre. Vous pouvez ignorer cette étape si vous envisagez d’utiliser un compte de stockage existant pour la réplication.

> [!NOTE]
> Lors de la réplication de machines virtuelles locales vers un compte de stockage Premium, vous devez spécifier un compte de stockage standard supplémentaire (compte de stockage de journal). Le compte de stockage de journal conserve des journaux de réplication en tant que stockage intermédiaire jusqu’à ce que les journaux puissent être appliqués à la cible de stockage Premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-virtual-machines"></a>Répliquer des machines virtuelles VMware

La découverte des machines virtuelles à partir du serveur vCenter prend environ 15 à 20 minutes. Ensuite, un objet d’élément protégeable est créé dans Azure Site Recovery pour chaque machine virtuelle découverte. À cette étape, trois machines virtuelles découvertes sont répliquées vers les comptes de stockage Azure créés à l’étape précédente.

Vous aurez besoin des détails suivants pour protéger une machine virtuelle découverte :
* Élément protégeable à répliquer.
* Compte de stockage vers lequel répliquer la machine virtuelle. En outre, un stockage de journal est nécessaire pour protéger les machines virtuelles sur un compte de stockage Premium.
* Serveur de traitement à utiliser pour la réplication. La liste des serveurs de traitement disponibles a été récupérée et enregistrée dans les variables ***$ProcessServers[0]*** *(ScaleOut-ProcessServer)* et ***$ProcessServers[1]*** *(ConfigurationServer)*.
* Compte à utiliser pour installer (push) le logiciel du service Mobilité sur les machines. La liste des comptes disponibles a été récupérée et stockée dans la variable ***$AccountHandles***.
* Mappage de conteneur de protection pour la stratégie de réplication à utiliser pour la réplication.
* Groupe de ressources dans lequel les machines virtuelles doivent être créées lors du basculement.
* (Facultatif) Réseau virtuel Azure et sous-réseau auquel la machine virtuelle basculée doit être connectée.

Maintenant, répliquez les machines virtuelles suivantes à l’aide des paramètres spécifiés dans ce tableau.


|Machine virtuelle  |Serveur de traitement        |Compte de stockage              |Compte de stockage de journal  |Stratégie           |Compte pour l’installation du service Mobilité|Groupe de ressources cible  | Réseau virtuel cible  |Sous-réseau cible  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   

 
```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableRepication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableRepication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableRepication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

Une fois que la tâche d’activation de la réplication est effectuée, la réplication initiale est démarrée pour les machines virtuelles. La réplication initiale peut prendre un certain temps en fonction de la quantité de données à répliquer et de la bande passante disponible pour la réplication. Une fois la réplication initiale terminée, la machine virtuelle passe dans un état protégé. Une fois que la machine est dans cet état protégé, vous pouvez effectuer un test de basculement pour la machine virtuelle, l’ajouter aux plans de récupération, etc.

Vous pouvez vérifier l’état et l’intégrité de la réplication pour la machine virtuelle avec la cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings-for-replicating-virtual-machines"></a>Configurer les paramètres de basculement pour la réplication des machines virtuelles

Les paramètres de basculement pour les machines protégées peuvent être mis à jour à l’aide de la cmdlet Set-ASRReplicationProtectedItem. Voici certains des paramètres concernés :
* Nom de la machine virtuelle à créer lors du basculement
* Taille de la machine virtuelle à créer lors du basculement
* Réseau virtuel Azure et sous-réseau auquel les cartes réseau de la machine virtuelle doivent être connectées lors du basculement
* Basculement vers les disques managés
* Application d’Azure Hybrid Use Benefit
* Affectation d’une adresse IP statique à partir du réseau virtuel cible à la machine virtuelle lors du basculement

Dans cet exemple, nous mettons à jour la taille de la machine virtuelle à créer lors du basculement de la machine virtuelle *Win2K12VM1* et spécifions que celle-ci utilise des disques managés lors du basculement.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Effectuer un test de basculement, puis valider et supprimer le test de basculement

```azurepowershell
#Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

#Get details of the test failover virtual network to be used
TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

#Start the test failover operation
$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
```
Une fois le travail de test de basculement terminé, vous remarquerez qu’une machine virtuelle avec le suffixe *« -Test »* (Win2K12VM1-Test, dans ce cas) accolé à son nom est créée dans Azure. 

Vous pouvez maintenant vous connecter à la machine virtuelle basculée de test et valider le test de basculement.

Supprimez le test de basculement à l’aide de la cmdlet Start-ASRTestFailoverCleanupJob. Cette opération supprime la machine virtuelle créée dans le cadre de l’opération de test de basculement.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
```

## <a name="failover-to-azure"></a>Basculement vers Azure

À cette étape, nous basculons la machine virtuelle Win2K12VM1 vers un point de récupération spécifique.

```azurepowershell
# Get the list of available recovery points for Win2K12VM1
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
```
```
CrashConsistent 11/24/2017 5:28:25 PM
```
```azurepowershell

#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 60;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
$Job_Failover.State
```
```
Succeeded
```

Après le basculement, vous pouvez valider cette opération et configurer la réplication inverse depuis Azure vers sur le site VMware local.

## <a name="next-steps"></a>Étapes suivantes
Affichez la [référence Azure Site Recovery PowerShell](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) pour savoir comment effectuer d’autres tâches, telles que la création de plans de récupération et le test de basculement des plans de récupération via PowerShell.
