---
title: "Utilisation d’une machine virtuelle de dépannage Windows dans le portail Azure | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes de machines virtuelles Windows dans Azure en connectant le disque du système d’exploitation à une machine virtuelle de récupération à l’aide du portail Azure"
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 0b6b2220f5b7287bae6bfd6e99390944b7a7578d
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Résoudre les problèmes d’une machine virtuelle Windows en connectant le disque du système d’exploitation à une machine virtuelle de récupération à l’aide du portail Azure
Si votre machine virtuelle Windows dans Azure rencontre une erreur de démarrage ou de disque, il vous faudra éventuellement appliquer la procédure de dépannage directement sur le disque dur virtuel. Comme exemple courant, citons l’échec de mise à jour d’une application qui empêche le bon démarrage de la machine virtuelle. Cet article vous explique comment utiliser le portail Azure pour connecter votre disque dur virtuel à une autre machine virtuelle Windows pour corriger les éventuelles erreurs, puis pour régénérer votre machine virtuelle d’origine.

## <a name="recovery-process-overview"></a>Vue d’ensemble du processus de récupération
Le processus de résolution de problème se présente comme suit :

1. Supprimez les machines virtuelles présentant des erreurs, en conservant les disques durs virtuels.
2. Fixez et montez le disque dur virtuel sur une autre machine virtuelle Windows, à des fins de résolution des problèmes.
3. Connectez-vous à la machine virtuelle de dépannage. Modifiez les fichiers ou exécutez des outils afin de corriger les problèmes sur le disque dur virtuel d’origine.
4. Démontez le disque dur virtuel d’origine et dissociez-le de la machine virtuelle de dépannage.
5. Créez une machine virtuelle à l’aide du disque dur virtuel d’origine.


## <a name="determine-boot-issues"></a>Identifier les problèmes de démarrage
Examinez les diagnostics de démarrage et la capture d’écran de la machine virtuelle afin d’identifier la raison pour laquelle votre machine virtuelle ne démarre pas correctement. Comme exemple courant, citons l’échec de mise à jour d’une application ou un disque dur virtuel en cours de suppression ou de déplacement.

Dans le portail, sélectionnez votre machine virtuelle, puis accédez à la section **Prise en charge et dépannage**. Cliquez sur **Diagnostics de démarrage** pour afficher la capture d’écran. Notez les messages d’erreur ou les codes d’erreur spécifiques pour vous aider à déterminer pourquoi la machine virtuelle rencontre un problème. L’exemple suivant montre une machine virtuelle en attente d’arrêt des services :

![Affichage des journaux de console des diagnostics de démarrage de la machine virtuelle](./media/troubleshoot-recovery-disks-portal/screenshot-error.png)

Vous pouvez également cliquer sur **Capture d’écran** pour télécharger une copie de la capture d’écran de la machine virtuelle.


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

1. Ouvrez une connexion Bureau à distance à votre machine virtuelle. Sélectionnez votre machine virtuelle dans le portail et cliquez sur **Se connecter**. Téléchargez et ouvrez le fichier de connexion RDP. Entrez vos informations d’identification pour vous connecter à votre machine virtuelle comme suit :

    ![Connexion à votre machine virtuelle à l'aide du Bureau à distance](./media/troubleshoot-recovery-disks-portal/open-remote-desktop.png)

2. Ouvrez le **Gestionnaire de serveur**, puis sélectionnez **Services de fichiers et de stockage**. 

    ![Sélection de Services de fichiers et de stockage dans le Gestionnaire de serveur](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

3. Le disque de données est automatiquement détecté et attaché. Pour afficher la liste des disques connectés, sélectionnez **Disques**. Vous pouvez sélectionner le disque de données pour afficher les informations sur le volume, notamment la lettre de lecteur. L’exemple suivant présente le disque de données attaché utilisant la lettre **F:** :

    ![Disque attaché et informations sur le volume dans le Gestionnaire de serveur](./media/troubleshoot-recovery-disks-portal/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Résoudre des problèmes sur le disque dur virtuel d’origine
Avec le disque dur virtuel existant monté, vous pouvez désormais exécuter les procédures de maintenance et de dépannage requises. Une fois que vous avez résolu les problèmes, passez aux étapes suivantes.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Démonter et dissocier le disque dur virtuel d’origine
Une fois les erreurs résolues, dissociez le disque dur virtuel existant de votre machine virtuelle de dépannage. Vous ne pouvez pas utiliser votre disque dur virtuel avec une autre machine virtuelle avant la libération du bail associant le disque dur virtuel à la machine virtuelle de dépannage.

1. Dans la session Bureau à distance sur votre machine virtuelle, ouvrez le **Gestionnaire de serveur**, puis sélectionnez **Services de fichiers et de stockage** :

    ![Sélection de Services de fichiers et de stockage dans le Gestionnaire de serveur](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

2. Sélectionnez **Disques**, puis sélectionnez votre disque de données. Cliquez avec le bouton droit sur votre disque de données et sélectionnez **Mettre hors connexion** :

    ![Définition du disque de données comme étant hors connexion dans le Gestionnaire de serveur](./media/troubleshoot-recovery-disks-portal/server-manager-set-disk-offline.png)

3. Dissociez le disque dur virtuel de la machine virtuelle. Sélectionnez votre machine virtuelle dans le portail Azure et cliquez sur **Disques**. Sélectionnez votre disque dur virtuel existant, puis cliquez **Dissocier** :

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
Si vous rencontrez des problèmes pour vous connecter à votre machine virtuelle, consultez [Dépannage d’une connexion Bureau à distance à une machine virtuelle Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pour résoudre les problèmes liés à l’accès aux applications exécutées sur votre machine virtuelle, consultez [Résoudre les problèmes de connectivité des applications sur une machine virtuelle Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour plus d’informations sur l’utilisation de Resource Manager, consultez la page [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
