---
title: Supprimer un archivage Site Recovery
description: Découvrez comment supprimer un archivage Azure Site Recovery, en fonction du scénario de Site Recovery.
service: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 89ab1e7c8b2fa0f4014ecfa0e677b398e601e6fa
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="delete-a-site-recovery-vault"></a>Supprimer un archivage Site Recovery
Des dépendances peuvent vous empêcher de supprimer un archivage Azure Site Recovery. Les mesures à prendre varient en fonction du scénario de Site Recovery : VMware vers Azure, Hyper-V (avec et sans System Center Virtual Machine Manager) vers Azure, et Sauvegarde Microsoft Azure. Pour supprimer un archivage utilisé dans Sauvegarde Microsoft Azure, voir [Supprimer un archivage Recovery Services](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>Supprimer un archivage Site Recovery 
Pour supprimer l’archivage, suivez les étapes recommandées pour votre scénario.

### <a name="vmware-vms-to-azure"></a>Machines virtuelles VMware vers Azure

1. Supprimez toutes les machines virtuelles protégées en suivant les étapes décrites dans [Désactiver la protection d’un VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Supprimez toutes les stratégies de réplication en suivant les étapes décrites dans [Supprimer une stratégie de réplication](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Supprimez les références à vCenter en suivant les étapes décrites dans la section [Supprimer un serveur vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Supprimez le serveur de configuration en suivant les étapes décrites dans [Désaffecter un serveur de configuration](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Supprimez l’archivage.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Machines virtuelles Hyper-V (avec Virtual Machine Manager) vers Azure
1. Supprimez toutes les machines virtuelles protégées en suivant les étapes décrites dans [Désactiver la protection d’une machine virtuelle Hyper-V dans un cloud VMM](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Dissociez et supprimez toutes les stratégies de réplication en accédant à votre coffre -> **Infrastructure Site Recovery** -> **For System Center VMM (Pour System Center VMM)** -> **Stratégies de réplication**

3.  Supprimez les références aux serveurs Virtual Machine Manager en suivant les étapes décrites dans [Annuler l’inscription d’un serveur VMM connecté](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Supprimez l’archivage.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Machines virtuelles Hyper-V (sans Virtual Machine Manager) vers Azure
1. Supprimez toutes les machines virtuelles protégées en suivant les étapes décrites dans [Désactiver la protection d’une machine virtuelle Hyper-V dans un site Hyper-V](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Dissociez et supprimez toutes les stratégies de réplication en accédant à votre coffre -> **Infrastructure Site Recovery** -> **For Hyper-V Sites (Pour des sites Hyper-V)** -> **Stratégies de réplication**

3. Supprimez les références aux serveurs Hyper-V en suivant les étapes décrites dans [Annuler l’inscription d’un ordinateur hôte Hyper-V](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Supprimez le site Hyper-V.

5. Supprimez l’archivage.


## <a name="use-powershell-to-force-delete-the-vault"></a>Utiliser PowerShell pour forcer la suppression de l’archivage 

> [!Important]
> Si vous testez le produit et ne vous inquiétez pas de perdre des données, utilisez la méthode de suppression de force pour supprimer rapidement l’archivage et toutes ses dépendances.
> La commande PowerShell supprime tout le contenu de l’archivage et n’est **pas réversible**.

Pour supprimer l’archivage Site Recovery, même s’il contient des éléments protégés, utilisez les commandes suivantes :

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault
