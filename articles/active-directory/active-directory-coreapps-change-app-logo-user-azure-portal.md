---
title: "Modifier le nom ou le logo d’une application d’entreprise dans Azure Active Directory | Microsoft Docs"
description: "Comment modifier le nom ou le logo d’une application d’entreprise personnalisée dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: d01303ce-e6cb-4f3b-a4d6-ec29dfd68146
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: fe03d2a8dcb39cb06679386959ac17354a543089
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Modifier le nom ou le logo d’une application d’entreprise dans Azure Active Directory
Il est facile de modifier le nom ou le logo d’une application d’entreprise personnalisée dans Azure Active Directory (Azure AD). Vous devez disposer des autorisations appropriées pour effectuer ces modifications, et vous devez être le créateur de l’application personnalisée.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Comment modifier le nom ou le logo d’une application d’entreprise ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Tous les services**, entrez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le volet **Azure Active Directory -*NomRépertoire*** (autrement dit, le volet Azure AD du répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

    ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. Dans le volet **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Dans le volet **Applications d’entreprise - Toutes les applications**, sélectionnez une application.
6. Dans le volet ***NomApplication*** (autrement dit, le volet intitulé avec le nom de l’application sélectionnée), sélectionnez **Propriétés**.

    ![Sélection de la commande Propriétés](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. Dans le volet ***NomApplication*** **- Propriétés**, accédez à un fichier à utiliser en tant que nouveau logo et/ou modifiez le nom de l’application.

    ![Modification du logo de l’application ou de la commande PropriétésNom](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Sélectionnez la commande **Enregistrer** .

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
