---
title: "Qu’est-ce qu’Azure Active Directory B2B Collaboration ? | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise."
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: twooley
ms.custom: aaddev
ms.reviewer: sasubram
ms.openlocfilehash: e1fa0a3e32935a8d601eac83c19c6c2b91ded210
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Qu’est-ce qu’Azure AD B2B Collaboration ?

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

Les fonctionnalités d’Azure AD business-to-business (B2B) Collaboration permettent à n’importe quelle organisation utilisant Azure AD de travailler en toute sécurité avec des utilisateurs de n’importe quelle autre organisation, petite ou grande. Ces organisations peuvent être avec ou sans Azure AD, avec ou sans service informatique. 

Les organisations utilisant Azure AD peuvent donner accès aux documents, ressources et applications à leurs partenaires, tout en conservant un contrôle complet sur leurs propres données d’entreprise. Les développeurs peuvent utiliser les API Azure AD business-to-business pour écrire des applications qui rapprochent deux organisations de manière sécurisée. En outre, la navigation est assez facile pour les utilisateurs finaux.

97 % de nos clients nous ont dit qu’Azure AD B2B Collaboration était très important pour eux.

![Graphique à secteurs](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

Dès début avril 2017, nous avions déjà près de trois millions d’utilisateurs bénéficiant des fonctionnalités d’Azure AD B2B Collaboration. Et plus de 23 % des organisations Azure AD comptant plus de 10 utilisateurs tirent déjà parti de ces fonctionnalités.

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Principaux avantages d’Azure AD B2B Collaboration pour votre organisation

### <a name="work-with-any-user-from-any-partner"></a>Travaillez avec n’importe quel utilisateur de n’importe quel partenaire

* Les partenaires utilisent leurs propres informations d’identification

* Aucune exigence pour les partenaires d’utiliser Azure AD

* Aucun répertoire externe ou configuration complexe requis

### <a name="simple-and-secure-collaboration"></a>Collaboration simple et sécurisée

* Permet l’accès à toutes données ou applications d’entreprise avec l’application de stratégies d’autorisation puissantes d’Azure AD

* Simplicité pour les utilisateurs

* Sécurité d’entreprise pour les applications et les données

### <a name="no-management-overhead"></a>Aucune surcharge de gestion

* Aucune gestion de compte ou mot de passe externe

* Aucune gestion manuelle des synchronisations ou du cycle de vie des comptes

* Aucune surcharge administrative externe

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>Vous pouvez facilement ajouter des utilisateurs B2B Collaboration à votre organisation

Les administrateurs peuvent ajouter des utilisateurs (invités) B2B Collaboration dans le [portail Azure](https://portal.azure.com).

![ajouter des utilisateurs invités](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Permettez à vos collaborateurs d’utiliser leur propre identité

Les collaborateurs B2B peuvent se connecter avec l’identité de leur choix. Si l’utilisateur n’a pas de compte Microsoft ou Azure AD, un compte est créé pour lui en toute transparence au moment de l’utilisation de l’offre.

![choix de l’identité à la connexion](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>Déléguez aux propriétaires d’applications et de groupes 
Les propriétaires d’applications et de groupes peuvent ajouter des utilisateurs B2B directement à n’importe quelle application qui vous intéresse, qu’il s’agisse d’une application Microsoft ou non. Les administrateurs peuvent déléguer l’autorisation d’ajouter des utilisateurs B2B aux utilisateurs non administrateurs. Les utilisateurs non administrateurs peuvent utiliser le [volet d’accès aux applications Azure AD](https://myapps.microsoft.com) pour ajouter des utilisateurs B2B Collaboration aux applications ou aux groupes.

![volet d’accès](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![ajout d’un membre](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Les stratégies d’autorisation protègent votre contenu d’entreprise

Les stratégies d’accès conditionnel, telles que l’authentification multifacteur, peuvent être appliquées :
- Au niveau du locataire
- Au niveau de l’application
- Pour des utilisateurs spécifiques afin de protéger les données et les applications d’entreprise

![ajout d’un membre](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Utilisez nos API et l’exemple de code pour créer facilement des applications à intégrer
Intégrez vos partenaires externes de façon personnalisée en fonction des besoins de votre organisation.

À l’aide de nos [API d’invitation B2B Collaboration](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation), vous pouvez personnaliser votre expérience d’intégration, notamment créer des portails d’inscription libre-service. Nous fournissons un exemple de code pour un portail libre-service [sur Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![portail d’inscription](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Avec Azure AD B2B Collaboration, vous pouvez tirer parti de la puissance d’Azure AD pour protéger vos relations avec vos partenaires de façon simple et intuitive pour vos utilisateurs finaux. Alors n’hésitez pas, rejoignez les milliers d’organisations qui utilisent déjà Azure AD B2B pour leur collaboration externe !

## <a name="next-steps"></a>Étapes suivantes

* Les expériences d’administrateurs sont trouvent dans le [portail Azure](https://portal.azure.com).

* Les expériences de professionnels de l’information sont disponibles dans le [volet d’accès](https://myapps.microsoft.com).

* Et, comme toujours, contactez l’équipe produit pour vos commentaires, discussions et suggestions par le biais de notre [Communauté technologique Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [B2B collaboration user auditing and reporting](active-directory-b2b-auditing-and-reporting.md) (Audit et création de rapports relatifs aux utilisateurs B2B Collaboration)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
