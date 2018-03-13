---
title: "Télécharger un fichier de disque dur virtuel vers Azure DevTest Labs avec PowerShell | Microsoft Docs"
description: "Télécharger le fichier de disque dur virtuel dans le compte de stockage du laboratoire avec PowerShell"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: c940f5646bc7845db7b774d7ada40b8105534d69
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Télécharger le fichier de disque dur virtuel dans le compte de stockage du laboratoire avec PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Dans Azure DevTest Labs, les fichiers de disque dur virtuel peuvent être utilisés pour créer des images personnalisées, qui servent à approvisionner des machines virtuelles. Les étapes suivantes vous guident lors de l’utilisation de PowerShell pour charger un fichier de disque dur virtuel sur le compte de stockage d’un laboratoire. Une fois que vous avez téléchargé votre fichier de disque dur virtuel, consultez la [section Étapes suivantes](#next-steps) qui répertorie quelques articles qui expliquent comment créer une image personnalisée à partir du fichier de disque dur virtuel téléchargé. Pour plus d’informations sur les disques et les disques durs virtuels dans Azure, consultez [À propos des disques et des VHD pour les machines virtuelles Azure](../virtual-machines/linux/about-disks-and-vhds.md).

## <a name="step-by-step-instructions"></a>Instructions pas à pas

Les étapes suivantes vous guident lors du téléchargement d’un fichier de disque dur virtuel dans Azure DevTest Labs avec PowerShell. 

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  

1. Dans le panneau du laboratoire, sélectionnez **Configuration**. 

1. Dans le panneau **Configuration** du laboratoire, sélectionnez **Custom images (VHDs)** (Images personnalisées (disques durs virtuels)).

1. Dans le panneau **Images personnalisées**, sélectionnez **+Ajouter**. 

1. Dans le panneau **Image personnalisée**, sélectionnez **Disque dur virtuel**.

1. Dans le panneau **Disque dur virtuel**, sélectionnez **Upload a VHD using PowerShell** (Télécharger un disque dur virtuel à l’aide de PowerShell).

    ![Télécharger un disque dur virtuel à l’aide de PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Dans le panneau **Charger un disque dur virtuel avec PowerShell**, copiez le script PowerShell généré dans un éditeur de texte.

1. Modifiez le paramètre **LocalFilePath** de l’applet de commande **Add-AzureVhd** pour pointer vers l’emplacement du fichier VHD à télécharger.

1. À l’invite de PowerShell, exécutez l’applet de commande **Add-AzureVhd** (avec le paramètre **LocalFilePath** modifié).

> [!WARNING] 
> 
> Le processus de téléchargement d’un fichier de disque dur virtuel peut durer un certain temps en fonction de sa taille et de votre vitesse de connexion.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel à l’aide du portail Azure](devtest-lab-create-template.md)
- [Créer une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel à l’aide de PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
