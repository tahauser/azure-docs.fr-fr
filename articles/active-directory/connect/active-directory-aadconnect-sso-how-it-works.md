---
title: "Azure AD Connect : authentification unique transparente - Fonctionnement | Microsoft Docs"
description: "Cet article décrit le fonctionnement de la fonctionnalité d’authentification unique transparente d’Azure Active Directory."
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
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0a28cd9016588d266670aa5a7fcbdd854d7ebce0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Authentification unique transparente Azure Active Directory : immersion technique

Cet article vous fournit des détails techniques sur le fonctionnement de la fonctionnalité d’authentification unique (SSO) transparente d’Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>Fonctionnement de l’authentification unique transparente (SSO)

Cette section se compose de deux parties :
1. Configuration de la fonctionnalité d’authentification unique transparente.
2. Fonctionnement d’une transaction de connexion mono-utilisateur avec l’authentification unique transparente.

### <a name="how-does-set-up-work"></a>Comment la configuration s’opère-t-elle ?

L’authentification unique transparente s’active via Azure AD Connect comme indiqué [ici](active-directory-aadconnect-sso-quick-start.md). Voici ce qu’il se passe pendant l’activation de la fonctionnalité :
- Un compte d’ordinateur nommé `AZUREADSSOACC` (c’est-à-dire Azure AD) est créé dans votre instance Active Directory (AD) locale.
- La clé de déchiffrement Kerberos du compte d’ordinateur est partagée en toute sécurité avec Azure AD.
- Par ailleurs, deux noms de principal du service (SPN) Kerberos sont créés pour représenter les deux URL utilisées pendant la connexion à Azure AD.

>[!NOTE]
> Le compte d’ordinateur et les SPN Kerberos sont créés dans chaque forêt AD que vous synchronisez avec Azure AD (via Azure AD Connect) et pour les utilisateurs qui doivent bénéficier de l’authentification unique transparente. Déplacez le compte d’ordinateur `AZUREADSSOACC` vers une unité d’organisation (UO) où d’autres comptes d’ordinateurs sont stockés. Vous serez ainsi assuré qu’il sera géré de la même façon et qu’il ne sera pas supprimé.

>[!IMPORTANT]
>Il est fortement recommandé que vous [substituiez la clé de déchiffrement Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) du `AZUREADSSOACC` compte d’ordinateur au moins tous les 30 jours.

### <a name="how-does-sign-in-with-seamless-sso-work"></a>Comment s’opère une connexion avec l’authentification unique transparente ?

Une fois la configuration terminée, l’authentification unique transparente fonctionne de la même façon que n’importe quelle autre connexion utilisant l’authentification Windows intégrée (IWA). Voici comment se déroule l’opération :

1. Un utilisateur tente d’accéder à une application (par exemple, Outlook Web App - https://outlook.office365.com/owa/) à partir d’un appareil d’entreprise joint à un domaine au sein du réseau d’entreprise.
2. Si l’utilisateur n’est pas déjà connecté, il est redirigé vers la page de connexion Azure AD.

  >[!NOTE]
  >Si la demande de connexion Azure AD inclut un paramètre `domain_hint` (identifiant votre locataire, par exemple contoso.onmicrosoft.com) ou un paramètre `login_hint` (identifiant l’utilisateur, par exemple user@contoso.onmicrosoft.com ou user@contoso.com), l’étape 2 est ignorée.

3. L’utilisateur tape son nom d’utilisateur dans la page de connexion Azure AD.
4. En utilisant JavaScript en arrière-plan, Azure AD demande au client, via une réponse 401 Non autorisé, de fournir un ticket Kerberos.
5. À son tour, le navigateur demande un ticket à Active Directory pour le compte d’ordinateur `AZUREADSSOACC` (qui représente Azure AD).
6. Active Directory localise le compte d’ordinateur et retourne un ticket Kerberos au navigateur chiffré avec le secret du compte d’ordinateur.
7. Le navigateur transmet le ticket Kerberos qu’il a acquis auprès d’Active Directory à Azure AD (à l’une des [URL Azure AD précédemment ajoutées aux paramètres Zone intranet du navigateur](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature)).
8. Azure AD déchiffre le ticket Kerberos, qui comprend l’identité de l’utilisateur connecté à l’appareil d’entreprise, en utilisant la clé partagée précédemment.
9. À l’issue de l’évaluation, Azure AD retourne un jeton à l’application ou bien demande à l’utilisateur de fournir des preuves supplémentaires, telles qu’une authentification multifacteur.
10. Si l’utilisateur parvient à se connecter, il peut accéder à l’application.

Le schéma suivant illustre tous les composants et les étapes impliquées dans ce processus.

![Authentification unique transparente](./media/active-directory-aadconnect-sso/sso2.png)

L’authentification unique transparente est opportuniste, ce qui signifie que si elle échoue, l’expérience de connexion reprend son comportement normal (l’utilisateur doit entrer son mot de passe pour se connecter).

## <a name="next-steps"></a>Étapes suivantes

- [**Démarrage rapide**](active-directory-aadconnect-sso-quick-start.md) : mettez en service l’authentification unique transparente Azure AD.
- [**Questions fréquentes (FAQ)**](active-directory-aadconnect-sso-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-sso.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec cette fonctionnalité.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.
