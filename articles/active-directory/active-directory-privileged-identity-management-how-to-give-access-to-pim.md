---
title: "Guide pratique pour donner accès à Privileged Identity Management - Azure | Microsoft Docs"
description: "Découvrez comment ajouter des rôles à des utilisateurs avec l’extension Azure Active Directory Privileged Identity Management pour qu’ils puissent gérer PIM."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: d4c53b53-2b37-41e6-813c-96ec08a1c897
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 39caeef2648730194827e04e020d8eaea5414f4f
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Donner accès à la gestion d’Azure AD Privileged Identity Management
L’administrateur global qui active Azure AD Privileged Identity Management (PIM) pour une organisation obtient automatiquement les affectations de rôles et l’accès à PIM. Aucune autre personne ne dispose d’un accès en écriture par défaut, y compris les autres administrateurs généraux. Les autres administrateurs généraux, administrateurs de la sécurité et lecteurs Sécurité ont un accès en lecture seule à Azure AD PIM. Pour donner accès à PIM, le premier utilisateur peut affecter les autres au rôle **Administrateur de rôle privilégié** . Cette affectation doit être effectuée depuis PIM proprement dit et ne peut pas être modifiée via PowerShell ou d’autres portails.

> [!NOTE]
> La gestion d’Azure AD PIM requiert l’authentification Azure MFA. Les comptes Microsoft ne pouvant pas s’inscrire pour l’authentification Azure MFA, un utilisateur qui se connecte avec un compte Microsoft ne peut pas accéder à Azure AD PIM.
> 
> 

Vérifiez qu’il y a toujours au moins deux utilisateurs dans un rôle d’administrateur de rôle privilégié, au cas où un utilisateur serait verrouillé ou son compte supprimé.

## <a name="give-another-user-access-to-manage-pim"></a>Donner accès à un autre utilisateur pour gérer PIM
1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez l’application **Azure AD Privileged Identity Management** sur le tableau de bord.
2. Sélectionnez **Gérer les rôles privilégiés** > **Administrateur de rôle privilégié** > **Ajouter**.
   
    ![Ajout d’administrateur de rôle privilégié - capture d’écran][1]
3. Sur le panneau Ajouter les utilisateurs gérés, l’étape 1 est déjà terminée. Sélectionnez l’étape 2, **Sélectionner les utilisateurs** et recherchez l’utilisateur que vous souhaitez ajouter.
   
    ![Sélection des utilisateurs - capture d’écran][2]
4. Sélectionnez l’utilisateur dans les résultats de la recherche, puis cliquez sur **OK**.
5. Cliquez sur **OK** pour enregistrer votre sélection. L’utilisateur que vous avez sélectionné s’affiche dans la liste des administrateurs de rôle privilégié.
   
   * Chaque fois que vous affectez un nouveau rôle à un utilisateur, celui-ci devient automatiquement éligible pour activer le rôle. Si vous souhaitez qu’il conserve le rôle, cliquez sur cet utilisateur dans la liste. Sélectionnez **Rendre permanent** dans le menu des informations utilisateur.
6. Envoyez à l'utilisateur un lien vers la ressource [Prise en main d'Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Supprimez les droits d'accès d'un autre utilisateur pour la gestion de PIM
Avant de supprimer le rôle d’administrateur de rôle privilégié d’un utilisateur, vérifiez toujours que deux utilisateurs y sont toujours affectés.

1. Dans le tableau de bord PIM, cliquez sur le rôle **Administrateur de rôle privilégié**.  La liste des utilisateurs actuels de ce rôle s'affiche.
2. Cliquez sur l’utilisateur dans la liste des utilisateurs.
3. Cliquez sur **Supprimer**.  Un message de confirmation s’affiche.
4. Cliquez sur **Oui** pour supprimer l’utilisateur du rôle.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
