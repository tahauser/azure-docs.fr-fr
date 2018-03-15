---
title: "Création et téléchargement d’un disque dur virtuel Red Hat Enterprise Linux pour une utilisation dans Azure | Microsoft Docs"
description: "Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Red Hat Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: szark
ms.openlocfilehash: 9769b3968f841334aebdc4a371ecd59e64d5ebc2
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Préparation d'une machine virtuelle Red Hat pour Azure
Dans cet article, vous allez apprendre à préparer une machine virtuelle Red Hat Enterprise Linux (RHEL) à utiliser dans Azure. Cet article couvre les versions de RHEL 6.7 et 7.1+. Les hyperviseurs de préparation abordés dans cet article sont Hyper-V, KVM (Machine virtuelle basée sur le noyau) et VMware. Pour plus d’informations sur les conditions d’éligibilité pour participer au programme d’accès au Cloud de Red Hat, consultez le [site Web d’accès au cloud de Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) et [Exécution RHEL sous Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Préparer une machine virtuelle Red Hat à partir du Gestionnaire Hyper-V

### <a name="prerequisites"></a>Prérequis
Cette section suppose que vous avez déjà obtenu un fichier ISO depuis le site web Red Hat et installé l’image RHEL sur un disque dur virtuel (VHD). Pour plus d’informations sur l’utilisation du Gestionnaire Hyper-V pour installer une image de système d’exploitation, voir [Installer le rôle Hyper-V et configurer une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d'installation de RHEL**

* Azure ne prend pas en charge le format VHDX. Azure prend uniquement en charge les VHD fixes. Vous pouvez utiliser Hyper-V Manager pour convertir le disque au format VHD, ou l’applet de commande Convert-VHD. Si vous utilisez VirtualBox, sélectionnez **Taille fixe** par opposition à l’option de valeur par défaut allouée dynamiquement lorsque vous créez le disque.
* Azure ne prend en charge que les machines virtuelles de la génération 1. Vous pouvez convertir une machine virtuelle de génération 1 du format VHDX au format VHD et d'une taille à expansion dynamique en taille fixe. Vous ne pouvez pas modifier la génération d’une machine virtuelle. Pour plus d’informations, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go.
* Lorsque vous installez le système d’exploitation Linux, nous vous recommandons d’utiliser les partitions standard plutôt que le Gestionnaire de volumes logiques (LVM), qui constitue souvent le choix par défaut pour de nombreuses installations. Cette pratique permettra d’éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d’exploitation doit être relié à une autre machine virtuelle identique à des fins de dépannage. La technique [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) peut être utilisée sur les disques de données.
* La prise en charge du noyau pour le montage de systèmes de fichiers UDF (Universal Disk Format) est requise. Au premier démarrage sur Azure, le support au format UDF relié à l’invité transmet la configuration d’approvisionnement à la machine virtuelle Linux. L’agent Linux Azure doit être en mesure de monter le système de fichiers UDF pour lire sa configuration et approvisionner la machine virtuelle.
* Les versions du noyau Linux antérieures à 2.6.37 ne gèrent pas les accès mémoire non uniformes (NUMA) sur Hyper-V avec des machines virtuelles de grande taille. Ce problème concerne principalement les distributions antérieures utilisant le noyau Red Hat 2.6.32 en amont et a été corrigé dans la version RHEL 6.6 (kernel-2.6.32-504). Pour les systèmes exécutant des noyaux personnalisés dont la version est antérieure à la version 2.6.37 ou des noyaux basés sur RHEL antérieurs à la version 2.6.32-504, le paramètre de démarrage `numa=off` doit être défini sur la ligne de commande du noyau dans grub.conf. Pour plus d’informations, consultez l’article [KB 436883](https://access.redhat.com/solutions/436883) sur Red Hat.
* Ne configurez pas de partition swap sur le système d’exploitation ou le disque. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire.  Les étapes suivantes fournissent plus d'informations à ce sujet.
* La taille de tout disque dur virtuel doit être un multiple de 1 Mo.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Préparer une machine virtuelle RHEL 6 à partir du Gestionnaire Hyper-V

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3. Dans RHEL 6, NetworkManager peut interférer avec l’agent Linux Azure. Exécutez la commande suivante pour désinstaller le package :
   
        # sudo rpm -e --nodeps NetworkManager

4. Créez ou modifiez le fichier `/etc/sysconfig/network`, puis ajoutez le texte suivant :
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant :
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Déplacez (ou supprimez) les règles udev afin d’éviter la génération de règles statiques pour l’interface Ethernet. Ces règles entraînent des problèmes lorsque vous clonez une machine virtuelle dans Microsoft Azure ou Hyper-V :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

8. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette modification, ouvrez `/boot/grub/menu.lst` dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure.
    
    Outre les précautions ci-dessus, nous recommandons de supprimer les paramètres suivants :
    
        rhgb quiet crashkernel=auto
    
    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.  Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus. Cette configuration peut être problématique sur les machines virtuelles de petite taille.

    >[!Important]
    RHEL 6.5 et versions antérieures doivent également définir le paramètre de noyau `numa=off`. Consultez Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

11. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. Il s’agit généralement du réglage par défaut. Modifiez /etc/ssh/sshd_config pour inclure la ligne suivante :

        ClientAliveInterval 180

12. Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    l'installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome, s'ils n'avaient pas déjà été supprimés à l'étape 3.

13. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Une fois que vous avez installé l’agent Linux Azure lors de l’étape précédente, modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # sudo subscription-manager unregister

15. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

16. Cliquez sur **Action** > **Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Préparer une machine virtuelle RHEL 7 à partir du Gestionnaire Hyper-V

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3. Créez ou modifiez le fichier `/etc/sysconfig/network`, puis ajoutez le texte suivant :
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant :
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

6. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette modification, ouvrez le fichier `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple : 
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cette configuration désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau. De plus, nous vous recommandons de supprimer les paramètres suivants :
   
        rhgb quiet crashkernel=auto
   
    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

8. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu, ce qui est généralement le réglage par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

        ClientAliveInterval 180

10. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

12. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Si vous souhaitez annuler l'inscription de l'abonnement, exécutez la commande suivante :

        # sudo subscription-manager unregister

14. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Cliquez sur **Action** > **Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Préparer une machine virtuelle Red Hat à partir de KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Préparer une machine virtuelle RHEL 6 à partir de KMV

1. Téléchargez l'image KVM de RHEL 6 depuis le site web Red Hat.

2. Définissez un mot de passe racine.

    Générez un mot de passe chiffré et copiez la sortie de la commande :

        # openssl passwd -1 changeme

    Définissez un mot de passe racine avec guestfish :
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Modifiez le second champ de l’utilisateur racine en remplaçant « !! » » par le mot de passe chiffré.

3. Créez une machine virtuelle dans KVM à partir de l’image qcow2. Définissez le type de disque sur **qcow2**, puis définissez le modèle d’appareil de l’interface réseau virtuelle sur **virtio**. Démarrez ensuite la machine virtuelle, puis connectez-vous en tant que racine.

4. Créez ou modifiez le fichier `/etc/sysconfig/network`, puis ajoutez le texte suivant :
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant :
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Déplacez (ou supprimez) les règles udev afin d’éviter la génération de règles statiques pour l’interface Ethernet. Ces règles entraînent des problèmes lorsque vous clonez une machine virtuelle dans Azure ou Hyper-V :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # chkconfig network on

8. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette configuration, ouvrez `/boot/grub/menu.lst` dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure.
    
    De plus, nous vous recommandons de supprimer les paramètres suivants :
    
        rhgb quiet crashkernel=auto
    
    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

    >[!Important]
    RHEL 6.5 et versions antérieures doivent également définir le paramètre de noyau `numa=off`. Consultez Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

10. Ajoutez des modules de Hyper-V dans initramfs :  

    Modifiez `/etc/dracut.conf` et ajoutez le contenu suivant :

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Régénérez initramfs :

        # dracut -f -v

11. Désinstallez Cloud-Init :

        # yum remove cloud-init

12. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu :

        # chkconfig sshd on

    Modifiez /etc/ssh/sshd_config pour y inclure les lignes suivantes :

        PasswordAuthentication yes
        ClientAliveInterval 180

13. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Installez l'agent linux Azure en exécutant la commande suivante :

        # yum install WALinuxAgent

        # chkconfig waagent on

15. L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Une fois que vous avez installé l’agent Linux Azure lors de l’étape précédente, modifiez les paramètres suivants dans le fichier **/etc/waagent.conf** :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # subscription-manager unregister

17. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Arrêtez la machine virtuelle dans KVM.

19. Convertissez l’image qcow2 au format VHD.

> [!NOTE]
> Il existe un bogue connu dans la version 2.2.1 de qemu-img, qui entraîne un formatage incorrect de disque dur virtuel. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou une version antérieure, ou d’effectuer une mise à jour à la version 2.6 ou à une version ultérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Préparer une machine virtuelle RHEL 7 à partir de KMV

1. Téléchargez l'image KVM de RHEL 7 depuis le site web Red Hat. Cette procédure utilise RHEL 7 comme exemple.

2. Définissez un mot de passe racine.

    Générez un mot de passe chiffré et copiez la sortie de la commande :

        # openssl passwd -1 changeme

    Définissez un mot de passe racine avec guestfish :

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Modifiez le second champ de l'utilisateur racine en remplaçant « !! » » par le mot de passe chiffré.

3. Créez une machine virtuelle dans KVM à partir de l’image qcow2. Définissez le type de disque sur **qcow2**, puis définissez le modèle d’appareil de l’interface réseau virtuelle sur **virtio**. Démarrez ensuite la machine virtuelle, puis connectez-vous en tant que racine.

4. Créez ou modifiez le fichier `/etc/sysconfig/network`, puis ajoutez le texte suivant :
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant :
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

6. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # chkconfig network on

7. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette configuration, ouvrez le fichier `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple : 
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Cette commande permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cette commande désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau. De plus, nous vous recommandons de supprimer les paramètres suivants :
   
        rhgb quiet crashkernel=auto
   
    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

9. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Ajoutez des modules de Hyper-V dans initramfs.

    Modifiez `/etc/dracut.conf` en y ajoutant le contenu suivant :

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Régénérez initramfs :

        # dracut -f -v

11. Désinstallez Cloud-Init :

        # yum remove cloud-init

12. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu :

        # systemctl enable sshd

    Modifiez /etc/ssh/sshd_config pour y inclure les lignes suivantes :

        PasswordAuthentication yes
        ClientAliveInterval 180

13. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Installez l'agent linux Azure en exécutant la commande suivante :

        # yum install WALinuxAgent

    Activez le service waagent :

        # systemctl enable waagent.service

15. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # subscription-manager unregister

17. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Arrêtez la machine virtuelle dans KVM.

19. Convertissez l’image qcow2 au format VHD.

> [!NOTE]
> Il existe un bogue connu dans la version 2.2.1 de qemu-img, qui entraîne un formatage incorrect de disque dur virtuel. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou une version antérieure, ou d’effectuer une mise à jour à la version 2.6 ou à une version ultérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Préparer une machine virtuelle Red Hat à partir de VMware
### <a name="prerequisites"></a>Prérequis
Cette section suppose que vous avez déjà installé une machine virtuelle RHEL dans VMWare. Pour plus d’informations sur l’installation d’un système d’exploitation dans VMWare, voir le document [VMware Guest Operating System Installation Guide](http://partnerweb.vmware.com/GOSIG/home.html)(Guide d’installation de système d’exploitation invité VMWare).

* Lorsque vous installez le système d’exploitation Linux, nous vous recommandons d’utiliser les partitions standard plutôt que LVM, ce qui constitue souvent le choix par défaut pour de nombreuses installations. Cela permettra d’éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d’exploitation doit être relié à une autre machine virtuelle à des fins de dépannage. Vous pouvez utiliser les techniques LVM ou RAID sur les disques de données si vous le souhaitez.
* Ne configurez pas de partition swap sur le système d’exploitation ou le disque. Vous pouvez configurer l’agent Linux pour la création d’un fichier d’échange sur le disque de ressources temporaire. Les étapes qui suivent fournissent plus d’informations à ce sujet.
* Lorsque vous créez le disque dur virtuel, sélectionnez **Stocker le disque virtuel en un seul fichier**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Préparer une machine virtuelle RHEL 6 à partir de VMWare
1. Dans RHEL 6, NetworkManager peut interférer avec l’agent Linux Azure. Exécutez la commande suivante pour désinstaller le package :
   
        # sudo rpm -e --nodeps NetworkManager

2. Créez un fichier nommé **network** dans le répertoire /etc/sysconfig/ contenant le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant :
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4. Déplacez (ou supprimez) les règles udev afin d’éviter la génération de règles statiques pour l’interface Ethernet. Ces règles entraînent des problèmes lorsque vous clonez une machine virtuelle dans Azure ou Hyper-V :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

5. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

6. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple : 
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. De plus, nous vous recommandons de supprimer les paramètres suivants :
   
        rhgb quiet crashkernel=auto
   
    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

9. Ajoutez des modules de Hyper-V dans initramfs :

    Modifiez `/etc/dracut.conf` et ajoutez le contenu suivant :

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Régénérez initramfs :

        # dracut -f -v

10. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu, ce qui est généralement le réglage par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

    ClientAliveInterval 180

11. Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

12. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # sudo subscription-manager unregister

14. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Arrêtez la machine virtuelle, puis convertissez le fichier VMDK en fichier .vhd.

> [!NOTE]
> Il existe un bogue connu dans la version 2.2.1 de qemu-img, qui entraîne un formatage incorrect de disque dur virtuel. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou une version antérieure, ou d’effectuer une mise à jour à la version 2.6 ou à une version ultérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Préparer une machine virtuelle RHEL 7 à partir de VMWare
1. Créez ou modifiez le fichier `/etc/sysconfig/network`, puis ajoutez le texte suivant :
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant :
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

3. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

4. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette modification, ouvrez le fichier `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple : 
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Cette configuration permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cela désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau. De plus, nous vous recommandons de supprimer les paramètres suivants :
   
        rhgb quiet crashkernel=auto
   
    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

6. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7. Ajoutez des modules de Hyper-V dans initramfs.

    Modifiez `/etc/dracut.conf`, ajoutez le contenu :

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Régénérez initramfs :

        # dracut -f -v

8. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. Ce paramètre est généralement la valeur par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

        ClientAliveInterval 180

9. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

11. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Si vous souhaitez annuler l'inscription de l'abonnement, exécutez la commande suivante :

        # sudo subscription-manager unregister

13. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

14. Arrêtez l’ordinateur virtuel et convertir le fichier VMDK au format VHD.

> [!NOTE]
> Il existe un bogue connu dans la version 2.2.1 de qemu-img, qui entraîne un formatage incorrect de disque dur virtuel. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou une version antérieure, ou d’effectuer une mise à jour à la version 2.6 ou à une version ultérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Préparer une machine virtuelle Red Hat à partir d’un fichier ISO grâce à l’utilisation automatique d’un fichier Kickstart
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Préparer une machine virtuelle RHEL 7 à partir d'un fichier Kickstart

1.  Créez un fichier qui inclut le contenu suivant et enregistrez-le. Pour plus d’informations sur l’installation de Kickstart, voir le document [Kickstart Installation Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)(Guide d’installation Kickstart).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2. Placez le fichier Kickstart à un emplacement auquel système d’installation peut accéder.

3. Dans le Gestionnaire Hyper-V, créez une machine virtuelle. Sur la page **Connecter un disque dur virtuel**, sélectionnez **Attacher un disque dur virtuel ultérieurement**, puis exécutez l’Assistant Nouvelle machine virtuelle.

4. Ouvrez les paramètres de la machine virtuelle :

    a.  Attachez un nouveau disque dur virtuel à la machine virtuelle. Veillez à sélectionner **Format VHD** et **Taille fixe**.

    b.  Attachez l’ISO d’installation au lecteur de DVD.

    c.  Configurez le BIOS de manière à exécuter le démarrage à partir d’un CD.

5. Démarrez la machine virtuelle. Lorsque le guide d’installation s’affiche, appuyez sur la touche **Tab** pour configurer les options de démarrage.

6. Entrez `inst.ks=<the location of the kickstart file>` à la fin des options de démarrage, puis appuyez sur **Entrée**.

7. Attendez que l'installation se termine. À la fin de l’installation, la machine virtuelle s’arrête automatiquement. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## <a name="known-issues"></a>Problèmes connus
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Impossible d’inclure le pilote Hyper-V dans le disque virtuel initial lors de l’utilisation d’un hyperviseur non-Hyper-V

Dans certains cas, les programmes d’installation de Linux n’incluent pas les pilotes pour Hyper-V dans le disque virtuel initial (initrd ou initramfs), sauf si Linux détecte que ce dernier s’exécute dans un environnement Hyper-V.

Lorsque vous utilisez un système de virtualisation différent (c’est-à-dire Virtualbox, Xen, etc.) pour préparer votre image Linux, vous pouvez avoir besoin de recréer initrd pour vous assurer que les modules noyau hv_vmbus et hv_storvsc figurent au moins parmi les modules disponibles sur le disque virtuel initial. Ceci est un problème connu, touchant au moins les systèmes basés sur la distribution Red Hat en amont.

Pour résoudre ce problème, ajoutez des modules Hyper-V dans initramfs, puis régénérez ce dernier :

Modifiez `/etc/dracut.conf` et ajoutez le contenu suivant :

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

Régénérez initramfs :

        # dracut -f -v

Pour plus d’informations, voir l’article concernant la [régénération d’initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel Red Hat Enterprise Linux pour créer des machines virtuelles dans Azure. S’il s’agit de votre premier téléchargement du fichier .vhd dans Azure, consultez [Créer une machine virtuelle Linux à partir d’un disque personnalisé avec Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).

Pour plus d’informations sur les hyperviseurs certifiés pour l’exécution de Red Hat Enterprise Linux, voir le [site web de Red Hat](https://access.redhat.com/certified-hypervisors).
