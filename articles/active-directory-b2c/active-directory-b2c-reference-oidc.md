---
title: Connexion web avec OpenID Connect - Azure AD B2C | Microsoft Docs
description: Création d’applications web à l’aide de l’implémentation du protocole d’authentification OpenID Connect d’Azure Active Directory
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
ms.openlocfilehash: e787ea36ab5099705f151504385dd5dc97029e37
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C : connexion web avec OpenID Connect
OpenID Connect est un protocole d’authentification basé sur OAuth 2.0, qui peut être utilisé pour connecter de façon sécurisée des utilisateurs à des applications web. En utilisant l’implémentation d’OpenID Connect d’Azure Active Directory B2C (Azure AD B2C), vous pouvez sous-traiter l’inscription, la connexion et d’autres tâches de gestion des identités de vos applications web à Azure Active Directory. Ce guide explique comment procéder, indépendamment du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l’une de nos bibliothèques open source.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) étend le protocole *d’autorisation* OAuth 2.0 pour l’utiliser en tant que protocole *d’authentification*. Vous pouvez ainsi procéder à une authentification unique à l’aide d’OAuth. Il introduit le concept de *jeton d’ID*, qui est un jeton de sécurité permettant au client de vérifier l’identité de l’utilisateur et d’obtenir des informations de base sur son profil.

Comme il étend OAuth 2.0, les applications peuvent acquérir de façon sécurisée des *jetons d’accès*. Vous pouvez utiliser des jetons d’accès access_tokens pour accéder aux ressources qui sont sécurisées par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics). Nous recommandons OpenID Connect si vous concevez une application web hébergée sur un serveur et accessible par le biais d’un navigateur. Si vous souhaitez ajouter la gestion des identités à votre application mobile ou à votre application de bureau à l’aide d’Azure AD B2C, vous devez utiliser [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) au lieu d’OpenID Connect.

Azure AD B2C étend le protocole OpenID Connect standard pour proposer plus qu’une simple authentification et une simple autorisation. Il introduit le [paramètre de stratégie](active-directory-b2c-reference-policies.md), grâce auquel vous pouvez utiliser OpenID Connect pour ajouter à votre application des expériences utilisateur comme l’inscription, la connexion et la gestion des profils. Vous pouvez découvrir ici comment utiliser OpenID Connect et des stratégies pour implémenter chacune de ces expériences dans vos applications web. Vous voyez également comment obtenir des jetons d’accès pour accéder à des API web.

