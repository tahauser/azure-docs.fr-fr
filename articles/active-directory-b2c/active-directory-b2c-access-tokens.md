---
title: Demande de jetons d’accès - Azure AD B2C | Microsoft Docs
description: Cet article vous montre comment configurer une application cliente et acquérir un jeton d’accès.
services: active-directory-b2c
documentationcenter: android
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/09/2017
ms.author: davidmu
ms.openlocfilehash: bd919543072a8d2bf5fb0ebba17e69ba2f467218
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C : Demande de jetons d’accès

Un jeton d’accès (défini en tant que **access\_token** dans les réponses d’Azure AD B2C) est une forme de jeton de sécurité qu’un client peut utiliser pour accéder aux ressources sécurisées par un [serveur d’autorisation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), tel qu’une API web. Les jetons d’accès sont représentés en tant que jetons [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) et contiennent des informations sur le serveur de ressources prévu et les autorisations accordées au serveur. Lorsque vous appelez le serveur de ressources, le jeton d’accès doit être présent dans la requête HTTP.

Cet article explique comment configurer une application cliente et une API web afin d’obtenir un **access\_token**.

> [!NOTE]
> **Les chaînes d’API web (pour le compte de) ne sont pas prises en charge par Azure AD B2C.**
>
> De nombreuses architectures incluent une API web qui doit appeler une autre API web en aval, toutes deux sécurisées par Azure AD B2C. Ce scénario est courant dans les clients natifs qui disposent d’une API web principale, qui à son tour appelle un service en ligne Microsoft, comme l’API Graph Azure AD.
>
> Ce scénario d’API web chaînée peut être pris en charge à l’aide de l’octroi des informations d’identification du porteur OAuth 2.0 JWT, également appelé flux On-Behalf-Of. Toutefois, le flux On-Behalf-Of n’est pas implémenté dans Azure AD B2C pour l’instant.

## <a name="register-a-web-api-and-publish-permissions"></a>Inscrire une API web et publier des autorisations

Avant de demander un jeton d’accès, vous devez inscrire une API web et publier des autorisations (étendues) qui peuvent être accordées à l’application cliente.

### <a name="register-a-web-api"></a>Inscrire une API web

1. Dans le panneau de fonctionnalités Azure AD B2C du Portail Azure, cliquez sur **Applications**.
1. Cliquez sur **+Ajouter** dans la partie supérieure du menu.
1. Entrez un **nom** pour l’application qui décrira votre application aux consommateurs. Par exemple, vous pouvez entrer « API Contoso ».
1. Activez/désactivez le commutateur **Include web app / web API** (Inclure l’application web/l’API web) pour le définir sur **Oui**.
1. Entrez une valeur arbitraire pour **URL de réponse**. Par exemple, entrez : `https://localhost:44316/`. La valeur n’a pas d’importance, car une API ne doit pas recevoir le jeton directement d’Azure AD B2C.
1. Entrez un **URI ID d’application**. Il s’agit de l’identificateur utilisé pour votre API web. Par exemple, entrez « notes » dans le champ. **L’URI ID d’application** serait alors `https://{tenantName}.onmicrosoft.com/notes`.
1. Cliquez sur **Créer** pour inscrire votre application.
1. Cliquez sur l’application que vous venez de créer, puis notez l’ **ID du client d’application** global unique à intégrer ultérieurement dans votre code.

### <a name="publishing-permissions"></a>Publication des autorisations

Les étendues, qui sont similaires aux autorisations, sont nécessaires lorsque votre application appelle une API. « Lecture » ou « écriture » sont des exemples d’étendues. Supposons que vous souhaitez que votre application web ou native « lise » à partir d’une API. Votre application appellerait Azure AD B2C et demanderait un jeton d’accès qui donne accès à l’étendue « lecture ». Pour qu’Azure AD B2C émette ce jeton d’accès, l’application doit disposer de l’autorisation de « lecture » à partir de l’API spécifique. Pour ce faire, votre API doit d’abord publier l’étendue « lecture ».

1. Dans le panneau **Applications** d’Azure AD B2C, ouvrez l’application d’API web (« API Contoso »).
1. Cliquez sur **Étendues publiées**. C’est ici que vous définissez les autorisations (étendues) qui peuvent être accordées à d’autres applications.
1. Ajoutez les **valeurs d’étendue** si nécessaire (par exemple, « lecture »). Par défaut, l’étendue « user_impersonation » est définie. Vous pouvez l’ignorer si vous le souhaitez. Entrez une description de l’étendue dans la colonne **Nom de l’étendue**.
1. Cliquez sur **Enregistrer**.

> [!IMPORTANT]
> Le **nom de l’étendue** est la description de la **valeur d’étendue**. Lorsque vous utilisez l’étendue, assurez-vous d’utiliser la **valeur d’étendue**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Attribuer des autorisations d’application web ou native à une API web

