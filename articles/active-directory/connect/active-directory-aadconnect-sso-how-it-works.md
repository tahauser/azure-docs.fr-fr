---
title: 'Azure AD Connect : authentification unique transparente - Fonctionnement | Microsoft Docs'
description: Cet article décrit le fonctionnement de la fonctionnalité d’authentification unique transparente d’Azure Active Directory.
services: active-directory
keywords: Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 9d17a4038f2171b74c8ba1dbc21e8335e6893691
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Authentification unique transparente Azure Active Directory : immersion technique

Cet article vous fournit des détails techniques sur le fonctionnement de la fonctionnalité d’authentification unique (SSO) transparente d’Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>Fonctionnement de l’authentification unique transparente (SSO)

Cette section est composée de trois parties :
1. Configuration de la fonctionnalité d’authentification unique transparente.
2. Fonctionnement d’une transaction de connexion mono-utilisateur dans un navigateur web avec l’authentification unique transparente
3. Fonctionnement d’une transaction de connexion mono-utilisateur sur un client natif avec l’authentification unique transparente

### <a name="how-does-set-up-work"></a>Comment la configuration s’opère-t-elle ?

L’authentification unique transparente s’active via Azure AD Connect comme indiqué [ici](active-directory-aadconnect-sso-quick-start.md). Voici ce qu’il se passe pendant l’activation de la fonctionnalité :
- Un compte d’ordinateur nommé `AZUREADSSOACC` (c’est-à-dire Azure AD) est créé dans votre instance Active Directory (AD) locale.
- La clé de déchiffrement Kerberos du compte d’ordinateur est partagée en toute sécurité avec Azure AD.
- Par ailleurs, deux noms de principal du service (SPN) Kerberos sont créés pour représenter les deux URL utilisées pendant la connexion à Azure AD.

>[!NOTE]
> Le compte d’ordinateur et les SPN Kerberos sont créés dans chaque forêt AD que vous synchronisez avec Azure AD (via Azure AD Connect) et pour les utilisateurs qui doivent bénéficier de l’authentification unique transparente. Déplacez le compte d’ordinateur `AZUREADSSOACC` vers une unité d’organisation (UO) où d’autres comptes d’ordinateurs sont stockés. Vous serez ainsi assuré qu’il sera géré de la même façon et qu’il ne sera pas supprimé.

>[!IMPORTANT]
>Il est fortement recommandé que vous [substituiez la clé de déchiffrement Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) du `AZUREADSSOACC` compte d’ordinateur au moins tous les 30 jours.

Une fois la configuration terminée, l’authentification unique transparente fonctionne de la même façon que n’importe quelle autre connexion utilisant l’authentification Windows intégrée (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Fonctionnement des connexions dans un navigateur web avec l’authentification unique transparente

Le flux de connexion dans un navigateur web est le suivant :

1. Un utilisateur tente d’accéder à une application web (par exemple, Outlook Web App : https://outlook.office365.com/owa/) à partir d’un appareil d’entreprise joint à un domaine du réseau de l’entreprise.
2. Si l’utilisateur n’est pas déjà connecté, il est redirigé vers la page de connexion Azure AD.
3. L’utilisateur tape son nom d’utilisateur dans la page de connexion Azure AD.

  >[!NOTE]
  >Pour [certaines applications](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso), les étapes 2 et 3 ne sont pas nécessaires.

4. En utilisant JavaScript en arrière-plan, Azure AD demande au client, via une réponse 401 Non autorisé, de fournir un ticket Kerberos.
5. À son tour, le navigateur demande un ticket à Active Directory pour le compte d’ordinateur `AZUREADSSOACC` (qui représente Azure AD).
6. Active Directory localise le compte d’ordinateur et retourne un ticket Kerberos au navigateur chiffré avec le secret du compte d’ordinateur.
7. Le navigateur transfère le ticket Kerberos reçu de la part d’Active Directory à Azure AD.
8. Azure AD déchiffre le ticket Kerberos, qui comprend l’identité de l’utilisateur connecté à l’appareil d’entreprise, en utilisant la clé partagée précédemment.
9. À l’issue de l’évaluation, Azure AD retourne un jeton à l’application ou bien demande à l’utilisateur de fournir des preuves supplémentaires, telles qu’une authentification multifacteur.
10. Si l’utilisateur parvient à se connecter, il peut accéder à l’application.

Le schéma suivant illustre tous les composants et les étapes impliquées dans ce processus.

![Authentification unique transparente : flux des applications web](./media/active-directory-aadconnect-sso/sso2.png)

L’authentification unique transparente est opportuniste, ce qui signifie que si elle échoue, l’expérience de connexion reprend son comportement normal (l’utilisateur doit entrer son mot de passe pour se connecter).

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Fonctionnement des connexions sur un client natif avec l’authentification unique transparente

Le flux de connexion sur un client natif est le suivant :

1. Un utilisateur tente d’accéder à une application native (par exemple, le client Outlook) à partir d’un appareil d’entreprise joint à un domaine du réseau de l’entreprise.
2. Si l’utilisateur n’est pas déjà connecté, l’application native récupère le nom d’utilisateur de l’utilisateur à partir de la session Windows de l’appareil.
3. L’application envoie le nom d’utilisateur à Azure AD et récupère le point de terminaison WS-Trust MEX de votre locataire.
4. L’application interroge ensuite le point de terminaison WS-Trust MEX pour savoir si le point de terminaison d’authentification intégrée est disponible.
5. Si l’étape 4 a réussi, une demande d’authentification Kerberos est émise.
6. Si l’application est en mesure de récupérer le ticket Kerberos, il le transfère au point de terminaison d’authentification intégrée d’Azure AD.
7. Azure AD déchiffre le ticket Kerberos, puis le valide.
8. Azure AD connecte l’utilisateur et émet un jeton SAML pour l’application.
9. L’application envoie ensuite le jeton SAML au point de terminaison du jeton OAuth2 d’Azure AD.
10. Azure AD valide le jeton SAML et envoie un jeton d’accès et un jeton d’actualisation à l’application pour la ressource spécifiée, ainsi qu’un jeton d’ID.
11. L’utilisateur peut alors accéder aux ressources de l’application.

Le schéma suivant illustre tous les composants et les étapes impliquées dans ce processus.

![Authentification unique transparente : flux des applications natives](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>Étapes suivantes

- [**Démarrage rapide**](active-directory-aadconnect-sso-quick-start.md) : mettez en service l’authentification unique transparente Azure AD.
- [**Questions fréquentes (FAQ)**](active-directory-aadconnect-sso-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-sso.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec cette fonctionnalité.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.
