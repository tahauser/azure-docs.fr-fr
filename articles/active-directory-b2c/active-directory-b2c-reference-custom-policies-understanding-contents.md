---
title: 'Azure Active Directory B2C : Présentation des stratégies personnalisées du pack de démarrage | Microsoft Docs'
description: Une rubrique sur les stratégies personnalisées Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.openlocfilehash: 624a40b1e40db6ceac9c567926b3932449e7bf7e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Présentation des stratégies personnalisées du pack de démarrage Azure AD B2C Custom Policy

Cette section répertorie les principaux éléments de la stratégie B2C_1A_base qui est fournie avec le **Pack de démarrage** et utilisée pour créer ses propres stratégies via l’héritage de la *stratégie B2C_1A_base_extensions*.

Par conséquent, il se concentre plutôt sur les types de revendication, les transformations de revendication, les définitions de contenu, les fournisseurs de revendications avec leurs profils techniques, et les principaux parcours utilisateur déjà définis.

> [!IMPORTANT]
> Microsoft n’offre aucune garantie, expresse ou implicite, concernant les informations contenues dans ce document. Des modifications peuvent être apportées à tout moment, avant, pendant ou après la mise à la disposition générale.

Vos propres stratégies, ainsi que la stratégie B2C_1A_base_extensions, peuvent remplacer ces définitions et étendre cette stratégie parente en fournissant de nouvelles définitions le cas échéant.

La *stratégie B2C_1A_base* comporte des types de revendication, des transformations de revendication et des définitions de contenu. Ces éléments peuvent être référencés dans vos propres stratégies, ainsi que dans la *stratégie B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Schéma de revendications

Ce schéma de revendications comprend trois sections :

1.  La première section répertorie les revendications minimales requises pour garantir le bon fonctionnement des parcours utilisateur.
2.  La deuxième section répertorie les revendications requises pour les paramètres de chaîne de requête et d’autres paramètres spéciaux devant être transférés à d’autres fournisseurs de revendications, notamment login.microsoftonline.com pour l’authentification. **Ne modifiez pas ces revendications**.
3.  Enfin, la troisième section répertorie toutes les revendications supplémentaires et facultatives qui peuvent être collectées à partir de l’utilisateur, stockées dans le répertoire et transmises aux jetons lors de la connexion. Vous pouvez ajouter dans cette section tout nouveau type de revendications à collecter à partir de l’utilisateur et/ou à transmettre au jeton.

> [!IMPORTANT]
> Le schéma de revendications inclut des restrictions pour certaines revendications, telles que des noms d’utilisateur et des mots de passe. La stratégie Trust Framework (TF) traite Azure AD comme n’importe quel autre fournisseur de revendications et toutes ses restrictions sont modélisées dans la stratégie premium. Vous pouvez modifier une stratégie pour y ajouter de nouvelles restrictions ou utiliser un autre fournisseur de revendications pour le stockage d’informations d’identification avec ses propres restrictions.

Les types de revendication disponibles sont répertoriés ci-dessous.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Revendications requises pour les parcours utilisateur

Les revendications suivantes sont requises pour garantir le bon fonctionnement des parcours utilisateur :

| Type de revendication | Description |
|-------------|-------------|
| *UserId* | Nom d’utilisateur |
| *signInName* | Nom de connexion |
| *tenantId* | ID de locataire de l’objet utilisateur dans Azure AD B2C Premium |
| *objectId* | ID d’objet de l’objet utilisateur dans Azure AD B2C Premium |
| *mot de passe* | Mot de passe |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Stratégies de mot de passe utilisées par Azure AD B2C Premium pour définir la force du mot de passe, la date d’expiration, etc. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Numéro de téléphone de l’utilisateur |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | Adresse e-mail qui peut être utilisée pour contacter l’utilisateur |
| *signInNamesInfo.emailAddress* | Adresse e-mail que l’utilisateur peut utiliser pour se connecter |
| *otherMails* | Adresses e-mail qui peuvent être utilisées pour contacter l’utilisateur |
| *userPrincipalName* | Nom d’utilisateur enregistré dans Azure AD B2C Premium |
| *upnUserName* | Nom d’utilisateur utilisé pour créer le nom d’utilisateur principal |
| *mailNickName* | Pseudonyme de l’utilisateur enregistré dans Azure AD B2C Premium |
| *newUser* | |
| *executed-SelfAsserted-Input* | Revendication qui spécifie si les attributs ont été collectés à partir de l’utilisateur |
| *executed-PhoneFactor-Input* | Revendication qui spécifie si un nouveau numéro de téléphone a été collecté à partir de l’utilisateur |
| *authenticationSource* | Spécifie si l’utilisateur a été authentifié via un fournisseur d’identité de réseaux sociaux, login.microsoftonline.com ou un compte local |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Revendications requises pour les paramètres de chaîne de requête et d’autres paramètres spéciaux

Les revendications suivantes sont requises pour transmettre des paramètres spéciaux (y compris certains paramètres de chaîne de requête) à d’autres fournisseurs de revendications :

