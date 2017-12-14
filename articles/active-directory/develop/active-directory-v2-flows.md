---
title: "Types d’application pour le point de terminaison Azure Active Directory v2.0 | Microsoft Docs"
description: "Les types d’applications et de scénarios pris en charge par le point de terminaison Azure Active Directory v2.0."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: b0344c1da626a8b4679a632db239fc9ded9d5ce6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Types d’application pour le point de terminaison Azure Active Directory v2.0
Le point de terminaison Azure Active Directory (Azure AD) v2.0 prend en charge l’authentification pour de multiples architectures d’application modernes, toutes basées sur des protocoles industriels standard [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md). Cet article décrit les types d’application que vous pouvez générer à l’aide d’Azure AD v2.0, quelle que soit votre plateforme ou langage par défaut. Les informations contenues dans cet article sont conçues pour vous aider à comprendre les scénarios de haut niveau avant de [commencer à travailler avec le code](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> Le point de terminaison v2.0 ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure Active Directory. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="the-basics"></a>Concepts de base
Vous devez inscrire chaque application qui utilise le point de terminaison v2.0 dans le [portail d’inscription Microsoft Application](https://apps.dev.microsoft.com). Le processus d’inscription des applications collecte les valeurs suivantes et les affecte à votre application :

* un **ID d’application** qui identifie de manière unique votre application ;
* un **URI de redirection** que vous pouvez utiliser pour renvoyer les réponses à votre application ;
* quelques valeurs spécifiques au scénario.

Pour en savoir plus, découvrez comment [inscrire une application](active-directory-v2-app-registration.md).

Une fois inscrite, l’application communique avec Azure AD en transmettant les requêtes au point de terminaison Azure AD v2.0. Nous fournissons les infrastructures et les bibliothèques open source qui gèrent les détails de ces requêtes. Vous avez également la possibilité d’implémenter la logique d’authentification vous-même en créant des requêtes à ces points de terminaison :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>les applications web
Pour les applications web (.NET, PHP, Java, Ruby, Python, Node, etc.) auxquelles l’utilisateur accède par le biais d’un navigateur, vous pouvez utiliser [OpenID Connect](active-directory-v2-protocols.md) pour la connexion de l’utilisateur. Dans OpenID Connect, l’application web reçoit un jeton d’ID. Un jeton d’ID est un jeton de sécurité qui vérifie l’identité de l’utilisateur et fournit des informations le concernant sous la forme de revendications :

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Pour en savoir plus sur les différents types de jetons et de revendications disponibles pour une application, consultez la page de [référence sur les jetons v2.0](active-directory-v2-tokens.md).

Dans les applications de serveur web, le flux d’authentification de connexion respecte cette procédure de niveau supérieur :

![Flux d’authentification d’application web](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Vous pouvez vérifier l’identité de l’utilisateur en validant le jeton d’ID avec une clé de signature publique reçue du point de terminaison v2.0. Un cookie de session qui peut être utilisé pour identifier l’utilisateur sur les requêtes de page suivantes est défini.

Pour voir ce scénario en action, exécutez l’un des exemples de code de connexion d’application web de la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started) de la version v2.0.

En plus de la connexion simple, une application de serveur web peut également nécessiter l’accès à un autre service Web, comme une API REST. Dans ce cas, l’application de serveur web s’engager dans un flux OpenID Connect et OAuth 2.0 à l’aide du [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md). Pour en savoir plus sur ce scénario, découvrez comment [la bien démarrer avec les applications web et des API web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API Web
Vous pouvez utiliser le point de terminaison v2.0 pour sécuriser des services Web, comme l’API web RESTful de votre application. En lieu et place des jetons d’ID et des cookies de session, une API Web utilise les jetons d’accès OAuth 2.0 pour sécuriser les données et authentifier les requêtes entrantes. L’appelant d’une API web ajoute un jeton d’accès dans l’en-tête d’autorisation d’une requête HTTP de la manière suivante :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L’API web utilise le jeton d’accès pour vérifier l’identité de l’appelant de l’API et extraire des informations à son sujet à partir de revendications encodées dans le jeton d’accès. Pour en savoir plus sur les différents types de jetons et de revendications disponibles pour une application, consultez la page de [référence sur les jetons v2.0](active-directory-v2-tokens.md).

Une API web peut octroyer aux utilisateurs la possibilité d’accepter/de refuser des fonctionnalités ou données spécifiques en exposant des autorisations (également appelées [étendues](active-directory-v2-scopes.md)). Pour qu’une application appelante puisse acquérir l’autorisation à une étendue, l’utilisateur doit accepter l’étendue au cours d’un flux. Le point de terminaison v2.0 demande l’autorisation à l’utilisateur, puis enregistre ces autorisations dans l’ensemble des jetons d’accès reçus par l’API web. L’API web valide les jetons d’accès qu’elle reçoit à chaque appel et effectue des vérifications d’autorisation.

Une API web peut recevoir des jetons d’accès de tous types d’applications, notamment des applications de serveur web, des applications de bureau et mobiles, des applications de page unique, des démons côté serveur, et même d’autres API web. Le flux de haut niveau pour une API Web ressemble à ceci :

![Flux d’authentification d’API web](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Pour savoir comment sécuriser une API web avec des jetons d’accès OAuth2, consultez les exemples de code d’API Web de notre [section Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

Dans de nombreux cas, les API web doivent également envoyer des demandes à d’autres API web en aval, sécurisées par Azure Active Directory.  Pour ce faire, elles peuvent utiliser le flux **Au nom de** d’Azure AD, qui permet d’échanger un jeton d’accès entrant contre un autre jeton d’accès à utiliser pour les demandes sortantes.  Le flux Au nom de du point de terminaison v2.0 est détaillé [ici](active-directory-v2-protocols-oauth-on-behalf-of.md).

## <a name="mobile-and-native-apps"></a>Applications mobiles et natives
Les applications installées sur un appareil, comme les applications de bureau et les applications mobiles nécessitent bien souvent un accès à des services principaux ou à des API web, qui stockent les données et exécutent des fonctions pour le compte d’un utilisateur. Ces applications peuvent ajouter des fonctionnalités de connexion et d’autorisation à des services principaux à l’aide du [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Dans ce flux, l’application reçoit un code d’autorisation à partir du point de terminaison 2.0, lorsque l’utilisateur se connecte. Le code d'autorisation représente l'autorisation de l'application d'appeler les services principaux pour le compte de l'utilisateur connecté. L’application peut ensuite échanger le code d’autorisation en arrière-plan contre un jeton d’accès et un jeton d’actualisation OAuth 2.0. L’application peut utiliser le jeton d’accès pour s’authentifier sur des API web dans des requêtes HTTP et solliciter le jeton d’actualisation afin de récupérer de nouveaux jetons d’accès une fois les anciens expirés.

![Flux d’authentification d’applications native](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Applications à page unique (Javascript)
De nombreuses applications modernes disposent d’un frontend d’application à page unique écrit principalement en JavaScript. Souvent, il est écrit à l’aide d’une infrastructure telle qu’AngularJS, Ember.js ou Durandal.js. Le point de terminaison Azure AD v2.0 prend en charge ces applications à l’aide du [flux implicite OAuth 2.0](active-directory-v2-protocols-implicit.md).

Dans ce flux, l'application reçoit des jetons directement du point de terminaison d'autorisation v2.0, sans exécuter d’échanges de serveur à serveur. Tout traitement de logique d'authentification et de gestion de sessions est entièrement exécuté dans le client javascript, sans redirections de pages supplémentaires.

![Flux d’authentification implicite](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Pour voir ce scénario dans la pratique, exécutez l'un des exemples de code de page unique de la section [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .

## <a name="daemons-and-server-side-apps"></a>Applications démons et côté serveur
Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans interaction d’un utilisateur doivent également disposer d’un moyen d’accès aux ressources sécurisées, comme les API web. Ces applications peuvent s'authentifier et récupérer des jetons à l'aide de l'identité d'application plutôt qu'avec l'identité déléguée d'un utilisateur avec le flux des informations d'identification du client OAuth 2.0.

Dans ce flux, l’application interagit directement avec le point de terminaison `/token` pour obtenir des points de terminaison :

![Flux d’authentification d’applications démons](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Pour créer une application démon, consultez la documentation sur les informations d’identification des clients dans la section [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) ou consultez [cet exemple d’application .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
