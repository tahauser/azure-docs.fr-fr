---
title: "Ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs"
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
ms.date: 02/24/2017
ms.author: v-craic
ms.openlocfilehash: c9395966c83626d09a556442e9f0b33c4995af76
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Ajouter une machine virtuelle à un laboratoire dans Azure DevTest Labs
Si vous avez déjà [créé votre première machine virtuelle](devtest-lab-create-first-vm.md), vous l’avez probablement fait à partir d’une [image de la Place de marché](devtest-lab-configure-marketplace-images.md) préchargée. Maintenant, si vous souhaitez ajouter d’autres machines virtuelles à votre laboratoire, vous pouvez également choisir une *base* qui est soit une [image personnalisée](devtest-lab-create-template.md) soit une [formule](devtest-lab-manage-formulas.md). Ce didacticiel vous guide tout au long de l’utilisation du Portail Azure pour ajouter une machine virtuelle à un laboratoire dans DevTest Labs.

Cet article vous montre également comment gérer les artefacts d’une machine virtuelle dans votre laboratoire.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Procédure d’ajout d’une machine virtuelle à un laboratoire dans Azure DevTest Labs
1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
1. Dans la liste des laboratoires, sélectionnez le laboratoire dans lequel vous souhaitez créer la machine virtuelle.  
1. Dans le volet **Vue d’ensemble** du laboratoire, sélectionnez **+ Ajouter**.  

    ![Ajout du bouton de machine virtuelle](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Dans le volet **Choisir une base**, sélectionnez une base pour la machine virtuelle.
1. Dans le volet **Machine virtuelle**, entrez un nom pour la nouvelle machine virtuelle dans la zone de texte **Nom de la machine virtuelle**.

    ![Volet Machine virtuelle de laboratoire](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Entrez un **nom d’utilisateur** qui obtient les privilèges d’administrateur sur la machine virtuelle.  
1. Si vous souhaitez utiliser un mot de passe stocké dans votre [magasin des secrets](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), sélectionnez **Use a saved secret** (Utiliser un secret enregistré), et spécifiez une valeur de clé correspondant à votre secret (mot de passe). Dans le cas contraire, entrez un mot de passe dans le champ de texte intitulé **Tapez une valeur**.
1. Le **type de disque de machine virtuelle** détermine le type de disque de stockage autorisé pour les machines virtuelles dans le laboratoire.
1. Sélectionnez **Taille de machine virtuelle** , puis l’un des éléments prédéfinis qui spécifient les cœurs du processeur, la taille de la RAM et la taille du disque dur de la machine virtuelle à créer.
1. Sélectionnez **Artefacts** et, dans la liste des artefacts, sélectionnez et configurez les artefacts que vous souhaitez ajouter à l’image de base.
    **Remarque :** si vous n’êtes pas familier avec DevTest Labs ou avec la configuration d’artefacts, reportez-vous à la section [Ajout d’un artefact existant à une machine virtuelle](#add-an-existing-artifact-to-a-vm), puis reprenez la procédure à ce stade.
1. Sélectionnez **Paramètres avancés** pour configurer les options réseau et les options d’expiration de la machine virtuelle. 

   Pour définir une option d’expiration, choisissez l’icône de calendrier pour spécifier la date à laquelle la machine virtuelle doit être automatiquement supprimée.  Par défaut, la machine virtuelle n’expire jamais. 
1. Si vous voulez visualiser ou copier le modèle Azure Resource Manager, reportez-vous à la section [Enregistrer un modèle Azure Resource Manager](#save-azure-resource-manager-template), puis reprenez la procédure à ce stade.
1. Sélectionnez **Créer** pour ajouter la machine virtuelle spécifiée au laboratoire.
1. Le volet du laboratoire affiche l’état de la création de la machine virtuelle, tout d’abord sous la forme **Création en cours**, puis sous la forme **En cours d’exécution** après le démarrage de la machine virtuelle.

> [!NOTE]
> [Ajouter une machine virtuelle qui peut être revendiquée](devtest-lab-add-claimable-vm.md) vous montre comment créer une machine virtuelle exigible afin qu’elle soit disponible pour tout utilisateur dans le laboratoire.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Ajout d’un artefact existant à une machine virtuelle
Au cours de la création d’une machine virtuelle, vous pouvez ajouter des artefacts existants. Chaque laboratoire comprend des artefacts provenant du dépôt d’artefacts DevTest Labs public, ainsi que les artefacts que vous avez créés et ajoutés à votre propre dépôt d’artefacts.

* Les *artefacts* Azure DevTest Labs vous permettent de spécifier les *actions* qui sont effectuées lorsque la machine virtuelle est approvisionnée (exécution de scripts Windows PowerShell, exécution de commandes Bash, installation de logiciel, etc.).
* Les *paramètres* de l’artefact vous permettent de personnaliser l’artefact pour votre scénario particulier

Pour découvrir comment créer des artefacts, consultez l’article [Découvrez comment créer vos propres artefacts pour les utiliser avec DevTest Labs](devtest-lab-artifact-author.md).

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
1. Dans la liste des laboratoires, sélectionnez le laboratoire contenant la machine virtuelle avec laquelle vous souhaitez travailler.  
1. Sélectionnez **Mes machines virtuelles**.
1. Sélectionnez la machine virtuelle qui vous intéresse.
1. Sélectionnez **Gérer les artefacts**. 
1. Sélectionnez **Appliquer des artefacts**.
1. Dans le volet **Appliquer des artefacts**, sélectionnez l’artefact que vous souhaitez ajouter à la machine virtuelle.
1. Dans le volet **Ajouter un artefact**, entrez les valeurs de paramètre requises et tous les paramètres facultatifs dont vous avez besoin.  
1. Sélectionnez **Ajouter** pour ajouter l’artefact et revenir au volet **Appliquer des artefacts**.
1. Continuez à ajouter des artefacts en fonction des besoins de votre machine virtuelle.
1. Une fois que vous avez ajouté vos artefacts, vous pouvez [modifier l’ordre dans lequel les artefacts sont exécutés](#change-the-order-in-which-artifacts-are-run). Vous pouvez également revenir à [Afficher ou modifier un artefact](#view-or-modify-an-artifact).
1. Lorsque vous avez terminé d’ajouter des artefacts, sélectionnez **Appliquer**.

## <a name="change-the-order-in-which-artifacts-are-run"></a>Modification de l'ordre dans lequel les artefacts sont exécutés
Par défaut, les actions des artefacts sont exécutées dans l'ordre dans lequel ceux-ci sont ajoutés à la machine virtuelle. Les étapes suivantes montrent comment modifier l’ordre dans lequel les artefacts sont exécutés.

1. En haut du volet **Appliquer des artefacts**, sélectionnez le lien indiquant le nombre d’artefacts ajoutés à la machine virtuelle.
   
    ![Nombre d’artefacts ajoutés à la machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Dans le volet **Artefacts sélectionnés**, effectuez un glisser-déplacer sur les artefacts dans l’ordre souhaité. **Remarque :** si vous avez des difficultés à faire glisser l’artefact, vérifiez que vous effectuez cette opération à partir du côté gauche de l’artefact. 
1. Cliquez sur **OK** quand vous avez terminé.  

## <a name="view-or-modify-an-artifact"></a>Afficher ou modifier un artefact
Les étapes suivantes montrent comment afficher ou modifier les paramètres d’un artefact :

1. En haut du volet **Appliquer des artefacts**, sélectionnez le lien indiquant le nombre d’artefacts ajoutés à la machine virtuelle.
   
    ![Nombre d’artefacts ajoutés à la machine virtuelle](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Dans le volet **Artefacts sélectionnés**, sélectionnez l’artefact que vous voulez visualiser ou modifier.  
1. Dans le volet **Ajouter un artefact**, apportez les modifications nécessaires, puis cliquez sur **OK** pour fermer le volet **Ajouter un artefact**.
1. Cliquez sur **OK** pour fermer le volet **Artefacts sélectionnés**.

## <a name="save-azure-resource-manager-template"></a>Enregistrer un modèle Azure Resource Manager
Un modèle Azure Resource Manager constitue un moyen déclaratif de définir un déploiement qui peut être répété. Les étapes suivantes expliquent comment enregistrer le modèle Azure Resource Manager pour la machine virtuelle en cours de création.
Une fois enregistré, vous pouvez utiliser le modèle Azure Resource Manager pour [déployer de nouvelles machines virtuelles avec Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Dans le volet **Machine virtuelle**, sélectionnez **Afficher le modèle ARM**.
2. Dans le volet **Afficher le modèle Azure Resource Manager**, sélectionnez le texte du modèle.
3. Copiez le texte sélectionné dans le Presse-papiers.
4. Cliquez sur **OK** pour fermer le volet **Afficher le modèle Azure Resource Manager**.
5. Ouvrez un éditeur de texte.
6. Collez le texte du modèle depuis le Presse-papiers.
7. Enregistrez le fichier pour une utilisation ultérieure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
* Une fois la machine virtuelle créée, vous pouvez vous y connecter en sélectionnant **Connexion** dans le volet de la machine virtuelle.
* Découvrez comment [créer des artefacts personnalisés pour vos machines virtuelles DevTest Labs](devtest-lab-artifact-author.md).
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
