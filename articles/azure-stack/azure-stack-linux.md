---
title: "Ajouter des images Linux à Azure Stack"
description: "Découvrez comment ajouter des images Linux à Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: anajod
ms.openlocfilehash: 29e5443de4dc43efe6d536b0f8b9cfc6ad37a669
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Ajouter des images Linux à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack* 

Vous pouvez déployer des machines virtuelles Linux sur Azure Stack en ajoutant une image Linux dans la Place de marché Azure Stack. Le moyen le plus simple d’ajouter une image Linux à Azure Stack est à travers la gestion de la Place de marché.

## <a name="marketplace-management"></a>Gestion de la Place de marché

Pour télécharger des images Linux à partir de la Place de marché Azure, utilisez les procédures décrites dans l’article suivant. Sélectionnez les images Linux que vous souhaitez proposer aux utilisateurs sur votre système Azure Stack.

[Téléchargez des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md).

## <a name="download-an-image"></a>Télécharger une image

Vous pouvez télécharger et extraire des images Linux compatibles avec Azure Stack à l’aide des liens suivants :


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. Extrayez le disque dur virtuel d’image si nécessaire et [ajoutez l’image à la Place de Marché](azure-stack-add-vm-image.md). Vérifiez que le paramètre `OSType` a la valeur `Linux`.
2. Une fois que vous avez ajouté l’image à la Place de marché, un élément de Place de marché est créé et les utilisateurs peuvent déployer une machine virtuelle Linux.

## <a name="prepare-your-own-image"></a>Préparer votre propre image

Vous pouvez préparer votre propre image Linux en appliquant l’une des instructions suivantes :
   
   * [Distributions CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES et openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Téléchargez et installez l’[agent Linux Azure](https://github.com/Azure/WALinuxAgent/).
   
    L’agent Linux Azure version 2.1.3 ou ultérieure est nécessaire pour approvisionner votre machine virtuelle Linux sur Azure Stack. La plupart des distributions répertoriées précédemment incluent déjà cette version de l’agent ou une version ultérieure en tant que package dans leurs référentiels (généralement nommé `WALinuxAgent` ou `walinuxagent`). Toutefois, si la version du package de l’agent Azure est inférieure à la version 2.1.3 (par exemple, 2.0.18 ou inférieure), vous devez installer l’agent manuellement. Vous pouvez déterminer la version installée à partir du nom de package ou en exécutant `/usr/sbin/waagent -version` sur la machine virtuelle.
   
    Pour installer l’agent Linux Azure manuellement, suivez les instructions ci-dessous :
   
   a. Commencez par télécharger l’agent Linux Azure le plus récent à partir de [GitHub](https://github.com/Azure/WALinuxAgent/releases), par exemple :
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   b. Décompressez l’agent Azure :
     
            # tar -vzxf v2.2.16.tar.gz
   c. Installez python-setuptools :
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d. Installez l’agent Azure :
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     Les systèmes sur lesquels Python 2.x et Python 3.x sont installés côte à côte devront peut-être exécuter la commande suivante :
     
         sudo python3 setup.py install --register-service
     Pour plus d’informations, consultez le [fichier LISEZMOI](https://github.com/Azure/WALinuxAgent/blob/master/README.md) de l’agent Linux Azure.
2. [Ajoutez l’image à la Place de Marché](azure-stack-add-vm-image.md). Vérifiez que le paramètre `OSType` a la valeur `Linux`.
3. Une fois que vous avez ajouté l’image à la Place de marché, un élément de Place de marché est créé et les utilisateurs peuvent déployer une machine virtuelle Linux.

## <a name="next-steps"></a>étapes suivantes
[Vue d’ensemble de l’offre de services dans Azure Stack](azure-stack-offer-services-overview.md)

