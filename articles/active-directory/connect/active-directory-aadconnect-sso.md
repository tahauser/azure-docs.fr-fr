---
title: 'Azure AD Connect : authentification unique transparente | Microsoft Docs'
description: "Cette rubrique décrit l’authentification unique transparente Azure Active Directory (Azure AD) et explique comment cette fonction vous permet de fournir une véritable authentification unique aux utilisateurs du réseau d’entreprise."
services: active-directory
keywords: "Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: billmath
ms.openlocfilehash: b71a2f19fee370ab1d732becd1c3b644505e2233
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Authentification unique transparente Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Qu’est-ce que l’authentification unique transparente Azure Active Directory ?

L’authentification unique transparente Azure Active Directory connecte automatiquement les utilisateurs lorsque leurs appareils d’entreprise sont connectés au réseau de l’entreprise. Lorsque cette fonctionnalité est activée, les utilisateurs n’ont plus besoin de taper leur mot de passe pour se connecter à Azure AD ni même, dans la plupart des cas, leur nom d’utilisateur. Cette fonctionnalité offre à vos utilisateurs un accès facilité à vos applications cloud sans nécessiter de composants locaux supplémentaires.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

L’authentification unique transparente peut être combinée avec la [synchronisation de hachage de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) et [l’authentification directe](active-directory-aadconnect-pass-through-authentication.md).

![Authentification unique transparente](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>L’authentification unique transparente n’est _pas_ applicable aux services de fédération Active Directory (AD FS).

## <a name="key-benefits"></a>Principaux avantages

- *Une meilleure expérience utilisateur*
  - Les utilisateurs sont automatiquement connectés aux applications cloud et locales.
  - Les utilisateurs n’ont pas à saisir leur mot de passe plusieurs fois.
- *Facile à déployer et à gérer*
  - Aucun composant local supplémentaire n’est nécessaire pour que cela fonctionne.
  - Fonctionne avec n’importe quelle méthode d’authentification cloud - [Synchronisation de hachage de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) ou [Authentification directe](active-directory-aadconnect-pass-through-authentication.md).
  - Peut être déployée pour certains utilisateurs ou pour l’ensemble de vos utilisateurs à l’aide d’une stratégie de groupe.
  - Inscrivez des appareils non-Windows 10 auprès d’Azure AD sans avoir besoin d’une infrastructure AD FS. Cette fonctionnalité exige que vous utilisiez la version 2.1 ou supérieure du [client workplace-join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Présentation des fonctionnalités

- Le nom d’utilisateur peut être soit le nom d’utilisateur local par défaut (`userPrincipalName`), soit un autre attribut configuré dans Azure AD Connect (`Alternate ID`). Les deux cas d’utilisation fonctionnent car l’authentification unique transparente utilise la revendication `securityIdentifier` dans le ticket Kerberos pour rechercher l’objet utilisateur correspondant dans Azure AD.
- L’authentification unique transparente est une fonctionnalité opportuniste. Si elle échoue pour une raison quelconque, l’expérience de connexion utilisateur retrouve son comportement normal (l’utilisateur doit alors entrer son mot de passe dans la page de connexion).
- Si une application transfère un paramètre `domain_hint` (OpenID Connect) ou `whr` (SAML), correspondant à votre locataire, ou encore un paramètre `login_hint`, correspondant à l’utilisateur, dans sa demande de connexion Azure AD, les utilisateurs sont automatiquement connectés, sans qu’ils n’aient à entrer leurs nom d’utilisateur et mot de passe.
- L’authentification unique transparente peut être activée par le biais d’Azure AD Connect.
- Cette fonctionnalité est gratuite et il est inutile de disposer des éditions payantes d’Azure AD pour l’utiliser.
- L’authentification unique est prise en charge par les clients basés sur le navigateur web et les clients Office qui prennent en charge [l’authentification moderne](https://aka.ms/modernauthga) sur les plateformes et navigateurs compatibles avec l’authentification Kerberos :

| Système d’exploitation\Navigateur |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Oui|Non |Oui|Oui\*|Non applicable
|Windows 8.1|Oui|Non applicable|Oui|Oui\*|Non applicable
|Windows 8|Oui|Non applicable|Oui|Oui\*|Non applicable
|Windows 7|Oui|Non applicable|Oui|Oui\*|Non applicable
|Mac OS X|Non applicable|Non applicable|Oui\*|Oui\*|Oui\*

\*Nécessite une [configuration supplémentaire](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Concernant Windows 10, il est recommandé d’utiliser [Azure AD Join](../active-directory-azureadjoin-overview.md) pour une expérience optimale d’authentification unique dans Azure AD.

## <a name="next-steps"></a>Étapes suivantes

- [**Démarrage rapide**](active-directory-aadconnect-sso-quick-start.md) : découvrez l’authentification unique transparente Azure AD.
- [**Immersion technique**](active-directory-aadconnect-sso-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Questions fréquentes (FAQ)**](active-directory-aadconnect-sso-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-sso.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec cette fonctionnalité.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.
