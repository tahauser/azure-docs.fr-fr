---
title: "Azure AD v2.0 permet d’accéder aux ressources sécurisées sans aucune intervention de l’utilisateur | Microsoft Docs"
description: "Créez des applications web à l’aide de la mise en œuvre du protocole d’authentification OAuth 2.0 d’Azure AD."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 28616657c5aae4f6ada1ec592a2a6287e8607b6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>Azure Active Directory v2.0 et le flux d'informations d'identification du client OAuth 2.0
Vous pouvez utiliser [l’octroi des informations d’identification du client OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), parfois appelé *OAuth à deux branches* pour accéder à des ressources hébergées sur le web à l’aide de l’identité d’une application. Ce type d'octroi est couramment utilisé pour les interactions de serveur à serveur qui doivent s’exécuter en arrière-plan sans l'interaction immédiate d’un utilisateur. Ces types d’application sont souvent appelés *démons* (daemons) ou *comptes de service*.

> [!NOTE]
> Le point de terminaison v2.0 ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure Active Directory. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).
>
>

Dans l’octroi *OAuth à trois branches* le plus courant, une application cliente est autorisée à accéder à une ressource pour le compte d’un utilisateur spécifique. L’autorisation est déléguée de l’utilisateur à l’application, en général, durant le processus de [consentement](active-directory-v2-scopes.md). Toutefois, dans le flux des informations d’identification du client, les autorisations sont accordées directement à l’application elle-même. Lorsque l’application présente un jeton à une ressource, la ressource impose que l’application elle-même, et non pas l'utilisateur, ait l’autorisation d’effectuer une action.

## <a name="protocol-diagram"></a>Schéma de protocole
Le flux d’informations d’identification client complet est similaire à l’illustration suivante. Nous décrirons en détail chacune des étapes plus loin dans cet article.