Les demandes HTTP d’exemple dans la section suivante utilisent notre répertoire B2C d’exemple fabrikamb2c.onmicrosoft.com, ainsi que notre exemple d’application, https://aadb2cplayground.azurewebsites.net, et nos stratégies. Vous êtes libre de tester ces demandes vous-même à l’aide de ces valeurs, ou de les remplacer par les vôtres.
Découvrez comment [obtenir votre propre client B2C, votre application et vos stratégies](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Envoi de demandes d’authentification
Lorsque votre application Web a besoin d’authentifier l’utilisateur et d’exécuter une stratégie, elle peut le diriger vers le point de terminaison `/authorize` . Il s’agit de la partie interactive du flux, dans laquelle l’utilisateur agit en fonction de la stratégie.

Dans cette demande, le client indique les autorisations qu’il a besoin d’acquérir de l’utilisateur dans le paramètre `scope` et la stratégie à exécuter dans le paramètre `p`. Trois exemples sont fournis dans les sections suivantes (avec des sauts de ligne pour une meilleure lisibilité), chacun utilisant une stratégie différente. Pour avoir une idée du fonctionnement de chaque demande, essayez de coller la demande dans un navigateur et exécutez-la.

#### <a name="use-a-sign-in-policy"></a>Utilisation d’une stratégie de connexion
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilisation d’une stratégie d’inscription
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilisation d’une stratégie de modification de profil
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paramètre | Requis ? | Description |
| --- | --- | --- |
| client_id |Obligatoire |L’ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| response_type |Obligatoire |Type de réponse, qui doit inclure un jeton d’ID pour OpenID Connect. Si votre application web a également besoin de jetons pour appeler une API web, vous pouvez utiliser `code+id_token`, comme nous l’avons fait ici. |
| redirect_uri |Recommandé |Paramètre `redirect_uri` de votre application, où les réponses d’authentification peuvent être envoyées et reçues par votre application. Il doit correspondre exactement à un des paramètres `redirect_uri` que vous avez inscrits dans le portail, excepté qu’il doit être codé dans une URL. |
| scope |Obligatoire |Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. L’étendue `openid` indique une autorisation de connecter l’utilisateur et d’obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID (vous pouvez trouver plus d’informations à ce sujet plus loin dans l’article). L’étendue `offline_access` est facultative pour les applications Web. Elle indique que votre application a besoin d’un *jeton d’actualisation* pour un accès de longue durée aux ressources. |
| response_mode |Recommandé |Méthode à utiliser pour renvoyer le code d’autorisation résultant à votre application. Il peut s’agir de `query`, `form_post` ou `fragment`.  Le mode de réponse `form_post` est recommandé pour une sécurité optimale. |
| state |Recommandé |Une valeur incluse dans la requête qui est également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page sur laquelle il était positionné. |
| nonce |Obligatoire |Valeur incluse dans la demande (générée par l’application), qui sera intégrée dans le jeton d’ID résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique aléatoire qui peut être utilisée pour identifier l’origine de la demande. |
| p |Obligatoire |Stratégie à exécuter. Il s’agit du nom d’une stratégie qui est créée dans votre client B2C. La valeur du nom de la stratégie doit commencer par `b2c\_1\_`. Découvrez plus d’informations sur les stratégies et [l’infrastructure de stratégie extensible](active-directory-b2c-reference-policies.md). |
| prompt |Facultatif |Type d’interaction utilisateur requis. La seule valeur valide pour l’instant est `login`, qui oblige l’utilisateur à saisir ses informations d’identification sur cette demande. L’authentification unique ne prendra pas effet. |

À ce stade, il est demandé à l’utilisateur d’effectuer le flux de travail de la stratégie. Il est possible que l’utilisateur doive entrer son nom d’utilisateur et son mot de passe, se connecter avec une identité sociale, s’inscrire à l’annuaire ou effectuer d’autres actions, en fonction de la façon dont la stratégie est définie.

Une fois que l’utilisateur a terminé la stratégie, Azure AD retourne une réponse à votre application dans le paramètre `redirect_uri` indiqué, en utilisant la méthode spécifiée dans le paramètre `response_mode`. La réponse est la même pour chacun des cas ci-dessus, indépendamment de la stratégie qui est exécutée.

Une réponse réussie utilisant `response_mode=fragment` se présenterait ainsi :

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| --- | --- |
| id_token |Le jeton d'ID que l’application a demandé. Vous pouvez utiliser le jeton d'ID pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour plus d’informations sur les jetons d’ID et leur contenu, consultez les [Informations de référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| code |Le code d’autorisation demandé par l’application, si vous avez utilisé `response_type=code+id_token`. L’application peut utiliser ce code d’autorisation afin de demander un jeton d’accès pour une ressource cible. Les codes d’autorisation ont des durées de vie très courtes. Généralement, ils expirent au bout de 10 minutes. |
| state |Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs `state` de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées au paramètre `redirect_uri`, pour que l’application puisse les traiter de façon appropriée :

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| --- | --- |
| error |Chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreur qui se produisent, et aussi pour réagir aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |
| state |Consultez la description complète dans le premier tableau de cette section. Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs `state` de la demande et de la réponse sont identiques. |

## <a name="validate-the-id-token"></a>Validation du jeton d’ID
La réception d’un jeton d’ID à elle seule n’est pas suffisante pour authentifier l’utilisateur. Vous devez valider la signature du jeton d’ID et vérifier les revendications du jeton selon les exigences de votre application. Azure AD B2C utilise les [jetons Web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Il existe de nombreuses bibliothèques open source pour valider les jetons JWT en fonction de votre langage préféré. Nous vous recommandons d’explorer ces options plutôt que d’implémenter votre propre logique de validation. Ces informations sont utiles pour savoir comment utiliser correctement ces bibliothèques.

Azure AD B2C présente un point de terminaison de métadonnées OpenID Connect, qui permet à une application de récupérer les informations relatives à Azure AD B2C lors de l’exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Il existe un document de métadonnées JSON pour chaque stratégie dans votre client B2C. Par exemple, le document de métadonnées pour la stratégie `b2c_1_sign_in` dans `fabrikamb2c.onmicrosoft.com` se trouve à l’emplacement suivant :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Une des propriétés de ce document de configuration est `jwks_uri`, dont la valeur pour la même stratégie serait :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Pour déterminer la stratégie utilisée pour la connexion d’un jeton d’ID (et d’où extraire les métadonnées), deux options sont possibles. Tout d’abord, le nom de la stratégie est inclus dans la revendication `acr` du jeton d’ID. Pour plus d’informations sur la façon d’analyser les revendications à partir d’un jeton d’ID, consultez les [Informations de référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md). L'autre option consiste à coder la stratégie dans la valeur du paramètre `state` lors de l'émission de la requête, puis à la décoder pour déterminer la stratégie qui a été utilisée. Les 2 méthodes sont valides.

Après avoir acquis le document de métadonnées auprès du point de terminaison de métadonnées OpenID Connect, vous pouvez utiliser les clés publiques RSA 256 (qui se trouvent sur ce point de terminaison) pour valider la signature du jeton d’ID. Ce point de terminaison peut comporter plusieurs clés à tout moment, chacune étant identifiée par une revendication `kid`. L’en-tête de ce jeton d’ID contient également une revendication `kid`, qui indique quelle clé a été utilisée pour signer le jeton d’ID. Pour plus d’informations, consultez les [Informations de référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md) (en particulier la section sur la [validation des jetons](active-directory-b2c-reference-tokens.md#token-validation)).
<!--TODO: Improve the information on this-->

Après avoir validé la signature du jeton d’ID, vous devez vérifier plusieurs revendications. Exemple :

* Vous devez valider la revendication `nonce` afin d’empêcher les attaques par relecture de jetons. Sa valeur doit correspondre à ce que vous avez spécifié dans la requête de connexion.
* Vous devez valider la revendication `aud` afin de vérifier que le jeton d’ID a été émis pour votre application. Sa valeur doit correspondre à l’ID d’application de votre application.
* Vous devez valider les revendications `iat` et `exp` afin de vérifier que le jeton d’ID n’est pas expiré.

Vous devez également effectuer plusieurs autres validations. Celles-ci sont décrites en détail dans les [Spécifications principales d’OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).  En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

* Vérifier que l’utilisateur/l’organisation s’est inscrit pour l’application.
* Vérifier que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
* S’assurer de l’utilisation d’une force certaine d’authentification, comme Azure Multi-Factor Authentication.

Pour plus d’informations sur les revendications dans un jeton d’ID, consultez les [Informations de référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md).

Une fois que vous avez validé le jeton d’ID, vous pouvez démarrer une session avec l’utilisateur. Vous pouvez utiliser les revendications du jeton d’ID pour obtenir des informations sur l’utilisateur dans votre application. Les utilisations de ces informations sont notamment l’affichage, les enregistrements et les autorisations.

## <a name="get-a-token"></a>Obtention d’un jeton
Si votre application web doit seulement exécuter des stratégies, vous pouvez ignorer les quelques sections suivantes. Ces sections s’appliquent seulement aux applications web qui doivent effectuer des appels authentifiés à une API web et qui sont également protégées par Azure AD B2C.

Vous pouvez échanger le code d’autorisation que vous avez acquis (en utilisant `response_type=code+id_token`) contre un jeton sur la ressource souhaitée en envoyant une demande `POST` au point de terminaison `/token`. Actuellement, la seule ressource pour laquelle vous pouvez demander un jeton est l’API web principale de votre application. La convention pour demander un jeton à vous-même consiste à utiliser votre ID de client d’application en tant qu’étendue :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Paramètre | Requis ? | Description |
| --- | --- | --- |
| p |Obligatoire |La stratégie qui a été utilisée pour obtenir le code d'autorisation. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la chaîne de requête, et non pas au corps de `POST`. |
| client_id |Obligatoire |L’ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| grant_type |Obligatoire |Le type d’octroi, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| scope |Recommandé |Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de paramètres id_token. Elle peut être utilisée afin d’obtenir des jetons pour l’API web principale de votre application, qui est représentée par le même ID d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un jeton d’actualisation pour un accès de longue durée aux ressources. |
| code |Obligatoire |Code d’autorisation acquis dans le premier tronçon du flux. |
| redirect_uri |Obligatoire |Le paramètre `redirect_uri` de l’application où vous avez reçu le code d’autorisation. |
| client_secret |Obligatoire |Le secret d'application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Ce secret d’application est un artefact de sécurité important. Vous devez le stocker sur votre serveur de manière sécurisée. Vous devez également veiller à renouveler ce secret du client régulièrement. |

Un jeton de réponse de réussite se présente ainsi :

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
| token_type |Valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est `Bearer`. |
| access_token |Le jeton JWT signé que vous avez demandé. |
| scope |Étendues pour lesquelles le jeton est valide. Elles peuvent être utilisées pour mettre en cache des jetons pour une utilisation ultérieure. |
| expires_in |Durée de validité du jeton d’accès (en secondes). |
| refresh_token |Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation ont une durée de vie longue et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes de temps étendues. Pour plus d’informations, consultez les [Informations de référence sur les jetons B2C](active-directory-b2c-reference-tokens.md). Notez que vous devez avoir utilisé l’étendue `offline_access` dans les demandes d’autorisation et de jeton pour recevoir un jeton d’actualisation. |

Les réponses d’erreur se présentent comme ceci :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| --- | --- |
| error |Chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreur qui se produisent, et aussi pour réagir aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## <a name="use-the-token"></a>Utilisation du jeton
Un jeton d’accès étant acquis, vous pouvez maintenant l’utiliser dans les demandes effectuées à vos API web principales en l’incluant dans l’en-tête `Authorization` :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Actualisation du jeton
Les jetons d’ID ont une durée de vie courte. Vous devez les actualiser après leur expiration pour continuer à accéder aux ressources. Pour ce faire, envoyez une nouvelle demande `POST` au point de terminaison `/token`. Cette fois-ci, spécifiez le paramètre `refresh_token` au lieu du paramètre `code` :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paramètre | Obligatoire | Description |
| --- | --- | --- |
| p |Obligatoire |Stratégie utilisée pour obtenir le jeton d’actualisation d’origine. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la chaîne de requête, et non pas au corps de POST. |
| client_id |Obligatoire |L’ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| grant_type |Obligatoire |Le type d’octroi, qui doit être un jeton d’actualisation pour ce tronçon du flux de code d’autorisation. |
| scope |Recommandé |Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID. Elle peut être utilisée afin d’obtenir des jetons pour l’API web principale de votre application, qui est représentée par le même ID d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un jeton d’actualisation pour un accès de longue durée aux ressources. |
| redirect_uri |Recommandé |Le paramètre `redirect_uri` de l’application où vous avez reçu le code d’autorisation. |
| refresh_token |Obligatoire |Jeton d’actualisation d’origine que vous avez acquis dans le second tronçon du flux. Notez que vous devez avoir utilisé l’étendue `offline_access` dans les demandes d’autorisation et de jeton pour recevoir un jeton d’actualisation. |
| client_secret |Obligatoire |Le secret d'application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Ce secret d’application est un artefact de sécurité important. Vous devez le stocker sur votre serveur de manière sécurisée. Vous devez également veiller à renouveler ce secret du client régulièrement. |

Un jeton de réponse de réussite se présente ainsi :

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
| token_type |Valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est `Bearer`. |
| access_token |Le jeton JWT signé que vous avez demandé. |
| scope |Les étendues pour lesquelles le jeton est valide, qui peuvent être utilisées afin de mettre en cache des jetons pour une utilisation ultérieure. |
| expires_in |Durée de validité du jeton d’accès (en secondes). |
| refresh_token |Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel.  Les jetons d’actualisation ont une durée de vie longue et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes de temps étendues. Pour plus d’informations, consultez la page de [référence des jetons B2C](active-directory-b2c-reference-tokens.md). |

Les réponses d’erreur se présentent comme ceci :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| --- | --- |
| error |Chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreur qui se produisent, et aussi pour réagir aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion
Quand vous souhaitez déconnecter l’utilisateur de l’application, la suppression des cookies de votre application ou l’arrêt de la session de l’utilisateur ne suffisent pas. Vous devez également rediriger l’utilisateur vers Azure AD. Si vous n’y parvenez pas, l’utilisateur peut être en mesure de se ré-authentifier auprès de votre application, sans entrer ses informations d’identification à nouveau. En effet, il dispose d’une session d’authentification unique valide avec Azure AD.

Vous pouvez simplement rediriger l’utilisateur vers le point de terminaison `end_session` qui est répertorié dans le document de métadonnées OpenID Connect précédemment décrit dans la section « Validation du jeton d’ID » :

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paramètre | Requis ? | Description |
| --- | --- | --- |
| p |Obligatoire |Stratégie que vous voulez utiliser pour déconnecter l’utilisateur de votre application. |
| post_logout_redirect_uri |Recommandé |URL vers laquelle l’utilisateur doit être redirigé après la déconnexion. Si elle n’est pas incluse, Azure AD B2C affiche un message générique à l’utilisateur. |

> [!NOTE]
> Bien que la redirection de l’utilisateur vers le point de terminaison `end_session` efface en partie l’état d’authentification unique de l’utilisateur auprès d’Azure AD B2C, elle ne déconnecte pas l’utilisateur de la session du fournisseur d’identité sociale. Si l’utilisateur sélectionne le même IDP pendant une connexion ultérieure, il est de nouveau authentifié sans entrer ses informations d’identification. Si un utilisateur veut se déconnecter de votre application B2C, cela ne signifie pas nécessairement qu’il souhaite se déconnecter de son compte Facebook. Toutefois, dans le cas de comptes locaux, la session de l’utilisateur sera terminée correctement.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Utilisation de votre propre client B2C
Si vous voulez tester ces demandes par vous-même, vous devez effectuer ces trois étapes puis remplacer les exemples de valeurs décrites précédemment par les vôtres :

1. [Créez un client B2C](active-directory-b2c-get-started.md)et utilisez le nom de votre client dans les demandes.
2. [Créez une application](active-directory-b2c-app-registration.md) pour obtenir un ID d’application. Incluez une application web/API web dans votre application. Si vous le souhaitez, créez un secret d’application.
3. [Créez vos stratégies](active-directory-b2c-reference-policies.md) pour obtenir les noms de vos stratégies.

