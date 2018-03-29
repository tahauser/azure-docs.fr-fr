---
title: Flux de code d’autorisation - Azure AD B2C | Microsoft Docs
description: Découvrez comment créer des applications web à l’aide d’Azure AD B2C et du protocole d’authentification OpenID Connect.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: d49a1c97a578726c26f8533476042646b0b302d3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C : flux de code d’autorisation OAuth 2.0
Vous pouvez utiliser l’octroi de code d’autorisation OAuth 2.0 dans les applications qui sont installées sur un appareil pour accéder à des ressources protégées, comme des API web. Grâce à l’implémentation Azure Active Directory B2C (Azure AD B2C) d’OAuth 2.0, vous pouvez ajouter l’inscription, la connexion et d’autres tâches de gestion d’identité à vos applications mobiles et applications de bureau. Cet article est indépendant du langage. Il décrit comment envoyer et recevoir des messages HTTP sans utiliser de bibliothèque open source.

<!-- TODO: Need link to libraries -->

Le flux de code d’autorisation OAuth 2.0 est décrit dans la [section 4.1 des spécifications OAuth 2.0](http://tools.ietf.org/html/rfc6749). Vous pouvez l’utiliser pour les activités d’authentification et d’autorisation avec la plupart des types d’applications, notamment les [applications web](active-directory-b2c-apps.md#web-apps) et les [applications installées de façon native](active-directory-b2c-apps.md#mobile-and-native-apps). Vous pouvez utiliser le flux de code d’autorisation OAuth 2.0 pour acquérir en toute sécurité des *jetons d’accès* pour vos applications, qui peuvent être utilisés pour accéder à des ressources sécurisés par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics).

Cet article est axé sur le flux de code d’autorisation OAuth 2.0 pour les **clients publics**. Un client public est une application cliente qui ne peut pas être approuvée pour maintenir de façon sécurisée l'intégrité d’un mot de passe secret. Cela comprend les applications mobiles, les applications de bureau et quasiment toutes les applications qui s’exécutent sur un appareil et qui ont besoin d’obtenir des jetons d’accès. 

> [!NOTE]
> Pour ajouter la gestion d’identité à une application web à l’aide d’Azure AD B2C, utilisez [OpenID Connect](active-directory-b2c-reference-oidc.md) plutôt qu’OAuth 2.0.

Azure AD B2C étend les flux OAuth 2.0 standard pour proposer plus qu’une simple authentification et une simple autorisation. Il introduit le [paramètre de stratégie](active-directory-b2c-reference-policies.md). Avec les stratégies intégrées, vous pouvez utiliser OAuth 2.0 pour ajouter des expériences utilisateur à votre application, comme l’inscription, la connexion et la gestion des profils. Dans cet article, nous allons vous montrer comment utiliser OAuth 2.0 et des stratégies pour implémenter chacune de ces expériences dans vos applications natives. Vous verrez également comment obtenir des jetons d’accès afin d’accéder à des API web.

Dans les exemples de demandes HTTP de cet article, nous utilisons notre exemple d’annuaire Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. Nous utilisons également nos exemples d’application et de stratégies. Vous pouvez essayer les demandes par vous-même en utilisant ces valeurs ou bien en les remplaçant par les vôtres.
Découvrez comment [obtenir vos propres stratégies, application et annuaire Azure AD B2C](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Obtention d’un code d’autorisation
Le flux de code d'autorisation commence par le client dirigeant l'utilisateur vers le point de terminaison `/authorize` . Il s’agit de la partie interactive du flux, dans laquelle l’utilisateur agit réellement. Dans cette demande, le client indique dans le paramètre `scope` les autorisations qu’il doit obtenir auprès de l’utilisateur. Dans le paramètre `p`, il indique la stratégie à exécuter. Les trois exemples suivants (avec sauts de ligne pour faciliter la lecture) utilisent chacun une stratégie différente.

### <a name="use-a-sign-in-policy"></a>Utilisation d’une stratégie de connexion
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Utilisation d’une stratégie d’inscription
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Utilisation d’une stratégie de modification de profil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Paramètre | Requis ? | Description |
| --- | --- | --- |
| client_id |Obligatoire |ID d’application affecté à votre application dans le [portail Azure](https://portal.azure.com). |
| response_type |Obligatoire |Le type de réponse, qui doit inclure `code` pour le flux de code d’autorisation. |
| redirect_uri |Obligatoire |URI de redirection de votre application, où votre application envoie et reçoit des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection que vous avez enregistrés dans le portail, sauf qu’il doit être encodé au format URL. |
| scope |Obligatoire |Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure Active Directory (Azure AD) les deux autorisations qui sont demandées. Utiliser l’ID de client comme étendue indique que votre application a besoin d’un jeton d’accès qui peut être utilisé avec votre propre service ou API web, représenté par le même ID de client.  L’étendue `offline_access` indique que votre application a besoin d’un jeton d’actualisation pour un accès durable aux ressources. Vous pouvez également utiliser l’étendue `openid` pour demander un jeton d’ID à partir d’Azure AD B2C. |
| response_mode |Recommandé |Méthode à utiliser pour renvoyer le code d’autorisation résultant à votre application. Il peut s’agir de `query`, `form_post` ou `fragment`. |
| state |Recommandé |Une valeur incluse dans la requête qui est également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne de n’importe quel contenu que vous voulez utiliser. Généralement, une valeur unique générée de manière aléatoire est utilisée, pour empêcher les attaques par falsification de requête intersites. L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification. Par exemple, la page dans laquelle l’utilisateur se trouvait ou la stratégie en cours d’exécution. |
| p |Obligatoire |Stratégie exécutée. Il s’agit du nom d’une stratégie créée dans votre annuaire Azure AD B2C. La valeur du nom de la stratégie doit commencer par **b2c\_1\_**. Pour en savoir plus sur les stratégies, consultez [Stratégies intégrées d’Azure AD B2C](active-directory-b2c-reference-policies.md). |
| prompt |Facultatif |Type d’interaction utilisateur requis. Actuellement, la seule valeur valide est `login`, ce qui oblige l’utilisateur à entrer ses informations d’identification sur cette demande. L’authentification unique ne prendra pas effet. |

À ce stade, il est demandé à l’utilisateur d’effectuer le flux de travail de la stratégie. Il est possible que l’utilisateur doive entrer son nom d’utilisateur et son mot de passe, se connecter avec une identité sociale, s’inscrire à l’annuaire, etc. Les actions de l’utilisateur dépendent de la façon dont la stratégie est définie.

Une fois que l’utilisateur a terminé la stratégie, Azure AD retourne une réponse à votre application avec la valeur que vous avez utilisée pour `redirect_uri`. Il utilise la méthode spécifiée dans le paramètre `response_mode`. La réponse est exactement la même pour chacun des scénarios d’actions utilisateur, indépendamment de la stratégie exécutée.

Une réponse correcte utilisant `response_mode=query` se présente ainsi :

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paramètre | Description |
| --- | --- |
| code |Le code d’autorisation demandé par l’application. L’application peut utiliser le code d’autorisation pour demander un jeton d’accès pour une ressource cible. Les codes d’autorisation ont des durées de vie très courtes. Généralement, ils expirent au bout de 10 minutes. |
| state |Consultez la description complète dans le tableau de la section précédente. Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs `state` de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées à l’URI de redirection, pour que l’application puisse les traiter de façon appropriée :

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| --- | --- |
| error |Chaîne de code d’erreur que vous pouvez utiliser pour classer les types d’erreurs se produisant. Vous pouvez également utiliser la chaîne pour réagir aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut vous aider à identifier la cause principale d’une erreur d’authentification. |
| state |Consultez la description complète dans le tableau précédent. Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs `state` de la demande et de la réponse sont identiques. |

## <a name="2-get-a-token"></a>2. Obtention d’un jeton
Un code d’autorisation étant acquis, vous pouvez maintenant échanger `code` contre un jeton sur la ressource prévue en envoyant une demande POST au point de terminaison `/token`. Dans Azure AD B2C, la seule ressource pour laquelle vous pouvez demander un jeton est l’API web principale de votre application. La convention utilisée pour demander un jeton à vous-même consiste à utiliser votre ID de client d’application en tant qu’étendue :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Paramètre | Requis ? | Description |
| --- | --- | --- |
| p |Obligatoire |La stratégie qui a été utilisée pour obtenir le code d'autorisation. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la *chaîne de requête*, et non au corps POST. |
| client_id |Obligatoire |ID d’application affecté à votre application dans le [portail Azure](https://portal.azure.com). |
| grant_type |Obligatoire |Type d’autorisation. Pour le flux de code d’autorisation, le type d’autorisation doit être `authorization_code`. |
| scope |Recommandé |Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. Utiliser l’ID de client comme étendue indique que votre application a besoin d’un jeton d’accès qui peut être utilisé avec votre propre service ou API web, représenté par le même ID de client.  L’étendue `offline_access` indique que votre application a besoin d’un jeton d’actualisation pour un accès durable aux ressources.  Vous pouvez également utiliser l’étendue `openid` pour demander un jeton d’ID à partir d’Azure AD B2C. |
| code |Obligatoire |Code d’autorisation acquis dans le premier tronçon du flux. |
| redirect_uri |Obligatoire |URI de redirection de l’application où vous avez reçu le code d’autorisation. |

Un jeton de réponse correct se présente ainsi :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| --- | --- |
| not_before |Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token_type |Valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| access_token |JSON Web Token (JWT) signé que vous avez demandé. |
| scope |Étendues de validité du jeton. Vous pouvez également utiliser des étendues pour mettre en cache des jetons pour une utilisation ultérieure. |
| expires_in |La durée de validité du jeton (en secondes). |
| refresh_token |Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation sont de longue durée. Vous pouvez les utiliser pour conserver l’accès aux ressources pendant de longues périodes. Pour plus d’informations, consultez la [référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Les réponses d’erreur ressemblent à ceci :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| --- | --- |
| error |Chaîne de code d’erreur que vous pouvez utiliser pour classer les types d’erreurs se produisant. Vous pouvez également utiliser la chaîne pour réagir aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut vous aider à identifier la cause principale d’une erreur d’authentification. |

## <a name="3-use-the-token"></a>3. Utilisation du jeton
Un jeton d’accès étant acquis, vous pouvez maintenant l’utiliser dans les demandes dirigées vers vos API web principales en l’incluant dans l’en-tête `Authorization` :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Actualisation du jeton
Les jetons d’accès et les jetons d’ID ont une courte durée de vie. Après leur expiration, vous devez les actualiser pour continuer à accéder aux ressources. Pour cela, envoyez une autre demande POST au point de terminaison `/token`. Cette fois, fournissez l’élément `refresh_token` au lieu de l’élément `code` :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paramètre | Requis ? | Description |
| --- | --- | --- |
| p |Obligatoire |Stratégie utilisée pour obtenir le jeton d’actualisation d’origine. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la *chaîne de requête*, et non au corps POST. |
| client_id |Recommandé |ID d’application affecté à votre application dans le [portail Azure](https://portal.azure.com). |
| grant_type |Obligatoire |Type d’autorisation. Pour ce tronçon du flux de code d’autorisation, le type d’autorisation doit être `refresh_token`. |
| scope |Recommandé |Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. Utiliser l’ID de client comme étendue indique que votre application a besoin d’un jeton d’accès qui peut être utilisé avec votre propre service ou API web, représenté par le même ID de client.  L’étendue `offline_access` indique que votre application a besoin d’un jeton d’actualisation pour un accès durable aux ressources.  Vous pouvez également utiliser l’étendue `openid` pour demander un jeton d’ID à partir d’Azure AD B2C. |
| redirect_uri |Facultatif |URI de redirection de l’application où vous avez reçu le code d’autorisation. |
| refresh_token |Obligatoire |Jeton d’actualisation d’origine que vous avez acquis dans le second tronçon du flux. |

Un jeton de réponse correct se présente ainsi :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| --- | --- |
| not_before |Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token_type |Valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| access_token |Jeton JWT signé que vous avez demandé. |
| scope |Étendues de validité du jeton. Vous pouvez également utiliser les étendues pour mettre en cache des jetons pour une utilisation ultérieure. |
| expires_in |La durée de validité du jeton (en secondes). |
| refresh_token |Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d’informations, consultez la [référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Les réponses d’erreur ressemblent à ceci :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| --- | --- |
| error |Chaîne de code d’erreur que vous pouvez utiliser pour classer les types d’erreurs qui se produisent. Vous pouvez également utiliser la chaîne pour réagir aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut vous aider à identifier la cause principale d’une erreur d’authentification. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Utiliser votre propre annuaire Azure AD B2C
Pour essayer vous-même ces demandes, effectuez les étapes suivantes. Remplacez les exemples de valeurs que nous utilisons dans cet article par vos propres valeurs.

1. [Créez un annuaire Azure AD B2C](active-directory-b2c-get-started.md). Utilisez le nom de votre annuaire dans les demandes.
2. [Créez une application](active-directory-b2c-app-registration.md) pour obtenir un ID d’application et un URI de redirection. Incluez un client natif dans votre application.
3. [Créez vos stratégies](active-directory-b2c-reference-policies.md) pour obtenir les noms de vos stratégies.