![Flux des informations d’identification du client](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obtenir l’autorisation directe
Une application reçoit généralement l’autorisation directe d’accéder à une ressource de deux manières : via une liste de contrôle d’accès (ACL) sur la ressource, ou via l’attribution de l’autorisation d’application dans Azure Active Directory (Azure AD). Ces deux méthodes sont les plus courantes dans Azure AD et sont recommandées pour les clients et les ressources qui exécutent le flux des informations d’identification du client. Toutefois, une ressource peut choisir d’autoriser ses clients d’autres manières. Chaque serveur de ressources peut choisir la méthode la plus logique pour son application.

### <a name="access-control-lists"></a>Listes de contrôle d'accès
Un fournisseur de ressources peut appliquer une vérification d’autorisation basée sur une liste d’ID d’application qu’il connaît et octroyer un niveau d’accès spécifique. Lorsque la ressource reçoit un jeton à partir du point de terminaison v2.0, elle peut décoder le jeton et extraire l’ID d’application du client à partir des revendications `appid` et `iss`. Elle compare ensuite l’application par rapport à une liste ACL qu’elle gère. La granularité et la méthode ACL peuvent varier considérablement entre les ressources.

Un cas d’utilisation typique consiste à utiliser une liste ACL afin d'exécuter des tests pour une application web ou une API Web. L’API Web peut accorder uniquement un sous-ensemble d'autorisations complètes à un client spécifique. Pour exécuter des tests de bout en bout sur l’API, créez un client de test qui acquiert des jetons à partir du point de terminaison v2.0 puis envoie ceux-ci à l’API. L’API vérifie ensuite l’ID d’application du client de test dans la liste ACL pour lui accorder un accès complet à toutes les fonctionnalités de l’API. Si vous utilisez ce type d’ACL, veillez à valider non seulement la valeur `appid` de l'appelant, mais également que la valeur `iss` du jeton approuvé.

Ce type d’autorisation est courant pour les démons et les comptes de service qui doivent accéder à des données qui appartiennent à des utilisateurs avec des comptes Microsoft personnels. Pour les données appartenant à des organisations, nous vous recommandons d’acquérir l’autorisation requise via les autorisations de l’application.

### <a name="application-permissions"></a>Autorisations de l’application
Au lieu d’utiliser des listes ACL, vous pouvez utiliser l’API pour exposer un ensemble d’autorisations de l’application. Une autorisation de l’application est accordée à une application par un administrateur d’une organisation et peut uniquement être utilisée pour accéder aux données appartenant à cette organisation et ses employés. Par exemple, Microsoft Graph expose plusieurs autorisations d’application pour effectuer les opérations suivantes :

* Lire les messages dans toutes les boîtes aux lettres
* Lire et écrire des messages dans toutes les boîtes aux lettres
* Envoyer des messages en tant que n’importe quel utilisateur
* Lire les données du répertoire

Pour plus d’informations sur les autorisations d’application, consultez la page [Microsoft Graph](https://graph.microsoft.io).

Pour utiliser les autorisations d’application dans votre application, effectuez les étapes abordées dans les sections suivantes.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail d’inscription de l’application
1. Accédez à votre application dans le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou [créez une application](active-directory-v2-app-registration.md), si ce n’est déjà fait. Vous devez utiliser au moins une clé secrète d’application lorsque vous créez votre application.
2. Recherchez la section **Autorisations directes pour les applications** puis ajoutez les autorisations dont votre application a besoin.
3. **Enregistrez** l’inscription de l’application.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Recommandé : connectez l’utilisateur à votre application
En général, lorsque vous créez une application qui utilise des autorisations d’application, l’application doit disposer d’une page/vue qui permet à l’administrateur d’approuver les autorisations de l’application. Cette page peut faire partie du flux d’inscription de l’application, des paramètres de l’application ou d’un flux de connexion dédié. Dans de nombreux cas, il est judicieux pour l’application d’afficher la vue de « connexion » uniquement après qu’un utilisateur se soit connecté avec un compte Microsoft professionnel ou scolaire.

Si vous connectez l’utilisateur à votre application vous pouvez identifier l’organisation à laquelle l’utilisateur appartient avant de lui demander d’approuver les autorisations d'application. Bien que cela ne soit pas strictement nécessaire, cela peut vous aider à créer une expérience plus intuitive pour vos utilisateurs. Pour connecter l’utilisateur, suivez nos [didacticiels sur le protocole v2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Demander les autorisations à un administrateur d’annuaire
Lorsque vous êtes prêt à demander les autorisations à l’administrateur de l'organisation, vous pouvez rediriger l’utilisateur vers le *point de terminaison de consentement de l’administrateur* v2.0.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paramètre | Condition | Description |
| --- | --- | --- |
| locataire |Requis |Le client d’annuaire auquel vous souhaitez demander l’autorisation. Peut être au format GUID ou sous forme de nom convivial. Si vous ne savez pas à quel client appartient l’utilisateur et si vous souhaitez lui permettre de se connecter avec n’importe quel client, utilisez `common`. |
| client_id |Requis |ID d’application que le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a affecté à votre application. |
| redirect_uri |Requis |L'URI de redirection où vous souhaitez que la réponse soit envoyée pour être gérée par votre application. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL, et peut avoir des segments de chemin d’accès supplémentaires. |
| state |Recommandé |Une valeur incluse dans la requête, qui est également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. La valeur d’état est utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |

À ce stade, Azure AD impose que seul un administrateur de client peut se connecter pour terminer la demande. L’administrateur est invité à approuver toutes les autorisations directes d’application demandées pour votre application dans le portail d’inscription des applications.

##### <a name="successful-response"></a>Réponse correcte
Si l’administrateur approuve les autorisations pour votre application, la réponse correcte sera :

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paramètre | Description |
| --- | --- | --- |
| locataire |Le client d’annuaire ayant accordé à votre application les autorisations demandées, au format GUID. |
| state |Une valeur incluse dans la requête, qui est également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. La valeur d’état est utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| admin_consent |Défini sur **true**. |

##### <a name="error-response"></a>Réponse d’erreur
Si l’administrateur n’approuve pas les autorisations pour votre application, la réponse d’échec ressemble à ce qui suit :

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paramètre | Description |
| --- | --- | --- |
| error |Une chaîne de code d’erreur que vous pouvez utiliser pour classer les types d’erreur et pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut vous aider à identifier la cause principale d’une erreur. |

Une fois que vous avez reçu une réponse correcte du point de terminaison de mise en service de l’application, votre application a acquis les autorisations directes d’application qu’elle avait demandées. Vous pouvez désormais demander un jeton pour la ressource que vous souhaitez.

## <a name="get-a-token"></a>Obtention d’un jeton
Une fois que vous avez acquis l’autorisation nécessaire pour votre application, passez à l’acquisition des jetons d’accès pour les API. Pour obtenir un jeton à l’aide de l’octroi des informations d’identification du client, envoyez une demande POST au point de terminaison v2.0 `/token` :

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Premier cas : demande de jeton d’accès avec un secret partagé

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Paramètre | Condition | Description |
| --- | --- | --- |
| client_id |Requis |L’ID d’application que le [portail d'inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a affecté à votre application. |
| scope |Requis |La valeur transmise pour le paramètre `scope` dans cette demande doit être l’identificateur de ressource (URI ID d’application) de la ressource souhaitée, avec le suffixe `.default`. Dans l’exemple Microsoft Graph, la valeur est `https://graph.microsoft.com/.default`. Cette valeur indique au point de terminaison v2.0 que parmi toutes les autorisations directes d’application que vous avez configurées pour votre application, il doit émettre un jeton pour celles associées à la ressource que vous souhaitez utiliser. |
| client_secret |Requis |La clé secrète que vous avez créée pour votre application dans le portail d’inscription des applications. |
| grant_type |Requis |Doit être `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Deuxième cas : demande de jeton d’accès avec un certificat

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

| Paramètre | Condition | Description |
| --- | --- | --- |
| client_id |Requis |L’ID d’application que le [portail d'inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a affecté à votre application. |
| scope |Requis |La valeur transmise pour le paramètre `scope` dans cette demande doit être l’identificateur de ressource (URI ID d’application) de la ressource souhaitée, avec le suffixe `.default`. Dans l’exemple Microsoft Graph, la valeur est `https://graph.microsoft.com/.default`. Cette valeur indique au point de terminaison v2.0 que parmi toutes les autorisations directes d’application que vous avez configurées pour votre application, il doit émettre un jeton pour celles associées à la ressource que vous souhaitez utiliser. |
| client_assertion_type |required |La valeur doit être `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| client_assertion |required | Assertion (JSON Web Token) dont vous avez besoin pour créer et signer avec le certificat inscrit comme informations d’identification pour votre application. Pour découvrir comment inscrire votre certificat et le format de l’assertion, consultez la rubrique traitant des [informations d’identification des certificats](active-directory-certificate-credentials.md).|
| grant_type |Requis |Doit être `client_credentials`. |

Notez que les paramètres sont presque les mêmes que dans le cas de la demande par secret partagé, sauf que le paramètre client_secret est remplacé par deux paramètres : client_assertion_type et client_assertion.

### <a name="successful-response"></a>Réponse correcte
Une réponse correcte se présente ainsi :

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Paramètre | Description |
| --- | --- |
| access_token |Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, par exemple une API Web. |
| token_type |Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est `bearer`. |
| expires_in |La durée de validité (en secondes) du jeton d’accès. |

### <a name="error-response"></a>Réponse d’erreur
Une réponse d’erreur ressemble à ceci :

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paramètre | Description |
| --- | --- |
| error |Une chaîne de code d’erreur que vous pouvez utiliser pour classer les types d’erreur se produisant et pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut vous aider à identifier la cause principale d’une erreur d’authentification. |
| error_codes |Liste des codes d’erreur STS spécifiques pouvant être utiles dans les tests de diagnostic. |
| timestamp |Heure à laquelle l’erreur s’est produite. |
| trace_id |Identifiant unique de la demande pouvant être utile dans les tests de diagnostic. |
| correlation_id |Identifiant unique de la demande pouvant être utile dans les tests de diagnostic sur les divers composants. |

## <a name="use-a-token"></a>Utilisation d’un jeton
Maintenant que vous avez acquis un jeton, utilisez-le pour effectuer des demandes auprès de la ressource. Lorsque le jeton expire, répétez la demande auprès du point de terminaison `/token` pour acquérir un nouveau jeton d’accès.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Exemple de code
Pour voir un exemple d’application qui implémente l’octroi client_credentials à l’aide du point de terminaison de consentement de l’administrateur, consultez notre [exemple de code de démon v2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
