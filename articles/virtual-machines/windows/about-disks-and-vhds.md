---
title: "À propos du stockage des disques non gérés (objets blob de pages) et des disques gérés pour les machines virtuelles Microsoft Azure Windows | Microsoft Docs"
description: "Découvrez les principes de base du stockage des disques non gérés (objets blob de pages) et des disques gérés pour les machines virtuelles Windows dans Azure."
services: virtual-machines
author: iainfoulds
manager: jeconnoc
ms.service: virtual-machines
ms.workload: storage
ms.tgt_pltfrm: windows
ms.topic: article
ms.date: 11/15/2017
ms.author: iainfou
ms.openlocfilehash: bf5c5cc0637b9a515bf567ff8933170d7fc1a8ba
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>À propos du stockage des disques pour les machines virtuelles Azure Windows
Comme tout autre ordinateur, les machines virtuelles dans Azure utilisent des disques comme emplacement de stockage pour un système d’exploitation, des applications et des données. Toutes les machines virtuelles Azure possèdent au moins deux disques : un disque de système d’exploitation Windows et un disque temporaire. Le disque de système d’exploitation est créé à partir d’une image. Le disque de système d’exploitation et l’image sont des disques durs virtuels (VHD) stockés dans un compte de stockage Azure. Les machines virtuelles peuvent également disposer d’un ou plusieurs disques de données, également stockés sur les VHD. 

Dans cet article, nous parlons des différentes utilisations pour les disques, puis nous abordons les types de disques que vous pouvez créer et utiliser. Cet article est également disponible pour les [machines virtuelles Linux](../linux/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Disques utilisés par les machines virtuelles

Examinons comment les disques sont utilisés par les machines virtuelles.

### <a name="operating-system-disk"></a>Disque de système d’exploitation
Chaque machine virtuelle dispose d’un disque de système d’exploitation attaché. Il est enregistré comme disque SATA et porte par défaut le nom de lecteur C:. Ce disque a une capacité maximale de 2048 gigaoctets (Go). 

### <a name="temporary-disk"></a>Disque temporaire
Chaque machine virtuelle contient un disque temporaire. Il fournit un stockage à court terme pour les applications et les processus, et est destiné à stocker seulement des données comme les fichiers de pagination ou d’échange. Les données présentes sur le disque temporaire peuvent être perdues lors d’un [événement de maintenance](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou quand vous [redéployez une machine virtuelle](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Lors d’un redémarrage standard de la machine virtuelle, les données présentes sur le disque temporaire doivent normalement être conservées.

Le disque temporaire est étiqueté lecteur D: par défaut, et utilisé pour le stockage de pagefile.sys. Pour remapper ce disque à une autre lettre de lecteur, voir [Modification de la lettre de lecteur du disque temporaire Windows](change-drive-letter.md). La taille du disque temporaire varie en fonction de la taille de la machine virtuelle. Pour plus d’informations, voir [Tailles des machines virtuelles Windows](sizes.md).

Pour plus d’informations sur l’utilisation du disque temporaire par Azure, voir [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>Disque de données
Un disque de données est un VHD attaché à une machine virtuelle pour stocker des données d’application ou d’autres données que vous devez conserver. Les disques de données sont enregistrés en tant que disques SCSI et sont nommés avec la lettre de votre choix. Chaque disque de données offre une capacité maximale de 4095 Go. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher et le type de stockage que vous pouvez utiliser pour héberger les disques.

> [!NOTE]
> Pour plus d’informations sur les capacités des machines virtuelles, consultez [Tailles des machines virtuelles Windows](sizes.md).
> 

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée un disque de système d’exploitation. Si vous utilisez une image incluant des disques de données, Azure crée également ces derniers lors de la création de la machine virtuelle. Vous pouvez également ajouter des disques de données après avoir créé la machine virtuelle.

Vous pouvez ajouter un disque de données à une machine virtuelle à tout moment, **en attachant** le disque à la machine virtuelle. Vous pouvez utiliser un disque dur virtuel que vous avez chargé ou copié sur votre compte de stockage, ou un disque dur virtuel qui a été créé pour vous par Azure. Le fait d’attacher un disque de données associe le fichier VHD à la machine virtuelle en plaçant un « bail » sur le VHD afin qu’il ne puisse pas être supprimé du stockage tant qu’il est attaché.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Une dernière recommandation : utilisez TRIM avec des disques standard non gérés 

Si vous utilisez les disques standard non gérés (disque dur), vous devez activer TRIM. TRIM ignore les blocs inutilisés sur le disque afin que vous soyez facturé uniquement pour le stockage que vous utilisez réellement. Vous pouvez ainsi faire des économies si vous créez des fichiers volumineux, puis les supprimez. 

Vous pouvez exécuter cette commande pour vérifier le paramètre TRIM. Ouvrez une invite de commandes sur votre machine virtuelle Windows et saisissez :


```
fsutil behavior query DisableDeleteNotify
```

Si la commande renvoie 0, TRIM est bien activé. Si la valeur 1 est renvoyée, exécutez la commande suivante pour activer TRIM :

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> Remarque : la prise en charge de Trim démarre avec Windows Server 2012 / Windows 8 et versions ultérieures. Voir [La nouvelle API permet aux applications d’envoyer des indications « TRIM et Unmap » au support de stockage](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints).
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Étapes suivantes
* [Attacher un disque](attach-disk-portal.md) pour ajouter un stockage supplémentaire pour votre machine virtuelle.
* [Créez un instantané](snapshot-copy-managed-disk.md).
* [Convertissez en disques gérés](convert-unmanaged-to-managed-disks.md).


