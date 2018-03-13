---
title: "Ajouter des utilisateurs B2B Collaboration à Azure Active Directory sans invitation | Microsoft Docs"
description: "Vous pouvez permettre à un utilisateur invité d’ajouter d’autres utilisateurs invités à votre Azure AD sans échanger d’invitation dans Azure Active Directory B2B Collaboration."
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
ms.openlocfilehash: 68d828b8fb23f7074be5fe691a4250d0fa653ab7
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Ajouter des utilisateurs invités B2B Collaboration sans invitation

Vous pouvez autoriser un utilisateur, tel qu’un représentant de partenaire, à ajouter des utilisateurs du partenaire vers votre organisation sans avoir à échanger des invitations. Il vous suffit d’accorder à cet utilisateur des privilèges d’énumération dans le répertoire que vous utilisez pour l’organisation partenaire. 

Accordez ces privilèges lorsque :

1. Un utilisateur de l’organisation hôte (par exemple, WoodGrove) invite un utilisateur d’une organisation partenaire (par exemple, Sam@litware.com) en tant qu’invité.
2. L’administrateur de l’organisation hôte définit les stratégies qui permettent à Sam d’identifier et d’ajouter d’autres utilisateurs de l’organisation partenaire (Litware).
3. Maintenant, Sam peut ajouter d’autres utilisateurs Litware au répertoire, à des groupes et à des applications WoodGrove sans avoir besoin d’utiliser des invitations. Si Sam possède les privilèges d’énumération appropriés de Litware, cela se produit automatiquement.

### <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)