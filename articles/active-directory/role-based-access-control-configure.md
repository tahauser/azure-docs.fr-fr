---
title: "Contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure | Microsoft Docs"
description: "Découvrez la gestion de l’accès avec le contrôle d’accès en fonction du rôle dans le portail Azure. Utilisez des attributions de rôle pour attribuer des autorisations à vos ressources."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 57b6aa57a57317563a9da8e1765f4d97b1b79852
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="use-role-based-access-control-to-manage-access-to-your-azure-subscription-resources"></a>Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources d’un abonnement Azure
> [!div class="op_single_selector"]
> * [Gérer l’accès par utilisateur ou par groupe](role-based-access-control-manage-assignments.md)
> * [Gérer l’accès par ressource](role-based-access-control-configure.md)

Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure. L’utilisation de RBAC vous permet de n’accorder que les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Cet article vous aide à utiliser RBAC dans le portail Azure. Si vous souhaitez plus d’informations sur la gestion des droits d’accès avec RBAC, consultez [Prise en main de la gestion des accès dans le portail Azure](role-based-access-control-what-is.md).

Dans chaque abonnement, vous pouvez accorder jusqu’à 2000 attributions de rôles. 

## <a name="view-access"></a>Voir l’accès
Vous pouvez voir qui a accès à une ressource, un groupe de ressources ou un abonnement dans son panneau principal du [portail Azure](https://portal.azure.com). Par exemple, nous voulons voir qui a accès à l’un de nos groupes de ressources :

1. Sélectionnez **Groupe de ressources** dans la barre de navigation de gauche.  
    ![Groupes de ressources - icône](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Sélectionnez le nom du groupe de ressources dans le panneau **Groupes de ressources** .
3. Sélectionnez **Contrôle d’accès (IAM)** à partir du menu de gauche.  
4. Le panneau Contrôle d’accès répertorie l’ensemble des utilisateurs, groupes et applications qui ont accès au groupe de ressources.  
   
    ![Panneau Utilisateurs - comparaison Accès hérité et accès affecté (capture d’écran)](./media/role-based-access-control-configure/view-access.png)

Notez que certains rôles inclus dans l’étendue de **cette ressource**, tandis que d’autres sont **hérités** à partir d’une autre étendue. L’accès est attribué spécifiquement au groupe de ressources ou hérité d’une affectation à l’abonnement parent.

> [!NOTE]
> Les administrateurs et coadministrateurs d’abonnement classiques sont considérés comme propriétaires de l’abonnement dans le nouveau modèle RBAC.

## <a name="add-access"></a>Ajout d’un accès
Vous accordez l’accès à partir de la ressource, du groupe de ressources ou de l’abonnement qui correspond à l’étendue de l’attribution du rôle.

1. Sélectionnez **Ajouter** dans le panneau de contrôle d’accès.  
2. Sélectionnez le rôle que vous souhaitez attribuer, dans le panneau **Sélectionner un rôle** .
3. Sélectionnez l’utilisateur, le groupe ou l’application de votre répertoire auquel vous voulez accorder l’accès. Dans le répertoire, vous pouvez rechercher des noms d’affichage, des adresses de messagerie et des identificateurs d’objet.  
   
    ![Panneau Ajouter des utilisateurs - rechercher (capture d’écran)](./media/role-based-access-control-configure/grant-access2.png)
4. Sélectionnez **OK** pour créer l’attribution. La fenêtre **Ajout d’utilisateur** indique l’état d’avancement.  
    ![Barre de progression Ajout d’utilisateur - capture d’écran](./media/role-based-access-control-configure/addinguser_popup.png)

Une fois l’attribution de rôle ajoutée, elle apparaît dans le panneau **Utilisateurs** .

## <a name="remove-access"></a>Supprimer un accès
1. Faites passer votre curseur sur le nom de l’affectation que vous souhaitez supprimer. Une case à cocher s’affiche en regard du nom.
2. Utilisez les cases à cocher pour sélectionner une ou plusieurs affectations de rôles.
2. Sélectionnez **Supprimer**.  
3. Sélectionnez **Oui** pour confirmer la suppression.

Les attributions héritées ne peuvent pas être supprimées. Si vous avez besoin de supprimer une affectation héritée, vous devez le faire au niveau de l’étendue où l’affectation de rôle a été créée. Dans la colonne **Étendue**, en regard de **Hérité**, un lien vous dirige vers les ressources auxquelles ce rôle a été affecté. Accédez à la ressource indiquée pour supprimer l’attribution de rôle.

![Panneau Utilisateurs - l’accès hérité désactive le bouton (capture d’écran)](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Autres outils pour gérer l’accès
Vous pouvez attribuer des rôles et gérer l’accès avec les commandes RBAC Azure dans des outils autres que le portail Azure.  Suivez les liens pour en savoir plus sur les conditions préalables et pour vous familiariser avec les commandes RBAC Azure.

* [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
* [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
* [API REST](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Étapes suivantes
* [Créer un rapport d’historique des modifications d’accès](role-based-access-control-access-change-history-report.md)
* Consulter les [rôles RBAC intégrés](role-based-access-built-in-roles.md)
* Définissez vos propres [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md)

