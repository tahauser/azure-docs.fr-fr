---
title: "Configurer la récupération d’urgence pour des machines virtuelles Azure après la migration vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment préparer des machines pour configurer la récupération d’urgence entre des régions Azure après une migration vers Azure à l’aide d’Azure Site Recovery."
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 01/07/2018
ms.author: ponatara
ms.openlocfilehash: c06af21cd6e273b98c004e8bd0e6eac61ba7d644
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurer la récupération d’urgence pour des machines virtuelles Azure après la migration vers Azure 

>[!NOTE]
> La récupération d’urgence pour les machines virtuelles Azure à l’aide d’Azure Site Recovery est disponible en préversion.

Suivez les étapes de cet article une fois que vous avez [migré des machines locales vers des machines virtuelles Azure](tutorial-migrate-on-premises-to-azure.md) à l’aide du service [Site Recovery](site-recovery-overview.md). Cet article vous aide à préparer les machines virtuelles Azure pour configurer la récupération d’urgence avec une région Azure secondaire, à l’aide de Site Recovery.



## <a name="before-you-start"></a>Avant de commencer

Avant de configurer la récupération d’urgence, assurez-vous que la migration s’est effectuée correctement. Pour réussir une migration, après le basculement, sélectionnez l’option **Terminer la migration** pour chaque machine à migrer. 



## <a name="install-the-azure-vm-agent"></a>Installer l’agent de machine virtuelle Azure

Vous devez installer [l’agent de machine virtuelle](../virtual-machines/windows/agent-user-guide.md) Azure sur la machine virtuelle pour permettre au service Site Recovery de la répliquer.


1. Pour installer l’agent de machine virtuelle sur des machines virtuelles Windows, téléchargez et exécutez le [programme d’installation de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous devez avoir des privilèges Administrateur sur les machines virtuelles pour effectuer l’installation.
2. Pour installer l’agent de machine virtuelle sur des machines virtuelles Linux, installez la dernière version de [l’agent Linux](../virtual-machines/linux/agent-user-guide.md). Vous avez besoin de privilèges d’administrateur pour terminer l’installation. Nous vous recommandons de procéder à l’installation à partir de votre dépôt de distribution. Nous vous déconseillons d’installer l’agent de machine virtuelle Linux directement à partir de GitHub. 


## <a name="validate-the-installation-on-windows-vms"></a>Vérifier l’installation sur des machines virtuelles Windows

1. Sur la machine virtuelle Azure, dans le dossier C:\WindowsAzure\Packages, vous devez voir le fichier WaAppAgent.exe.
2. Cliquez avec le bouton droit sur ce fichier et, dans **Propriétés**, sélectionnez l’onglet **Détails**.
3. Vérifiez que le champ **Version du produit** indique 2.6.1198.718 ou une version ultérieure.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migration de machines virtuelles ou serveurs physiques VMware

Si vous migrez des machines virtuelles VMware locales (ou des serveurs physiques) vers Azure, gardez à l’esprit les points suivants :

- Installez l’agent de machine virtuelle Azure uniquement si la version 9.6 ou antérieure du service Mobilité est installée sur la machine migrée.
- Sur les machines virtuelles Windows ayant la version 9.7.0.0 ou ultérieure du service Mobilité, le programme d’installation du service installe la dernière version disponible de l’agent de machine virtuelle Azure. Quand vous effectuez la migration, ces machines virtuelles remplissent déjà les conditions préalables à l’installation de l’agent pour toutes les extensions de machine virtuelle, dont l’extension Site Recovery.
- Vous devez désinstaller manuellement le service Mobilité de la machine virtuelle Azure à l’aide d’une des méthodes suivantes. Redémarrez la machine virtuelle avant de configurer la réplication.
    - Pour Windows, dans le Panneau de configuration > **Ajout/Suppression de programmes**, désinstallez **Service Mobilité/Serveur cible maître Microsoft Azure Site Recovery**. À partir d’une invite de commandes avec élévation de privilèges, exécutez :
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Pour Linux, connectez-vous en tant qu’utilisateur racine. Sur un terminal, accédez à **/user/local/ASR** et exécutez la commande suivante :
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>Étapes suivantes

[Répliquer rapidement](azure-to-azure-quickstart.md) une machine virtuelle Azure dans une région secondaire.
