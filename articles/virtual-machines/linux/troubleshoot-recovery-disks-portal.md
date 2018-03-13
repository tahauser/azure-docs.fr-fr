---
title: "Utiliser une machine virtuelle de dépannage Linux dans le portail Azure | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes de machines virtuelles Linux en connectant le disque du système d’exploitation à une machine virtuelle de récupération à l’aide du portail Azure"
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: iainfou
ms.openlocfilehash: c96ff625c3e83f6fc9057f1163c877e8e0aed5e3
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Résoudre les problèmes d’une machine virtuelle Linux en connectant le disque du système d’exploitation à une machine virtuelle de récupération à l’aide du portail Azure
Si votre machine virtuelle Linux rencontre une erreur de démarrage ou de disque, il vous faudra éventuellement appliquer la procédure de dépannage directement sur le disque dur virtuel. Comme exemple courant, citons une entrée non valide dans `/etc/fstab` qui empêche le bon démarrage de la machine virtuelle. Cet article vous explique comment utiliser le portail Azure pour connecter votre disque dur virtuel à une autre machine virtuelle Linux pour corriger les éventuelles erreurs, puis pour régénérer votre machine virtuelle d’origine.

## <a name="recovery-process-overview"></a>Vue d’ensemble du processus de récupération
Le processus de résolution de problème se présente comme suit :

1. Supprimez les machines virtuelles présentant des erreurs, en conservant les disques durs virtuels.
2. Fixez et montez le disque dur virtuel sur une autre machine virtuelle Linux, à des fins de résolution des problèmes.
3. Connectez-vous à la machine virtuelle de dépannage. Modifiez les fichiers ou exécutez des outils afin de corriger les problèmes sur le disque dur virtuel d’origine.
4. Démontez le disque dur virtuel d’origine et dissociez-le de la machine virtuelle de dépannage.
5. Créez une machine virtuelle à l’aide du disque dur virtuel d’origine.


## <a name="determine-boot-issues"></a>Identifier les problèmes de démarrage
Examinez les diagnostics de démarrage et la capture d’écran de la machine virtuelle afin d’identifier la raison pour laquelle votre machine virtuelle ne démarre pas correctement. Comme exemple courant, citons une entrée non valide dans `/etc/fstab`, ou le disque dur virtuel en cours de suppression ou de déplacement.

Dans le portail, sélectionnez votre machine virtuelle, puis accédez à la section **Prise en charge et dépannage**. Cliquez sur **Démarrer les diagnostics** pour afficher les messages de console transmis en continu à partir de votre machine virtuelle. Passez en revue les journaux de la console pour voir si vous pouvez déterminer la raison pour laquelle la machine virtuelle rencontre un problème. L’exemple suivant montre une machine virtuelle bloquée en mode de maintenance nécessitant une interaction manuelle :

