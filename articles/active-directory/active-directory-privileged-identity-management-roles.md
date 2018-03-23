---
title: Rôles dans Azure AD Privileged Identity Management | Microsoft Docs
description: Découvrez les rôles utilisés pour les identités dotées de privilèges avec l’extension Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ac812ccc-cf4e-4ac2-b981-69598056c9ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/04/2018
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 500fb11627fa0ce7ac838c650a582dd9d49b598c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Rôle d’administrateur différent dans Azure Active Directory PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Vous pouvez affecter aux utilisateurs de votre organisation différents rôles administratifs dans Azure AD. Ces affectations de rôles contrôlent quelles tâches, telles que l’ajout/la suppression d’utilisateurs ou la modification des paramètres de service, les utilisateurs sont autorisés à effectuer sur Azure AD, Office 365 et autres services Microsoft Online Services et applications connectées.  

> [!IMPORTANT]
> Microsoft vous recommande de gérer Azure AD à l’aide du [Centre d’administration Azure AD](https://aad.portal.azure.com) dans le portail Azure.

Un administrateur général peut mettre à jour les utilisateurs **définitivement** affectés aux rôles dans Azure AD à l’aide des applets de commande PowerShell, telles que `Add-MsolRoleMember` et `Remove-MsolRoleMember`, ou par le biais du portail, comme décrit à la rubrique [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Azure AD Privileged Identity Management (PIM) gère les stratégies pour l’accès privilégié des utilisateurs dans Azure AD. PIM affecte aux utilisateurs un ou plusieurs rôles dans Azure AD, et vous pouvez affecter définitivement le rôle à une personne, ou la rendre éligible au rôle. Lorsqu’un utilisateur est définitivement affecté à un rôle ou qu’il active une affectation de rôle éligible, il peut alors gérer Azure Active Directory, Office 365 et d’autres applications avec les autorisations attribuées aux rôles.

Il n’existe aucune différence entre un accès accordé de façon permanente à un utilisateur et l’affectation d’un rôle éligible. La seule différence réside dans le fait que certaines personnes n’ont pas besoin d’un accès permanent. Ces personnes deviennent éligibles au rôle et peuvent l’activer et le désactiver chaque fois que cela est nécessaire.

## <a name="roles-managed-in-pim"></a>Rôles gérés dans PIM
Privileged Identity Management vous permet d’affecter des utilisateurs à des rôles d’administrateur courants, notamment :

* **Administrateur général** (également connu sous le nom d’administrateur d’entreprise) : a accès à toutes les fonctionnalités d’administration. Vous pouvez avoir plus d’un administrateur général dans votre organisation. La personne qui s’inscrit pour acheter Office 365 devient automatiquement un administrateur général.
* **Administrateur de rôle privilégié** : gère Azure AD PIM et met à jour les attributions de rôles pour d’autres utilisateurs.  
* **Administrateur de facturation** : effectue des achats, gère les abonnements, gère les tickets de support et surveille l’état des services.
* **Administrateur de mots de passe** : réinitialise les mots de passe, gère les demandes de service et surveille l’état des services. Les administrateurs de mot de passe sont limités à la réinitialisation des mots de passe des utilisateurs.
* **Administrateur de services fédérés** : gère les demandes de service et surveille l’état des services.
  
  > [!NOTE]
  > Si vous utilisez Office 365, avant d’affecter le rôle d’administrateur de service à un utilisateur, commencez par affecter les autorisations d’administrateur à un service, comme Exchange Online.
  > 
  > 
* **Administrateur de gestion des utilisateurs** : réinitialise les mots de passe, surveille l’état des services et gère les comptes d’utilisateur, les groupes d’utilisateurs et les demandes de service. L’administrateur de gestion des utilisateurs ne peut pas supprimer un administrateur général, créer d’autres rôles d’administrateur ou réinitialiser des mots de passe pour les administrateurs de facturation, généraux et du service.
* **Administrateur Exchange** : dispose d’un accès administratif à Exchange Online par le biais du Centre d’administration Exchange (EAC) et peut effectuer presque n’importe quelle tâche dans Exchange Online.
* **Administrateur SharePoint (préversion)** : dispose d’un accès administratif à SharePoint Online par le biais du Centre d’administration SharePoint Online, et peut effectuer presque n’importe quelle tâche dans SharePoint Online. Ce rôle est actuellement en préversion. Les utilisateurs éligibles peuvent rencontrer des problèmes de latence en utilisant ce rôle dans SharePoint après son activation dans Privileged Identity Management (PIM).
* **Administrateur Skype Entreprise** : dispose d’un accès administratif à Skype Entreprise par le biais du Centre d’administration Skype Entreprise et peut effectuer presque n’importe quelle tâche dans Skype Entreprise Online.

Consultez les articles suivants pour plus d’informations sur l’[attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles-azure-portal.md) et sur l’[attribution de rôles d’administrateur dans Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


À partir de PIM, vous pouvez [affecter ces rôles à un utilisateur](active-directory-privileged-identity-management-how-to-add-role-to-user.md) pour qu’il puisse [activer le rôle si nécessaire](active-directory-privileged-identity-management-how-to-activate-role.md).

Si vous souhaitez autoriser un autre utilisateur à effectuer la gestion dans PIM, les rôles que requiert PIM pour cet utilisateur sont décrits dans [Comment accorder l’accès à PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Rôles non gérés dans PIM
Les rôles dans Exchange Online ou SharePoint Online, à l’exception de ceux mentionnés ci-dessus, ne sont pas représentés dans Azure AD et par conséquent, ne sont pas visibles dans PIM. Pour plus d’informations sur la modification des affectations de rôles affinées dans ces services Office 365, consultez [Autorisations dans Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Les groupes de ressources et les abonnements Azure ne sont pas représentés non plus dans Azure AD. Pour gérer les abonnements Azure, consultez [Guide pratique pour ajouter ou modifier des rôles d’administrateur Azure](../billing/billing-add-change-azure-subscription-administrator.md) et, pour plus d’informations sur le contrôle d’accès en fonction du rôle (RBAC) Azure, consultez [Contrôle d’accès en fonction du rôle (RBAC) Azure](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Rôles d’utilisateur et connexion
Pour certains services et applications Microsoft, l’affectation d’un utilisateur à un rôle peut ne pas suffire pour permettre à cet utilisateur d’être un administrateur.

L’accès au portail Azure nécessite que l’utilisateur soit un administrateur de service ou un coadministrateur sur un abonnement Azure, même si l’utilisateur n’a pas besoin de gérer les abonnements Azure.  Par exemple, pour gérer les paramètres de configuration pour Azure AD, un utilisateur doit être administrateur général dans Azure AD et coadministrateur des abonnements sur un abonnement Azure.  Pour savoir comment ajouter des utilisateurs aux abonnements Azure, consultez [Ajout ou modification de rôles d’administrateur Azure](../billing/billing-add-change-azure-subscription-administrator.md).

L’accès à Microsoft Online Services peut nécessiter l’affectation d’une licence à l’utilisateur pour qu’il puisse ouvrir le portail du service ou effectuer des tâches administratives.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Attribution d’une licence à un utilisateur dans Azure AD
1. Avec un compte d’administrateur général ou un compte de coadministrateur, connectez-vous au [portail Azure](http://portal.azure.com).
3. Sélectionnez Azure AD et l’annuaire que vous souhaitez utiliser et auquel des licences sont associées.
4. Sélectionnez **Licences** sur la gauche. La liste des licences disponibles s’affiche.
5. Sélectionnez le plan de licence contenant les licences que vous souhaitez distribuer.
6. Sélectionnez **Affecter des utilisateurs**.
7. Sélectionnez l’utilisateur auquel vous souhaitez affecter une licence.
8. Cliquez sur le bouton **Affecter** .  L’utilisateur peut à présent se connecter à Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

