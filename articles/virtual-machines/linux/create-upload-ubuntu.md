---
title: "Création et téléchargement d'un disque dur virtuel Linux Ubuntu dans Azure"
description: "Apprenez à créer et à charger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Linux Ubuntu."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 2f4983f918eccd2ae1adb4ec2c88133465f47e07
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Préparation d'une machine virtuelle Linux Ubuntu pour Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Images cloud Ubuntu officielles
Ubuntu publie désormais des disques durs virtuels Azure officiels téléchargeables depuis l’adresse [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Si vous avez besoin de créer votre propre image Ubuntu spécialisée pour Azure, plutôt que d’utiliser la procédure manuelle ci-après, nous vous recommandons de démarrer avec ces disques durs virtuels opérationnels connus et de les personnaliser selon vos besoins. Les dernières versions de l’image se trouvent toujours aux emplacements suivants :

* Ubuntu 12.04/Precise : [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty : [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial : [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## <a name="prerequisites"></a>Prérequis
Cet article suppose que vous avez déjà installé un système d'exploitation Linux Ubuntu dans un disque dur virtuel. Il existe de multiples outils dédiés à la création de fichiers .vhd, comme la solution de virtualisation Hyper-V. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d'installation Ubuntu**

* Consultez également les [Notes générales d’installation sous Linux](create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.
* Azure ne prend pas en charge le format VHDX, seulement le **VHD fixe**.  Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de l’applet de commande convert-vhd.
* Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. La technique [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) peut être utilisée sur les disques de données, le cas échéant.
* Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire.  Les étapes ci-dessous fournissent plus d’informations à ce sujet.
* La taille des disques durs virtuels doit être un multiple de 1 Mo.

## <a name="manual-steps"></a>Étapes manuelles
> [!NOTE]
> Avant d’essayer de créer votre propre image Ubuntu personnalisée pour Azure, envisagez d’utiliser les images préconçues et testées disponibles à l’adresse [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) .
> 
> 

1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2. Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3. Remplacez les référentiels actuels dans l'image par les référentiels Azure Ubuntu. Les étapes varient légèrement selon la version d'Ubuntu.
   
    Avant de modifier `/etc/apt/sources.list`, il est recommandé d’effectuer une sauvegarde :
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04 :
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04 :
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04 :
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Les images Ubuntu Azure suivent désormais le noyau *HWE* (HardWare Enablement). Exécutez les commandes suivantes pour mettre à jour le système d’exploitation avec le dernier noyau :

    Ubuntu 12.04 :
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04 :
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04 :
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **Voir aussi :**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Modifiez la ligne de démarrage du noyau afin que Grub y inclue les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier `/etc/default/grub` dans un éditeur de texte. Recherchez la variable nommée `GRUB_CMDLINE_LINUX_DEFAULT` (ou ajoutez-la le cas échéant) et modifiez-la pour inclure les paramètres suivants :
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Enregistrez ce fichier, puis fermez-le. Exécutez ensuite la commande `sudo update-grub`. Ce permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance technique d'Azure.

6. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.  C'est généralement le cas par défaut.

7. Installez l'agent Linux Azure :
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    Le package `walinuxagent` peut entraîner la suppression des packages `NetworkManager` et `NetworkManager-gnome` (s’ils sont installés).

8. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## <a name="next-steps"></a>étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel Ubuntu Linux pour créer des machines virtuelles dans Azure. S’il s’agit de votre premier chargement du fichier .vhd sur Azure, consultez [Créer une machine virtuelle Linux à partir d’un disque personnalisé](upload-vhd.md#option-1-upload-a-vhd).

## <a name="references"></a>Références
Noyau HWE (HardWare Enablement) Ubuntu :

* [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
* [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

