---
title: Activer une image sous licence dans votre laboratoire Azure DevTest Labs | Microsoft Docs
description: "Découvrez comment activer une image sous licence dans Azure DevTest Labs à l’aide du portail Azure"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: v-craic
ms.openlocfilehash: 022b6340a8d2748624ba292fb4a28a956d28c6f9
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2018
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Activer une image sous licence dans votre laboratoire Azure DevTest Labs

Dans Azure DevTest Labs, une image sous licence est une image qui inclut des conditions générales (généralement fournies par un tiers) qui doivent être acceptées pour que l’image soit accessible aux utilisateurs dans le laboratoire. Les sections suivantes décrivent comment travailler avec des images sous licence afin qu’elles soient disponibles pour la création de machines virtuelles.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Pour déterminer si une image sous licence est accessible aux utilisateurs
La première étape pour permettre aux utilisateurs de créer des machines virtuelles à partir d’une image sous licence consiste à vérifier que les conditions générales ont été acceptées pour l’image sous licence. Les étapes suivantes montrent comment afficher l’état de l’offre d’une image sous licence et, si nécessaire, accepter les conditions générales.

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.  

1. Dans le volet gauche sous **Paramètres**, sélectionnez **Configuration et stratégies**.

1. Dans le volet gauche sous **Bases de machine virtuelle**, sélectionnez **Images de la Place de marché**. 

    ![Élément de menu Images de la Place de marché](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Une liste de toutes les images de la Place de Marché disponibles est affichée, notamment **l’État de l’offre** de chaque image.

    ![Liste d’images de la Place de Marché montrant l’état de l’offre pour chaque image](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    L’offre d’une image sous licence est à l’état 
    
    - **Conditions acceptées :** l’image sous licence est accessible aux utilisateurs pour la création de machines virtuelles. 
    - **Revue nécessaire des conditions :** l’image sous licence n’est actuellement pas accessible aux utilisateurs. Les conditions générales de la licence doivent être acceptées pour que les utilisateurs du laboratoire puissent l’utiliser pour créer des machines virtuelles. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Mise à disposition d’une image sous licence à des utilisateurs de laboratoire
Pour vérifier qu’une image sous licence est accessible aux utilisateurs du laboratoire, un propriétaire de laboratoire disposant d’autorisations d’administrateur doit d’abord accepter les conditions générales de cette image sous licence. Le fait d’activer un déploiement par programmation pour l’abonnement associé à une image sous licence entraîne l’acceptation automatique des conditions juridiques et des déclarations de confidentialité pour cette image. [Working with Marketplace Images on Azure Resource Manager (Utilisation d’images de la Place de Marché sur Azure Resource Manager)](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) fournit des informations supplémentaires sur le déploiement par programmation des images de la Place de Marché.

Vous pouvez activer le déploiement par programmation pour une image sous licence en suivant ces étapes :

1. Dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), accédez à la liste des **Images de la Place de marché**.

1. Identifiez une image sous licence à laquelle vous souhaitez que les utilisateurs aient accès, mais dont les conditions générales n’ont pas été acceptées. Par exemple, vous pouvez voir une Data Science Virtual Machine dont l’état est **Conditions acceptées** ou **Revue nécessaire des conditions**.

    ![Fenêtre Configurer le déploiement par programmation](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Les machines virtuelles Data Science sont des images de machines virtuelles Azure pré-installées, configurées et testées avec plusieurs outils populaires fréquemment utilisés pour l’analytique des données, l’apprentissage automatique et la formation de l’intelligence artificielle. [Présentation d’Azure Data Science Virtual Machine pour Linux et Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) fournit une grande quantité d’informations sur les machines virtuelles DSVM.
   >
   >

1. Dans la colonne **État de l’offre** pour l’image, sélectionnez **Revue nécessaire des conditions**.

1. Dans la fenêtre Configurer le déploiement par programmation, sélectionnez **Activer**.

    ![Fenêtre Configurer le déploiement par programmation](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Plusieurs abonnements peuvent être répertoriés dans la fenêtre Configurer le déploiement par programmation. Vérifiez que vous activez le déploiement par programmation uniquement pour l’abonnement souhaité.
   >
   >


1. Sélectionnez **Enregistrer**. 

    Dans la liste des images de la Place de Marché, **Conditions acceptées** est maintenant affichée pour cette image, et celle-ci est accessible aux utilisateurs pour la création de machines virtuelles.

> [!NOTE]
> Les utilisateurs peuvent créer une image personnalisée à partir d’une image sous licence. Pour plus d’informations, consultez [Créer une image personnalisée à partir d’un fichier de disque dur virtuel](devtest-lab-create-template.md).
>
>


## <a name="related-blog-posts"></a>Billets de blog connexes

- [Custom images or formulas? (Images personnalisées ou formules ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copie d’images personnalisées entre plusieurs Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Étapes suivantes

- [Créer une image personnalisée à partir d’une machine virtuelle](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Créer une image personnalisée à partir d’un fichier de disque dur virtuel](devtest-lab-create-template.md)
- [Ajout d’une machine virtuelle à votre laboratoire](devtest-lab-add-vm.md)