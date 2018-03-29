---
title: 'Azure Active Directory B2C : stratégies intégrées | Microsoft Docs'
description: Une rubrique sur l'infrastructure de stratégie extensible d'Azure Active Directory B2C et la création de différents types de stratégies
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/26/2017
ms.author: davidmu
ms.openlocfilehash: 35b31c46b350474caa1472829d705b4d068d9642
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: stratégies intégrées


L’infrastructure de stratégie extensible d’Azure Active Directory (Azure AD) B2C est le pilier central du service. Les stratégies décrivent entièrement les expériences relatives à l’identité des consommateurs, telles que l’inscription, la connexion ou la modification de profil. Par exemple, une stratégie d'inscription vous permet de contrôler les comportements en configurant les paramètres suivants :

* types de compte (réseaux sociaux tels que Facebook et comptes locaux tels que des adresses de courrier) que les clients peuvent utiliser pour s’inscrire à l’application ;
* attributs (par exemple, prénom, code postal et pointure) à recueillir auprès du client lors de l’inscription ;
* utilisation d’Azure Multi-Factor Authentication ;
* aspect de toutes les pages d'inscription ;
* informations (qui apparaissent en tant que revendications dans un jeton) que l’application reçoit lorsque l’exécution de la stratégie est terminée.

Vous pouvez créer plusieurs stratégies de types différents dans votre client et les utiliser dans vos applications en fonction des besoins. Les stratégies peuvent être réutilisées entre les différentes applications. Cette flexibilité permet aux développeurs de définir et de modifier les expériences relatives à l’identité du client avec peu ou pas de modification du code.

Les stratégies sont disponibles à l'utilisation par le biais d'une interface développeur simple. Votre application déclenche une stratégie avec une requête d'authentification HTTP standard (en transmettant un paramètre de stratégie dans la requête) et reçoit un jeton personnalisé en réponse. Par exemple, la seule différence entre les requêtes invoquant une stratégie d’inscription et celles invoquant une stratégie de connexion réside dans le nom de stratégie utilisé dans le paramètre de chaîne de requête « p » :

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Création d’une stratégie d’inscription ou de connexion

Cette stratégie gère les expériences d’inscription et de connexion des utilisateurs avec une configuration unique. Les utilisateurs sont dirigés vers le chemin d’accès correct (inscription ou connexion) en fonction du contexte. Cet article décrit également le contenu des jetons que l’application recevra en cas d’inscription ou de connexion réussie.  Un exemple de code pour la stratégie d’inscription ou de connexion est [disponible ici](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Il est recommandé d’utiliser cette stratégie plutôt qu’une stratégie d’inscription et de connexion.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Création d’une stratégie d’inscription

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Création d’une stratégie de connexion

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Création d’une stratégie de modification de profil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Création d’une stratégie de réinitialisation du mot de passe

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Comment lier une stratégie d’inscription ou de connexion à une stratégie de réinitialisation de mot de passe ?
Lorsque vous créez une stratégie d’inscription ou de connexion (avec des comptes locaux), vous voyez un lien **Mot de passe oublié ?** sur la première page. Cliquer sur ce lien ne déclenche pas automatiquement une stratégie de réinitialisation de mot de passe. 

Au lieu de cela, le code d’erreur **`AADB2C90118`** est retourné à votre application. Votre application a besoin de gérer ce code d’erreur en appelant une stratégie de réinitialisation de mot de passe spécifique. Pour plus d’informations, consultez un [exemple qui illustre l’approche de la liaison de stratégies](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Dois-je utiliser une stratégie d’inscription ou de connexion ou une stratégie d’inscription et une stratégie de connexion ?
Nous vous recommandons d’utiliser une stratégie d’inscription ou de connexion plutôt qu’une stratégie d’inscription et une stratégie de connexion.  

La stratégie d’inscription ou de connexion offre plus de possibilités que la stratégie de connexion. Elle vous permet également d’utiliser une personnalisation de l’interface utilisateur de la page et offre une meilleure prise en charge de la localisation. 

La stratégie de connexion est recommandée si vous n’avez pas besoin de localiser vos stratégies mais que vous avez besoin uniquement de fonctionnalités de personnalisation mineures, et souhaitez que la réinitialisation de mot de passe y soit intégrée.

## <a name="next-steps"></a>Étapes suivantes
* [Configuration du jeton, de la session et de l’authentification unique](active-directory-b2c-token-session-sso.md)
* [Désactiver la vérification par courrier électronique lors de l’inscription du consommateur](active-directory-b2c-reference-disable-ev.md)

