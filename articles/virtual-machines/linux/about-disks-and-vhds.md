---
title: "À propos du stockage des disques non gérés (objets blob de pages) et des disques gérés pour les machines virtuelles Microsoft Azure Linux | Microsoft Docs"
description: "Découvrez les principes de base du stockage des disques non gérés (objets blob de pages) et des disques gérés pour les machines virtuelles Linux dans Azure."
services: virtual-machines
author: iainfoulds
manager: jeconnoc
ms.service: virtual-machines
ms.workload: storage
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: iainfou
ms.openlocfilehash: 107e332a0f8c9d5a84a74de685ca458fb29caa8b
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>À propos du stockage des disques pour les machines virtuelles Azure Linux
Comme tout autre ordinateur, les machines virtuelles dans Azure utilisent des disques comme emplacement de stockage pour un système d’exploitation, des applications et des données. Toutes les machines virtuelles Azure possèdent au moins deux disques : un disque de système d’exploitation Linux et un disque temporaire. Le disque de système d’exploitation est créé à partir d’une image. Le disque de système d’exploitation et l’image sont en fait des disques durs virtuels (VHD) stockés dans un compte de stockage Azure. Les machines virtuelles peuvent également disposer d’un ou plusieurs disques de données, également stockés sur les VHD. 

Dans cet article, nous parlons des différentes utilisations pour les disques, puis nous abordons les types de disques que vous pouvez créer et utiliser. Cet article est également disponible pour les [machines virtuelles Windows](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Disques utilisés par les machines virtuelles

Examinons comment les disques sont utilisés par les machines virtuelles.

## <a name="operating-system-disk"></a>Disque de système d’exploitation
Chaque machine virtuelle dispose d’un disque de système d’exploitation attaché. Il est inscrit comme disque SATA et porte par défaut le nom /dev/sda. Ce disque a une capacité maximale de 2 048 gigaoctets (Go). 

## <a name="temporary-disk"></a>Disque temporaire
Chaque machine virtuelle contient un disque temporaire. Il fournit un stockage à court terme pour les applications et les processus, et est destiné à stocker seulement des données comme les fichiers de pagination ou d’échange. Les données présentes sur le disque temporaire peuvent être perdues lors d’un [événement de maintenance](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou quand vous [redéployez une machine virtuelle](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Lors d’un redémarrage standard de la machine virtuelle, les données présentes sur le disque temporaire doivent normalement être conservées.

Sur les machines virtuelles Linux, le disque se nomme généralement **/dev/sdb**, et est formaté et monté sur **/mnt** par l’agent Linux Azure. La taille du disque temporaire varie en fonction de la taille de la machine virtuelle. Pour plus d’informations, consultez [Taille des machines virtuelles Linux](../windows/sizes.md).

Pour plus d’informations sur l’utilisation du disque temporaire par Azure, voir [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disque de données
Un disque de données est un VHD attaché à une machine virtuelle pour stocker des données d’application ou d’autres données que vous devez conserver. Les disques de données sont enregistrés en tant que disques SCSI et sont nommés avec la lettre de votre choix. Chaque disque de données offre une capacité maximale de 4095 Go. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher et le type de stockage que vous pouvez utiliser pour héberger les disques.

> [!NOTE]
> Pour plus d’informations sur les capacités des machines virtuelles, consultez [Taille des machines virtuelles Linux](./sizes.md).
> 

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée un disque de système d’exploitation. Si vous utilisez une image incluant des disques de données, Azure crée également ces derniers lors de la création de la machine virtuelle. Vous pouvez également ajouter des disques de données après avoir créé la machine virtuelle.

Vous pouvez ajouter un disque de données à une machine virtuelle à tout moment, **en attachant** le disque à la machine virtuelle. Vous pouvez utiliser un VHD que vous avez chargé ou copié sur votre compte de stockage, ou qui a été créé pour vous par Azure. Le fait d’attacher un disque de données associe le fichier VHD à la machine virtuelle en plaçant un « bail » sur le VHD, afin qu’il ne puisse pas être supprimé du stockage tant qu’il est attaché.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Résolution de problèmes
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Attacher un disque](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour ajouter un stockage supplémentaire pour votre machine virtuelle.
* [Créez un instantané](snapshot-copy-managed-disk.md).
* [Convertissez en disques gérés](convert-unmanaged-to-managed-disks.md).

