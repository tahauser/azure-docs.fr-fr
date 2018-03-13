---
title: "Montage du stockage de fichiers Azure sur les machines virtuelles Linux à l’aide de SMB | Microsoft Docs"
description: "Procédure de montage du stockage de fichiers Azure sur les machines virtuelles Linux à l’aide de SMB avec Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
ms.openlocfilehash: 4566e9b236049c336858e9149cca80066b029775
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Monter le stockage de fichiers Azure sur les machines virtuelles Linux à l’aide de SMB

Cet article vous montre comment utiliser le service de stockage de fichiers Azure sur une machine virtuelle Linux à l’aide d’un montage SMB avec Azure CLI 2.0. Le stockage de fichiers Azure propose des partages de fichiers dans le cloud s’appuyant sur le protocole SMB standard. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Les conditions requises sont :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)
- [des fichiers de clés SSH publiques et privées](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Commandes rapides

* Un groupe de ressources.
* Un réseau virtuel Azure.
* Un groupe de sécurité réseau avec une connexion SSH entrante.
* Un sous-réseau.
* Un compte de stockage Azure.
* Des clés de compte de stockage Azure.
* Un partage de stockage de fichiers Azure.
* Une machine virtuelle Linux.

Remplacez les exemples par vos propres paramètres.

### <a name="create-a-directory-for-the-local-mount"></a>Créez un répertoire pour le montage local

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Montez le partage SMB de stockage de fichiers sur le point de montage.

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Conservez le montage après redémarrage.
Pour ce faire, ajoutez la ligne suivante à l’élément `/etc/fstab` :

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas

Le stockage de fichiers propose des partages de fichiers dans le cloud qui utilisent le protocole SMB standard. Avec la dernière version du Stockage Fichier, vous pouvez également monter un partage de fichiers à partir d’un système d’exploitation prenant en charge SMB 3.0. Un montage SMB sur Linux permet d’effectuer facilement une sauvegarde vers un emplacement de stockage d’archivage robuste et permanent pris en charge par un contrat de niveau de service.

Un bon moyen de déboguer les journaux consiste à déplacer les fichiers vers un montage SMB hébergé sur le stockage de fichiers à partir d’une machine virtuelle. En effet, un même partage SMB peut être monté localement sur votre station de travail Windows, Linux ou Mac. SMB ne constitue pas la meilleure solution pour transmettre en continu des journaux d’applications ou Linux en temps réel, car le protocole SMB n’est pas conçu pour gérer des tâches de journalisation aussi lourdes. Un outil de couche de journalisation unifié et dédié comme Fluentd resprésente un meilleur choix que SMB pour collecter la sortie de journalisation d’applications et Linux.

Pour cette procédure pas à pas détaillée, nous créons la configuration requise pour d’abord créer le partage de Stockage Fichier, puis le monter via SMB sur une machine virtuelle Linux.

1. Créez un groupe de ressource avec la commande [az group create](/cli/azure/group#az_group_create) pour contenir le partage de fichiers.

    Créez un groupe de ressources nommé `myResourceGroup` dans la région États-Unis de l’Ouest en suivant l’exemple suivant :

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. Créez un compte de stockage Azure avec [az storage account create](/cli/azure/storage/account#az_storage_account_create) pour stocker les fichiers.

    Pour créer un compte de stockage nommé mystorageaccount à l’aide de la référence de stockage Standard_LRS, suivez l’exemple suivant :

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. Affichez les clés de compte de stockage.

    Quand vous créez un compte de stockage, les clés du compte sont créées par paires afin qu’elles puissent faire l’objet d’une rotation sans interruption du service. Lorsque vous basculez vers la deuxième clé de la paire, vous créez une nouvelle paire de clés. Les nouvelles clés de compte de stockage sont toujours créées par paires, pour garantir que vous disposiez toujours d’au moins une clé de stockage inutilisée prête au basculement.

    Utilisez [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) pour afficher les clés du compte de stockage. Les clés de compte de stockage pour l’élément nommé `mystorageaccount` sont répertoriées dans l’exemple suivant :

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    Pour extraire une clé unique, utilisez l’indicateur `--query`. L’exemple suivant extrait la première clé (`[0]`) :

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. Créez le partage de stockage de fichiers.

    Créez le partage de stockage de fichiers qui contient le partage SMB avec [az storage share create](/cli/azure/storage/share#az_storage_share_create). Le quota est toujours exprimé en gigaoctets (Go). Passez l’une des clés de la commande `az storage account keys list` précédente. Créez un partage nommé mystorageshare avec un quota de 10 Go en appliquant l’exemple suivant :

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Créez un répertoire de point de montage.

    Créez un répertoire local sur le système de fichiers Linux pour monter le partage SMB. Tout ce qui est écrit ou lu à partir du répertoire de montage local est transféré au partage SMB hébergé sur le stockage de fichiers. Pour créer un répertoire local à l’emplacement /mnt/mymountdirectory, appliquez l’exemple suivant :

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

6. Montez le partage SMB sur le répertoire local.

    Fournissez votre propre nom d’utilisateur de compte de stockage et la clé de compte de stockage pour les informations d’identification de montage comme suit :

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Conservez le montage SMB au cours des redémarrages.

    Lorsque vous redémarrez la machine virtuelle Linux, le partage SMB monté est démonté lors de l’arrêt. Pour remonter le partage SMB au démarrage, ajoutez une ligne à /etc/fstab dans Linux. Linux utilise le fichier fstab pour lister les systèmes de fichiers à monter pendant le processus de démarrage. L’ajout du partage SMB garantit que le partage de stockage de fichiers est un système de fichiers monté définitivement pour la machine virtuelle Linux. Il est possible d’ajouter le partage SMB du Stockage Fichier sur une nouvelle machine virtuelle si vous utilisez cloud-init.

    ```bash
    //myaccountname.file.core.windows.net/mystorageshare /mnt/mymountdirectory cifs vers=3.0,username=mystorageaccount,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ajouter un disque à une machine virtuelle Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Chiffrer des disques sur une machine virtuelle Linux à l’aide de l’interface de ligne de commande (CLI) Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
