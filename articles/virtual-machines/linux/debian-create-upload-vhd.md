---
title: Préparer un disque dur virtuel Linux Debian dans Azure | Microsoft Docs
description: Apprenez à créer des fichiers de disque dur virtuel Debian 7 et 8 pour un déploiement dans Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 9b32b298f141e9ee54b4c42d3ee9c15174daf8b7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Préparer un disque dur virtuel Debian pour Azure
## <a name="prerequisites"></a>Prérequis

Cette section suppose que vous avez déjà installé un système d’exploitation Debian Linux à l’aide d’un fichier .iso téléchargé à partir du [site web Debian](https://www.debian.org/distrib/) sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Hyper-V n’est qu’un exemple parmi d’autres. Pour obtenir des instructions sur l’utilisation de Hyper-V, consultez [Installation du rôle Hyper-V et configuration d’une machine virtuelle](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Notes d'installation
* Consultez également les [Notes générales d’installation sous Linux](create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.
* Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide de Hyper-V Manager ou de l’applet de commande **convert-vhd** .
* Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. La technique [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) peut être utilisée sur les disques de données, le cas échéant.
* Ne configurez pas une partition d'échange sur le disque du système d'exploitation. Vous pouvez configurer l’agent Linux Azure pour créer un fichier d’échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d’informations à ce sujet.
* Tous les VDH sur Azure doivent avoir une taille virtuelle alignée à 1 Mo. Lors de la conversion d’un disque brut vers VDH, vous devez vous assurer que la taille du disque brut est un multiple de 1 Mo avant la conversion. Pour plus d’informations, consultez [Notes d’installation Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Utiliser Azure-Manage pour créer des disques durs virtuels Debian
Il existe plusieurs outils permettant de créer des disques durs virtuels Debian pour Azure, par exemple les scripts [azure-manage](https://github.com/credativ/azure-manage) de [credativ](http://www.credativ.com/). Cette approche est préférable à la création d'une image à partir de zéro. Par exemple, pour créer un disque dur virtuel Debian 8, exécutez les commandes suivantes pour télécharger azure-manage (et les dépendances) puis exécutez le script azure_build_image :

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Préparer manuellement un disque dur virtuel Debian
1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.
2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.
3. Si vous configurez la machine virtuelle par rapport à un fichier ISO, commentez la ligne **deb cdrom** dans `/etc/apt/source.list`.
4. Modifiez le fichier `/etc/default/grub` et le paramètre **GRUB_CMDLINE_LINUX** comme suit pour y inclure des paramètres de noyau supplémentaires pour Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. Régénérez le grub et exécutez-le :
   
        # sudo update-grub
6. Ajoutez les référentiels Azure de Debian à /etc/apt/sources.list pour Debian 7 ou 8 :
   
    **Debian 7.x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Installez l'agent Linux Azure :
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Pour Debian 7, il est nécessaire d’exécuter le noyau 3.16 à partir du référentiel wheezy-backports. Commencez par créer un fichier nommé /etc/apt/preferences.d/linux.pref avec le contenu suivant :
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    Puis exécutez "sudo apt-get install linux-image-amd64" pour installer le nouveau noyau.
3. Mettez hors service la machine virtuelle, préparez-la pour un approvisionnement sur Azure, puis exécutez :
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Cliquez sur **Action** -> Arrêter dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel Debian pour créer des machines virtuelles dans Azure. S’il s’agit de votre premier chargement du fichier .vhd sur Azure, consultez [Créer une machine virtuelle Linux à partir d’un disque personnalisé](upload-vhd.md#option-1-upload-a-vhd).

