---
title: "Désactiver les connexions utilisateur pour une application d’entreprise dans Azure Active Directory | Microsoft Docs"
description: "Comment désactiver une application d’entreprise afin qu’aucun utilisateur ne puisse s’y connecter dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: d31aa3b1399bab2b1dcfdb2eb6ece393610c769f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Désactiver les connexions utilisateur pour une application d’entreprise dans Azure Active Directory
Il est facile de désactiver une application d’entreprise afin qu’aucun utilisateur ne puisse s’y connecter dans Azure Active Directory (Azure AD). Vous devez disposer des autorisations nécessaires pour gérer l’application d’entreprise, et vous devez être l’administrateur général du répertoire.

## <a name="how-do-i-disable-user-sign-ins"></a>Comment désactiver les connexions des utilisateurs ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le panneau **Azure Active Directory** -  ***NomRépertoire*** (autrement dit, le panneau Azure AD du répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

    ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. Dans le panneau **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Sur le panneau **Applications d’entreprise - Toutes les applications** , sélectionnez une application.
6. Dans le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Propriétés**.

    ![Sélection de la commande Toutes les applications](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. Dans le panneau ***NomApplication*** - **Propriétés**, sélectionnez **Non** pour **Activé pour que les utilisateurs se connectent ?**.
8. Sélectionnez la commande **Enregistrer** .

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-change-app-logo-user-azure-portal.md)