![Affichage des journaux de console des diagnostics de démarrage de la machine virtuelle](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

Vous pouvez également cliquer sur la **capture d’écran** dans la partie supérieure du journal de diagnostics de démarrage pour télécharger la capture d’écran de la machine virtuelle.


## <a name="view-existing-virtual-hard-disk-details"></a>Afficher les détails du disque dur virtuel existant
Avant de pouvoir associer votre disque dur virtuel à une autre machine virtuelle, vous devez identifier le nom du disque dur virtuel. 

Sélectionnez votre groupe de ressources à partir du portail, puis sélectionnez votre compte de stockage. Cliquez sur **Blobs**, comme dans l’exemple suivant :

![Sélectionner les blobs de stockage](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

En général, vous avez un conteneur nommé **vhds** qui stocke vos disques durs virtuels. Sélectionnez le conteneur pour afficher une liste des disques durs virtuels. Notez le nom de votre disque dur virtuel (le préfixe est généralement le nom de votre machine virtuelle) :

![Identifier le disque dur virtuel dans un conteneur de stockage](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Sélectionnez votre disque dur virtuel existant dans la liste, puis copiez l’URL à utiliser dans les étapes suivantes :

![Copier l’URL du disque dur virtuel existant](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Supprimer une machine virtuelle existante
Les disques durs virtuels et les machines virtuelles sont deux ressources distinctes dans Azure. Le disque dur virtuel est l’emplacement de stockage du système d’exploitation, des applications et des configurations. La machine virtuelle correspond à des métadonnées définissant la taille ou l’emplacement ; elle référence des ressources comme un disque dur virtuel ou une carte d’interface réseau virtuelle (NIC). Chaque disque dur virtuel associé à une machine virtuelle se voit attribuer un bail. Bien que l’association et la dissociation des disques de données puisse s’effectuer pendant l’exécution de la machine virtuelle, le disque du système d’exploitation ne peut pas être dissocié, sauf si la ressource de machine virtuelle est supprimée. Le bail continue à associer le disque du système d’exploitation à une machine virtuelle, même lorsque cette machine virtuelle se trouve dans un état d’arrêt ou de libération.

La première étape de la récupération de votre machine virtuelle consiste à supprimer la ressource de machine virtuelle. En supprimant la machine virtuelle, vous ne vous séparez pas des disques durs virtuels de votre compte de stockage. Une fois la machine virtuelle supprimée, vous associez le disque dur virtuel à une autre machine virtuelle afin de réparer et de corriger les erreurs.

Sélectionnez votre machine virtuelle dans le portail, puis cliquez sur **Supprimer** :

![Capture d’écran de diagnostics de démarrage de machine virtuelle présentant une erreur de démarrage](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Attendez la fin de la suppression de la machine virtuelle avant d’associer le disque dur virtuel à une autre machine virtuelle. Le bail du disque dur virtuel, qui l’associe à la machine virtuelle, doit être libéré avant l’association du disque dur virtuel à une autre machine virtuelle.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Associer un disque dur virtuel existant à une autre machine virtuelle
Pour les prochaines étapes, vous utilisez une autre machine virtuelle à des fins de résolution des problèmes. Vous associez le disque dur virtuel existant à cette machine virtuelle de dépannage pour pouvoir modifier le contenu du disque. Ce processus vous permet de corriger les éventuelles erreurs de configuration ou d’examiner des fichiers journaux supplémentaires de système ou d’application, par exemple. Sélectionnez ou créez une autre machine virtuelle à des fins de résolution des problèmes.

1. Sélectionnez votre groupe de ressources à partir du portail, puis sélectionnez votre machine virtuelle de résolution de problèmes. Sélectionnez **Disques**, puis cliquez sur **Attacher existant** :

    ![Attacher un disque existant dans le portail](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Pour sélectionner votre disque dur virtuel existant, cliquez sur **Fichier VHD** :

    ![Rechercher le disque dur virtuel existant](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Sélectionnez votre compte de stockage et votre conteneur, puis cliquez sur votre disque dur virtuel existant. Cliquez sur le bouton **Sélectionner** pour confirmer votre choix :

    ![Sélectionner votre disque dur virtuel existant](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Une fois que votre disque dur virtuel est sélectionné, cliquez sur **OK** pour attacher le disque dur virtuel existant :

    ![Confirmer l’attachement du disque dur virtuel existant](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Après quelques secondes, le volet **Disques** de votre machine virtuelle répertorie votre disque dur virtuel existant connecté en tant que disque de données :

    ![Disque dur virtuel existant attaché en tant que disque de données](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Monter le disque de données associé

> [!NOTE]
> Les exemples suivants décrivent les étapes requises sur une machine virtuelle Ubuntu. Si vous utilisez une distribution Linux différente, comme Red Hat Enterprise Linux ou SUSE, les emplacements de fichiers journaux et les commandes `mount` peuvent varier. Pour connaître les modifications appropriées dans les commandes, reportez-vous à la documentation de votre distribution spécifique.

1. Exécutez une commande SSH sur votre machine virtuelle de dépannage à l’aide des informations d’identification appropriées. Si ce disque est le premier disque de données associé à votre machine virtuelle de dépannage, il est probablement connecté à `/dev/sdc`. Utilisez `dmseg` pour répertorier les disques associés :

    ```bash
    dmesg | grep SCSI
    ```
    Le résultat ressemble à l’exemple suivant :

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    Dans l’exemple précédent, le disque du système d’exploitation se trouve au niveau de `/dev/sda`, et le disque temporaire fourni pour chaque machine virtuelle se trouve au niveau de `/dev/sdb`. Si vous possédiez plusieurs disques de données, ils doivent se trouver au niveau de `/dev/sdd`, `/dev/sde`, etc.

2. Créez un répertoire pour monter votre disque dur virtuel existant. L’exemple suivant crée un répertoire nommé `troubleshootingdisk` :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Si votre disque dur virtuel existant présente plusieurs partitions, montez la partition requise. L’exemple suivant monte la première partition primaire au niveau de `/dev/sdc1` :

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Nous vous recommandons de monter les disques de données sur les machines virtuelles à l’aide de l’identifiant unique universel (UUID) du disque dur virtuel. Pour ce scénario court de résolution des problèmes, il n’est pas nécessaire de monter le disque dur virtuel à l’aide de l’UUID. Toutefois, dans des conditions normales, la modification de `/etc/fstab` pour monter les disques durs virtuels avec le nom d’appareil en lieu et place de l’UUID peut entraîner la mise en échec du démarrage de la machine virtuelle.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Résoudre des problèmes sur le disque dur virtuel d’origine
Avec le disque dur virtuel existant monté, vous pouvez désormais exécuter les procédures de maintenance et de dépannage requises. Une fois que vous avez résolu les problèmes, passez aux étapes suivantes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Démonter et dissocier le disque dur virtuel d’origine
Une fois les erreurs résolues, dissociez le disque dur virtuel existant de votre machine virtuelle de dépannage. Vous ne pouvez pas utiliser votre disque dur virtuel avec une autre machine virtuelle avant la libération du bail associant le disque dur virtuel à la machine virtuelle de dépannage.

1. À partir de la session SSH vers votre machine virtuelle de dépannage, démontez le disque dur virtuel existant. Commencez par modifier votre point de montage dans le répertoire parent :

    ```bash
    cd /
    ```

    Montez alors le disque dur virtuel existant. L’exemple suivant démonte l’appareil au niveau de `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Dissociez le disque dur virtuel de la machine virtuelle. Sélectionnez votre machine virtuelle dans le portail, et cliquez sur **Disques**. Sélectionnez votre disque dur virtuel existant, puis cliquez **Dissocier** :

    ![Dissocier le disque dur virtuel existant](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Attendez que la machine virtuelle ait correctement dissocié le disque de données avant de continuer.

## <a name="create-vm-from-original-hard-disk"></a>Créer une machine virtuelle à partir du disque dur d’origine
Pour créer une machine virtuelle à partir de votre disque dur d’origine, utilisez [ce modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Le modèle déploie une machine virtuelle dans un réseau virtuel existant, à l’aide de l’URL de disque dur virtuel de la commande précédente. Cliquez sur le bouton **Déployer dans Azure** comme suit :

![Déployer la machine virtuelle du modèle à partir de GitHub](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

Le modèle est chargé dans le portail Azure pour le déploiement. Entrez les noms de votre nouvelle machine virtuelle et des ressources Azure existantes et collez l’URL dans votre disque dur virtuel existant. Pour commencer le déploiement, cliquez sur **Acheter** :

![Déployer une machine virtuelle à partir d’un modèle](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Réactiver les diagnostics de démarrage
Lorsque vous créez votre machine virtuelle à partir du disque dur virtuel existant, les diagnostics de démarrage peuvent ne pas être automatiquement activés. Pour vérifier l’état des diagnostics de démarrage et l’activer si nécessaire, sélectionnez votre machine virtuelle dans le portail. Sous **Surveillance**, cliquez sur **Paramètres de diagnostic**. Vérifiez que l’état est **Activé**, et que la case à cocher en regard de **Diagnostics de démarrage** est sélectionnée. Si vous apportez des modifications, cliquez sur **Enregistrer** :

![Mettre à jour les paramètres des diagnostics de démarrage](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes pour vous connecter à votre machine virtuelle, consultez la rubrique [Dépannage d’une connexion SSH à une machine virtuelle Linux Azure défaillante, qui génère une erreur ou qui est refusée](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pour résoudre les problèmes liés à l’accès aux applications exécutées sur votre machine virtuelle, consultez la section [Résoudre les problèmes de connectivité des applications sur une machine virtuelle Linux Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour plus d’informations sur l’utilisation de Resource Manager, consultez la page [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
