---
title: "Développer les disques durs virtuels sur une machine virtuelle Linux dans Azure | Microsoft Docs"
description: "Apprenez à développer des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: f717aca4f68811b5973fcee8791ac7db3a66e74a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Comment développer des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI
La taille par défaut de disque virtuel pour le système d’exploitation est généralement de 30 Go sur une machine virtuelle Linux dans Azure. Vous pouvez [ajouter des disques de données](add-disk.md) afin d’offrir un espace de stockage supplémentaire, mais vous pouvez également développer un disque de données existant. Cet article vous explique comment développer les disques gérés pour une machine virtuelle Linux à l’aide de l’interface CLI Azure 2.0. Vous pouvez également développer le disque du système d’exploitation non managé avec [Azure CLI 1.0](expand-disks-nodejs.md).

> [!WARNING]
> Assurez-vous de toujours sauvegarder vos données avant de redimensionner des disques. Pour plus d’informations, consultez [Back up Linux VMs in Azure](tutorial-backup-vms.md) (Sauvegarder des machines virtuelles Linux dans Azure).

## <a name="expand-azure-managed-disk"></a>Développer un disque géré Azure
Assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

Cet article nécessite une machine virtuelle existante dans Azure avec au moins un disque de données attaché et préparé. Si vous n’avez pas encore de machine virtuelle à utiliser, consultez [Create and prepare a VM with data disks](tutorial-manage-disks.md#create-and-attach-disks) (Créer et préparer une machine virtuelle avec des disques de données).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup* et *myVM*.

1. Il est impossible d’exécuter les opérations sur les disques durs virtuels avec la machine virtuelle en cours d’exécution. Libérez la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L’exemple suivant libère la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup* :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > La machine virtuelle doit être libérée pour développer le disque dur virtuel. `az vm stop` ne publie pas les ressources de calcul. Pour publier les ressources de calcul, utilisez `az vm deallocate`.

2. Affichez la liste des disques gérés dans un groupe de ressources avec la commande [az disk list](/cli/azure/disk#az_disk_list). L’exemple suivant affiche la liste des disques managés dans le groupe de ressources nommé *myResourceGroup* :

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Développez le disque requis avec la commande [az disk update](/cli/azure/disk#az_disk_update). L’exemple suivant développe le disque managé nommé *myDataDisk* pour qu’il ait une taille de *200* Go :

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Lorsque vous développez un disque géré, la taille mise à jour est mappée à la taille de disque géré la plus proche. Pour obtenir un tableau des tailles et des niveaux de disques gérés disponibles, consultez [Vue d’ensemble des disques gérés Azure - Tarification et facturation](../windows/managed-disks-overview.md#pricing-and-billing).

3. Démarrez votre machine virtuelle avec [az vm start](/cli/azure/vm#az_vm_start). L’exemple suivant démarre la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup* :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Développer le système de fichiers et la partition du disque
Pour utiliser le disque étendu, vous devez développer la partition et le système de fichiers sous-jacents.

1. Établissez une connexion SSH à votre machine virtuelle à l’aide des informations d’identification appropriées. Vous pouvez obtenir l’adresse IP publique de votre machine virtuelle à l’aide de la commande [az vm show](/cli/azure/vm#az_vm_show) :

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

2. Pour utiliser le disque étendu, vous devez développer la partition et le système de fichiers sous-jacents.

    a. Si le disque est déjà monté, démontez-le :

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Utilisez `parted` pour afficher des informations sur le disque et redimensionner la partition :

    ```bash
    sudo parted /dev/sdc
    ```

    Affichez les informations sur la disposition actuelle de la partition avec `print`. Le résultat est comparable à l’exemple ci-dessous, qui indique que le disque sous-jacent fait 215 Go :

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Développez la partition avec `resizepart`. Entrez le numéro de la partition, *1*, et la taille de la nouvelle partition :

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Pour quitter l’outil, saisissez `quit`.

3. Une fois la partition redimensionnée, vérifiez la cohérence de la partition avec `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

4. Redimensionnez ensuite le système de fichiers avec `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

5. Montez la partition à l’emplacement souhaité, tel que `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

6. Pour vérifier que le disque du système d’exploitation a été redimensionné, utilisez `df -h`. L’exemple de sortie suivant indique que le disque de données */dev/sdc1* fait désormais 200 Go :

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin de stockage supplémentaire, vous pouvez également [ajouter des disques de données à une machine virtuelle Linux](add-disk.md). Pour plus d’informations sur le chiffrement de disque, consultez la section [Chiffrer des disques sur une machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure (CLI)](encrypt-disks.md).
