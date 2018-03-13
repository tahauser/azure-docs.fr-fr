---
title: "Ajouter un utilisateur Azure Active Directory B2B Collaboration à un rôle | Microsoft Docs"
description: "Ajouter un utilisateur invité à un rôle dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b788b003299ff11bcbdcb11a2c7a21ac5081d634
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Accorder des autorisations aux utilisateurs d’organisations partenaires dans votre locataire Azure Active Directory

Les utilisateurs Azure Active Directory (Azure AD) B2B Collaboration sont ajoutés en tant qu’utilisateurs invités au répertoire et les autorisations des invités dans le répertoire sont restreintes par défaut. Votre entreprise a peut-être besoin que certains utilisateurs invités occupent des rôles avec davantage de privilèges dans votre organisation. Pour prendre la définition de rôles avec davantage de privilèges, les utilisateurs invités peuvent être ajoutés à n’importe quel rôle souhaité, en fonction des besoins de votre organisation.

## <a name="default-role"></a>Rôle par défaut

![rôle par défaut](./media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Rôle Administrateur général

![rôle administrateur général](./media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Rôle Administrateur limité

![rôle administrateur limité](./media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
* [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
* [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
* [Configurer des applications SaaS pour B2B Collaboration](active-directory-b2b-configure-saas-apps.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
* [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)
