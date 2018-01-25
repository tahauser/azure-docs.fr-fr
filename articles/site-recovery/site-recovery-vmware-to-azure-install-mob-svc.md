---
title: "Installation du service Mobilité (VMware ou serveur physique vers Azure) | Microsoft Docs"
description: "Découvrez comment installer l’agent du service Mobilité pour protéger vos machines locales."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 01/11/2018
ms.author: anoopkv
ms.openlocfilehash: 939115aedd624dde637f00c02865b1adab47c7c4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="install-mobility-service-vmware-or-physical-to-azure"></a>Installation du service Mobilité (VMware ou serveur physique vers Azure)
Le service de mobilité Azure Site Recovery capture les écritures de données sur un ordinateur, puis les transfère au serveur de traitement. Déployez le service Mobilité sur chaque ordinateur (machine virtuelle VMware ou serveur physique) que vous souhaitez répliquer vers Azure. Vous pouvez déployer le service Mobilité pour les serveurs que vous souhaitez protéger à l’aide des méthodes suivantes :


* [Installation du service Mobilité à l’aide d’outils de déploiement de logiciels tels que System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
* [Installation du Service Mobilité à l’aide d’Azure Automation et de la Configuration de l’état souhaité (Automation DSC)](site-recovery-automate-mobility-service-install.md)
* [Installation du service Mobilité manuellement à l’aide de l’interface utilisateur graphique](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)
* [Installation manuelle du service Mobilité à l’aide d’une ligne de commande](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)
* [Installation du service Mobilité à l’aide de l’installation de transmission de type push à partir de Site Recovery](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> À compter de la version 9.7.0.0, sur les machines virtuelles Windows, le programme d’installation du service Mobilité installe également [l’agent Azure VM](../virtual-machines/windows/extensions-features.md#azure-vm-agent) le plus récent disponible. Lorsqu’un ordinateur bascule vers Azure, l’ordinateur répond aux conditions requises d’installation de l’agent pour l’utilisation de n’importe quelle extension de machine virtuelle.

## <a name="prerequisites"></a>configuration requise
Effectuez ces étapes préalables avant d’installer manuellement le service Mobilité sur votre serveur :
1. Connectez-vous à votre serveur de configuration, puis ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Indiquez le dossier bin, puis créez un fichier de phrase secrète :

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Stockez le fichier de phrase secrète dans un emplacement sécurisé. Vous utilisez le fichier pendant l’installation du service Mobilité.
4. Les programmes d’installation du service Mobilité pour tous les systèmes d’exploitation pris en charge se trouvent dans le dossier %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Correspondance entre le programme d’installation du service Mobilité et le système d’exploitation

| Nom du modèle de fichier de programme d’installation| Système d’exploitation |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bits) </br> Windows Server 2012 (64 bits) </br> Windows Server 2012 R2 (64 bits) </br> Windows Server 2016 (64 bits) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (64 bits uniquement) </br> CentOS 6.4, 6.5, 6.6, 6.7, 6.8, 6.9 (64 bits uniquement) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.1, 7.2, 7.3 (64 bits uniquement) </br> CentOS 7.0, 7.1, 7.2, 7.3 (64 bits uniquement) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (64 bits uniquement)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (64 bits uniquement)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Serveur Ubuntu Linux 16.04 LTS (64 bits uniquement)|
|Microsoft-ASR_UA\*DEBIAN7 64\*release.tar.gz | Debian 7 (64 bits uniquement)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (64 bits uniquement)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>Installer le service Mobilité manuellement à l’aide de l’interface utilisateur

>[!IMPORTANT]
> Si vous utilisez un **Serveur de configuration** pour répliquer les **machines virtuelles Azure IaaS** entre abonnements/régions Azure, vous devez **utiliser la méthode d’installation en ligne de commande**.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Installation manuelle du service Mobilité à l’aide d’une ligne de commande

### <a name="command-line-installation-on-a-windows-computer"></a>Installation avec une ligne de commande sur un ordinateur Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Installation avec une ligne de commande sur un ordinateur Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Installation du service Mobilité à l’aide de l’installation de transmission (push) à partir d’Azure Site Recovery
Pour effectuer une installation Push du service Mobilité à l’aide de Site Recovery, tous les ordinateurs cibles doivent répondre aux conditions préalables suivantes :

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Une fois le service Mobilité installé, dans le portail Azure, sélectionnez le bouton **+Répliquer** pour commencer à protéger ces machines virtuelles.

## <a name="update-mobility-service"></a>Mettre à jour le service Mobilité.

> [!WARNING]
> Vérifiez que le serveur de configuration, les serveurs de traitement de montée en puissance parallèle et les serveurs cibles maîtres qui font partie de votre déploiement sont mis à jour avant que vous ne mettiez à jour le service Mobilité sur les serveurs protégés.

1. Sur le portail Azure, accédez à <Your Vault> -> vue Éléments répliqués.
2. Si le **serveur de configuration** a déjà été mis à jour vers la dernière version, une notification doit s’afficher et indiquer *Une nouvelle mise à jour de l’agent de réplication Site Recovery est disponible. Cliquez pour installer*.

     ![ReplicatedItems](.\media\site-recovery-vmware-to-azure-install-mob-svc\replicated-item-notif.png)
3. Cliquez sur la notification pour ouvrir la page de sélection des machines virtuelles.
4. Sélectionnez les machines virtuelles sur lesquelles vous souhaitez mettre à niveau le service Mobilité, puis cliquez sur le bouton OK.

     ![ReplicatedItemsVMList](.\media\site-recovery-vmware-to-azure-install-mob-svc\update-okpng.png)
5. Ce faisant, vous démarrez le travail Mettre à jour le service Mobilité pour chacune des machines virtuelles sélectionnées.

> [!NOTE]
> [En savoir plus](site-recovery-vmware-to-azure-manage-configuration-server.md) sur la mise à jour du mot de passe pour le compte utilisé pour installer le Service mobilité.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Désinstallation d’un service Mobilité sur un ordinateur Windows Server
Utilisez une des méthodes suivantes pour désinstaller le service Mobilité sur un ordinateur Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Désinstallation à l’aide de l’interface utilisateur graphique
1. Dans le panneau de configuration, sélectionnez **Programmes**.
2. Sélectionnez **Service Mobilité/Serveur cible maître Microsoft Azure Site Recovery**, puis sélectionnez **Désinstaller**.

### <a name="uninstall-at-a-command-prompt"></a>Désinstallation avec une invite de commandes
1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Exécutez la commande suivante pour désinstaller le service Mobilité :

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Désinstaller le service Mobilité sur un ordinateur Linux
1. Sur votre serveur Linux, connectez-vous en tant qu’utilisateur **root**.
2. Dans un terminal, accédez à /user/local/ASR.
3. Exécutez la commande suivante pour désinstaller le service Mobilité :

```
uninstall.sh -Y
```
