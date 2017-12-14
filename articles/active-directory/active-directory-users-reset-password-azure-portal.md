---
title: "Réinitialisation de mot de passe dans Azure Active Directory | Microsoft Docs"
description: "Réinitialisation de mot de passe initiée par l’administrateur pour un utilisateur dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: 6d01dff567e49b602e98f717dace4dc75abecb4c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Réinitialiser le mot de passe d’un utilisateur dans Azure Active Directory

Les administrateurs peuvent avoir besoin de réinitialiser le mot de passe d’un utilisateur, par exemple si celui-ci l’a oublié ou s’est vu verrouiller l’accès à son compte. Les étapes qui suivent vous guident tout au long du processus de réinitialisation du mot de passe d’un utilisateur.

## <a name="how-to-reset-the-password-for-a-user"></a>Comment réinitialiser le du mot de passe pour un utilisateur

1. Connectez-vous à [Centre d’administration Azure AD](https://aad.portal.azure.com) à l’aide d’un compte autorisé à réinitialiser le mot de passe des utilisateurs.
2. Sélectionnez **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez l’utilisateur dont vous souhaitez réinitialiser le mot de passe.
2. Pour l’utilisateur sélectionné, choisissez **Réinitialiser le mot de passe**.

    ![Réinitialiser le mot de passe d’un utilisateur à partir du profil utilisateur dans Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. Dans **Réinitialiser le mot de passe**, sélectionnez **Réinitialiser le mot de passe**.
7. Un mot de passe temporaire s’affiche, mot de passe que vous pouvez ensuite fournir à l’utilisateur. L’utilisateur sera alors invité à modifier son mot de passe à la prochaine ouverture de session. 

   > [!NOTE]
   > Ce mot de passe temporaire ne présente pas de délai d’expiration. Par conséquent, il restera valide jusqu’à ce que l’utilisateur se connecte et soit alors obligé de le modifier. 

## <a name="next-steps"></a>Étapes suivantes
* [Ajouter un utilisateur](active-directory-users-create-azure-portal.md)
* [Affecter des rôles d’administrateur à un utilisateur](active-directory-users-assign-role-azure-portal.md)
* [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
* [Supprimer un utilisateur dans Azure AD](active-directory-users-delete-user-azure-portal.md)
