---
title: "Préparer des machines pour configurer la récupération d’urgence entre régions Azure après une migration vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment préparer des machines pour configurer la récupération d’urgence entre régions Azure après une migration vers Azure à l’aide d’Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: ponatara
ms.openlocfilehash: 2943d33744a29da7ffd1f1544fc81696195ecce1
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>Répliquer des machines virtuelles Azure vers une autre région après migration vers Azure à l’aide d’Azure Site Recovery

>[!NOTE]
> La réplication Azure Site Recovery pour les machines virtuelles Azure est actuellement en préversion.

## <a name="overview"></a>Vue d’ensemble

Cet article vous aide à préparer des machines virtuelles Azure pour la réplication entre deux régions Azure après que ces machines ont été migrées à partir d’un environnement local vers Azure à l’aide d’Azure Site Recovery.

## <a name="disaster-recovery-and-compliance"></a>Récupération d’urgence et conformité
Aujourd’hui, de plus en plus d’entreprises migrent leurs charges de travail vers Azure. Avec des entreprises déplaçant des charges de travail de production locales stratégiques vers Azure, la configuration d’une récupération d’urgence pour ces charges de travail est obligatoire à des fins de conformité et pour éviter toute interruption dans une région Azure.

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>Étapes de préparation de machines migrées pour la réplication
Pour préparer des machines migrées à la configuration de la réplication vers une autre région Azure :

1. Achevez la migration.
2. Installez l’agent Azure si nécessaire.
3. Supprimez le service Mobilité.  
4. Redémarrez la machine virtuelle.

Ces étapes sont décrites de façon plus détaillée dans les sections suivantes.

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>Étape 1 : Migrer des charges de travail s’exécutant sur des machines virtuelles Hyper-V, des machines virtuelles VMware et des serveurs physiques, pour les exécuter sur des machines virtuelles Azure

Pour configurer la réplication et migrer vos charges de travail Hyper-V, VMware et physiques locales vers Azure, suivez les étapes décrites dans l’article [Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Azure Site Recovery](site-recovery-migrate-azure-to-azure.md). 

Après la migration, vous n’avez pas besoin de valider ou de supprimer un basculement. Au lieu de cela, sélectionnez l’option **Terminer la migration** pour chacune des machines que vous souhaitez migrer :
1. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle et cliquez sur **Terminer la migration**. Cliquez sur **OK** pour achever l’étape. Vous pouvez suivre la progression dans les propriétés de machine virtuelle en surveillant le travail Terminer la migration dans **Tâches Azure Site Recovery**.
2. L’action **Terminer la migration** achève le processus de migration, supprime la réplication de la machine et arrête la facturation de Site Recovery pour celle-ci.

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>Étape 2 : Installer l’agent de machine virtuelle Azure sur la machine virtuelle
L’[agent de machine virtuelle](../../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux) Azure doit être installé sur la machine virtuelle pour que l’extension Site Recovery fonctionne et pour aider à protéger la machine virtuelle.

>[!IMPORTANT]
>Depuis la version 9.7.0.0, sur les machines virtuelles Windows, le programme d’installation du service Mobilité installe également l’agent de machine virtuelle Azure le plus récent disponible. Lors de la migration, la machine virtuelle remplit les conditions préalables à l’installation de l’agent pour l’utilisation de toute extension de machine virtuelle, dont l’extension Site Recovery. L’agent de machine virtuelle Azure doit être installé manuellement uniquement si le service Mobilité installé sur la machine migrée est de la version 9.6 ou d’une version antérieure.

Le tableau suivant fournit des informations supplémentaires sur l’installation de l’agent de machine virtuelle et la validation de son installation :

| **Opération** | **Windows** | **Linux** |
| --- | --- | --- |
| Installation de l’agent de machine virtuelle |Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous avez besoin de privilèges d’administrateur pour terminer l’installation. |Installez l’[agent Linux](../../virtual-machines/linux/agent-user-guide.md) le plus récent. Vous avez besoin de privilèges d’administrateur pour terminer l’installation. Nous vous recommandons d’installer l’agent à partir de votre référentiel de distribution. Nous *déconseillons* d’installer l’agent de machine virtuelle Linux directement à partir de GitHub.  |
| Validation de l’installation de l’agent de machine virtuelle |1. Accédez au dossier C:\WindowsAzure\Packages sur la machine virtuelle Azure. Vous devriez voir le fichier WaAppAgent.exe. <br>2. Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ **Version du produit** doit indiquer 2.6.1198.718 ou une version ultérieure. |N/A |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>Étape 3 : Supprimer le service Mobilité de la machine virtuelle migrée

Si vous avez migré vos machines ou serveurs physiques VMware locaux sur Windows/Linux, vous devez supprimer/désinstaller manuellement le service Mobilité de la machine virtuelle migrée.

>[!IMPORTANT]
>Cette étape n’est pas requise pour les machines virtuelles Hyper-V migrées vers Azure.

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>Désinstaller le service Mobilité sur un ordinateur Windows Server
Utilisez l’une des méthodes suivantes pour désinstaller le service Mobilité sur un ordinateur Windows Server.

##### <a name="uninstall-by-using-the-windows-ui"></a>Désinstaller à l’aide de l’interface utilisateur Windows
1. Dans le Panneau de configuration, sélectionnez **Programmes**.
2. Sélectionnez **Service Mobilité/Serveur cible maître Microsoft Azure Site Recovery**, puis sélectionnez **Désinstaller**.

##### <a name="uninstall-at-a-command-prompt"></a>Désinstallation avec une invite de commandes
1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Pour désinstaller le service Mobilité, exécutez la commande suivante :

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>Désinstaller le service Mobilité sur un ordinateur Linux
1. Sur votre serveur Linux, connectez-vous en tant qu’utilisateur **root**.
2. Dans un terminal, accédez à /user/local/ASR.
3. Pour désinstaller le service Mobilité, exécutez la commande suivante :

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>Étape 4 : Redémarrer la machine virtuelle

Après avoir désinstallé le service Mobilité, redémarrez la machine virtuelle avant de configurer la réplication vers une autre région Azure.


## <a name="next-steps"></a>étapes suivantes
- Commencer à protéger vos charges de travail en [répliquant des machines virtuelles Azure](../azure-to-azure-quickstart.md).
- Découvrir l’[Aide à la mise en réseau pour la réplication des machines virtuelles Azure](../site-recovery-azure-to-azure-networking-guidance.md).
