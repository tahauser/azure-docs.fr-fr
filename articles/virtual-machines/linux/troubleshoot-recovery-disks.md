---
title: "Utiliser une machine virtuelle de dépannage Linux avec Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes de machines virtuelles Linux en connectant le disque du système d’exploitation à une machine virtuelle de récupération avec Azure CLI 2.0."
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: iainfou
ms.openlocfilehash: 9f1ac319e87f321306a2239b2e17725d281fbf59
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli-20"></a>Résoudre les problèmes d’une machine virtuelle Linux en connectant le disque du système d’exploitation à une machine virtuelle de récupération avec Azure CLI 2.0
Si votre machine virtuelle Linux rencontre une erreur de démarrage ou de disque, il vous faudra éventuellement appliquer la procédure de dépannage directement sur le disque dur virtuel. Comme exemple courant, citons une entrée non valide dans `/etc/fstab` qui empêche le bon démarrage de la machine virtuelle. Cet article vous explique comment utiliser Azure CLI 2.0 pour connecter votre disque dur virtuel à une autre machine virtuelle Linux afin de corriger les éventuelles erreurs, puis pour régénérer votre machine virtuelle d’origine. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](troubleshoot-recovery-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="recovery-process-overview"></a>Vue d’ensemble du processus de récupération
Le processus de résolution de problème se présente comme suit :

1. Supprimez les machines virtuelles présentant des erreurs, en conservant les disques durs virtuels.
2. Fixez et montez le disque dur virtuel sur une autre machine virtuelle Linux, à des fins de résolution des problèmes.
3. Connectez-vous à la machine virtuelle de dépannage. Modifiez les fichiers ou exécutez des outils afin de corriger les problèmes sur le disque dur virtuel d’origine.
4. Démontez le disque dur virtuel d’origine et dissociez-le de la machine virtuelle de dépannage.
5. Créez une machine virtuelle à l’aide du disque dur virtuel d’origine.

Pour mener à bien ces étapes de dépannage, vous devez disposer de la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

Dans les exemples suivants, remplacez les noms de paramètres avec vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.


## <a name="determine-boot-issues"></a>Identifier les problèmes de démarrage
Examinez la sortie en série afin d’identifier la raison pour laquelle votre machine virtuelle ne démarre pas correctement. Comme exemple courant, citons une entrée non valide dans `/etc/fstab`, ou le disque dur virtuel en cours de suppression ou de déplacement.

Accédez aux journaux du démarrage avec [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log). L’exemple suivant récupère la sortie en série de la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Examinez la sortie en série afin d’identifier la raison pour laquelle le démarrage de la machine virtuelle est mis en échec. Si la sortie en série ne fournit aucune indication, il vous faudra éventuellement passer en revue les fichiers journaux de `/var/log` une fois que le disque dur virtuel est connecté à une machine virtuelle de dépannage.


## <a name="view-existing-virtual-hard-disk-details"></a>Afficher les détails du disque dur virtuel existant
Avant de pouvoir associer votre disque dur virtuel à une autre machine virtuelle, vous devez identifier l’URI du disque du système d’exploitation. 

Pour afficher des informations sur votre machine virtuelle, utilisez la commande [az vm show](/cli/azure/vm#az_vm_show). Utilisez l’indicateur `--query` pour extraire l’URI du disque du système d’exploitation. L’exemple suivant récupère des informations de disque pour la machine virtuelle `myVM` dans le groupe de ressources `myResourceGroup` :

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

L’URI est de type **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Supprimer une machine virtuelle existante
Les disques durs virtuels et les machines virtuelles sont deux ressources distinctes dans Azure. Le disque dur virtuel est l’emplacement de stockage du système d’exploitation, des applications et des configurations. La machine virtuelle correspond à des métadonnées définissant la taille ou l’emplacement ; elle référence des ressources comme un disque dur virtuel ou une carte d’interface réseau virtuelle (NIC). Chaque disque dur virtuel associé à une machine virtuelle se voit attribuer un bail. Bien que l’association et la dissociation des disques de données puisse s’effectuer pendant l’exécution de la machine virtuelle, le disque du système d’exploitation ne peut pas être dissocié, sauf si la ressource de machine virtuelle est supprimée. Le bail continue à associer le disque du système d’exploitation à une machine virtuelle, même lorsque cette machine virtuelle se trouve dans un état d’arrêt ou de libération.

La première étape de la récupération de votre machine virtuelle consiste à supprimer la ressource de machine virtuelle. En supprimant la machine virtuelle, vous ne vous séparez pas des disques durs virtuels de votre compte de stockage. Une fois la machine virtuelle supprimée, vous associez le disque dur virtuel à une autre machine virtuelle afin de réparer et de corriger les erreurs.

Supprimez la machine virtuelle avec la commande [az vm delete](/cli/azure/vm#az_vm_delete). L’exemple suivant supprime la machine virtuelle nommée `myVM` du groupe de ressource nommé `myResourceGroup` :

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Attendez la suppression définitive de la machine virtuelle avant d’associer le disque dur virtuel à une autre machine virtuelle. Le bail du disque dur virtuel, qui l’associe à la machine virtuelle, doit être libéré avant l’association du disque dur virtuel à une autre machine virtuelle.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Associer un disque dur virtuel existant à une autre machine virtuelle
Pour les prochaines étapes, vous utilisez une autre machine virtuelle à des fins de résolution des problèmes. Vous associez le disque dur virtuel existant à cette machine virtuelle de dépannage et modifiez le contenu du disque. Ce processus vous permet de corriger les éventuelles erreurs de configuration ou d’examiner des fichiers journaux supplémentaires de système ou d’application, par exemple. Sélectionnez ou créez une autre machine virtuelle à des fins de résolution des problèmes.

Attachez le disque dur virtuel existant avec la commande [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_attach). Lorsque vous associez le disque dur virtuel existant, spécifiez l’URI du disque obtenu dans la commande `az vm show` précédente. L’exemple suivant associe un disque dur virtuel existant à la machine virtuelle de dépannage nommée `myVMRecovery` dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Monter le disque de données associé

> [!NOTE]
> Les exemples suivants décrivent les étapes requises sur une machine virtuelle Ubuntu. Si vous utilisez une distribution Linux différente, comme Red Hat Enterprise Linux ou SUSE, les emplacements de fichiers journaux et les commandes `mount` peuvent varier. Pour connaître les modifications appropriées dans les commandes, reportez-vous à la documentation de votre distribution spécifique.

1. Exécutez une commande SSH sur votre machine virtuelle de dépannage à l’aide des informations d’identification appropriées. Si ce disque est le premier disque de données associé à votre machine virtuelle de dépannage, il est probablement connecté à `/dev/sdc`. Pour afficher les disques associés, utilisez `dmseg` :

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
Une fois les erreurs résolues, vous démontez et dissociez le disque dur virtuel existant de votre machine virtuelle de dépannage. Vous ne pouvez pas utiliser votre disque dur virtuel avec une autre machine virtuelle avant la libération du bail associant le disque dur virtuel à la machine virtuelle de dépannage.

1. À partir de la session SSH vers votre machine virtuelle de dépannage, démontez le disque dur virtuel existant. Commencez par modifier votre point de montage dans le répertoire parent :

    ```bash
    cd /
    ```

    Montez alors le disque dur virtuel existant. L’exemple suivant démonte l’appareil au niveau de `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Dissociez le disque dur virtuel de la machine virtuelle. Quittez la session SSH vers votre machine virtuelle de dépannage. Utilisez la commande [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_list) pour répertorier les disques de données associés à votre machine virtuelle de dépannage. L’exemple suivant répertorie les disques de données associés à la machine virtuelle nommée `myVMRecovery` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Notez le nom de votre disque dur virtuel existant. Par exemple, le nom d’un disque qui a pour URI **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** est **myVHD**. 

    Détachez le disque de données de votre machine virtuelle à l’aide de la commande [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_detach). L’exemple suivant supprime la machine virtuelle nommée `myVHD` du groupe de ressources `myVMRecovery` dans le groupe de ressources `myResourceGroup` :

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Créer une machine virtuelle à partir du disque dur d’origine
Pour créer une machine virtuelle à partir de votre disque dur d’origine, utilisez [ce modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Le véritable modèle JSON se trouve sur le lien suivant :

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

Le modèle déploie une machine virtuelle à l’aide de l’URI du disque dur virtuel, à partir de la commande précédente. Déployez ensuite le modèle avec la commande [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Indiquez l’URI de votre disque dur virtuel d’origine, puis spécifiez le type de système d’exploitation, la taille de la machine virtuelle et son nom, comme suit :

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Réactiver les diagnostics de démarrage
Lorsque vous créez votre machine virtuelle à partir du disque dur virtuel existant, les diagnostics de démarrage peuvent ne pas être automatiquement activés. Activez les diagnostics de démarrage avec la commande [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). L’exemple suivant active l’extension de diagnostic sur la machine virtuelle nommée `myDeployedVM`, dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes pour vous connecter à votre machine virtuelle, consultez la rubrique [Dépannage d’une connexion SSH à une machine virtuelle Linux Azure défaillante, qui génère une erreur ou qui est refusée](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pour résoudre les problèmes liés à l’accès aux applications exécutées sur votre machine virtuelle, consultez la section [Résoudre les problèmes de connectivité des applications sur une machine virtuelle Linux Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).