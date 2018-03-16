---
title: "Création et téléchargement d'un disque dur virtuel Linux dans Azure"
description: "Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 631557e0ad712827bb3375c4f152c0e2185fda18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="information-for-non-endorsed-distributions"></a>Informations concernant les distributions non approuvées
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Le contrat SLA de la plateforme Azure s’applique aux machines virtuelles exécutant le système d’exploitation Linux uniquement lorsqu’une des [distributions approuvées](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) est utilisée. Pour ces distributions approuvées, des images Linux sont fournies dans la Place de marché Microsoft Azure avec la configuration nécessaire.

* [Linux sur Azure : Distributions approuvées](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Prise en charge d’images Linux dans Microsoft Azure](https://support.microsoft.com/kb/2941892)

Toutes les distributions exécutées sur Azure doivent remplir les conditions suivantes pour fonctionner correctement sur la plateforme.  Cet article n'est pas exhaustif, car chaque distribution est différente. Il est également possible que, même en répondant à tous les critères ci-dessous, il s'avère nécessaire de modifier votre système Linux pour garantir son fonctionnement correct sur la plateforme.

C'est pourquoi nous recommandons de commencer avec une de nos [distributions Linux approuvées sur Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dans la mesure du possible. Les articles suivants vous montrent comment préparer les diverses distributions Linux approuvées prises en charge dans Azure :

* **[Distributions CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES et openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

La suite de cet article fournit des conseils généraux pour exécuter votre distribution Linux sur Azure.

## <a name="general-linux-installation-notes"></a>Notes générales d'installation de Linux
* Azure ne prend pas en charge le format VHDX, seulement le **VHD fixe**.  Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de l’applet de commande convert-vhd. Si vous utilisez VirtualBox, vous devez sélectionner **Taille fixe** par opposition à la valeur par défaut allouée dynamiquement lors de la création du disque.
* Azure ne prend en charge que les machines virtuelles de la génération 1. Vous pouvez convertir une machine virtuelle de génération 1 du format VHDX au format VHD et d'une taille à expansion dynamique en taille fixe. Mais vous ne pouvez pas modifier la génération d’une machine virtuelle. Pour plus d’informations, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go.
* Lors de l’installation du système Linux, il est *recommandé* d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d’éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d’exploitation doit être relié à une autre machine virtuelle identique à des fins de dépannage. La technique [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) peut être utilisée sur les disques de données.
* La prise en charge du noyau pour le montage de systèmes de fichiers UDF est requise. Au premier démarrage sur Azure, la configuration d’approvisionnement est transmise à la machine virtuelle Linux via des supports au format UDF reliés à l’invité. L’agent Linux Azure doit être en mesure de monter le système de fichiers UDF pour lire sa configuration et approvisionner la machine virtuelle.
* Les versions du noyau Linux antérieures à la version 2.6.37 ne prennent pas en charge NUMA sur Hyper-V avec des machines virtuelles de taille supérieure. Ce problème concerne principalement les distributions antérieures utilisant le noyau Red Hat 2.6.32 en amont et a été corrigé dans la version RHEL 6.6 (kernel-2.6.32-504). Pour les systèmes exécutant des noyaux personnalisés dont la version est antérieure à la version 2.6.37 ou des noyaux basés sur RHEL antérieurs à la version 2.6.32-504, le paramètre de démarrage `numa=off` doit être défini sur la ligne de commande du noyau dans grub.conf. Pour plus d’informations, consultez l’article [KB 436883](https://access.redhat.com/solutions/436883) sur Red Hat.
* Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire.  Les étapes ci-dessous fournissent plus d’informations à ce sujet.
* La taille des disques durs virtuels doit être un multiple de 1 Mo.

### <a name="installing-kernel-modules-without-hyper-v"></a>Installation de modules de noyau sans Hyper-V
Azure s’exécute sur l’hyperviseur Hyper-V. Linux nécessite donc l’installation de certains modules de noyau pour pouvoir s’exécuter dans Azure. Si vous avez une machine virtuelle qui a été créée en dehors d’Hyper-V, les programmes d’installation de Linux risquent de ne pas comprendre les pilotes pour Hyper-V dans le ramdisk initial (initrd ou initramfs), sauf s’ils détectent qu’il s’exécute dans un environnement Hyper-V. Lorsque vous utilisez un système de virtualisation différent (Virtualbox, KVM, etc.) pour préparer votre image Linux, vous devrez peut-être recréer le fichier initrd pour vous assurer que les modules noyaux `hv_vmbus` et `hv_storvsc` sont disponibles dans le ramdisk initial.  Ceci est un problème connu, touchant au moins les systèmes basés sur la distribution Red Hat en amont.

Le mécanisme de reconstruction d'image initrd ou initramfs varie en fonction de la distribution. Consultez la documentation ou le support de votre distribution pour trouver la procédure appropriée.  L’exemple suivant permet de régénérer le fichier initrd, à l’aide de l’utilitaire `mkinitrd` :

Tout d'abord, sauvegardez l'image initrd existante :

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Puis, régénérez initrd avec les modules noyau `hv_vmbus` et `hv_storvsc` :

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Redimensionnement des disques durs virtuels
Les images de disque dur virtuel sur Azure doivent avoir une taille virtuelle alignée à 1 Mo.  En règle générale, les disques durs virtuels créés à l'aide d'Hyper-V sont déjà alignés correctement.  Si le disque dur virtuel n’est pas correctement aligné, un message d’erreur semblable au suivant peut s’afficher lorsque vous tentez de créer une *image* à partir de votre disque dur virtuel :

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Pour résoudre ce problème, vous pouvez redimensionner la machine virtuelle à l’aide de la console Gestionnaire Hyper-V ou de l’applet de commande Powershell [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx) .  Si vous n’utilisez pas un environnement Windows, il est recommandé d’utiliser qemu-img pour convertir (si nécessaire) et redimensionner le disque dur virtuel.

> [!NOTE]
> Il existe un bogue connu dans la version 2.2.1 de qemu-img, qui entraîne un formatage incorrect de disque dur virtuel. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou une version antérieure, ou d’effectuer une mise à jour à la version 2.6 ou à une version ultérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Redimensionner le disque dur virtuel directement à l’aide d’outils comme `qemu-img` ou `vbox-manage` peut rendre le disque dur virtuel non démarrable.  Il est donc recommandé de convertir d'abord le disque dur virtuel en image disque RAW.  Si l'image de machine virtuelle a déjà été créée comme image disque RAW (c'est la valeur par défaut pour certains hyperviseurs comme KVM), vous pouvez ignorer cette étape :
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Calculez la taille requise pour l'image disque afin de vous assurer que la taille virtuelle est alignée à 1 Mo.  Le script shell bash suivant peut vous y aider.  Le script utilise « `qemu-img info` » pour déterminer la taille virtuelle de l’image disque, puis calcule la taille au 1 Mo supérieur :
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Redimensionnez le disque brut à l'aide de $rounded_size défini dans le script ci-dessus :
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. À présent, convertissez le disque RAW en disque dur virtuel à taille fixe :
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Ou, avec la version qemu **2.6 +** inclut l’option `force_size` :

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Conditions requises pour le noyau Linux
Les pilotes LIS (Linux Integration Services) pour Hyper-V et Azure sont directement liés au noyau Linux en amont. Ces pilotes sont déjà disponibles dans de nombreuses distributions qui comprennent une version récente du noyau Linux (3.x et supérieures). Sinon, ces distributions fournissent des versions rétroportées de ces pilotes avec leurs noyaux.  Ces pilotes sont mis à jour en permanence dans le noyau en amont avec de nouveaux correctifs et de nouvelles fonctionnalités. Aussi, dans la mesure du possible, il est recommandé d’exécuter une [distribution approuvée](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) comportant ces correctifs et ces modifications.

Si vous exécutez une variante des versions Red Hat Enterprise Linux **6.0-6.3**, vous devez installer les pilotes LIS les plus récents pour Hyper-V. Les pilotes sont disponibles [ici](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Pour RHEL **6.4+** (et les distributions dérivées), les pilotes LIS sont déjà inclus dans le noyau ; aucun package d'installation supplémentaire n'est donc nécessaire pour exécuter ces systèmes sur Azure.

Si un noyau personnalisé est requis, il est recommandé d’utiliser une version plus récente du noyau (c.-à-d. **3.8+**). Pour ces distributions ou les fournisseurs qui maintiennent leur propre noyau, il est nécessaire de rétroporter régulièrement les pilotes LIS du noyau en amont vers votre noyau personnalisé.  Même si vous exécutez une version relativement récente du noyau, il est fortement recommandé de conserver une trace des correctifs en amont des pilotes LIS et de les rétroporter en fonction des besoins. L’emplacement des fichiers sources du pilote LIS est disponible dans le fichier [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) dans l’arborescence source du noyau Linux :

    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/

Au minimum, l'absence des correctifs suivants a été reconnue pour poser des problèmes sur Azure : ils doivent donc être inclus dans le noyau. Cette liste n’est pas exhaustive ni complète pour toutes les distributions :

* [ata_piix : reporter des disques dans les pilotes Hyper-V par défaut](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc : compte des paquets en transit dans le chemin RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc : éviter l’utilisation de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc :désactiver WRITE_SAME pour RAID et les pilotes adaptateurs de l’hôte virtuel](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc : correctif de déréférence du pointeur NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc : des défaillances de la mémoire tampon de l’anneau peuvent entraîner un gel des E/S](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs : se protéger contre une double exécution de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>agent Linux Azure
L' [Agent Linux Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (waagent) est requis pour approvisionner une machine virtuelle Linux dans Azure. La dernière version, les problèmes des fichiers ou l'envoi de requêtes d'extraction sont disponibles dans le [référentiel de l'agent Linux sur GitHub](https://github.com/Azure/WALinuxAgent).

* L'agent Linux est publié avec la licence Apache 2.0. De nombreuses distributions fournissent déjà des packages RPM ou deb pour l'agent : dans certains cas, l'installation et la mise à jour ne nécessitent aucun travail.
* L'agent Linux Azure nécessite Python v2.6+.
* L'agent nécessite également le module python-pyasn1. La plupart des distributions le fournissent sous la forme d'un package indépendant que vous pouvez installer.
* Dans certains cas, l'agent Linux Azure n'est pas compatible avec NetworkManager. Dans la plupart des cas, NetworkManager, configuré dans les packages RPM/Deb fournis avec les distributions, entre en conflit avec le package waagent, ce qui entraîne la désinstallation de NetworkManager lors de l'installation du package de l'agent Linux.
* La version de l’Agent Linux Azure doit être supérieure ou égale à la version minimale prise en charge ; consultez cet article pour [plus d’informations](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Configuration générale requise Linux

* Modifiez la ligne de démarrage du noyau dans GRUB ou GRUB2 afin d'y inclure les paramètres suivants. Ceci permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure :
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants s'ils sont présents :
  
        rhgb quiet crashkernel=auto
  
    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

* Installation de l'agent Linux Azure
  
    L'agent Linux Azure est requis pour approvisionner une image Linux sur Azure.  De nombreuses distributions fournissent cet agent sous forme de package RPM ou Deb (ce package est généralement nommé WALinuxAgent ou walinuxagent).  Il est également possible d'installer manuellement cet agent en suivant les instructions du [Guide de l'agent Linux](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.  C'est généralement le cas par défaut.

* Ne créez pas d'espace swap sur le disque du système d'exploitation.
  
    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* Enfin, exécutez la commande suivante pour annuler l'approvisionnement de la machine virtuelle :
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > Sur Virtualbox, vous pouvez voir l’erreur suivante après l’exécution de « waagent -force -deprovision » : `[Errno 5] Input/output error`. Ce message d’erreur n’est pas critique et peut être ignoré.
  > 
  > 

* Vous devez ensuite arrêter la machine virtuelle et télécharger le disque dur virtuel dans Azure.

