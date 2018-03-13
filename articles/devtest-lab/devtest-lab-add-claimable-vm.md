---
title: "Créer et gérer des machines virtuelles revendicables dans un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment ajouter une machine virtuelle exigible à un laboratoire dans Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: v-craic
ms.openlocfilehash: 616117e1f1e55e9c59b409740e17e295ce114d52
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Créer et gérer des machines virtuelles revendicables dans un laboratoire dans Azure DevTest Labs
Vous ajoutez une machine virtuelle exigible à un laboratoire comme vous [ajouteriez une machine virtuelle standard](devtest-lab-add-vm.md), à partir d’une *base* qui est une [image personnalisée](devtest-lab-create-template.md), une [formule](devtest-lab-manage-formulas.md) ou une [image de la plateforme Place de marché](devtest-lab-configure-marketplace-images.md). Ce didacticiel vous guide tout au long de l’utilisation du Portail Azure pour ajouter une machine virtuelle revendicable à un laboratoire dans DevTest Labs, et vous présente la procédure qu’un utilisateur suit pour revendiquer ou cesser de revendiquer la machine virtuelle.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Procédure d’ajout d’une machine virtuelle exigible à un laboratoire dans Azure DevTest Labs
1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
1. Dans la liste des laboratoires, sélectionnez le laboratoire dans lequel vous souhaitez créer la machine virtuelle revendicable.  
1. Dans le volet **Vue d’ensemble** du laboratoire, sélectionnez **+ Ajouter**.  

    ![Ajout du bouton de machine virtuelle](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Dans la zone **Choisir une base**, sélectionnez une base pour la machine virtuelle.
1. Dans le volet **Machine virtuelle**, entrez un nom pour la nouvelle machine virtuelle dans la zone de texte **Nom de la machine virtuelle**.

    ![Volet Machine virtuelle de laboratoire](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Entrez un **nom d’utilisateur** qui obtient les privilèges d’administrateur sur la machine virtuelle.  
1. Si vous souhaitez utiliser un mot de passe stocké dans votre [magasin des secrets](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), sélectionnez **Use a saved secret** (Utiliser un secret enregistré), et spécifiez une valeur de clé correspondant à votre secret (mot de passe). Dans le cas contraire, entrez un mot de passe dans le champ de texte intitulé **Tapez une valeur**.
1. Le **type de disque de machine virtuelle** détermine le type de disque de stockage autorisé pour les machines virtuelles dans le laboratoire.
1. Sélectionnez **Taille de machine virtuelle** , puis l’un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.
1. Sélectionnez **Artefacts** et, dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l’image de base. Si vous n’êtes pas familier avec DevTest Labs ou avec la configuration d’artefacts, reportez-vous à la section [Ajout d’un artefact existant à une machine virtuelle](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm), puis reprenez la procédure à ce stade.
1. Sélectionnez **Paramètres avancés** pour configurer les options réseau et les options d’expiration de la machine virtuelle. Sous **Options de revendication**, choisissez **Oui** pour rendre la machine exigible.

  ![Choisissez de rendre la machine virtuelle exigible.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Si vous voulez visualiser ou copier le modèle Azure Resource Manager, reportez-vous à la section [Enregistrer un modèle Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template), puis reprenez la procédure à ce stade.
1. Sélectionnez **Créer** pour ajouter la machine virtuelle spécifiée au laboratoire.

   L’état de la création de la machine virtuelle s’affiche, tout d’abord sous la forme **Création en cours**, puis sous la forme **En cours d’exécution** après le démarrage de la machine virtuelle.

> [!NOTE]
> Si vous déployez des machines virtuelles via des [Modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md), vous pouvez créer des machines virtuelles exigibles qui peuvent être revendiquées en définissant la propriété **allowClaim** sur true dans la section Propriétés.
>
>

## <a name="using-a-claimable-vm"></a>Utilisation d’une machine virtuelle exigible

Un utilisateur peut revendiquer toute machine virtuelle dans la liste des « Machines virtuelles exigibles » en procédant comme suit :

* Dans la liste des « machines virtuelles revendicables », en bas du volet « Vue d’ensemble » du laboratoire, cliquez avec le bouton droit sur une des machines virtuelles, puis choisissez **Revendiquer la machine**.

 ![Demandez une machine virtuelle exigible spécifique.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* En haut du volet « Vue d’ensemble », choisissez **Revendiquer n’importe laquelle**. Une machine virtuelle aléatoire est attribuée dans la liste des machines virtuelles qui peuvent être revendiquées.

 ![Demandez n’importe quelle machine virtuelle exigible.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Une fois que l’utilisateur revendique une machine virtuelle, cette dernière est placée dans la liste « Mes machines virtuelles » et n’est plus exigible par un autre utilisateur.

## <a name="unclaim-a-vm"></a>Cesser de revendiquer une machine virtuelle

Quand un utilisateur a fini d’utiliser une machine virtuelle revendiquée qu’il souhaite rendre disponible pour une autre personne, il peut renvoyer la machine virtuelle revendiquée à la liste des machines virtuelles revendiquées en procédant de l’une des manières suivantes :

- Dans la liste « Mes machines virtuelles », cliquez avec le bouton droit sur l’une des machines virtuelles (ou sélectionnez ses points de suspension (...)), puis choisissez **Cesser de revendiquer**.

  ![Cessez de revendiquer une machine virtuelle figurant dans la liste des machines virtuelles.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Dans la liste « Mes machines virtuelles », sélectionnez une machine virtuelle pour ouvrir son volet de gestion, puis sélectionnez **Cesser de revendiquer** dans la barre de menus supérieure.

  ![Cessez de revendiquer un machine virtuelle dans le volet de gestion de celle-ci.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Quand un utilisateur cesse de revendiquer une machine virtuelle, il perd toute autorisation sur cette machine virtuelle de laboratoire.

### <a name="transferring-the-data-disk"></a>Transfert du disque de données
Si un utilisateur cesse de revendiquer une machine virtuelle revendicable disposant d’un disque de données attaché, celui-ci reste avec la machine virtuelle. Ensuite, quand un autre utilisateur revendique cette machine virtuelle, il revendique également le disque de données.

C’est ce qu’on appelle le « transfert du disque de données ». Le disque de données devient alors disponible dans la liste **Mes disques de données** du nouvel utilisateur qui peut ainsi le gérer.

![Cessez de revendiquer des disques de données.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Étapes suivantes
* Une fois la machine virtuelle crée, vous pouvez vous y connecter en sélectionnant **Se connecter** dans son volet de gestion.
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
