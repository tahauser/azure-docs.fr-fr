---
title: "Créer un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Créer un laboratoire dans Azure DevTest Labs pour les machines virtuelles"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: v-craic
ms.openlocfilehash: a2148f8891c94f457a95282b8a3b7067c86c62ed
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Créer un laboratoire dans Azure DevTest Labs
Un laboratoire dans Azure DevTest Labs est l’infrastructure qui comprend un groupe de ressources, telles que des machines virtuelles, qui vous permet de mieux gérer ces ressources en spécifiant des limites et des quotas. Cet article vous guide dans le processus de création d’un laboratoire à l’aide du portail Azure.

## <a name="prerequisites"></a>configuration requise
Pour créer un laboratoire, vous devez avoir :

* Un abonnement Azure. Pour en savoir plus sur les options d’achat d’Azure, consultez [Comment acheter Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/). Pour créer le laboratoire, vous devez être le propriétaire de l’abonnement.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Étapes de création d’un laboratoire dans Azure DevTest Labs
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un laboratoire dans Azure DevTest Labs. 

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Dans le menu principal sur le côté gauche, sélectionnez **Tous les services** (en haut de la liste).

    ![Option de menu Tous les services](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. Dans la liste des services disponibles, cliquez sur **DevTest Labs**.
1. Dans la zone **DevTest Labs**, sélectionnez **Ajouter**.
   
    ![Ajouter un laboratoire](./media/devtest-lab-create-lab/add-lab-button.png)

1. Sous **Créer un laboratoire Dev/Test Lab** :
   
    1. Entrez un **Nom de laboratoire** pour le nouveau laboratoire.
    2. Sélectionnez **l’abonnement** à associer au laboratoire.
    3. Sélectionnez un **Emplacement** dans lequel stocker le laboratoire.
    4. Sélectionnez **Arrêt automatique** pour spécifier si vous souhaitez activer l’arrêt automatique de toutes les machines virtuelles du laboratoire et en définir les paramètres. La fonction d’arrêt automatique est essentiellement une fonctionnalité économique dans laquelle vous pouvez spécifier les moments auxquels arrêter automatiquement la machine virtuelle. Vous pouvez modifier les paramètres d’arrêt automatique après avoir créé le laboratoire en suivant les étapes décrites dans l’article [Gérer toutes les stratégies d’un laboratoire dans Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Saisissez les informations **NOM** et **VALEUR** pour les **balises** si vous souhaitez créer un balisage personnalisé à ajouter à toutes les ressources que vous créerez dans le laboratoire. Les balises sont utiles pour vous aider à gérer et à organiser les ressources du laboratoire par catégorie. Pour plus d’informations sur les balises, y compris comment ajouter des balises après avoir créé le laboratoire, consultez [Ajouter des balises à un laboratoire](devtest-lab-add-tag.md).
    5. Sélectionnez **Épingler au tableau de bord** si vous souhaitez faire apparaître un raccourci vers le laboratoire sur le tableau de bord du portail.
    6. Sélectionnez **Options d’automatisation** pour obtenir des modèles Azure Resource Manager pour l’automatisation de la configuration. 
    7. Sélectionnez **Créer**. Vous pouvez surveiller l’état du processus de création de laboratoire en regardant la zone **Notifications**. Une fois terminé, actualisez la page pour afficher le laboratoire nouvellement créé dans la liste des laboratoires.  
    
    ![Création d’un laboratoire de DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé votre laboratoire, voici quelques étapes à prendre en compte :

* [Sécuriser l’accès à un laboratoire](devtest-lab-add-devtest-user.md)
* [Définir des stratégies de laboratoire](devtest-lab-set-lab-policy.md)
* [Créer un modèle de laboratoire](devtest-lab-create-template.md)
* [Créer des artefacts personnalisés pour vos machines virtuelles](devtest-lab-artifact-author.md)
* [Ajouter une machine virtuelle à un laboratoire](devtest-lab-add-vm.md)

