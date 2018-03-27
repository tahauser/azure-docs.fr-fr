---
title: Dans ce didacticiel, vous allez apprendre à vous mettre à jour les offres et les plans Azure Stack | Microsoft Docs
description: Cet article décrit comment afficher et modifier les offres et les plans Azure Stack existants.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>Didacticiel : Mettre à jour les offres et les plans
En tant qu’opérateur Azure Stack, vous créez des plans qui contiennent les services souhaités et les quotas applicables auxquels vos utilisateurs peuvent s’abonner. Ces *plans de base* contiennent les principaux services offerts à vos utilisateurs et vous ne pouvez avoir qu’un plan de base par offre. Si vous devez modifier votre offre, vous pouvez utiliser des *plans d’extension* qui vous permettent de modifier le plan pour étendre les quotas d’ordinateurs, de stockage ou réseau proposés initialement avec le plan de base. 

Même s’il peut s’avérer optimal de tout regrouper dans un seul plan dans certains cas, il est judicieux d’avoir un plan de base et d’offrir des services complémentaires avec des plans d’extension. Par exemple, vous pouvez choisir d’offrir des services IaaS dans le cadre d’un plan de base, avec tous les services PaaS traités comme des plans d’extension. Vous pouvez également vous servir des plans pour contrôler la consommation des ressources dans votre environnement ASDK limité. Par exemple, si vous souhaitez que vos utilisateurs fassent attention à leur utilisation des ressources, vous pourriez proposer un plan de base relativement petit (selon les services requis) où dès lors que les utilisateurs atteignent le seuil défini, ils sont avertis qu’ils ont déjà consommé les ressources allouées de leur plan attribué. À partir de là, les utilisateurs peuvent sélectionner un plan d’extension disponible pour obtenir des ressources supplémentaires. 

> [!NOTE]
> Lorsqu’un utilisateur ajoute un plan d’extension à son abonnement existant à une offre, les ressources supplémentaires peuvent prendre une heure à s’afficher. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un plan d’extension 
> * S’abonner à un plan d’extension

## <a name="create-an-add-on-plan"></a>Créer un plan d’extension
Les **plans d’extension** sont créés en modifiant une offre existante.

1. Connectez-vous au [portail Azure Stack](https://adminportal.local.azurestack.external) en tant qu’administrateur de cloud.
2. Suivez les étapes décrites précédemment pour [créer un plan de base](asdk-offer-services.md) afin de créer un plan offrant des services qui n’étaient pas proposés auparavant. Dans cet exemple, les services Key Vault (Microsoft.KeyVault) seront inclus dans le plan.
3. Dans le portail de l’administrateur, cliquez sur **Offres** , puis sélectionnez l’offre à mettre à jour avec un plan d’extension.

   ![](media/asdk-update-offers/1.PNG)

4.  Faites défiler les propriétés de l’offre jusqu’en bas et sélectionnez **Plans d’extension**. Cliquez sur **Add**.
   
    ![](media/asdk-update-offers/2.PNG)

5. Sélectionnez le plan à ajouter. Dans cet exemple, le plan se nomme **Key vault plan**. Cliquez ensuite sur **Sélectionner** pour ajouter le plan à l’offre. Vous devriez recevoir une notification vous indiquant que le plan a été ajouté à l’offre.
   
    ![](media/asdk-update-offers/3.PNG)

6. Passez en revue la liste des plans d’extension inclus avec l’offre pour vérifier que le nouveau plan y figure.
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>S’abonner à un plan d’extension
Vous devez vous connecter au portail de l’utilisateur Azure Stack pour étendre un abonnement Azure Stack existant avec un plan d’extension.

Suivez ces étapes pour découvrir les ressources supplémentaires proposées par l’opérateur Azure Stack et ajouter un plan d’extension à une offre à laquelle vous êtes déjà abonné.

1. Connectez-vous au [portail de l’utilisateur](https://portal.local.azurestack.external).
2. Pour trouver l’abonnement à étendre avec un plan d’extension, cliquez sur **Plus de services**, cliquez sur **Abonnements**, puis sélectionnez votre abonnement.
   
    ![](media/asdk-update-offers/5.PNG)

3.  Dans la vue d’ensemble de l’abonnement, cliquez sur **Ajouter un plan**.
   
    ![](media/asdk-update-offers/6.PNG)

4. Dans le panneau Ajouter un plan, sélectionnez le plan d’extension à ajouter à l’abonnement. Dans cet exemple, **Key vault plan** est sélectionné. Vous devriez ensuite recevoir une notification indiquant que le plan d’extension a été acquis et que vous devez actualiser le portail pour accéder à l’abonnement mis à jour.
   
    ![](media/asdk-update-offers/7.PNG)

5. Enfin, passez en revue les plans d’extension associés à l’abonnement pour vous assurer que le plan a été ajouté.
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un plan d’extension 
> * S’abonner à un plan d’extension

> [!div class="nextstepaction"]
> [Créer une machine virtuelle à partir d’un modèle](asdk-create-vm-template.md)

