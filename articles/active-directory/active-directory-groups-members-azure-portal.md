---
title: "Gérer les membres des groupes dans Azure Active Directory | Microsoft Docs"
description: "Comment ajouter ou supprimer des utilisateurs et des appareils dans un groupe d’Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: d399a97d-fd2a-4b2d-b73d-0975db83f41b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 49c975aa34f28c6c00591435726e538bb79a78cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Gérer l’appartenance à un groupe des utilisateurs dans votre client Azure Active Directory
Cet article explique comment gérer les membres d’un groupe dans Azure Active Directory (Azure AD).

## <a name="how-do-i-find-the-members-and-manage-them"></a>Comment trouver les membres et les gérer ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

   ![Ouvrir la gestion des utilisateurs](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Tous les groupes**.

   ![Ouvrir le panneau de groupes](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. Dans le panneau **Utilisateurs et groupes - Tous les groupes** , sélectionnez un groupe.
5. Dans le panneau **Groupe - *NomGroupe*** , sélectionnez **Membres**.

   ![Ouverture du panneau Membres](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Pour ajouter des membres au groupe, dans le panneau **Groupe - Membres**, sélectionnez **Ajouter des membres**.

   ![Commande Ajouter des membres](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. Dans le panneau **Membres**, sélectionnez un ou plusieurs utilisateurs ou appareils à ajouter au groupe, puis cliquez sur le **Sélectionner** en bas du panneau pour les ajouter au groupe. La zone **Utilisateur** filtre l’affichage en fonction de la correspondance de votre entrée avec une partie ou l’intégralité d’un nom d’utilisateur ou d’appareil. Dans cette zone aucun caractère générique n’est accepté.
8. Pour supprimer des membres du groupe, dans le panneau **Groupe - Membres** , sélectionnez un membre.
9. Dans le panneau ***NomMembre***, sélectionnez la commande **Supprimer**, puis confirmez votre choix dans l’invite de commandes.

   ![Commande Supprimer des membres](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Lorsque vous avez terminé la modification des membres du groupe, sélectionnez **Enregistrer**.

## <a name="additional-information"></a>Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
