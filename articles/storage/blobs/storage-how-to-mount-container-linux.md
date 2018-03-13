---
title: "Comment monter le stockage Blob Azure en tant que système de fichiers sur Linux | Microsoft Docs"
description: Monter un conteneur de stockage Blob Azure avec FUSE sur Linux
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 01/19/2018
ms.author: seguler
ms.openlocfilehash: 299b96c783fb3606347bb448d00d44f0071da429
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse-preview"></a>Comment monter le stockage Blob en tant que système de fichiers avec blobfuse (préversion)

## <a name="overview"></a>Vue d’ensemble
[Blobfuse](https://github.com/Azure/azure-storage-fuse) est un pilote de système de fichiers virtuel pour le Stockage Blob Azure, qui vous permet d’accéder à vos données blob de blocs dans votre compte de stockage via le système de fichiers Linux. Le Stockage Blob Azure est un service de stockage d’objets qui n’a pas d’espace de noms hiérarchique. Blobfuse fournit cet espace de noms grâce au schéma de répertoire virtuel qui utilise la barre oblique (/) comme délimiteur.  

Ce guide vous explique comment utiliser blobfuse, monter un conteneur de stockage Blob sur Linux et accéder aux données. Pour en savoir plus sur blobfuse, consultez le [référentiel blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse ne garantit pas la conformité totale à POSIX, car il traduit simplement les demandes en [API REST Blob](https://docs.microsoft.com/en-us/rest/api/storageservices/blob-service-rest-api). Par exemple, les opérations de changement de nom sont atomiques dans POSIX, mais pas dans blobfuse.
> Pour obtenir la liste complète des différences entre un système de fichiers natif et blobfuse, consultez le [référentiel de code source blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Installer blobfuse sur Linux
Les fichiers binaires de blobfuse sont disponibles sur les [référentiels de logiciels Microsoft pour Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Pour installer blobfuse, configurez l’un de ces référentiels.

### <a name="configure-the-microsoft-package-repository"></a>Configurer le référentiel de packages Microsoft
Configurez le [référentiel de packages Linux pour les produits Microsoft](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Par exemple, sur une distribution Enterprise Linux 6 :
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

De manière similaire, remplacez l’URL par `.../rhel/7/...` pour pointer vers une distribution Enterprise Linux 7.

Autre exemple sur un Ubuntu 14.04 :
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

De manière similaire, remplacez l’URL par `.../ubuntu/16.04/...` pour pointer vers une distribution Ubuntu 16.04.

### <a name="install-blobfuse"></a>Installer blobfuse

Sur une distribution Ubuntu/Debian :
```bash
sudo apt-get install blobfuse
```

Sur une distribution Enterprise Linux :
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Préparer le montage
Blobfuse requiert un chemin d’accès temporaire dans le système de fichiers pour mettre les fichiers ouverts en mémoire tampon et en cache, afin d’obtenir des performances de type natif. Pour ce chemin d’accès temporaire, choisissez le disque le plus performant ou utilisez un disque virtuel afin d’obtenir des performances optimales. 

> [!NOTE]
> Blobfuse stocke tout le contenu des fichiers ouverts dans ce chemin d’accès temporaire. Veillez à disposer d’un espace suffisant pour prendre en compte tous les fichiers ouverts. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Facultatif) Utiliser un disque virtuel pour le chemin d’accès temporaire
L’exemple suivant crée un disque virtuel de 16 Go, ainsi qu’un répertoire pour blobfuse. Choisissez la taille selon vos besoins. Ce disque virtuel permet à blobfuse d’ouvrir des fichiers d’une taille maximale de 16 Go. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Utiliser un disque SSD pour le chemin d’accès temporaire
Dans Azure, vous pouvez utiliser les disques éphémères (SSD) disponibles sur vos machines virtuelles pour fournir une mémoire tampon à faible latence à blobfuse. Dans les distributions Ubuntu, ce disque éphémère est monté sur '/mnt', alors qu’il est monté sur '/mnt/resource/' dans les distributions RedHat et CentOS.

Vérifiez que votre utilisateur a accès au chemin d’accès temporaire :
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurer les informations d’identification de votre compte de stockage
Blobfuse requiert que vos informations d’identification soient stockées dans un fichier texte au format suivant : 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

Dès que vous avez créé ce fichier, veillez à en restreindre l’accès afin qu’aucun autre utilisateur ne puisse le lire.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Créer un répertoire vide pour le montage
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Monter

> [!NOTE]
> Pour obtenir la liste complète des options de montage, consultez le [référentiel blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Pour monter blobfuse, exécutez la commande suivante avec votre utilisateur. Cette commande monte le conteneur spécifié dans '/path/to/fuse_connection.cfg' sur l’emplacement '/mycontainer'.

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Vous devez maintenant avoir accès à vos objets blob de blocs via les API normales du système de fichiers. Notez que le répertoire monté n’est accessible qu’à l’utilisateur qui le monte, ce qui en sécurise l’accès. Si vous souhaitez autoriser l’accès à tous les utilisateurs, vous pouvez effectuer le montage avec l’option ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Étapes suivantes

* [Page d’accueil de blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Signalement des problèmes de blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

