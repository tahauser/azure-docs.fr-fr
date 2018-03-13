---
title: "Créer votre première machine virtuelle dans un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment créer votre première machine virtuelle dans un laboratoire dans Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2017
ms.author: v-craic
ms.openlocfilehash: 95fc017e64ac5c8a31be93790f53eb7fefa8f69a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Créer votre première machine virtuelle dans un laboratoire dans Azure DevTest Labs

Lorsque vous accédez initialement à DevTest Labs et que vous voulez créer votre première machine virtuelle, téléchargez une [image préchargée de base de la Place de marché](devtest-lab-configure-marketplace-images.md). Vous pourrez également choisir une [image personnalisée et une formule](devtest-lab-add-vm.md) lorsque vous allez créer d’autres machines virtuelles. 

Ce tutoriel vous guide tout au long de l’utilisation du Portail Azure pour ajouter votre première machine virtuelle à un laboratoire dans DevTest Labs.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Procédure d’ajout de votre première machine virtuelle à un laboratoire dans Azure DevTest Labs
1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
1. Dans la liste des laboratoires, sélectionnez le laboratoire dans lequel vous souhaitez créer la machine virtuelle.  
1. Dans le panneau **Vue d’ensemble** du laboratoire, sélectionnez **+ Ajouter**.  

    ![Ajout du bouton de machine virtuelle](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Dans le panneau **Choisir une base**, sélectionnez une image de la Place de marché pour la machine virtuelle.
1. Dans le panneau **Machine virtuelle**, entrez un nom pour la nouvelle machine virtuelle dans la zone de texte **Nom de la machine virtuelle**.

    ![Panneau Machine virtuelle de laboratoire](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Entrez un **nom d’utilisateur** qui obtient les privilèges d’administrateur sur la machine virtuelle.  
1. Entrez un mot de passe dans le champ de texte intitulé **Tapez une valeur**.
1. Le **type de disque de machine virtuelle** détermine le type de disque de stockage autorisé pour les machines virtuelles dans le laboratoire.
1. Sélectionnez **Taille de machine virtuelle** , puis l’un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.
1. Sélectionnez **Artefacts** et, dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l’image de base.
    **Remarque :** si vous n’êtes pas familier avec DevTest Labs ou avec la configuration d’artefacts, reportez-vous à la section [Ajout d’un artefact existant à une machine virtuelle](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm), puis reprenez la procédure à ce stade.
1. Sélectionnez **Créer** pour ajouter la machine virtuelle spécifiée au laboratoire.

   Le panneau du laboratoire affiche l’état de la création de la machine virtuelle, tout d’abord sous la forme **Création en cours**, puis sous la forme **En cours d’exécution** après le démarrage de la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
* Une fois la machine virtuelle créée, vous pouvez vous y connecter en sélectionnant **Connexion** dans le panneau de la machine virtuelle.
* Pour plus d’informations sur l’ajout des machines virtuelles suivantes à votre laboratoire, consultez [Ajouter une machine virtuelle à un laboratoire](devtest-lab-add-vm.md).
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
