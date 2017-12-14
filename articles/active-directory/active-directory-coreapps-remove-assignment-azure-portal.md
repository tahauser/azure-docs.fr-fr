---
title: "Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans Azure Active Directory | Microsoft Docs"
description: "Comment supprimer l’affectation de l’accès à un utilisateur ou à un groupe à une application d’entreprise dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 8459f9a890f6f57e8c93da9b1d703449b09ba666
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans Azure Active Directory
Il est facile de supprimer l’affectation de l’accès à un utilisateur ou un groupe à vos applications d’entreprise dans Azure Active Directory (Azure AD). Vous devez disposer des autorisations nécessaires pour gérer l’application d’entreprise, et vous devez être l’administrateur général du répertoire.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Comment supprimer une affectation d’utilisateur ou de groupe ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le panneau **Azure Active Directory - *Nom_répertoire*** (autrement dit, le panneau Azure AD correspondant au répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

    ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. Dans le panneau **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Sur le panneau **Applications d’entreprise - Toutes les applications** , sélectionnez une application.
6. Dans le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Utilisateurs et groupes**.

    ![Sélection d’utilisateurs ou groupes](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. Sur le panneau ***NomApplication***  **- Affectation d’utilisateur et de groupe**, sélectionnez un ou plusieurs utilisateurs ou groupes, puis sélectionnez la commande **Supprimer**. Confirmez votre choix dans l’invite de commandes.

    ![Sélection de la commande Supprimer](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-change-app-logo-user-azure-portal.md)
