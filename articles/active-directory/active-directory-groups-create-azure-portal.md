---
title: "Créer un groupe pour des utilisateurs dans Azure Active Directory | Microsoft Docs"
description: "Comment créer un groupe dans Azure Active Directory et ajouter des membres au groupe"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 656e0e16167a73ef3c3eab21d6896f5fc280e375
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Créer un groupe et ajouter des membres dans Azure Active Directory
> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Cet article explique comment créer et remplir un nouveau groupe dans Azure Active Directory. Un groupe vous permet d’effectuer différentes tâches de gestion, par exemple l’attribution de licences ou autorisations à plusieurs utilisateurs ou appareils simultanément.

## <a name="how-do-i-create-a-group"></a>Comment créer un groupe ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Tous les services**, entrez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

   ![Ouvrir la gestion des utilisateurs](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Tous les groupes**.

   ![Ouvrir le panneau de groupes](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. Dans le panneau **Utilisateurs et groupes - Tous les groupes**, sélectionnez la commande **Ajouter**.

   ![Sélection de la commande Ajouter](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. Dans le panneau **Groupe** , ajoutez un nom et une description pour le groupe.
6. Pour sélectionner les membres à ajouter au groupe, sélectionnez **Affecté** dans la zone **Type d’appartenance**, puis sélectionnez **Membres**. Pour plus d’informations sur la façon de gérer l’appartenance à un groupe de manière dynamique, consultez la page [Utilisation d’attributs pour créer des règles avancées pour l’appartenance au groupe](active-directory-groups-dynamic-membership-azure-portal.md).

   ![Sélection de membres à ajouter](./media/active-directory-groups-create-azure-portal/select-members.png)
7. Dans le panneau **Membres**, sélectionnez un ou plusieurs utilisateurs ou appareils à ajouter au groupe, puis cliquez sur le **Sélectionner** en bas du panneau pour les ajouter au groupe. La zone **Utilisateur** filtre l’affichage en fonction de la correspondance de votre entrée avec une partie ou l’intégralité d’un nom d’utilisateur ou d’appareil. Dans cette zone aucun caractère générique n’est accepté.
8. Lorsque vous avez terminé l’ajout de membres au groupe, sélectionnez **Créer** dans le panneau **Groupe**.    

   ![Confirmation de création du groupe](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