Une fois qu’une API est configurée pour publier des étendues, ces étendues doivent être attribuées à l’application cliente via le portail Azure.

1. Accédez au menu **Applications** dans le panneau des fonctionnalités Azure AD B2C.
1. Inscrivez une application cliente ([application web](active-directory-b2c-app-registration.md#register-a-web-app) ou [cliente native](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) si vous ne l’avez pas déjà fait. Si vous suivez ce guide comme point de départ, vous devrez inscrire une application cliente.
1. Cliquez sur **Accès d’API**.
1. Cliquez sur **Ajouter**.
1. Sélectionnez l’API web et les étendues (autorisations) que vous souhaitez accorder.
1. Cliquez sur **OK**.

> [!NOTE]
> Azure Active Directory B2C ne demande pas leur consentement aux utilisateurs de votre application cliente. À la place, le consentement est fourni par l’administrateur, selon les autorisations configurées entre les applications décrites ci-dessus. Si l’octroi d’une autorisation pour une application est révoqué, tous les utilisateurs qui ont précédemment pu acquérir cette autorisation ne sont plus en mesure de le faire.

## <a name="requesting-a-token"></a>Demande d’un jeton

Lorsque vous demandez un jeton d’accès, l’application cliente doit spécifier les autorisations souhaitées dans le paramètre **d’étendue** de la requête. Par exemple, pour spécifier la **valeur d’étendue** « lecture » pour l’API qui a **l’URI ID d’application** de `https://contoso.onmicrosoft.com/notes`, l’étendue serait `https://contoso.onmicrosoft.com/notes/read`. Voici un exemple de demande de code d’autorisation adressée au point de terminaison `/authorize`.

> [!NOTE]
> Actuellement, les domaines personnalisés ne sont pas pris en charge avec les jetons d’accès. Vous devez utiliser votre domaine tenantName.onmicrosoft.com dans l’URL de la requête.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Pour acquérir plusieurs autorisations dans la même requête, vous pouvez ajouter plusieurs entrées séparées par des espaces dans le même paramètre d’**étendue**. Par exemple : 

URL décodée :

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

URL encodée :

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Vous pouvez demander plus d’étendues (autorisations) pour une ressource que ce qui est accordé pour votre application cliente. Quand c’est le cas, l’appel réussit si au moins une autorisation est accordée. Le jeton d’accès (**access\_token**) a sa demande « scp » remplie uniquement avec les autorisations qui ont été accordées.

> [!NOTE] 
> Nous ne prenons pas en charge les demandes d’autorisation d’accès à deux ressources web différentes dans la même demande. Ce type de demande échoue.

### <a name="special-cases"></a>Cas particuliers

Le standard OpenID Connect spécifie plusieurs valeurs spéciales d’étendue (« scope »). Les étendues spéciales suivantes représentent l’autorisation d’accès au profil de l’utilisateur :

* **OpenID** : cette étendue demande un jeton d’ID
* **offline\_access** : cette étendue demande un jeton d’actualisation (à l’aide du [flux de code d’authentification](active-directory-b2c-reference-oauth-code.md)).

Si le paramètre `response_type` dans une requête `/authorize` inclut `token`, le paramètre `scope` doit inclure au moins l’une des étendues de ressource (autre que `openid` et `offline_access`) qui sera accordée. Sinon, la demande `/authorize` échoue.

## <a name="the-returned-token"></a>Jeton retourné

Dans un jeton d’accès (**access\_token**) correctement configuré (à partir du point de terminaison `/authorize` ou `/token`), les revendications suivantes sont présentes :

| NOM | Revendication | Description |
| --- | --- | --- |
|Audience |`aud` |L’**ID d’application** de la ressource unique à laquelle le jeton accorde l’accès. |
|Étendue |`scp` |Les autorisations accordées à la ressource. Plusieurs autorisations octroyées doivent être séparées par une espace. |
|Tiers autorisé |`azp` |**ID** de l’application cliente qui a initié la demande. |

Lorsque votre API reçoit le jeton d’accès (**access\_token**), elle doit [valider le jeton](active-directory-b2c-reference-tokens.md) pour prouver que le jeton est authentique et qu’il a les revendications adéquates.

Nous sommes ouverts aux commentaires et suggestions ! Si vous rencontrez des difficultés vis-à-vis de ce sujet, publiez sur Stack Overflow à l’aide de la mention [« azure-ad-b2c »](https://stackoverflow.com/questions/tagged/azure-ad-b2c). En cas de demandes liées aux fonctionnalités, utilisez [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Étapes suivantes

* Générer une API web à l’aide de [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Générer une API web à l’aide de [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
