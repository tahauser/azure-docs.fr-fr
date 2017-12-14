---
title: "Limitations d’Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Limitations actuelles d’Azure Active Directory B2B Collaboration"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 2136015eeb3d8ccfc58bf7a94b423144b9aed52e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitations d’Azure AD B2B Collaboration
Azure Active Directory (Azure AD) B2B Collaboration subit actuellement les limitations décrites dans le présent article.

## <a name="possible-double-multi-factor-authentication"></a>Risque de redondance de l’authentification multifacteur
Avec Azure AD B2B, vous pouvez appliquer l’authentification multifacteur au niveau de l’organisation de la ressource (l’organisation à l’origine de l’invitation). Les raisons de cette approche sont détaillées dans l’article [Conditional access for B2B collaboration users](active-directory-b2b-mfa-instructions.md) (Accès conditionnel pour les utilisateurs de B2B Collaboration). Si un partenaire a déjà configuré et appliqué l’authentification multifacteur, ses utilisateurs devront peut-être effectuer l’authentification une fois dans leur organisation d’origine, puis de nouveau dans les vôtres.

## <a name="instant-on"></a>Activation instantanée
Dans les flux B2B Collaboration, nous ajoutons des utilisateurs au répertoire et les mettons à jour de manière dynamique pendant l’échange d’invitation, l’affectation d’application, etc. Les mises à jour et les écritures se produisent d’ordinaire dans une instance de répertoire et doivent être répliquées entre toutes les instances. La réplication est terminée une fois toutes les instances mises à jour. Parfois, lorsque l’objet est écrit ou mis à jour dans une instance et quand l’appel pour récupérer cet objet se fait vers une autre instance, des latences de réplication peuvent se produire. Si cela se produit, actualisez ou recommencez. Si vous écrivez une application à l’aide de notre API, effectuer de nouvelles tentatives avec des temporisations peut être une pratique judicieuse et préventive pour atténuer ce problème.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
* [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
* [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
* [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
* [Configurer des applications SaaS pour B2B Collaboration](active-directory-b2b-configure-saas-apps.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
* [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
