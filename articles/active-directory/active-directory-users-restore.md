---
title: "Restaurer un utilisateur supprimé dans Azure Active Directory | Microsoft Docs"
description: "Comment restaurer un utilisateur supprimé, afficher les utilisateurs pouvant être restaurés et supprimer de façon définitve un utilisateur dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: c3b7550c2aea0e8bcb7998e0e8c732894b500403
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Restaurer un utilisateur supprimé dans Azure Active Directory

Cet article contient des instructions pour restaurer ou supprimer définitivement un utilisateur supprimé précédemment. Lorsque vous supprimez un utilisateur dans Azure Active Directory (Azure AD), celui-ci est conservé pendant 30 jours à partir de la date de suppression. Durant ce laps de temps, l’utilisateur et ses propriétés peuvent être restaurés. 

## <a name="required-permissions"></a>Autorisations requises
Les autorisations suivantes sont suffisantes pour restaurer un utilisateur.

Rôle  | Autorisations 
--------- | ---------
Administrateur d’entreprise<p>Prise en charge de niveau 1 de partenaire<p>Prise en charge de niveau 2 de partenaire<p>Administrateur de compte utilisateur | Peut restaurer les utilisateurs supprimés 
Administrateur d’entreprise<p>Prise en charge de niveau 1 de partenaire<p>Prise en charge de niveau 2 de partenaire<p>Administrateur de compte utilisateur | Peut supprimer définitivement les utilisateurs

## <a name="how-to-restore-a-deleted-user"></a>Comment restaurer un utilisateur supprimé

Dans le portail Azure, vous pouvez restaurer un utilisateur supprimé, et supprimer définitivement un utilisateur supprimé.

1. Dans le [centre d’administration Azure AD](https://aad.portal.azure.com), sélectionnez **Utilisateurs et groupes** &gt; **Tous les utilisateurs**. 
2. Sous **Afficher**, filtrez la page pour afficher les **Utilisateurs supprimés récemment**. 
3. Sélectionnez un ou plusieurs utilisateurs récemment supprimés.
4. Sélectionnez **Restaurer l’utilisateur** ou **Supprimer définitivement**.

## <a name="next-steps"></a>étapes suivantes
Ces articles fournissent des informations supplémentaires sur la gestion des utilisateurs Azure Active Directory.

* [Démarrage rapide : Ajouter ou supprimer des utilisateurs dans Azure Active Directory](add-users-azure-active-directory.md)
* [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
* [Ajouter des utilisateurs invités à partir d’un autre répertoire](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Affecter un utilisateur à un rôle dans Azure AD](active-directory-users-assign-role-azure-portal.md)
