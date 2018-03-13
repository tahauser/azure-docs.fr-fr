---
title: Ajouter ou supprimer des utilisateurs dans Azure Active Directory | Microsoft Docs
description: Explique comment ajouter de nouveaux utilisateurs ou supprimer des utilisateurs existants dans Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: f0f18f377f194e78c05e63e9b6cbc31c1b945335
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Démarrage rapide : Ajouter de nouveaux utilisateurs à Azure Active Directory
Cet article explique comment supprimer ou ajouter de nouveaux utilisateurs de votre organisation à Azure Active Directory (Azure AD) à l’aide du portail Azure ou en synchronisant vos données de compte d’utilisateur Windows Server AD local. 

## <a name="add-cloud-based-users"></a>Ajouter des utilisateurs basés sur le cloud
1. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Azure Active Directory**, puis **Utilisateurs et groupes**.
3. Dans **Utilisateurs et groupes**, sélectionnez **Tous les utilisateurs**, puis **Nouvel utilisateur**.
   ![Sélection de la commande Ajouter](./media/add-users-azure-active-directory/add-user.png)
4. Entrez les détails de l’utilisateur, dont son **nom** et son **nom d’utilisateur**. La partie du nom de domaine du nom d’utilisateur doit être le nom de domaine initial par défaut, « [nom de domaine].onmicrosoft.com », ou un [nom de domaine personnalisé](add-custom-domain.md) vérifié, non fédéré, comme « contoso.com ».
5. Copiez ou notez d’une autre façon le mot de passe généré de sorte à pouvoir le fournir à l’utilisateur une fois ce processus terminé.
6. Si vous le souhaitez, vous pouvez ouvrir et renseigner les informations dans **Profil**, **Groupes** ou **Rôle d’annuaire** pour l’utilisateur. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles-azure-portal.md).
7. Dans **Utilisateur**, sélectionnez **Créer**.
8. Distribuez de manière sécurisée le mot de passe généré au nouvel utilisateur afin qu’il puisse se connecter.

> [!TIP]
> Vous pouvez également synchroniser les données de compte d’utilisateur à partir de Windows Server AD local. Les solutions d'identité de Microsoft regroupent des fonctionnalités, locales et cloud, de création d'une identité d'utilisateur unique pour l'authentification et l'autorisation d'accès à toutes les ressources, indépendamment de l'emplacement. Nous appelons cette identité « identité hybride ». [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) peut également être utilisé pour intégrer vos répertoires locaux à Azure Active Directory pour les scénarios d’identité hybride. Cela vous permet de fournir une identité commune à vos utilisateurs pour les applications Office 365, Azure et SaaS intégrées à Azure AD. 

## <a name="delete-users-from-azure-ad"></a>Supprimer des utilisateurs d’Azure AD
1. Connectez-vous au [centre d’administration Azure Active Directory](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Utilisateurs et groupes**.
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez l’utilisateur à supprimer dans la liste. 
4. Dans le panneau de l’utilisateur sélectionné, sélectionnez **Vue d’ensemble**, puis, dans la barre de commandes, sélectionnez **Supprimer**.
   ![Sélection de la commande Ajouter](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>En savoir plus 
* [Ajouter des utilisateurs invités à partir d’un autre répertoire](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Affecter un utilisateur à un rôle dans Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
* [Restaurer un utilisateur supprimé](active-directory-users-restore.md)



## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez appris comment ajouter de nouveaux utilisateurs à Azure AD Premium. 

Vous pouvez utiliser le lien suivant pour créer un nouvel utilisateur dans Azure AD à partir du portail Azure.

> [!div class="nextstepaction"]
> [Ajouter des utilisateurs dans Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 