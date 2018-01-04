---
title: "Créer une image personnalisée Azure DevTest Labs à partir d’une machine virtuelle | Microsoft Docs"
description: "Découvrez comment créer une image personnalisée dans Azure DevTest Labs à partir d’une machine virtuelle approvisionnée à l’aide du portail Azure"
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
ms.openlocfilehash: ce854024df5c6974873cf3574bf2de282946f6d0
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>Créer une image personnalisée à partir d’une machine virtuelle

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instructions pas à pas

Vous pouvez créer une image personnalisée à partir d’une machine virtuelle approvisionnée et ensuite utiliser cette image personnalisée pour créer d’autres machines virtuelles identiques. Les étapes suivantes expliquent comment créer une image personnalisée à partir d’une machine virtuelle :

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Plus de services**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  

1. Sur le panneau du laboratoire, sélectionnez **Mes machines virtuelles**.
 
1. Dans le panneau **Mes machines virtuelles** du laboratoire, sélectionnez la machine virtuelle à partir de laquelle vous souhaitez créer l’image personnalisée.

1. Sur le panneau de la machine virtuelle, sélectionnez **Créer une image personnalisée (VHD)**.

    ![Élément de menu Créer une image personnalisée](./media/devtest-lab-create-template/create-custom-image.png)

1. Dans le panneau **Créer une image** , entrez un nom et une description pour votre image personnalisée. Ces informations s’affichent dans la liste de bases lorsque vous créez une machine virtuelle.

    ![Panneau Créer une image personnalisée](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Choisissez si sysprep a été exécuté sur la machine virtuelle. Si sysprep n’a pas été exécuté sur la machine virtuelle, sélectionnez si vous souhaitez exécuter sysprep lorsqu’une machine virtuelle est créée à partir de cette image personnalisée.

1. Cliquez sur **OK** lorsque vous avez terminé de créer l’image personnalisée.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes

- [Custom images or formulas? (Images personnalisées ou formules ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copie d’images personnalisées entre plusieurs Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>étapes suivantes

- [Ajout d’une machine virtuelle à votre laboratoire](devtest-lab-add-vm.md)
