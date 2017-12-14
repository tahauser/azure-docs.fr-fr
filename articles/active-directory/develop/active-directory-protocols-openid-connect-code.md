---
title: "Comprendre le flux de code d’authentification OpenID Connect dans Azure AD | Microsoft Docs"
description: "Cet article explique comment utiliser des messages HTTP pour autoriser l’accès aux applications web et API web dans votre client à l’aide d’Azure Active Directory et d’OpenID Connect."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3a813d73dc8a80c46e1b7500ec72ccb2a47bc6d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autoriser l’accès aux applications web à l’aide d’OpenID Connect et d’Azure Active Directory
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) est une couche d’identité simple basée sur le protocole OAuth 2.0. OAuth 2.0 définit des mécanismes permettant d’obtenir et d’utiliser des **jetons d’accès** pour accéder à des ressources protégées ; en revanche, ces mécanismes ne définissent aucune méthode standard pour fournir des informations d’identité. OpenID Connect implémente l’authentification en tant qu’extension pour le processus d’autorisation OAuth 2.0. Il fournit des informations sur l’utilisateur final sous la forme d’un `id_token` qui vérifie l’identité de l’utilisateur et fournit des informations de profil de base sur l’utilisateur.

Nous recommandons OpenID Connect si vous concevez une application web hébergée sur un serveur et accessible par le biais d’un navigateur.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Flux d’authentification à l’aide d’OpenID Connect
Le flux de connexion le plus simple comprend les étapes suivantes (chacune d’elles est décrite en détail ci-dessous).

