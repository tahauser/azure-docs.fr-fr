---
title: 'Azure AD Connect : authentification directe - Limitations actuelles | Microsoft Docs'
description: "Cet article décrit les limitations actuelles de l’authentification directe Azure Active Directory (Azure AD)"
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 978ad8f14d70fe60cb220136e87ce4a064672b8a
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Authentification directe Azure Active Directory : limitations actuelles

>[!IMPORTANT]
>L’authentification directe Azure Active Directory (Azure AD) est une fonctionnalité gratuite que vous pouvez utiliser sans avoir besoin d’aucune édition payante d’Azure AD. L’authentification directe est disponible seulement dans l’instance mondiale d’Azure AD, et pas sur le [cloud Microsoft Azure Allemagne](http://www.microsoft.de/cloud-deutschland) ni sur le [cloud Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scénarios pris en charge

Les scénarios suivants sont entièrement pris en charge :

- L’utilisateur se connecte dans toutes les applications basées sur un navigateur web
- L’utilisateur se connecte dans les applications clientes Office 365 prenant en charge [l’authentification moderne](https://aka.ms/modernauthga)
- Office 2016 et Office 2013 _avec_ authentification moderne
- Jonctions de domaine Azure AD pour les appareils Windows 10
- Prise en charge d’Exchange ActiveSync

## <a name="unsupported-scenarios"></a>Scénarios non pris en charge

Les scénarios suivants ne sont _pas_ pris en charge :

- L’utilisateur se connecte aux applications clientes Office héritées : Office 2010 et Office 2013 _sans_ authentification moderne. Nous recommandons aux entreprises de basculer si possible vers l’authentification moderne. L'authentification moderne permet la prise en charge de l'authentification directe. Elle vous aide à sécuriser vos comptes d'utilisateurs grâce à des fonctionnalités d'[accès conditionnel](../active-directory-conditional-access-azure-portal.md) comme Azure Multi-Factor Authentication.
- Connexions des utilisateurs à des applications clientes Skype Entreprise, y compris Skype Entreprise 2016.
- L'utilisateur se connecte à PowerShell version 1.0. Nous vous recommandons d’utiliser PowerShell version 2.0.
- Azure Active Directory Domain Services.
- Mots de passe d'application pour l’authentification multifacteur.
- Détection des utilisateurs avec des [informations d’identification volées](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>Comme solution de contournement _uniquement_ pour les scénarios non pris en charge, activez la synchronisation du hachage de mot de passe dans la page [Fonctionnalités facultatives](active-directory-aadconnect-get-started-custom.md#optional-features) de l’Assistant Azure AD Connect. L’activation de la synchronisation de hachage de mot de passe vous donne également la possibilité de basculer l’authentification en cas d’interruption de votre infrastructure sur site. Ce basculement de l’authentification directe vers la synchronisation de hachage de mot de passe Active Directory n’est pas automatique. Il nécessite l’aide du Support Microsoft.

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide](active-directory-aadconnect-pass-through-authentication-quick-start.md) : soyez opérationnel avec l’authentification directe Azure AD.
- [Verrouillage intelligent](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) : guide pratique pour configurer la fonctionnalité Verrouillage intelligent sur votre locataire pour protéger les comptes d’utilisateur.
- [Présentation technique approfondie](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez comment fonctionne l'authentification directe.
- [Forum aux questions](active-directory-aadconnect-pass-through-authentication-faq.md) : trouvez des réponses aux questions fréquemment posées sur la fonctionnalité Authentification directe.
- [Résoudre les problèmes](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants liés à la fonctionnalité d’authentification directe.
- [Présentation approfondie de sécurité](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) : obtenez des informations techniques approfondies sur l’authentification directe.
- [Authentification unique transparente Azure AD](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : utilisez le Forum Azure Active Directory pour consigner de nouvelles demandes de fonctionnalités.

