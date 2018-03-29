---
title: Types d’applications - Azure AD B2C | Microsoft Docs
description: Types d’applications que vous pouvez générer dans Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: davidmu1
ms.openlocfilehash: 011426f3d8eab4eb2513270a9bcd1562e3c12b31
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C : types d’applications
Azure Active Directory (Azure AD) B2C prend en charge l’authentification pour une variété d’architectures d’applications modernes. Toutes sont basées sur les protocoles standard [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID Connect](active-directory-b2c-reference-protocols.md). Ce document décrit brièvement les types d’applications pouvant être créées, indépendamment de la langue ou de la plate-forme souhaitées. Il vous permet également de comprendre les principaux scénarios avant de [commencer à créer des applications](active-directory-b2c-overview.md#get-started).

## <a name="the-basics"></a>Concepts de base
Chaque application qui utilise Azure AD B2C doit être inscrite dans votre [répertoire B2C](active-directory-b2c-get-started.md) par le biais du [Portail Azure](https://portal.azure.com/). Le processus d’inscription des applications collecte quelques valeurs et les affecte à votre application :

* un **ID d’application** qui identifie de manière unique votre application ;
* un **URI de redirection** pouvant être utilisé pour diriger les réponses vers votre application ;
* n’importe quelle valeur spécifique au scénario. Pour plus d’informations, découvrez comment [inscrire une application](active-directory-b2c-app-registration.md).

Une fois inscrite, l’application communique avec Azure AD en transmettant les requêtes au point de terminaison Azure AD v2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Chaque requête qui est envoyée à Azure AD B2C spécifie une **stratégie**. Une stratégie contrôle le comportement d’Azure AD. Vous pouvez également utiliser ces points de terminaison pour créer un ensemble d’expériences utilisateur hautement personnalisable. Les stratégies courantes comprennent les stratégies d’inscription, les stratégies de connexion et les stratégies de modification de profil. Si vous n’êtes pas familiarisé avec les stratégies, consultez l’article décrivant [l’infrastructure de stratégie extensible](active-directory-b2c-reference-policies.md) d’Azure AD B2C avant de continuer.

Chaque interaction d’une application avec un point de terminaison v2.0 suit un modèle principal similaire :

1. L’application dirige l’utilisateur vers le point de terminaison v2.0 pour exécuter une [stratégie](active-directory-b2c-reference-policies.md).
2. L'utilisateur exécute la stratégie en fonction de la définition de celle-ci.
3. L’application reçoit un type de jeton de sécurité du point de terminaison v2.0.
4. L’application utilise le jeton de sécurité pour accéder aux informations ou à la ressource protégées.
5. Le serveur de ressources valide le jeton de sécurité afin de garantir l’octroi de l’accès.
6. L’application actualise régulièrement le jeton de sécurité.

<!-- TODO: Need a page for libraries to link to -->
Ces étapes peuvent différer légèrement selon le type d’application que vous créez. Vous trouverez des informations supplémentaires dans les bibliothèques open source.

## <a name="web-apps"></a>les applications web
Pour les applications web (notamment .NET, PHP, Java, Ruby, Python et Node.js.) qui sont hébergées sur un serveur et accessibles par le biais d’un navigateur, Azure AD B2C prend en charge [OpenID Connect](active-directory-b2c-reference-protocols.md) pour toutes les expériences utilisateur. Cela inclut la connexion, l’inscription et la gestion des profils. Dans la mise en œuvre Azure AD B2C de OpenID Connect, votre application web déclenche ces expériences utilisateur en émettant des demandes d’authentification à Azure AD. Le résultat de la demande est un élément `id_token`. Ce jeton de sécurité représente l’identité de l’utilisateur. Il fournit également des informations sur l’utilisateur sous forme de revendications :

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Pour plus d’informations sur les différents types de jetons et de revendications disponibles pour une application, voir la [référence sur les jetons B2C](active-directory-b2c-reference-tokens.md).

Dans les applications web, chaque exécution d’une [stratégie](active-directory-b2c-reference-policies.md) suit la procédure générale ci-après :

![Images de couloirs d’application Web](./media/active-directory-b2c-apps/webapp.png)

La validation de l’élément `id_token` à l’aide d’une clé de signature publique provenant d’Azure AD est suffisante pour vérifier l’identité de l’utilisateur. Cela définit également un cookie de session qui peut être utilisé pour identifier l’utilisateur sur les demandes de page suivantes.

Pour voir ce scénario en action, exécutez l’un des exemples de code de connexion d’application web de la section [Prise en main](active-directory-b2c-overview.md#get-started).

En plus de faciliter la connexion simple, une application de serveur web peut également nécessiter l’accès à un service web principal. Dans ce cas, l’application web peut exécuter un [flux OpenID Connect](active-directory-b2c-reference-oidc.md) légèrement différent et acquérir des jetons à l’aide de codes d’autorisation et de jetons d’actualisation. Ce scénario est représenté dans la section [API Web](#web-apis)ci-après.

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>API Web
Vous pouvez utiliser Azure AD B2C pour sécuriser les services web, comme l’API web RESTful de votre application. Les API web peuvent utiliser OAuth 2.0 pour sécuriser leurs données, en authentifiant les requêtes HTTP entrantes à l’aide de jetons. L’appelant d’une API web ajoute un jeton dans l’en-tête d’autorisation d’une requête HTTP :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L’API web peut ensuite utiliser le jeton pour vérifier l’identité de l’appelant de l’API et extraire les informations à son sujet, à partir des revendications encodées dans le jeton. Pour plus d’informations sur les différents types de jetons et de revendications disponibles pour une application, voir la [référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> Actuellement, Azure AD B2C prend uniquement en charge les API web qui sont accessibles par leurs clients connus. Par exemple, votre application dans son ensemble peut inclure une application iOS, une application Android et une API web principale. Cette architecture est entièrement prise en charge. Autoriser un client tiers, comme une autre application iOS, à accéder également à la même API web n’est pas pris en charge pour le moment. Tous les composants de votre application complète doivent partager un ID d’application unique.
>
>

Une API web peut recevoir des jetons de tous types de clients, notamment des applications web, des applications de bureau et mobiles, des applications de page unique, des démons côté serveur, et même d’autres API web. Voici un exemple de flux complet d’une application web appelant une API web :

![Images de couloirs d’API Web d’application Web](./media/active-directory-b2c-apps/webapi.png)

Pour plus d’informations sur les codes d’autorisation, les jetons d’actualisation et les étapes d’obtention des jetons, voir l’article concernant le [protocole OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Pour savoir comment sécuriser une API web avec Azure AD B2C, consultez les didacticiels d’API web dans notre section [Prise en main](active-directory-b2c-overview.md#get-started).

## <a name="mobile-and-native-apps"></a>Applications mobiles et natives
Les applications installées sur des appareils, comme les applications de bureau et les applications mobiles nécessitent bien souvent l’accès à des services principaux ou des API web pour le compte d’un utilisateur. Vous pouvez ajouter des expériences de gestion des identités personnalisées à vos applications natives et appeler en toute sécurité les services principaux à l’aide d’Azure AD B2C et du [flux de code d’autorisation OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

Dans ce flux, l’application exécute des [stratégies](active-directory-b2c-reference-policies.md) et reçoit un élément `authorization_code` d’Azure AD une fois que l’utilisateur a exécuté la stratégie. L’élément `authorization_code` représente l’autorisation de l’application d’appeler les services principaux pour le compte de l’utilisateur actuellement connecté. L’application peut ensuite échanger l’élément `authorization_code` en arrière-plan contre des éléments `id_token` et `refresh_token`.  L’application peut utiliser l’élément `id_token` pour s’authentifier auprès d’une API web principale dans les requêtes HTTP. Elle peut également utiliser l’élément `refresh_token` pour obtenir un nouvel élément `id_token` lorsque le précédent arrive à expiration.

> [!NOTE]
> Actuellement, Azure AD B2C prend uniquement en charge les jetons qui sont utilisés pour accéder au service web principal de l’application. Par exemple, votre application dans son ensemble peut inclure une application iOS, une application Android et une API web principale. Cette architecture est entièrement prise en charge. Autoriser votre application iOS à accéder à une API web tierce à l’aide de jetons d’accès OAuth 2.0 n’est pas pris en charge pour le moment. Tous les composants de votre application complète doivent partager un ID d’application unique.
>
>

![Images de couloirs d’application native](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitations actuelles
Pour l’instant, Azure AD B2C ne prend pas en charge les types d’applications ci-après, mais ces dernières figurent sur la feuille de route. 

### <a name="daemonsserver-side-apps"></a>Démons/applications côté serveur
Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d’un utilisateur doivent également disposer d’un moyen d’accès aux ressources sécurisées, comme les API web. Ces applications peuvent s’authentifier et récupérer des jetons à l’aide de l’identité d’application (plutôt qu’avec l’identité déléguée d’un utilisateur) et à l’aide du flux des informations d’identification du client OAuth 2.0.

Ce flux n’est pas actuellement pris en charge par Azure AD B2C. Ces applications peuvent uniquement obtenir des jetons après l’exécution d’un flux interactif utilisateur.

### <a name="web-api-chains-on-behalf-of-flow"></a>Chaînes d’API web (flux On-Behalf-Of)
De nombreuses architectures incluent une API web qui doit appeler une autre API web en aval, toutes deux sécurisées par Azure AD B2C. Ce scénario est courant chez les clients natifs disposant d’une API web principale. Il appelle ensuite un service en ligne Microsoft, tel que l’API Azure AD Graph.

Ce scénario d’API web chaînée peut être pris en charge à l’aide de la concession des informations d’identification du porteur OAuth 2.0 Jwt, également appelé flux On-Behalf-Of.  Toutefois, le flux On-Behalf-Of n’est pas implémenté dans Azure AD B2C pour l’instant.
