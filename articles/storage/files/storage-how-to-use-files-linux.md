---
title: Utiliser Azure Files avec Linux | Microsoft Docs
description: "Découvrez comment monter un partage de fichiers Azure via SMB sur Linux."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 0a87f8572af2620420faa0e3c2e575aa8add42ab
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="use-azure-files-with-linux"></a>Utiliser Azure Files avec Linux
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l’aide du [client noyau CIFS](https://wiki.samba.org/index.php/LinuxCIFS). Cet article présente deux méthodes de montage d’un partage de fichiers Azure : à la demande avec la commande `mount` et au démarrage en créant une entrée dans `/etc/fstab`.

> [!NOTE]  
> Pour monter un partage de fichiers Azure en dehors de la région Azure dans laquelle il est hébergé, par exemple en local ou dans une région Azure différente, le système d’exploitation doit prendre en charge la fonctionnalité de chiffrement de SMB 3.0. La fonctionnalité de chiffrement pour SMB 3.0 pour Linux a été introduite dans le noyau 4.11. Cette fonctionnalité permet le montage du partage de fichiers Azure en local ou à partir d’une autre région Azure. Quand nous avons publié cet article, cette fonctionnalité a été rétroportée dans Ubuntu à partir de 16.04 et versions ultérieures.

## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Conditions préalables au montage d’un partage de fichiers Azure avec Linux et le package cifs-utils
* **Choisissez une distribution Linux pour laquelle le package cifs-utils peut être installé** : Microsoft recommande les distributions Linux suivantes dans la galerie d’images Azure :

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**Le package cifs-utils est installé** : le package cifs-utils peut être installé à l’aide du gestionnaire de packages sur la distribution Linux de votre choix. 

    Sur les distributions **Ubuntu** et **basées sur Debian**, utilisez le gestionnaire de packages `apt-get` :

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    Sur **RHEL** et **CentOS**, utilisez le gestionnaire de packages `yum` :

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    Sur **openSUSE**, utilisez le gestionnaire de packages `zypper` :

    ```
    sudo zypper install samba*
    ```

    Sur les autres distributions, utilisez le gestionnaire de packages approprié ou effectuez une [compilation à partir de la source](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Déterminez les autorisations de fichier/répertoire du partage monté** : dans les exemples ci-dessous, nous utilisons 0777 pour accorder des autorisations de lecture, d’écriture et d’exécution à tous les utilisateurs. Vous pouvez les remplacer par d’autres [autorisations chmod](https://en.wikipedia.org/wiki/Chmod) comme vous le souhaitez. 

* **Nom de compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin du nom du compte de stockage.

* **Clé du compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin de la clé de stockage primaire (ou secondaire). Actuellement, les clés SAS ne sont pas prises en charge pour le montage.

* **Vérifiez que le port 445 est ouvert** : SMB communique via le port TCP 445. Assurez-vous que votre pare-feu ne bloque pas les ports TCP 445 de la machine cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montage du partage de fichiers Azure à la demande avec `mount`
1. **[Installez le package cifs-utils pour votre distribution Linux](#install-cifs-utils)**.

2. **Créez un dossier pour le point de montage** : cela peut être fait n’importe où sur le système de fichiers.

    ```
    mkdir mymountpoint
    ```

3. **Utilisez la commande de montage pour monter le partage de fichiers Azure** : n’oubliez pas de remplacer `<storage-account-name>`, `<share-name>` et `<storage-account-key>` par les informations appropriées.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Lorsque vous avez terminé d’utiliser le partage de fichiers Azure, vous pouvez utiliser `sudo umount ./mymountpoint` pour démonter le partage.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Création d’un point de montage persistant pour le partage de fichiers Azure avec `/etc/fstab`
1. **[Installez le package cifs-utils pour votre distribution Linux](#install-cifs-utils)**.

2. **Créez un dossier pour le point de montage** : cela peut être fait n’importe où sur le système de fichiers, mais vous devez noter le chemin d’accès absolu du dossier. L’exemple suivant permet de créer un dossier à la racine.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Utilisez la commande suivante pour ajouter la ligne suivante à `/etc/fstab`** : n’oubliez pas de remplacer `<storage-account-name>`, `<share-name>` et `<storage-account-key>` par les informations appropriées.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs nofail,vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> N’oubliez pas d’ajouter l’option `nofail` dans l’entrée `/etc/fstab`. Sinon, votre machine virtuelle risque de ne pas répondre au démarrage si une configuration incorrecte ou une autre erreur est détectée pendant le montage du partage de fichiers Azure.

> [!Note]  
> Vous pouvez utiliser `sudo mount -a` pour monter le partage de fichiers Azure après la modification de `/etc/fstab` plutôt que de redémarrer.

## <a name="feedback"></a>Commentaires
Utilisateurs de Linux, nous attendons votre avis !

Azure Files pour le groupe d’utilisateurs Linux propose un forum qui vous permet de partager vos commentaires quand vous évaluez et adoptez le stockage de fichiers sur Linux. Envoyez un e-mail aux [utilisateurs Linux d’Azure Files](mailto:azurefileslinuxusers@microsoft.com) pour rejoindre le groupe d’utilisateurs.

## <a name="next-steps"></a>étapes suivantes
Consultez ces liens pour en savoir plus sur Azure Files.
* [Référence de l’API REST du service de fichiers](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Transférer des données avec AzCopy sur Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Utilisation d’Azure CLI 2.0 avec le stockage Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [FORUM AUX QUESTIONS](../storage-files-faq.md)
* [Résolution des problèmes](storage-troubleshoot-linux-file-connection-problems.md)
