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
ms.date: 12/20/2017
ms.author: renash
ms.openlocfilehash: cca0d315a815faca5db07099b8e8e451ef55fad5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="use-azure-files-with-linux"></a>Utiliser Azure Files avec Linux
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l’aide du [client noyau CIFS](https://wiki.samba.org/index.php/LinuxCIFS). Cet article présente deux méthodes de montage d’un partage de fichiers Azure : à la demande avec la commande `mount` et au démarrage en créant une entrée dans `/etc/fstab`.

> [!NOTE]  
> Pour monter un partage de fichiers Azure en dehors de la région Azure dans laquelle il est hébergé, par exemple en local ou dans une région Azure différente, le système d’exploitation doit prendre en charge la fonctionnalité de chiffrement de SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Conditions préalables au montage d’un partage de fichiers Azure avec Linux et le package cifs-utils
* **Choisir une distribution Linux sur laquelle le package cifs-utils peut être installé.**  
    Les distributions Linux suivantes sont disponibles dans la galerie Azure :

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8+
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**Le package cifs-utils est installé.**  
    Le package cifs-utils peut être installé à l’aide du gestionnaire de package sur la distribution Linux de votre choix. 

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

* <a id="smb-client-reqs"></a>**Comprendre les besoins du client SMB.**  
    Azure Files peut être monté via SMB 2.1 et SMB 3.0. Pour les connexions provenant de clients en local ou dans d’autres régions Azure, Azure Files refuse SMB 2.1 (ou SMB 3.0 sans chiffrement). Si le *transfert sécurisé exigé* est activé pour un compte de stockage, Azure Files autorise uniquement les connexions utilisant SMB 3.0 avec chiffrement.
    
    La prise en charge du chiffrement SMB 3.0 a été introduite dans Linux Kernel version 4.11 et a été rétroportée sur des versions antérieures du noyau pour les distributions Linux populaires. Au moment de la publication de ce document, les distributions suivantes de la galerie Azure prennent en charge cette fonctionnalité :

    - Ubuntu Server 16.04+
    - openSUSE 42.3+
    - SUSE Linux Enterprise Server 12 SP3+
    
    Si votre distribution Linux n’est pas répertoriée ici, vous pouvez vérifier la version de Linux Kernel avec la commande suivante :

    ```
    uname -r
    ```

* **Déterminer les autorisations de fichier/répertoire du partage monté** : dans les exemples ci-dessous, l’autorisation `0777` permet d’accorder des autorisations de lecture, d’écriture et d’exécution à tous les utilisateurs. Vous pouvez les remplacer par d’autres [autorisations chmod](https://en.wikipedia.org/wiki/Chmod) comme vous le souhaitez. 

* **Nom de compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin du nom du compte de stockage.

* **Clé du compte de stockage** : pour monter un partage de fichiers Azure, vous avez besoin de la clé de stockage primaire (ou secondaire). Actuellement, les clés SAS ne sont pas prises en charge pour le montage.

* **Vérifiez que le port 445 est ouvert** : SMB communique via le port TCP 445. Assurez-vous que votre pare-feu ne bloque pas les ports TCP 445 de la machine cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montage du partage de fichiers Azure à la demande avec `mount`
1. **[Installez le package cifs-utils pour votre distribution Linux](#install-cifs-utils)**.

2. **Créez un dossier pour le point de montage** : un dossier pour un point de montage peut être créé n’importe où sur le système de fichiers, mais il est d’usage courant de le créer sous le dossier `/mnt`. Par exemple : 

    ```
    mkdir /mnt/MyAzureFileShare
    ```

3. **Utilisez la commande de montage pour monter le partage de fichiers Azure** : n’oubliez pas de remplacer `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` et `<mount-point>` par les informations appropriées à votre environnement. Si votre distribution Linux prend en charge SMB 3.0 avec chiffrement (pour plus d’informations, consultez [Comprendre les besoins du client SMB](#smb-client-reqs)), utilisez `3.0` pour `<smb-version>`. Pour les distributions Linux qui ne prennent pas en charge SMB 3.0 avec chiffrement, utilisez `2.1` pour `<smb-version>`. Notez qu’un partage de fichiers Azure peut uniquement être monté en dehors d’une région Azure (notamment en local ou dans une région Azure différente) avec SMB 3.0. 

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quand vous avez terminé d’utiliser le partage de fichiers Azure, vous pouvez utiliser `sudo umount <mount-point>` pour démonter le partage.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Création d’un point de montage persistant pour le partage de fichiers Azure avec `/etc/fstab`
1. **[Installez le package cifs-utils pour votre distribution Linux](#install-cifs-utils)**.

2. **Créez un dossier pour le point de montage** : un dossier pour un point de montage peut être créé n’importe où sur le système de fichiers, mais il est d’usage courant de le créer sous le dossier `/mnt`. Lors de chaque création, notez le chemin absolu du dossier. Par exemple, la commande suivante crée un dossier sous `/mnt` (le chemin est un chemin absolu).

    ```
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Utilisez la commande suivante pour ajouter la ligne suivante à `/etc/fstab`** : n’oubliez pas de remplacer `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` et `<mount-point>` par les informations appropriées à votre environnement. Si votre distribution Linux prend en charge SMB 3.0 avec chiffrement (pour plus d’informations, consultez [Comprendre les besoins du client SMB](#smb-client-reqs)), utilisez `3.0` pour `<smb-version>`. Pour les distributions Linux qui ne prennent pas en charge SMB 3.0 avec chiffrement, utilisez `2.1` pour `<smb-version>`. Notez qu’un partage de fichiers Azure peut uniquement être monté en dehors d’une région Azure (notamment en local ou dans une région Azure différente) avec SMB 3.0. 

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Vous pouvez utiliser `sudo mount -a` pour monter le partage de fichiers Azure après la modification de `/etc/fstab` plutôt que de redémarrer.

## <a name="feedback"></a>Commentaires
Utilisateurs de Linux, nous attendons votre avis !

Azure Files pour le groupe d’utilisateurs Linux propose un forum qui vous permet de partager vos commentaires quand vous évaluez et adoptez le stockage de fichiers sur Linux. Envoyez un e-mail aux [utilisateurs Linux d’Azure Files](mailto:azurefileslinuxusers@microsoft.com) pour rejoindre le groupe d’utilisateurs.

## <a name="next-steps"></a>Étapes suivantes
Consultez ces liens pour en savoir plus sur Azure Files.
* [Présentation d’Azure Files](storage-files-introduction.md)
* [Planification d’un déploiement Azure Files](storage-files-planning.md)
* [Questions fréquentes (FAQ)](../storage-files-faq.md)
* [Résolution des problèmes](storage-troubleshoot-linux-file-connection-problems.md)