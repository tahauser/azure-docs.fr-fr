---
title: Flux de Code OAuth v2.0 d’Azure AD | Microsoft Docs
description: Création d'applications Web à l'aide de la mise en œuvre du protocole d'authentification OAuth 2.0 d'Azure AD.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: c6af6fb6ab957d79d71c816e6a23b0526f016a71
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>Protocoles v2.0 : flux du code d’autorisation OAuth 2.0
L'octroi d'un code d'autorisation OAuth 2.0 peut servir dans les applications qui sont installées sur un périphérique pour accéder à des ressources protégées, comme des API Web.  Avec la mise en œuvre du modèle d’application v2.0 d’OAuth 2.0, vous pouvez ajouter une connexion et un accès API à vos applications mobiles et de bureau.  Ce guide est indépendant de la langue. Il explique comment envoyer et recevoir des messages HTTP sans utiliser aucune des [bibliothèques d’authentification Open Source Azure](active-directory-authentication-libraries.md).

> [!NOTE]
> Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).
> 
> 

Le flux de code d’autorisation OAuth 2.0 est décrit dans la [section 4.1 des spécifications OAuth 2.0](http://tools.ietf.org/html/rfc6749).  Il est utilisé pour exécuter des activités d’authentification et d’autorisation dans la majorité des types d’applications, notamment les [applications web](active-directory-v2-flows.md#web-apps) et les [applications installées de façon native](active-directory-v2-flows.md#mobile-and-native-apps).  Le flux permet aux applications d’acquérir de manière sûre les access_tokens pouvant être utilisés pour accéder aux ressources sécurisées par le point de terminaison v2.0.  

## <a name="protocol-diagram"></a>Schéma de protocole
À un niveau élevé, le flux d'authentification complet pour une application native/mobile ressemble est semblable à l'illustration suivante :

![Flux de code d’authentification OAuth](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Demander un code d’autorisation
Le flux de code d'autorisation commence par le client dirigeant l'utilisateur vers le point de terminaison `/authorize` .  Dans cette requête, le client indique les autorisations dont il a besoin d’obtenir auprès de l’utilisateur :

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> Cliquez sur le lien ci-dessous pour exécuter cette requête ! Une fois que vous êtes connecté, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec une valeur `code` dans la barre d’adresse.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Paramètre             |             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| locataire                | required    | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client.  Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                                                                                                                                                      |
| client_id             | required    | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) a assigné à votre application.                                                                                                                                                                                                                                                                                                                                                                                                |
| response_type         | required    | Doit inclure `code` pour le flux de code d’autorisation.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| redirect_uri          | recommandé | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification.  Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL.  Pour les applications natives et mobiles, vous devez utiliser la valeur par défaut `https://login.microsoftonline.com/common/oauth2/nativeclient`.                                                                                                                                                                                                                                                              |
| scope                 | required    | Liste séparée par des espaces d’ [étendues](active-directory-v2-scopes.md) pour lesquelles vous souhaitez que l’utilisateur donne son consentement.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| response_mode         | recommandé | Spécifie la méthode à utiliser pour envoyer le jeton résultant à votre application.  Peut être `query` ou `form_post`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| state                 | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton.  Il peut s’agir d’une chaîne du contenu de votre choix.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  La valeur peut également encoder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou la vue sur laquelle il était.                                                                                                                                          |
| prompt                | facultatif    | Indique le type d’interaction utilisateur requis.  Les seules valeurs valides pour l’instant sont « login », « none » et « consent ».  `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique.  Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu'aucune invite interactive d'aucune sorte n'est présentée à l'utilisateur.  Si la demande ne peut pas être exécutée en mode silencieux au moyen d’une authentification unique, le point de terminaison v2.0 renvoie une erreur.  `prompt=consent` déclenche l’affichage de la boîte de dialogue de consentement OAuth après la connexion de l’utilisateur, afin de lui demander d’octroyer des autorisations à l’application. |
| login_hint            | facultatif    | Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur.  Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username`.                                                                                                                                                                                                                                                                                                      |
| domain_hint           | facultatif    | Peut être `consumers` ou `organizations`.  S’il est inclus, ce paramètre ignore le processus de découverte par courrier électronique auquel l’utilisateur doit se soumettre sur la page de connexion v2.0, ce qui améliore légèrement l’expérience utilisateur.  Les applications utilisent souvent ce paramètre au cours de la réauthentification, en extrayant la revendication `tid` à partir d’une connexion précédente.  Si la revendication `tid` est définie sur la valeur `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`.  Sinon, utilisez `domain_hint=organizations`.                                                                                                                |
| code_challenge_method | facultatif    | La méthode utilisée pour encoder le `code_verifier` pour le paramètre `code_challenge`. Peut être `plain` ou `S256`.  S’il est exclu, `code_challenge` est censé être dans un texte en clair si `code_challenge` est inclus.  Azure AAD v2.0 prend en charge `plain` et `S256`. Pour plus d'informations, consultez le [RFC PKCE](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | facultatif    | Utilisé pour sécuriser l’octroi du code d’autorisation via la clé de preuve pour le Code Exchange (PKCE) à partir d’un client natif. Obligatoire si `code_challenge_method` est inclus.  Pour plus d'informations, consultez le [RFC PKCE](https://tools.ietf.org/html/rfc7636).                                                                                                                                                                                                                                                                                                                                                                      |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification.  Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope` .  Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement.  Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte utilisant `response_mode=query` se présente ainsi :

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Paramètre | Description                                                                                                                                                                                                                        |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| code      | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible.  Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| state     | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques.                                              |

#### <a name="error-response"></a>Réponse d’erreur
Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre         | Description                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification.            |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison d’autorisation
Le tableau suivant décrit les différents codes d’erreur qui peuvent être retournés dans le paramètre `error` de la réponse d’erreur.

| Code d'erreur                | Description                                                                                                           | Action du client                                                                                                                                                                                                                               |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request           | Erreur de protocole, tel qu’un paramètre obligatoire manquant.                                                                 | Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux.                                                                                                                                       |
| unauthorized_client       | L’application cliente n’est pas autorisée à demander un code d’autorisation.                                             | Cette erreur se produit généralement lorsque l’application client n’est pas inscrite dans Azure AD ou n’est pas ajoutée à l’abonné Azure AD de l’utilisateur. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |
| access_denied             | Le propriétaire de la ressource n’a pas accordé son consentement.                                                                                         | L’application cliente peut avertir l’utilisateur qu’elle ne peut pas continuer sans son consentement.                                                                                                                                                 |
| unsupported_response_type | Le serveur d’autorisation ne prend pas en charge le type de réponse dans la demande.                                           | Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux.                                                                                                                                       |
| server_error              | Le serveur a rencontré une erreur inattendue.                                                                           | relancez la requête. Ces erreurs peuvent résulter de conditions temporaires. L’application client peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une erreur temporaire.                                                                  |
| temporarily_unavailable   | Le serveur est temporairement trop occupé pour traiter la demande.                                                             | relancez la requête. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une condition temporaire.                                                                                                                 |
| invalid_resource          | La ressource cible n’est pas valide car elle n’existe pas, Azure AD ne la trouve pas ou elle n’est pas configurée correctement. | Cette erreur indique que la ressource, si elle existe, n’a pas été configurée dans l’abonné. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD.                                            |

## <a name="request-an-access-token"></a>Demander un jeton d’accès
Maintenant que vous avez acquis un authorization_code et que l'utilisateur vous a octroyé une autorisation, vous pouvez échanger `code` contre un `access_token` sur la ressource souhaitée. Pour ce faire, envoyez une requête `POST` au point de terminaison  `/token` :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Essayez d'exécuter cette requête dans Postman ! (N’oubliez pas de remplacer la valeur `code`) [![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Paramètre     |                       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| locataire        | required              | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client.  Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints).                                                                                                                                                     |
| client_id     | required              | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) a assigné à votre application.                                                                                                                                                                                                                               |
| grant_type    | required              | Doit être `authorization_code` pour le flux de code d'autorisation.                                                                                                                                                                                                                                                                                                                                                                              |
| scope         | required              | Une liste d’étendues séparées par des espaces.  Les étendues demandées dans ce tronçon doivent être équivalentes aux étendues demandées dans le premier tronçon, ou correspondre à un sous-ensemble de ces dernières.  Si les étendues spécifiées dans cette requête couvrent plusieurs serveurs de ressources, le point de terminaison v2.0 renvoie alors un jeton pour la ressource spécifiée dans la première étendue.  Pour obtenir une explication plus détaillée des étendues, consultez les [autorisations, consentements et étendues](active-directory-v2-scopes.md). |
| code          | required              | Le code d’autorisation acquis dans le premier tronçon du flux.                                                                                                                                                                                                                                                                                                                                                                     |
| redirect_uri  | required              | Valeur redirect_uri qui a déjà été utilisée pour obtenir le paramètre authorization_code.                                                                                                                                                                                                                                                                                                                                                               |
| client_secret | requis pour les applications Web | Le secret d’application que vous avez créé dans le portail d’inscription des applications pour votre application.  Il ne doit pas être utilisé dans une application native, car les clés secrètes client ne peuvent pas être stockées de manière sûre sur les appareils.  Il est requis pour les applications Web et les API Web, qui présentent la capacité de stocker de manière sûre les clés secrètes client sur le côté serveur.                                                                                                                        |
| code_verifier | facultatif              | Le même code_verifier utilisé pour obtenir le authorization_code.  Obligatoire si PKCE est utilisé dans la requête d’octroi du code d’autorisation.  Pour plus d'informations, consultez le [RFC PKCE](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
#### <a name="successful-response"></a>Réponse correcte
Une réponse de jeton réussie se présente ainsi :

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paramètre     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, par exemple une API Web.                                                                                                                                                                                                                                                                                                                                      |
| token_type    | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur.                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | La durée de validité (en secondes) du jeton d’accès.                                                                                                                                                                                                                                                                                                                                                                                                         |
| scope         | L’étendue de validité du jeton d’accès.                                                                                                                                                                                                                                                                                                                                                                                                           |
| refresh_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons d’accès supplémentaires après l’expiration du jeton d’accès actuel.  Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées.  Pour plus d'informations, consultez la page de [référence sur les jetons v2.0](active-directory-v2-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `offline_access` a été demandée.                                                 |
| id_token      | Un jeton Web JSON non signé (JWT). L’application peut décoder les segments de ce jeton à l’aide d’un décodeur base64Url afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais ne peut aucunement les utiliser pour les limites d’autorisation ou de sécurité.  Pour en savoir plus sur les id_tokens, consultez la [référence sur les jetons du point de terminaison v2.0](active-directory-v2-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |
#### <a name="error-response"></a>Réponse d’erreur
Les réponses d’erreur se présentent comme suit :

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paramètre         | Description                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification.            |
| error_codes       | Liste des codes d’erreur STS spécifiques pouvant être utiles dans les tests de diagnostic.                                                  |
| timestamp         | Heure à laquelle l’erreur s’est produite.                                                                             |
| trace_id          | Identifiant unique de la demande pouvant être utile dans les tests de diagnostic.                                                 |
| correlation_id    | Identifiant unique de la demande pouvant être utile dans les tests de diagnostic sur les divers composants.                               |

#### <a name="error-codes-for-token-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison de jeton
| Code d'erreur              | Description                                                                                                           | Action du client                                                                                                                                                                                                                               |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request         | Erreur de protocole, tel qu’un paramètre obligatoire manquant.                                                                 | Corrigez l’erreur, puis envoyez à nouveau la demande.                                                                                                                                                                                                                |
| invalid_grant           | Le code d’autorisation ou le vérificateur du code PCKE n’est pas valide ou a expiré.                                               | Essayez une nouvelle requête au point de terminaison `/authorize` et vérifiez que le paramètre code_verifier est correct.                                                                                                                                     |
| unauthorized_client     | Le client authentifié n’est pas autorisé à utiliser ce type d’octroi d’autorisation.                                      | Cela se produit généralement lorsque l’application cliente n’est pas inscrite dans Azure AD ou n’est pas ajoutée au client Azure AD de l’utilisateur. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |
| invalid_client          | Échec d’authentification du client.                                                                                         | Les informations d’identification du client ne sont pas valides. Pour résoudre le problème, l’administrateur de l’application met à jour les informations d’identification.                                                                                                                                        |
| unsupported_grant_type  | Le serveur d’autorisation ne prend pas en charge le type d’octroi d’autorisation.                                               | Modifiez le type d’octroi dans la demande. Ce type d’erreur doit se produire uniquement lors du développement et doit être détecté lors du test initial.                                                                                                       |
| invalid_resource        | La ressource cible n’est pas valide car elle n’existe pas, Azure AD ne la trouve pas ou elle n’est pas configurée correctement. | Cela indique que la ressource, si elle existe, n’a pas été configurée dans le client. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD.                                            |
| interaction_required    | La demande nécessite une interaction utilisateur. Par exemple, une étape d’authentification supplémentaire est nécessaire.                    | Relancez la demande avec la même ressource.                                                                                                                                                                                                   |
| temporarily_unavailable | Le serveur est temporairement trop occupé pour traiter la demande.                                                             | relancez la requête. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une condition temporaire.                                                                                                                 |

## <a name="use-the-access-token"></a>Utiliser le jeton d’accès
Maintenant que vous avez acquis un jeton `access_token`, vous pouvez l'utiliser dans des requêtes dirigées vers des API Web en l'incluant dans l'en-tête `Authorization` :

> [!TIP]
> Exécutez cette requête dans Postman ! (Remplacez d’abord l’en-tête `Authorization`) [![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Actualiser le jeton d’accès
Les jetons d’accès présentent une durée de vie courte. Après leur expiration, vous devez les actualiser afin de pouvoir continuer à accéder aux ressources.  Pour ce faire, envoyez une nouvelle requête `POST` au point de terminaison `/token`, en fournissant l’élément `refresh_token` au lieu de l’élément `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Essayez d'exécuter cette requête dans Postman ! (N’oubliez pas de remplacer la valeur `refresh_token`) [![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Paramètre     |                       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| locataire        | required              | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client.  Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                   |
| client_id     | required              | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) a assigné à votre application.                                                                                                                                                                                                                                                             |
| grant_type    | required              | Doit inclure `refresh_token` pour ce tronçon du flux de code d'autorisation.                                                                                                                                                                                                                                                                                                                                                                                                     |
| scope         | required              | Une liste d’étendues séparées par des espaces.  Les étendues demandées dans ce tronçon doivent être équivalentes aux étendues demandées dans le tronçon de requête authorization_code d’origine, ou correspondre à un sous-ensemble de ces dernières.  Si les étendues spécifiées dans cette requête couvrent plusieurs serveurs de ressources, le point de terminaison v2.0 renvoie alors un jeton pour la ressource spécifiée dans la première étendue.  Pour obtenir une explication plus détaillée des étendues, consultez les [autorisations, consentements et étendues](active-directory-v2-scopes.md). |
| refresh_token | required              | Le jeton d’actualisation que vous avez acquis dans le second tronçon du flux.                                                                                                                                                                                                                                                                                                                                                                                                       |
| redirect_uri  | required              | Valeur redirect_uri qui a déjà été utilisée pour obtenir le paramètre authorization_code.                                                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | requis pour les applications Web | Le secret d’application que vous avez créé dans le portail d’inscription des applications pour votre application.  Il ne doit pas être utilisé dans une application native, car les clés secrètes client ne peuvent pas être stockées de manière sûre sur les appareils.  Il est requis pour les applications Web et les API Web, qui présentent la capacité de stocker de manière sûre les clés secrètes client sur le côté serveur.                                                                                                                                                     |

#### <a name="successful-response"></a>Réponse correcte
Une réponse de jeton réussie se présente ainsi :

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paramètre     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, par exemple une API Web.                                                                                                                                                                                                                                                                                                                                      |
| token_type    | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur.                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | La durée de validité (en secondes) du jeton d’accès.                                                                                                                                                                                                                                                                                                                                                                                                         |
| scope         | L’étendue de validité du jeton d’accès.                                                                                                                                                                                                                                                                                                                                                                                                           |
| refresh_token | Un nouveau jeton d’actualisation OAuth 2.0. Vous devez remplacer l’ancien jeton d’actualisation par ce nouveau jeton d’actualisation nouvellement acquis, afin de vous assurer que vos jetons d’actualisation demeurent valident le plus longtemps possible. <br> **Remarque :** Fourni uniquement si l’étendue `offline_access` a été demandée.                                                                                                                                                                                                 |
| id_token      | Un jeton Web JSON non signé (JWT). L’application peut décoder les segments de ce jeton à l’aide d’un décodeur base64Url afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais ne peut aucunement les utiliser pour les limites d’autorisation ou de sécurité.  Pour en savoir plus sur les id_tokens, consultez la [référence sur les jetons du point de terminaison v2.0](active-directory-v2-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |

#### <a name="error-response"></a>Réponse d’erreur
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paramètre         | Description                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification.            |
| error_codes |Liste des codes d’erreur STS spécifiques pouvant être utiles dans les tests de diagnostic. |
| timestamp |Heure à laquelle l’erreur s’est produite. |
| trace_id |Identifiant unique de la demande pouvant être utile dans les tests de diagnostic. |
| correlation_id |Identifiant unique de la demande pouvant être utile dans les tests de diagnostic sur les divers composants. |

Pour obtenir une description des codes d’erreur et connaître l’action client recommandée, consultez [Codes d’erreur pour les erreurs de point de terminaison de jeton](#error-codes-for-token-endpoint-errors).

