---
title: "Portail d’inscription en libre-service pour Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
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
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: 28c93212e247dae23cfb35a587d0d6e87cb56a6f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portail d’inscription en libre-service pour Azure AD B2B Collaboration

Les clients peuvent faire beaucoup d’opérations avec les fonctionnalités intégrées qui sont exposées par le biais de notre [portail Azure](https://portal.azure.com) d’administration utilisateur et notre [panneau d’accès aux applications](https://myapps.microsoft.com) pour les utilisateurs. Mais nous sommes également conscients que les entreprises ont besoin de personnaliser le flux de travail d’intégration pour les utilisateurs B2B en fonction des besoins de leur organisation. Elles le peuvent avec [nos API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

En discutant avec nos clients, nous avons constaté un besoin commun de sortir du lot. Une organisation invitante ne peut pas savoir à l’avance quels collaborateurs externes auront besoin d’accéder à ses ressources. Elle souhaite disposer d’un moyen de permettre à des utilisateurs d’entreprises partenaires de s’inscrire eux-mêmes avec un ensemble de stratégies que l’organisation invitante contrôle. Ce scénario est possible grâce à nos API. Nous avons donc publié un projet dédié sur GitHub : [exemple de projet GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Notre projet GitHub montre comment les organisations peuvent utiliser nos API afin d’offrir à leurs partenaires approuvés des fonctionnalités d’inscription en libre-service basées sur une stratégie, avec des règles déterminant les applications auxquelles ils peuvent accéder. Les utilisateurs partenaires peuvent accéder aux ressources quand ils en ont besoin, en toute sécurité, mais sans que l’organisation invitante n’ait à les intégrer manuellement. Vous pouvez facilement déployer le projet dans un abonnement Azure de votre choix.

## <a name="as-is-code"></a>Code en l’état

N’oubliez pas que ce code est mis à disposition en tant qu’exemple pour illustrer l’utilisation de l’API Invitation Azure Active Directory B2B. Il doit être personnalisé par votre équipe de développement ou un partenaire, et doit être révisé avant tout déploiement en environnement de production.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :
* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)