| Type de revendication | Description |
|-------------|-------------|
| *nux* | Paramètre spécial transmis pour l’authentification du compte local à login.microsoftonline.com |
| *nca* | Paramètre spécial transmis pour l’authentification du compte local à login.microsoftonline.com |
| *prompt* | Paramètre spécial transmis pour l’authentification du compte local à login.microsoftonline.com |
| *mkt* | Paramètre spécial transmis pour l’authentification du compte local à login.microsoftonline.com |
| *lc* | Paramètre spécial transmis pour l’authentification du compte local à login.microsoftonline.com |
| *grant_type* | Paramètre spécial transmis pour l’authentification du compte local à login.microsoftonline.com |
| *scope* | Paramètre spécial transmis pour l’authentification du compte local à login.microsoftonline.com |
| *client_id* | Paramètre spécial transmis pour l’authentification du compte local à login.microsoftonline.com |
| *objectIdFromSession* | Paramètre fourni par le fournisseur de gestion des sessions par défaut pour indiquer que l’ID d’objet a été récupéré à partir d’une session d’authentification unique |
| *isActiveMFASession* | Paramètre fourni par le fournisseur de gestion des sessions MFA pour indiquer que l’utilisateur dispose d’une session MFA active |

### <a name="additional-optional-claims-that-can-be-collected"></a>Revendications supplémentaires (facultatives) qui peuvent être collectées

Les revendications suivantes sont des revendications supplémentaires qui peuvent être collectées à partir des utilisateurs, stockées dans le répertoire et transmises aux jetons. Comme indiqué ci-dessus, des revendications supplémentaires peuvent être ajoutées à cette liste.

| Type de revendication | Description |
|-------------|-------------|
| *givenName* | Prénom de l’utilisateur |
| *surname* | Nom de famille de l’utilisateur |
| *Extension_picture* | Image de l’utilisateur obtenue via les réseaux sociaux |

## <a name="claim-transformations"></a>Transformations de revendication

Les transformations de revendication disponibles sont répertoriées ci-dessous.

| Transformation de revendication | Description |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Définitions de contenu

Cette section présente les définitions de contenu déjà déclarées dans la stratégie *B2C_1A_base*. Ces définitions de contenu peuvent être référencées, remplacées et/ou étendues autant que nécessaire dans vos propres stratégies, ainsi que dans la stratégie *B2C_1A_base_extensions*.

| Fournisseur de revendications | Description |
|-----------------|-------------|
| *Facebook* | |
| *Local Account SignIn* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Self Asserted* | |
| *Local Account* | |
| *Gestion des sessions* | |
| *Trustframework Policy Engine* | |
| *TechnicalProfiles* | |
| *Token Issuer* | |

## <a name="technical-profiles"></a>Profils techniques

Cette section décrit les profils techniques déjà déclarés par le fournisseur de revendications dans la stratégie *B2C_1A_base*. Ces profils techniques peuvent être référencés, remplacés et/ou étendus autant que nécessaire dans vos propres stratégies, ainsi que dans la stratégie *B2C_1A_base_extensions*.

### <a name="technical-profiles-for-facebook"></a>Profils techniques pour Facebook

| Profil technique | Description |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Profils techniques pour Local Account Signin

| Profil technique | Description |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Profils techniques pour PhoneFactor

| Profil technique | Description |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Profils techniques pour Azure Active Directory

| Profil technique | Description |
|-------------------|-------------|
| *AAD-Common* | Profil technique contenu dans les autres profils techniques AAD-xxx |
| *AAD-UserWriteUsingAlternativeSecurityId* | Profil technique pour les connexions via les réseaux sociaux |
| *AAD-UserReadUsingAlternativeSecurityId* | Profil technique pour les connexions via les réseaux sociaux |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Profil technique pour les connexions via les réseaux sociaux |
| *AAD-UserWritePasswordUsingLogonEmail* | Profils techniques pour les comptes locaux |
| *AAD-UserReadUsingEmailAddress* | Profils techniques pour les comptes locaux |
| *AAD-UserWriteProfileUsingObjectId* | Profil technique pour la mise à jour de l’enregistrement utilisateur à l’aide de l’ID d’objet |
| *AAD-UserWritePhoneNumberUsingObjectId* | Profil technique pour la mise à jour de l’enregistrement utilisateur à l’aide de l’ID d’objet |
| *AAD-UserWritePasswordUsingObjectId* | Profil technique pour la mise à jour de l’enregistrement utilisateur à l’aide de l’ID d’objet |
| *AAD-UserReadUsingObjectId* | Profil technique utilisé pour lire les données une fois que l’utilisateur s’est authentifié |

### <a name="technical-profiles-for-self-asserted"></a>Profils techniques pour Self Asserted

| Profil technique | Description |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Profils techniques pour Local Account

| Profil technique | Description |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Profils techniques pour Session Management

| Profil technique | Description |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Nom de profil utilisé pour enlever toute ambiguïté entre l’inscription et la connexion de la session AAD |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>Profils techniques pour Trustframework Policy Engine TechnicalProfiles

Aucun profil technique n’est actuellement défini pour le fournisseur de revendications **Trustframework Policy Engine TechnicalProfiles**.

### <a name="technical-profiles-for-token-issuer"></a>Profils techniques pour Token Issuer

| Profil technique | Description |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Parcours utilisateur

Cette section décrit les parcours utilisateur déjà déclarés dans la stratégie *B2C_1A_base*. Ces parcours utilisateur peuvent être référencés, remplacés et/ou étendus autant que nécessaire dans vos propres stratégies, ainsi que dans la stratégie *B2C_1A_base_extensions*.

| Parcours utilisateur | Description |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