![Flux d’authentification OpenID Connect](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Document de métadonnées OpenID Connect

OpenID Connect décrit un document de métadonnées qui contient la plupart des informations nécessaires pour qu’une application effectue la connexion. Cela inclut des informations telles que les URL à utiliser et l’emplacement des clés de signature publiques du service. Le document de métadonnées OpenID Connect est disponible ici :

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Les métadonnées représentent un simple document JavaScript Objet Notation (JSON). Consultez l’extrait suivant pour obtenir un exemple. Le contenu de l'extrait de code est décrit en détail dans les [spécifications d’OpenID Connect](https://openid.net).

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## <a name="send-the-sign-in-request"></a>Envoyer la requête de connexion
Lorsque votre application web a besoin d’authentifier l’utilisateur, elle doit le diriger vers le point de terminaison `/authorize`. Cette requête est similaire au premier tronçon du [flux de code d’autorisation OAuth 2.0](active-directory-protocols-oauth-code.md). Notons toutefois quelques distinctions importantes :

* La requête doit inclure l’étendue `openid` dans le paramètre `scope`.
* Le paramètre `response_type` doit inclure `id_token`.
* La demande doit inclure le paramètre `nonce` .

Voici donc un exemple de requête :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Paramètre |  | Description |
| --- | --- | --- |
| locataire |required |La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application.  Les valeurs autorisées sont les identificateurs du client, par exemple `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` ou `common` pour les jetons indépendants du client |
| client_id |required |L’ID d’application attribué à votre application lorsque vous l’avez inscrite auprès d’Azure AD. Vous le trouverez sur le portail Azure. Cliquez sur **Azure Active Directory**, puis sur **Inscriptions des applications**. Sélectionnez ensuite l’application et recherchez son identifiant sur la page de l’application. |
| response_type |required |Doit inclure `id_token` pour la connexion à OpenID Connect.  Il peut inclure d’autres types de réponses, comme `code`. |
| scope |required |Une liste d’étendues séparées par des espaces.  Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion dans l’interface utilisateur de consentement.  Vous pouvez inclure d’autres étendues dans cette requête pour solliciter le consentement. |
| nonce |required |Valeur incluse dans la demande (générée par l’application) qui est intégrée au jeton `id_token` obtenu sous la forme d’une revendication.  L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons.  La valeur est généralement une valeur unique et aléatoire ou un GUID pouvant être utilisé pour identifier l’origine de la requête. |
| redirect_uri |recommandé |L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification.  Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| response_mode |recommandé |Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application.  Les valeurs prises en charge sont `form_post` pour une *requête HTTP POST de type formulaire* ou `fragment` pour un *fragment d’URL*.  Pour les applications web, nous vous recommandons d’utiliser `response_mode=form_post` pour garantir le transfert le plus sécurisé des jetons à votre application. |
| state |recommandé |Une valeur incluse dans la requête qui est également renvoyée dans la réponse de jeton.  Il peut s’agir d’une chaîne du contenu de votre choix.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt |facultatif |Indique le type d’interaction utilisateur requis.  Les seules valeurs valides pour l’instant sont « login », « none » et « consent ».  `prompt=login` oblige l’utilisateur à saisir ses informations d’identification lors de cette requête, annulant de fait l’authentification unique.  Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu’aucune invite interactive d’aucune sorte n’est présentée à l’utilisateur.  Si la demande ne peut pas être exécutée en mode silencieux au moyen d’une authentification unique, le point de terminaison renvoie une erreur.  `prompt=consent` déclenche l’affichage de la boîte de dialogue de consentement OAuth après la connexion de l’utilisateur, afin de lui demander d’octroyer des autorisations à l’application. |
| login_hint |facultatif |Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur.  Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur d’une connexion précédente à l’aide de la revendication `preferred_username`. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification.

### <a name="sample-response"></a>Exemple de réponse
Exemple de réponse obtenue après l’authentification de l’utilisateur :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paramètre | Description |
| --- | --- |
| id_token |Jeton `id_token` que l’application a demandé. Vous pouvez utiliser ce jeton `id_token` pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. |
| state |Une valeur incluse dans la requête qui est également renvoyée dans la réponse de jeton. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |

### <a name="error-response"></a>Réponse d’erreur
Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| . | Description |
| --- | --- |
| error |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison d’autorisation
Le tableau suivant décrit les différents codes d’erreur qui peuvent être retournés dans le paramètre `error` de la réponse d’erreur.

| Code d'erreur | Description | Action du client |
| --- | --- | --- |
| invalid_request |Erreur de protocole, tel qu’un paramètre obligatoire manquant. |Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux. |
| unauthorized_client |L’application cliente n’est pas autorisée à demander un code d’autorisation. |Cela se produit généralement lorsque l’application cliente n’est pas inscrite dans Azure AD ou n’est pas ajoutée au client Azure AD de l’utilisateur. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |
| access_denied |Le propriétaire de la ressource n’a pas accordé son consentement. |L’application cliente peut avertir l’utilisateur qu’elle ne peut pas continuer sans son consentement. |
| unsupported_response_type |Le serveur d’autorisation ne prend pas en charge le type de réponse dans la demande. |Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux. |
| server_error |Le serveur a rencontré une erreur inattendue. |relancez la requête. Ces erreurs peuvent résulter de conditions temporaires. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une erreur temporaire. |
| temporarily_unavailable |Le serveur est temporairement trop occupé pour traiter la demande. |relancez la requête. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une condition temporaire. |
| invalid_resource |La ressource cible n’est pas valide car elle n’existe pas, Azure AD ne la trouve pas ou elle n’est pas configurée correctement. |Cela indique que la ressource, si elle existe, n’a pas été configurée dans le client. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |

## <a name="validate-the-idtoken"></a>Valider le jeton id_token
La réception du jeton `id_token` ne suffit pas à authentifier l’utilisateur. Vous devez valider la signature et vérifier la conformité des revendications du jeton `id_token` par rapport à la configuration requise de votre application. Le point de terminaison Azure AD utilise les jetons web JSON (JWT) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Vous pouvez décider de valider l’élément `id_token` dans le code du client, mais une pratique courante consiste à envoyer l’élément `id_token` vers un serveur principal, afin d’y appliquer la validation. Une fois que vous avez validé la signature du jeton `id_token`, vous devez vérifier quelques revendications.

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

* S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
* S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
* S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Une fois que vous avez validé le jeton `id_token`, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton `id_token` pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, les autorisations, etc. Pour plus d’informations sur les types de jeton et les revendications, consultez la page [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion
Lorsque vous souhaitez déconnecter l'utilisateur de l'application, la suppression des cookies de votre application ou l’arrêt de la session de l’utilisateur ne suffisent pas.  Vous devez également rediriger l’utilisateur vers le `end_session_endpoint` pour suivre la procédure de déconnexion.  Si vous n’y parvenez pas, l’utilisateur sera en mesure de se réauthentifier à votre application sans avoir à saisir de nouveau ses informations d’identification, car il disposera d’une session d’authentification unique valide auprès du point de terminaison Azure AD.

Vous pouvez simplement rediriger l’utilisateur vers le `end_session_endpoint` répertorié dans le document de métadonnées OpenID Connect :

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Paramètre |  | Description |
| --- | --- | --- |
| post_logout_redirect_uri |recommandé |URL vers laquelle l’utilisateur doit être redirigé après la déconnexion.  Si elle n’est pas incluse, l’utilisateur voit un message générique. |

## <a name="single-sign-out"></a>Authentification unique
Lorsque vous redirigez l’utilisateur vers `end_session_endpoint`, Azure AD efface la session de l’utilisateur dans le navigateur. Toutefois, l’utilisateur peut rester connecté à d’autres applications qui utilisent Azure AD pour l’authentification. Pour permettre à ces applications de déconnecter simultanément l’utilisateur, Azure AD envoie une requête HTTP GET au paramètre `LogoutUrl` enregistré de toutes les applications auxquelles l’utilisateur est actuellement connecté. Les applications doivent répondre à cette requête en effaçant toute session qui identifie l’utilisateur et en renvoyant une réponse `200`.  Si vous souhaitez prendre en charge la déconnexion unique dans votre application, vous devez implémenter ce paramètre `LogoutUrl` dans le code de votre application.  Vous pouvez définir le paramètre `LogoutUrl` à partir du portail Azure :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez votre client Active Directory en cliquant sur votre compte en haut à droite de la page.
3. Dans le volet de navigation de gauche, choisissez **Azure Active Directory**, **Inscriptions des applications** puis sélectionnez votre application.
4. Cliquez sur **Propriétés** et recherchez la zone de texte **URL de déconnexion**. 

## <a name="token-acquisition"></a>Acquisition de jeton
Beaucoup d’applications web nécessitent une connexion de l’utilisateur, puis un accès au service web pour le compte de cet utilisateur à l’aide d’OAuth. Ce scénario utilise OpenID Connect pour l’authentification de l’utilisateur tout en récupérant un `authorization_code` pouvant être sollicité pour obtenir des jetons `access_tokens` à l’aide du flux de code d’autorisation OAuth.

## <a name="get-access-tokens"></a>Obtenir des jetons d’accès
Pour acquérir des jetons d’accès, vous devez modifier la requête de connexion ci-dessus :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

En incluant des étendues d’autorisation dans la demande et en utilisant `response_type=code+id_token`, le point de terminaison `authorize` garantit que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope` et renvoie un code d’autorisation à votre application afin de l’échanger contre un jeton d’accès.

### <a name="successful-response"></a>Réponse correcte
Une réponse correcte utilisant `response_mode=form_post` se présente ainsi :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paramètre | Description |
| --- | --- |
| id_token |Jeton `id_token` que l’application a demandé. Vous pouvez utiliser ce jeton `id_token` pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. |
| code |Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible. Les codes d’autorisation présentent une durée de vie courte. Généralement, ils expirent au bout de 10 minutes. |
| state |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

### <a name="error-response"></a>Réponse d’erreur
Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| . | Description |
| --- | --- |
| error |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

Pour obtenir une description des codes d’erreur éventuels et connaître l’action client recommandée associée, consultez [Codes d’erreur pour les erreurs de point de terminaison d’autorisation](#error-codes-for-authorization-endpoint-errors).

Une fois que vous avez obtenu une autorisation `code` et un `id_token`, vous pouvez connecter l’utilisateur et obtenir des jetons d’accès pour son compte.  Pour connecter l’utilisateur, vous devez valider le `id_token` conformément à la description indiquée ci-dessus. Pour obtenir des jetons d’accès, vous pouvez suivre la procédure décrite dans la section « Utiliser le code d’autorisation pour demander un jeton d’accès » de notre [documentation du protocole OAuth](active-directory-protocols-oauth-code.md).
