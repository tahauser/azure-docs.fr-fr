---
title: "Affectation de rôles d’administrateur à un utilisateur dans Azure Active Directory | Microsoft Docs"
description: "Expliquez comment modifier les informations administratives d’un utilisateur dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: dcb52e9de98d881474007410f3db599682e151ce
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Affecter des rôles d’administrateur à un utilisateur dans Azure Active Directory
Cet article explique comment affecter un rôle d’administration à un utilisateur dans Azure Active Directory (Azure AD). Pour en savoir plus sur l’ajout d’utilisateurs dans votre organisation, consultez [Ajout de nouveaux utilisateurs à Azure Active Directory](active-directory-users-create-azure-portal.md). Par défaut, les utilisateurs ajoutés ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez leur attribuer des rôles à tout moment.

## <a name="assign-a-role-to-a-user"></a>Affecter un rôle à un utilisateur
1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Utilisateurs et groupes**.

   ![Ouvrir la gestion des utilisateurs](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Sélectionnez **Tous les utilisateurs**.
  
  ![Ouverture du groupe Tous les utilisateurs](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Sélectionnez un utilisateur dans la liste.
5. Pour l’utilisateur sélectionné, sélectionnez **Rôle d’annuaire**, puis affectez l’utilisateur à un rôle dans la liste **Rôle d’annuaire**. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles-azure-portal.md).

      ![Affectation d’un utilisateur à un rôle](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
* [Guide de démarrage rapide : Ajouter ou supprimer des utilisateurs dans Azure Active Directory](add-users-azure-active-directory.md)
* [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
* [Ajouter des utilisateurs invités à partir d’un autre répertoire](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Affecter un utilisateur à un rôle dans Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Restaurer un utilisateur supprimé](active-directory-users-restore.md)
