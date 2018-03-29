---
title: 'Azure AD Connect : authentification directe | Microsoft Docs'
description: Cet article présente l’authentification directe Azure Active Directory (Azure AD) et explique comment l’utiliser pour autoriser les connexions à Azure AD en validant les mots de passe des utilisateurs à partir d’Active Directory local.
services: active-directory
keywords: qu’est-ce que l’authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: d19e63e10f2d42d97bb6fabca9c9e47028cbaf39
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Connexion de l’utilisateur avec l’authentification directe Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Qu’est-ce que l’authentification directe Azure Active Directory ?

L’authentification directe Azure Active Directory (Azure AD) permet à vos utilisateurs de se connecter aux applications locales et aux applications cloud à l’aide des mêmes mots de passe. Cette fonctionnalité offre aux utilisateurs une meilleure expérience : moins de mots de passe à mémoriser et réduction des coûts de support technique informatique, car les utilisateurs sont moins susceptibles d’oublier comment se connecter. Lorsque les utilisateurs se connectent à l’aide d’Azure AD, cette fonctionnalité valide les mots de passe utilisateurs directement à partir d’Active Directory local.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Cette fonctionnalité est une alternative à [Synchronisation du hachage de mot de passe Azure AD](active-directory-aadconnectsync-implement-password-hash-synchronization.md), qui offre les mêmes fonctionnalités d’authentification sur le cloud aux organisations. Toutefois, les stratégies de conformité et de sécurité de certaines organisations ne permettent pas à ces organisations d’envoyer des mots de passe utilisateurs, même dans un format haché, en dehors de leurs limites internes. L’authentification directe est la solution appropriée pour ces organisations.

![Authentification directe Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Vous pouvez combiner l’authentification directe avec la fonctionnalité [Authentification unique transparente](active-directory-aadconnect-sso.md). De cette façon, lorsque vos utilisateurs accèdent à leurs ordinateurs d’entreprise au sein de votre réseau d’entreprise, ils n’ont pas besoin de saisir leurs mots de passe pour se connecter.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Avantages clés de utilisation de l’authentification directe Azure AD

- *Une meilleure expérience utilisateur*
  - Les utilisateurs utilisent les mêmes mots de passe pour se connecter aux applications locales et sur le cloud.
  - Les utilisateurs passent moins de temps à communiquer avec le service d’assistance informatique pour résoudre les problèmes de mot de passe.
  - Les utilisateurs peuvent effectuer les tâches de [gestion de mot de passe en libre-service](../active-directory-passwords-overview.md) dans le cloud.
- *Facile à déployer et à gérer*
  - Des déploiements locaux ou une configuration réseau complexes ne sont pas nécessaires.
  - Seul un agent léger doit être installé localement.
  - Aucun frais de gestion. L’agent reçoit automatiquement des améliorations et correctifs de bogues.
- *Sécuriser*
  - Les mots de passe locaux ne sont jamais stockés dans le cloud sous quelque forme que ce soit.
  - L’agent établit uniquement les connexions sortantes depuis votre réseau. Il n’est donc pas nécessaire d’installer l’agent dans un réseau de périmètre, également appelé DMZ.
  - Il protège vos comptes utilisateur en toute transparence avec les [stratégies d’accès conditionnel d’Azure AD](../active-directory-conditional-access-azure-portal.md), y compris l’authentification multifacteur (MFA) et en [filtrant des attaques de mot de passe par recherche exhaustive](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).
- *Hautement disponible*
  - Il est possible d’installer des agents supplémentaires sur plusieurs serveurs locaux pour assurer la haute disponibilité des requêtes de connexion.

## <a name="feature-highlights"></a>Présentation des fonctionnalités

- Prend en charge la connexion de l’utilisateur dans toutes les applications basées sur un navigateur web et dans les applications clientes Microsoft Office qui utilisent [l’authentification moderne](https://aka.ms/modernauthga).
- Les noms d’utilisateur de connexion peuvent être soit un nom d’utilisateur local par défaut (`userPrincipalName`), soit un autre attribut configuré dans Azure AD Connect (appelé `Alternate ID`).
- La fonctionnalité fonctionne de façon transparente avec les fonctionnalités [d’accès conditionnel](../active-directory-conditional-access-azure-portal.md) telles que l’authentification multifacteur (MFA) pour aider à sécuriser vos utilisateurs.
- Elle est intégrée à la [gestion des mots de passe libre-service](../active-directory-passwords-overview.md) sur le cloud, y compris la réécriture des mots de passe dans l’annuaire Active Directory local et la protection par mot de passe en interdisant l’emploi de mots de passe courants.
- Les environnements à plusieurs forêts sont pris en charge s’il existe des approbations de forêts entre les forêts AD et si le routage du suffixe de leurs noms est configuré correctement.
- Cette fonctionnalité est gratuite et il est inutile de disposer des éditions payantes d’Azure AD pour l’utiliser.
- Elle peut être activée via [Azure AD Connect](active-directory-aadconnect.md).
- Elle utilise un agent local léger qui écoute et répond aux requêtes de validation du mot de passe.
- L’installation de plusieurs agents fournit une haute disponibilité des requêtes de connexion.
- Elle permet de [protéger](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) vos comptes locaux contre les attaques de mot de passe par recherche exhaustive dans le cloud.

## <a name="next-steps"></a>Étapes suivantes

- [**Démarrage rapide**](active-directory-aadconnect-pass-through-authentication-quick-start.md) : soyez opérationnel avec l’authentification directe Azure AD.
- [**Verrouillage intelligent**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) : configurez la fonctionnalité Verrouillage intelligent sur votre locataire pour protéger les comptes d’utilisateur.
- [**Limitations actuelles**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) : découvrez les scénarios pris en charge et ceux qui ne le sont pas.
- [**Immersion technique**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Questions fréquentes (FAQ)**](active-directory-aadconnect-pass-through-authentication-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec cette fonctionnalité.
- [**Immersion dans la sécurité**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) : informations techniques supplémentaires sur la fonctionnalité.
- [**Authentification unique transparente Azure AD**](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour formuler des demandes de nouvelles fonctionnalités.
